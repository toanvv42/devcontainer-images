# DevSecOps devcontainer image with full security toolchain
FROM mcr.microsoft.com/devcontainers/base:ubuntu-22.04

LABEL org.opencontainers.image.source="https://github.com/toanvv42/devcontainer-images"
LABEL org.opencontainers.image.description="DevSecOps devcontainer with security scanning tools"

# Pinned versions for reproducible builds
ARG TERRAFORM_VERSION="1.6.6"
ARG TRIVY_VERSION="0.56.2"
ARG CHECKOV_VERSION="3.2.0"
ARG SEMGREP_VERSION="1.90.0"
ARG GITLEAKS_VERSION="8.18.4"
ARG HADOLINT_VERSION="2.12.0"
ARG TFSEC_VERSION="1.28.10"
ARG KUBESEC_VERSION="2.14.0"
ARG KUBE_LINTER_VERSION="0.6.8"
ARG SOPS_VERSION="3.8.1"
ARG KUSTOMIZE_VERSION="5.3.0"

# Detect architecture
ARG TARGETARCH

# Install base dependencies and Python for pip-based tools
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
    python3 \
    python3-pip \
    python3-venv \
    age \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# ============================================
# IaC Security Tools
# ============================================

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

# Install Checkov - IaC security scanner
RUN pip3 install --no-cache-dir checkov==${CHECKOV_VERSION}

# Install tfsec - Terraform-specific security scanner
RUN set -eux; \
    case "${TARGETARCH}" in \
        amd64) TFSEC_ARCH="amd64" ;; \
        arm64) TFSEC_ARCH="arm64" ;; \
        *) echo "Unsupported arch: ${TARGETARCH}"; exit 1 ;; \
    esac; \
    curl -fsSLo /usr/local/bin/tfsec \
        "https://github.com/aquasecurity/tfsec/releases/download/v${TFSEC_VERSION}/tfsec-linux-${TFSEC_ARCH}"; \
    chmod +x /usr/local/bin/tfsec

# ============================================
# Container Security Tools
# ============================================

# Install Trivy - Comprehensive vulnerability scanner
RUN set -eux; \
    case "${TARGETARCH}" in \
        amd64) TRIVY_ARCH="64bit" ;; \
        arm64) TRIVY_ARCH="ARM64" ;; \
        *) echo "Unsupported arch: ${TARGETARCH}"; exit 1 ;; \
    esac; \
    curl -fsSLo /tmp/trivy.tar.gz \
        "https://github.com/aquasecurity/trivy/releases/download/v${TRIVY_VERSION}/trivy_${TRIVY_VERSION}_Linux-${TRIVY_ARCH}.tar.gz"; \
    tar -xzf /tmp/trivy.tar.gz -C /tmp/; \
    install -m 0755 /tmp/trivy /usr/local/bin/trivy; \
    rm -rf /tmp/trivy*

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

# ============================================
# Kubernetes Security Tools
# ============================================

# Install kubesec - Kubernetes manifest security scanner
RUN set -eux; \
    case "${TARGETARCH}" in \
        amd64) KS_ARCH="amd64" ;; \
        arm64) KS_ARCH="arm64" ;; \
        *) echo "Unsupported arch: ${TARGETARCH}"; exit 1 ;; \
    esac; \
    curl -fsSLo /tmp/kubesec.tar.gz \
        "https://github.com/controlplaneio/kubesec/releases/download/v${KUBESEC_VERSION}/kubesec_linux_${KS_ARCH}.tar.gz"; \
    tar -xzf /tmp/kubesec.tar.gz -C /tmp/; \
    install -m 0755 /tmp/kubesec /usr/local/bin/kubesec; \
    rm -rf /tmp/kubesec*

# Install kube-linter - Kubernetes YAML linter
RUN set -eux; \
    case "${TARGETARCH}" in \
        amd64) KL_ARCH="amd64" ;; \
        arm64) KL_ARCH="arm64" ;; \
        *) echo "Unsupported arch: ${TARGETARCH}"; exit 1 ;; \
    esac; \
    curl -fsSLo /tmp/kube-linter.tar.gz \
        "https://github.com/stackrox/kube-linter/releases/download/v${KUBE_LINTER_VERSION}/kube-linter-linux.tar.gz"; \
    tar -xzf /tmp/kube-linter.tar.gz -C /tmp/; \
    install -m 0755 /tmp/kube-linter /usr/local/bin/kube-linter; \
    rm -rf /tmp/kube-linter*

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

# ============================================
# SAST Tools
# ============================================

# Install Semgrep - Static analysis tool
RUN pip3 install --no-cache-dir semgrep==${SEMGREP_VERSION}

# ============================================
# Secrets Detection
# ============================================

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

# Install SOPS - Secrets management
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

# ============================================
# Cloud CLIs
# ============================================

# Install Google Cloud CLI
RUN echo "deb [signed-by=/usr/share/keyrings/cloud.google.gpg] https://packages.cloud.google.com/apt cloud-sdk main" | \
    tee -a /etc/apt/sources.list.d/google-cloud-sdk.list && \
    curl https://packages.cloud.google.com/apt/doc/apt-key.gpg | \
    gpg --dearmor -o /usr/share/keyrings/cloud.google.gpg && \
    apt-get update && apt-get install -y google-cloud-cli && \
    apt-get clean && rm -rf /var/lib/apt/lists/*

# Install AWS CLI v2
RUN set -eux; \
    case "${TARGETARCH}" in \
        amd64) AWS_ARCH="x86_64" ;; \
        arm64) AWS_ARCH="aarch64" ;; \
        *) echo "Unsupported arch: ${TARGETARCH}"; exit 1 ;; \
    esac; \
    curl -fsSLo /tmp/awscliv2.zip \
        "https://awscli.amazonaws.com/awscli-exe-linux-${AWS_ARCH}.zip"; \
    unzip -q /tmp/awscliv2.zip -d /tmp/; \
    /tmp/aws/install; \
    rm -rf /tmp/aws*

# Set default shell to zsh
ENV SHELL=/bin/zsh

# Verify installations
RUN echo "=== Installed Security Tools ===" && \
    echo "Terraform: $(terraform version | head -1)" && \
    echo "Trivy: $(trivy --version | head -1)" && \
    echo "Checkov: $(checkov --version)" && \
    echo "tfsec: $(tfsec --version)" && \
    echo "Semgrep: $(semgrep --version)" && \
    echo "Gitleaks: $(gitleaks version)" && \
    echo "Hadolint: $(hadolint --version)" && \
    echo "kubesec: $(kubesec version | head -1)" && \
    echo "kube-linter: $(kube-linter version)" && \
    echo "SOPS: $(sops --version)" && \
    echo "AWS CLI: $(aws --version)" && \
    echo "gcloud: $(gcloud --version | head -1)"
