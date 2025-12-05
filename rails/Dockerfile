# Rails devcontainer image - optimized for Rails 7+ development
FROM mcr.microsoft.com/devcontainers/ruby:3.1-bullseye

LABEL org.opencontainers.image.source="https://github.com/toanvv42/devcontainer-images"
LABEL org.opencontainers.image.description="Ruby on Rails devcontainer with PostgreSQL, Redis, and Node.js"

# Install system dependencies for Rails
RUN apt-get update && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y \
    # PostgreSQL client
    libpq-dev \
    postgresql-client \
    # Image processing (Active Storage)
    libvips-dev \
    imagemagick \
    # PDF processing
    pdftk \
    poppler-utils \
    # Node.js for asset pipeline
    nodejs \
    npm \
    # Redis CLI
    redis-tools \
    # Other useful tools
    libxml2-dev \
    libxslt1-dev \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Install Yarn
RUN npm install -g yarn

# Configure bundler
RUN gem update --system && \
    gem install bundler

# Set Rails environment defaults
ENV RAILS_ENV=development
ENV SHELL=/bin/zsh
