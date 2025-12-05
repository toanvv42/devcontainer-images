# Node.js devcontainer image
FROM mcr.microsoft.com/devcontainers/javascript-node:22

LABEL org.opencontainers.image.source="https://github.com/toanvv42/devcontainer-images"
LABEL org.opencontainers.image.description="Node.js 22 devcontainer with pnpm"

# Enable corepack for pnpm
RUN corepack enable && corepack prepare pnpm@latest --activate

# Set default shell
ENV SHELL=/bin/zsh
