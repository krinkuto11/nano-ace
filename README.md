# Nano-Ace: Distroless Acestream Docker Images

This repository provides distroless Docker images for Acestream Engine, offering minimal, secure container images for running Acestream.

## Available Versions

We provide 4 different versions of Acestream with distroless base images:

| Version | Python | Dockerfile | Container Tag |
|---------|--------|------------|---------------|
| 3.2.11 (latest) | 3.10 | `Dockerfile.3.2.11-py3.10` | `ghcr.io/krinkuto11/nano-ace:latest` or `:3.2.11-py3.10` |
| 3.2.11 | 3.8 | `Dockerfile.3.2.11-py3.8` | `ghcr.io/krinkuto11/nano-ace:3.2.11-py3.8` |
| 3.1.75rc4 | 3.7 | `Dockerfile.3.1.75rc4-py3.7` | `ghcr.io/krinkuto11/nano-ace:3.1.75rc4-py3.7` |
| 3.1.74 | 3.7 | `Dockerfile.3.1.74` | `ghcr.io/krinkuto11/nano-ace:3.1.74` |

## Why Distroless?

Distroless images contain only your application and its runtime dependencies. They do not contain package managers, shells, or any other programs you would expect to find in a standard Linux distribution.

Benefits:
- **Smaller image size**: Distroless images are significantly smaller than traditional base images
- **Improved security**: Reduced attack surface with fewer components
- **Better CVE scanning**: Less noise from unnecessary packages
- **Production-ready**: Best practice employed by Google and other tech giants

## Building the Images

### Build a specific version locally

```bash
# Build Acestream 3.2.11 with Python 3.10
docker build -f Dockerfile.3.2.11-py3.10 -t acestream:3.2.11-py3.10 .

# Build Acestream 3.2.11 with Python 3.8
docker build -f Dockerfile.3.2.11-py3.8 -t acestream:3.2.11-py3.8 .

# Build Acestream 3.1.75rc4 with Python 3.7
docker build -f Dockerfile.3.1.75rc4-py3.7 -t acestream:3.1.75rc4-py3.7 .

# Build Acestream 3.1.74
docker build -f Dockerfile.3.1.74 -t acestream:3.1.74 .
```

### Build all versions

```bash
for dockerfile in Dockerfile.*; do
    version=$(echo $dockerfile | sed 's/Dockerfile.//')
    docker build -f $dockerfile -t acestream:$version .
done
```

## Running the Container

### Basic usage

```bash
# Use the latest version
docker run -d \
  --name acestream \
  -p 6878:6878 \
  -p 8621:8621 \
  ghcr.io/krinkuto11/nano-ace:latest

# Or use a specific version
docker run -d \
  --name acestream \
  -p 6878:6878 \
  -p 8621:8621 \
  ghcr.io/krinkuto11/nano-ace:3.2.11-py3.10
```

### With custom arguments

The default arguments are `--client-console --bind-all`. You can override the entire CMD:

```bash
docker run -d \
  --name acestream \
  -p 6878:6878 \
  -p 8621:8621 \
  ghcr.io/krinkuto11/nano-ace:3.2.11-py3.10 \
  /acestream/start-engine --client-console --bind-all --log-debug 1
```

### With persistent cache

```bash
docker run -d \
  --name acestream \
  -p 6878:6878 \
  -p 8621:8621 \
  -v acestream-cache:/acestream/.ACEStream \
  ghcr.io/krinkuto11/nano-ace:3.2.11-py3.10
```

## Exposed Ports

- **6878**: HTTP API endpoint
- **8621**: P2P BitTorrent port

## API Access

Once the container is running, you can access the API at:

```
http://localhost:6878/webui/api/service?method=get_version
```

## GitHub Actions

This repository includes automated GitHub Actions workflows that:
- Build all 4 Acestream versions
- Push images to GitHub Container Registry (ghcr.io)
- Trigger on:
  - Push to `main` or `develop` branches
  - Pull requests
  - Manual workflow dispatch
  - Release events

## Multi-Stage Build Architecture

Each Dockerfile uses a multi-stage build:

1. **Builder Stage**: Uses a full Python image to download and extract Acestream binaries
2. **Runtime Stage**: Uses distroless cc-debian12 image (includes C libraries) with copied Python runtime for minimal, secure runtime

This approach ensures:
- Build tools are not included in the final image
- Minimal attack surface
- Smaller image size
- Better security posture
- Python version compatibility (Acestream binaries require specific Python versions)

## Security

- Based on Google's distroless Python images (Debian 12)
- No shell or package managers in final image
- Minimal dependencies
- Regular updates via GitHub Actions

## Contributing

Feel free to open issues or submit pull requests for improvements.

## License

This repository provides Dockerfiles for Acestream. Acestream itself is subject to its own license terms.
