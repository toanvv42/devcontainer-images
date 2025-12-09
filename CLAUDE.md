# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This repository contains reusable devcontainer base images hosted on GitHub Container Registry (ghcr.io). Images are built for multiple architectures (linux/amd64, linux/arm64).

## Build Commands

```bash
# Build a specific image locally
docker build -t devcontainer-<image-name> ./<image-name>

# Build with specific platform
docker build --platform linux/amd64 -t devcontainer-<image-name> ./<image-name>

# Test an image
docker run --rm -it devcontainer-<image-name> <tool> --version
```

**Lint Dockerfiles:**
```bash
hadolint <image-name>/Dockerfile
```

## Architecture

### Image Hierarchy

All images are standalone (no inter-image dependencies). Each image directory contains a single `Dockerfile`:

- `base/` - Common tools + gitleaks + hadolint
- `node/` - Node.js 22 + pnpm
- `python/` - Python 3.12 + poetry
- `ruby/` - Ruby 3.1 + bundler + database dev libraries
- `rails/` - Ruby + Rails dependencies (Node.js, ImageMagick, Redis, etc.)
- `terraform/` - Terraform + SOPS + age + Kustomize + gcloud CLI
- `devsecops/` - Full security toolchain (Trivy, Checkov, tfsec, Semgrep, kubesec, kube-linter, etc.)

### Dockerfile Patterns

- Base image: `mcr.microsoft.com/devcontainers/base:ubuntu-22.04`
- Tool versions pinned via `ARG` at top of Dockerfile for reproducibility
- Multi-arch support via `TARGETARCH` variable
- Architecture-specific downloads handled with case statements

### CI/CD

GitHub Actions workflow in `.github/workflows/build-images.yml`:
- Builds all images in matrix (base, node, python, ruby, rails, terraform, devsecops)
- Pushes to ghcr.io on main branch, PRs build only
- Weekly scheduled rebuild (Sundays 00:00 UTC) for security updates
- Tags: `latest` (main), `sha-<commit>`, `pr-<number>`

## Version Updates

To update tool versions, modify the `ARG` values at the top of the respective Dockerfile:

```dockerfile
ARG TRIVY_VERSION="0.56.2"
ARG CHECKOV_VERSION="3.2.0"
```
