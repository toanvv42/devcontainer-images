# Fullstack devcontainer image (Python + Node.js + Tailscale)
FROM mcr.microsoft.com/devcontainers/python:3.12

LABEL org.opencontainers.image.source="https://github.com/toanvv42/devcontainer-images"
LABEL org.opencontainers.image.description="Fullstack devcontainer with Python 3.12, Node.js 22, and Tailscale"

# Pinned versions
ARG NODE_MAJOR=22

# Install Node.js 22
RUN curl -fsSL https://deb.nodesource.com/setup_${NODE_MAJOR}.x | bash - \
    && apt-get install -y nodejs \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Enable corepack for pnpm
RUN corepack enable && corepack prepare pnpm@latest --activate

# Install poetry for Python dependency management
RUN pip install --no-cache-dir poetry

# Configure poetry to not create virtual envs (container is already isolated)
RUN poetry config virtualenvs.create false

# Install Tailscale
RUN curl -fsSL https://tailscale.com/install.sh | sh

# Set default shell
ENV SHELL=/bin/zsh
