# Ruby devcontainer image
FROM mcr.microsoft.com/devcontainers/ruby:3.1-bullseye

LABEL org.opencontainers.image.source="https://github.com/toanvv42/devcontainer-images"
LABEL org.opencontainers.image.description="Ruby 3.1 devcontainer"

# Install common Ruby development dependencies
RUN apt-get update && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y \
    libpq-dev \
    libsqlite3-dev \
    libmysqlclient-dev \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

ENV SHELL=/bin/zsh
