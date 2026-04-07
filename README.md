# PHP gRPC Releases

Automated release management for pre-compiled PHP gRPC extensions with **zero-touch publishing**.

Build gRPC → Artifacts Generated → **Release Published Automatically** 🚀

## Features

✅ **Fully Automated** - No manual steps needed after triggering build
✅ **Multi-Architecture** - AMD64 & ARM64 binaries
✅ **Easy Integration** - Use in Dockerfile with simple curl
✅ **Version Tracking** - gRPC and PHP version in release tags

---

## Quick Start

### Option 1: Download Pre-built Binaries

Visit [GitHub Releases](https://github.com/dicoding-dev/php-grpc-releases/releases) and download:
- `grpc-amd64.so` - For x86_64 servers
- `grpc-arm64.so` - For ARM64 servers

### Option 2: Use in Dockerfile

```dockerfile
# Example: PHP 8.3 with gRPC v1.80.0
FROM php:8.3.29-fpm

# Download & install pre-compiled gRPC
RUN curl -L -o /tmp/grpc.so \
    https://github.com/dicoding-dev/php-grpc-releases/releases/download/v1.80.0-php8.3/grpc-amd64.so && \
    mv /tmp/grpc.so /usr/local/lib/php/extensions/no-debug-non-zts-20220829/ && \
    docker-php-ext-enable grpc && \
    rm -rf /tmp/*
```

---

## How It Works

### Architecture

```
┌─────────────────────────────────────────────────────┐
│  Build Trigger                                      │
│  (gRPC Version + PHP Version)                       │
└────────────────┬────────────────────────────────────┘
                 │
                 ▼
┌─────────────────────────────────────────────────────┐
│  build-grpc.yml (GitHub Actions Workflow)           │
│  • Compile gRPC extensions                          │
│  • Upload AMD64 & ARM64 artifacts                   │
└────────────────┬────────────────────────────────────┘
                 │
                 ▼ (Auto-Trigger)
┌─────────────────────────────────────────────────────┐
│  publish-to-releases.yml (GitHub Actions)           │
│  • Download artifacts                               │
│  • Extract versions from artifact names             │
│  • Create GitHub Release                            │
└────────────────┬────────────────────────────────────┘
                 │
                 ▼
┌─────────────────────────────────────────────────────┐
│  📦 GitHub Releases (Public Downloads)              │
│  Tag: v{GRPC_VERSION}-php{PHP_VERSION}              │
│  Example: v1.80.0-php8.3                            │
└─────────────────────────────────────────────────────┘
```

### Workflow Triggers

| Workflow | Trigger | Action |
|----------|---------|--------|
| **build-grpc.yml** | Manual dispatch | Compile gRPC + upload artifacts |
| **publish-to-releases.yml** | Auto (on build success) | Download artifacts → create release |
| **publish-to-releases.yml** | Manual dispatch | Override auto-detection for custom releases |

---

## Creating a New Release

### Automatic Process (Recommended)

1. **Trigger Build Workflow:**
   ```bash
   gh workflow run build-grpc.yml \
     --repo dicoding-dev/php-grpc-releases \
     --field grpc_version=1.80.0 \
     --field php_version=8.3
   ```

2. **Done! 🎉** The publish workflow will automatically:
   - Wait for build to complete
   - Download artifacts
   - Extract version info
   - Create GitHub Release
   - Generate download URLs

### Manual Process (Via GitHub UI)

1. Go to [Actions → Build gRPC PHP](https://github.com/dicoding-dev/php-grpc-releases/actions/workflows/build-grpc.yml)
2. Click **"Run workflow"**
3. Enter gRPC version and PHP version
4. Click **"Run workflow"**
5. Wait for build to complete...
6. Release will be created automatically ✨

---

## Release Format

### Release Naming Convention

```
Tag: v{GRPC_VERSION}-php{PHP_VERSION}
Example: v1.80.0RC1-php8.3
```

### Available Binaries

Each release includes:
- **grpc-amd64.so** - For x86_64 (Intel/AMD) architecture
- **grpc-arm64.so** - For ARM64 (Apple Silicon, Raspberry Pi, etc.)

### Download URLs

```
https://github.com/dicoding-dev/php-grpc-releases/releases/download/v{GRPC_VERSION}-php{PHP_VERSION}/grpc-amd64.so
https://github.com/dicoding-dev/php-grpc-releases/releases/download/v{GRPC_VERSION}-php{PHP_VERSION}/grpc-arm64.so
```

---

## Supported Versions

### PHP Versions
- PHP 8.1
- PHP 8.2
- PHP 8.3

### gRPC Versions
- 1.80.0+
- Release Candidates (1.80.0RC1, etc.)

---

## Workflow Files

### build-grpc.yml
- Compiles gRPC PHP extension from PECL
- Builds for both AMD64 and ARM64 architectures
- Strips debug symbols to reduce binary size
- Uploads artifacts with naming pattern: `grpc-{arch}-ubuntu22.04-php{version}-v{grpc_version}`

### publish-to-releases.yml
- **Triggers:**
  - ✅ Automatic when `build-grpc.yml` completes
  - ✅ Manual override available
- **Steps:**
  1. Download artifacts from build workflow
  2. Parse version info from artifact names
  3. Organize binaries
  4. Create GitHub Release with notes & assets
  5. Print download summary

---

## Troubleshooting

### Release not created after build?

Check the workflow logs:
1. Go to [Actions](https://github.com/dicoding-dev/php-grpc-releases/actions)
2. Click on the failed `publish-to-releases` run
3. Look for error messages in the logs

Common issues:
- **"No successful build found"** → Wait for build to complete first
- **"Could not parse artifact name"** → Artifact naming mismatch
- **"Release already exists"** → That version was already released (skip)

### Artifact download fails in Docker?

Verify URL format:
```bash
# Test the URL
curl -I https://github.com/dicoding-dev/php-grpc-releases/releases/download/v1.80.0-php8.3/grpc-amd64.so
# Should return: 302 Found (redirect to CDN)
```

---

## Environment Details

- **Build OS:** Ubuntu 22.04
- **PHP Build Extensions:** Development headers, CLI, PEAR, XML
- **Binary Optimization:** Debug symbols stripped for smaller file size
- **Architectures:** AMD64 (x86_64), ARM64

---

## License

Same as [php-grpc-1.8RC](https://github.com/agissept/php-grpc-1.8RC)

---

## Related Projects

- **Build Source:** [php-grpc-1.8RC](https://github.com/agissept/php-grpc-1.8RC)
- **Official gRPC:** [grpc/grpc](https://github.com/grpc/grpc)
- **PHP PECL gRPC:** [pecl.php.net](https://pecl.php.net/package/grpc)

---

## Contributing

Found an issue? Have suggestions?

- Create an issue: [GitHub Issues](https://github.com/dicoding-dev/php-grpc-releases/issues)
- Check the workflow logs for debugging

---

**Last Updated:** 2026-04-07
**Repository:** [dicoding-dev/php-grpc-releases](https://github.com/dicoding-dev/php-grpc-releases)
