# Devcontainer Base Images

Reusable devcontainer base images hosted on GitHub Container Registry (ghcr.io).

## Available Images

| Image | Description | Base |
|-------|-------------|------|
| `ghcr.io/toanvv42/devcontainer-images/base` | Common tools (git, curl, jq, etc.) | Ubuntu 22.04 |
| `ghcr.io/toanvv42/devcontainer-images/node` | Node.js 22 + pnpm | node base |
| `ghcr.io/toanvv42/devcontainer-images/python` | Python 3.12 + poetry | python base |
| `ghcr.io/toanvv42/devcontainer-images/ruby` | Ruby 3.1 + bundler | ruby base |
| `ghcr.io/toanvv42/devcontainer-images/rails` | Ruby + Rails dependencies | ruby |
| `ghcr.io/toanvv42/devcontainer-images/terraform` | Terraform + cloud CLIs | base |

## Usage

Reference in your project's `.devcontainer/devcontainer.json`:

```json
{
  "image": "ghcr.io/toanvv42/devcontainer-images/rails:latest"
}
```

Or extend with a Dockerfile:

```dockerfile
FROM ghcr.io/toanvv42/devcontainer-images/rails:latest
# Add project-specific tools
```

## Building Locally

```bash
# Build specific image
docker build -t devcontainer-rails ./rails

# Build all images
docker compose build
```

## CI/CD

Images are automatically built and pushed to ghcr.io on:
- Push to `main` branch
- Pull request (build only, no push)
- Weekly schedule (to get security updates)

## Image Tags

- `latest` - Latest stable build from main
- `sha-<commit>` - Specific commit builds
