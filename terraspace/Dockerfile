# Terraspace devcontainer image (Ruby + Terraform + gcloud)
# ARM64 only
FROM mcr.microsoft.com/devcontainers/base:ubuntu-22.04

LABEL org.opencontainers.image.source="https://github.com/toanvv42/devcontainer-images"
LABEL org.opencontainers.image.description="Terraspace devcontainer with Ruby, Terraform, and gcloud CLI"

# Pinned versions
ARG RUBY_VERSION="3.4.1"
ARG TERRAFORM_VERSION="1.6.6"
ARG TERRAGRUNT_VERSION="0.68.4"
ARG SOPS_VERSION="3.9.4"
ARG KUSTOMIZE_VERSION="5.3.0"
ARG KSOPS_VERSION="4.3.2"

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
    # Ruby build dependencies
    autoconf \
    bison \
    build-essential \
    libssl-dev \
    libyaml-dev \
    libreadline-dev \
    zlib1g-dev \
    libncurses5-dev \
    libffi-dev \
    libgdbm-dev \
    # Python 3.10+ (Ubuntu 22.04 default) for gcloud
    python3 \
    python3-pip \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Install Ruby via rbenv
ENV RBENV_ROOT=/usr/local/rbenv
ENV PATH="${RBENV_ROOT}/bin:${RBENV_ROOT}/shims:${PATH}"

RUN git clone https://github.com/rbenv/rbenv.git ${RBENV_ROOT} && \
    git clone https://github.com/rbenv/ruby-build.git ${RBENV_ROOT}/plugins/ruby-build && \
    ${RBENV_ROOT}/bin/rbenv install ${RUBY_VERSION} && \
    ${RBENV_ROOT}/bin/rbenv global ${RUBY_VERSION} && \
    eval "$(rbenv init -)" && \
    gem install bundler terraspace terraspace_plugin_google

# Install Terraform (arm64)
RUN curl -fsSLo /tmp/terraform.zip \
        "https://releases.hashicorp.com/terraform/${TERRAFORM_VERSION}/terraform_${TERRAFORM_VERSION}_linux_arm64.zip" && \
    unzip /tmp/terraform.zip -d /usr/local/bin/ && \
    rm /tmp/terraform.zip

# Install Terragrunt (arm64)
RUN curl -fsSLo /usr/local/bin/terragrunt \
        "https://github.com/gruntwork-io/terragrunt/releases/download/v${TERRAGRUNT_VERSION}/terragrunt_linux_arm64" && \
    chmod +x /usr/local/bin/terragrunt

# Install SOPS (arm64)
RUN curl -fsSLo /tmp/sops \
        "https://github.com/getsops/sops/releases/download/v${SOPS_VERSION}/sops-v${SOPS_VERSION}.linux.arm64" && \
    install -m 0755 /tmp/sops /usr/local/bin/sops && \
    rm /tmp/sops

# Install Kustomize (arm64)
RUN curl -fsSLo /tmp/kustomize.tgz \
        "https://github.com/kubernetes-sigs/kustomize/releases/download/kustomize%2Fv${KUSTOMIZE_VERSION}/kustomize_v${KUSTOMIZE_VERSION}_linux_arm64.tar.gz" && \
    tar -xzf /tmp/kustomize.tgz -C /usr/local/bin/ && \
    rm /tmp/kustomize.tgz

# Install ksops (arm64)
RUN curl -fsSLo /tmp/ksops.tgz \
        "https://github.com/viaduct-ai/kustomize-sops/releases/download/v${KSOPS_VERSION}/ksops_${KSOPS_VERSION}_Linux_arm64.tar.gz" && \
    tar -xzf /tmp/ksops.tgz -C /usr/local/bin/ && \
    rm /tmp/ksops.tgz

# Install Google Cloud CLI
RUN echo "deb [signed-by=/usr/share/keyrings/cloud.google.gpg] https://packages.cloud.google.com/apt cloud-sdk main" | \
    tee -a /etc/apt/sources.list.d/google-cloud-sdk.list && \
    curl https://packages.cloud.google.com/apt/doc/apt-key.gpg | \
    gpg --dearmor -o /usr/share/keyrings/cloud.google.gpg && \
    apt-get update && apt-get install -y google-cloud-cli && \
    apt-get clean && rm -rf /var/lib/apt/lists/*

# Set Python 3.10 for gcloud (avoids deprecation warning)
ENV CLOUDSDK_PYTHON=/usr/bin/python3

ENV SHELL=/bin/zsh
ENV KUSTOMIZE_ENABLE_ALPHA_PLUGINS=true
