# Terraform/Infrastructure devcontainer image
FROM mcr.microsoft.com/devcontainers/base:ubuntu-22.04

LABEL org.opencontainers.image.source="https://github.com/toanvv42/devcontainer-images"
LABEL org.opencontainers.image.description="Terraform devcontainer with cloud CLIs"

# Pinned versions
ARG TERRAFORM_VERSION="1.6.6"
ARG SOPS_VERSION="3.8.1"
ARG KUSTOMIZE_VERSION="5.3.0"

# Detect architecture
ARG TARGETARCH

# Install base dependencies
RUN apt-get update && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y \
    curl \
    wget \
    git \
    jq \
    unzip \
    gnupg \
    lsb-release \
    ca-certificates \
    apt-transport-https \
    age \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Install Terraform
RUN set -eux; \
    case "${TARGETARCH}" in \
        amd64) TF_ARCH="amd64" ;; \
        arm64) TF_ARCH="arm64" ;; \
        *) echo "Unsupported arch: ${TARGETARCH}"; exit 1 ;; \
    esac; \
    curl -fsSLo /tmp/terraform.zip \
        "https://releases.hashicorp.com/terraform/${TERRAFORM_VERSION}/terraform_${TERRAFORM_VERSION}_linux_${TF_ARCH}.zip"; \
    unzip /tmp/terraform.zip -d /usr/local/bin/; \
    rm /tmp/terraform.zip

# Install SOPS
RUN set -eux; \
    case "${TARGETARCH}" in \
        amd64) SOPS_ARCH="amd64" ;; \
        arm64) SOPS_ARCH="arm64" ;; \
        *) echo "Unsupported arch: ${TARGETARCH}"; exit 1 ;; \
    esac; \
    curl -fsSLo /tmp/sops \
        "https://github.com/getsops/sops/releases/download/v${SOPS_VERSION}/sops-v${SOPS_VERSION}.linux.${SOPS_ARCH}"; \
    install -m 0755 /tmp/sops /usr/local/bin/sops; \
    rm /tmp/sops

# Install Kustomize
RUN set -eux; \
    case "${TARGETARCH}" in \
        amd64) KUST_ARCH="amd64" ;; \
        arm64) KUST_ARCH="arm64" ;; \
        *) echo "Unsupported arch: ${TARGETARCH}"; exit 1 ;; \
    esac; \
    curl -fsSLo /tmp/kustomize.tgz \
        "https://github.com/kubernetes-sigs/kustomize/releases/download/kustomize%2Fv${KUSTOMIZE_VERSION}/kustomize_v${KUSTOMIZE_VERSION}_linux_${KUST_ARCH}.tar.gz"; \
    tar -xzf /tmp/kustomize.tgz -C /usr/local/bin/; \
    rm /tmp/kustomize.tgz

# Install Google Cloud CLI
RUN echo "deb [signed-by=/usr/share/keyrings/cloud.google.gpg] https://packages.cloud.google.com/apt cloud-sdk main" | \
    tee -a /etc/apt/sources.list.d/google-cloud-sdk.list && \
    curl https://packages.cloud.google.com/apt/doc/apt-key.gpg | \
    gpg --dearmor -o /usr/share/keyrings/cloud.google.gpg && \
    apt-get update && apt-get install -y google-cloud-cli && \
    apt-get clean && rm -rf /var/lib/apt/lists/*

ENV SHELL=/bin/zsh
