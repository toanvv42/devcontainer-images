# Base devcontainer image with common tools
FROM mcr.microsoft.com/devcontainers/base:ubuntu-22.04

LABEL org.opencontainers.image.source="https://github.com/toanvv42/devcontainer-images"
LABEL org.opencontainers.image.description="Base devcontainer with common tools"

# Pinned versions for security tools
ARG GITLEAKS_VERSION="8.18.4"
ARG HADOLINT_VERSION="2.12.0"

# Detect architecture
ARG TARGETARCH

# Install common development tools
RUN apt-get update && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y \
    curl \
    wget \
    git \
    jq \
    unzip \
    zip \
    gnupg \
    lsb-release \
    ca-certificates \
    apt-transport-https \
    build-essential \
    software-properties-common \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Install Gitleaks - Secret detection tool
RUN set -eux; \
    case "${TARGETARCH}" in \
        amd64) GL_ARCH="x64" ;; \
        arm64) GL_ARCH="arm64" ;; \
        *) echo "Unsupported arch: ${TARGETARCH}"; exit 1 ;; \
    esac; \
    curl -fsSLo /tmp/gitleaks.tar.gz \
        "https://github.com/gitleaks/gitleaks/releases/download/v${GITLEAKS_VERSION}/gitleaks_${GITLEAKS_VERSION}_linux_${GL_ARCH}.tar.gz"; \
    tar -xzf /tmp/gitleaks.tar.gz -C /tmp/; \
    install -m 0755 /tmp/gitleaks /usr/local/bin/gitleaks; \
    rm -rf /tmp/gitleaks*

# Install Hadolint - Dockerfile linter
RUN set -eux; \
    case "${TARGETARCH}" in \
        amd64) HL_ARCH="x86_64" ;; \
        arm64) HL_ARCH="arm64" ;; \
        *) echo "Unsupported arch: ${TARGETARCH}"; exit 1 ;; \
    esac; \
    curl -fsSLo /usr/local/bin/hadolint \
        "https://github.com/hadolint/hadolint/releases/download/v${HADOLINT_VERSION}/hadolint-Linux-${HL_ARCH}"; \
    chmod +x /usr/local/bin/hadolint

# Set default shell to zsh
ENV SHELL=/bin/zsh
