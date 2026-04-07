# PHP gRPC Releases

Automated release management for pre-compiled PHP gRPC extensions.

This repository downloads gRPC extensions built from [php-grpc-1.8RC](https://github.com/agissept/php-grpc-1.8RC) and publishes them to GitHub Releases for easy distribution.

## Usage

### Option 1: Download Binary Files
Download pre-compiled `.so` files from [Releases](https://github.com/agissept/php-grpc-releases/releases)

### Option 2: Use in Dockerfile

```dockerfile
# Example for PHP 8.3 with gRPC v1.80.0
FROM php:8.3.29-fpm

# Download gRPC extension from GitHub Releases
RUN curl -L -o /tmp/grpc.so \
    https://github.com/agissept/php-grpc-releases/releases/download/v1.80.0-php8.3/grpc-amd64.so && \
    mv /tmp/grpc.so /usr/local/lib/php/extensions/no-debug-non-zts-20220829/ && \
    docker-php-ext-enable grpc
```

## How It Works

1. Build gRPC in [php-grpc-1.8RC](https://github.com/agissept/php-grpc-1.8RC) using GitHub Actions
2. Trigger this repo's workflow with the build run ID
3. Artifacts are automatically downloaded and published to GitHub Releases
4. Use the release URL in your Dockerfile

## Workflow: Publish gRPC to GitHub Releases

**Trigger:** Manual (workflow_dispatch)

**Inputs:**
- `grpc_version` - gRPC version (e.g., 1.80.0RC1, 1.80.0)
- `php_version` - PHP version (e.g., 8.3)
- `source_run_id` - GitHub Actions Run ID (optional, auto-finds latest if empty)

**Steps:**
1. Find build artifacts from php-grpc-1.8RC
2. Download AMD64 and ARM64 binaries
3. Create GitHub Release with assets
4. Generate download URLs

## Release Format

Releases use the following convention:
```
Tag: v{GRPC_VERSION}-php{PHP_VERSION}
Example: v1.80.0-php8.3
```

Each release includes:
- `grpc-amd64.so` - AMD64 architecture
- `grpc-arm64.so` - ARM64 architecture

## Architecture

```
php-grpc-1.8RC (Build gRPC)
        ↓
Artifacts (amd64, arm64)
        ↓
php-grpc-releases (Publish)
        ↓
GitHub Releases (Public Download)
        ↓
Your Dockerfile (Use in container)
```

## Supported Versions

- PHP: 8.1, 8.2, 8.3
- gRPC: 1.80.0+

## How to Create a Release

1. Go to [php-grpc-1.8RC](https://github.com/agissept/php-grpc-1.8RC/actions/workflows/build-grpc.yml)
2. Click "Run workflow"
3. Fill in:
   - gRPC Version: `1.80.0` (or desired version)
   - PHP Version: `8.3` (or desired version)
4. Wait for build to complete
5. Note the Run ID from the completed workflow
6. Go to [this repo's Actions](https://github.com/agissept/php-grpc-releases/actions/workflows/publish-to-releases.yml)
7. Click "Run workflow"
8. Fill in:
   - gRPC Version: `1.80.0`
   - PHP Version: `8.3`
   - Source Run ID: (the ID from step 5, optional if auto-finds it)
9. Wait for release to be created
10. Download from [Releases](https://github.com/agissept/php-grpc-releases/releases)

## License

Same as [php-grpc-1.8RC](https://github.com/agissept/php-grpc-1.8RC)
