# Devcontainer Base Images

Reusable devcontainer base images hosted on GitHub Container Registry (ghcr.io).

## Quick Start

Reference in your project's `.devcontainer/devcontainer.json`:

```json
{
  "image": "ghcr.io/toanvv42/devcontainer-images/rails:latest"
}
```

Or extend with a Dockerfile:

```dockerfile
FROM ghcr.io/toanvv42/devcontainer-images/devsecops:latest
# Add project-specific tools
```

---

## Available Images

| Image | Description | Use When |
|-------|-------------|----------|
| [`base`](#base) | Common tools + basic security | General development |
| [`node`](#node) | Node.js 22 + pnpm | JavaScript/TypeScript projects |
| [`python`](#python) | Python 3.12 + poetry | Python projects |
| [`ruby`](#ruby) | Ruby 3.1 + bundler | Ruby projects |
| [`rails`](#rails) | Ruby + Rails dependencies | Rails applications |
| [`terraform`](#terraform) | Terraform + cloud CLIs | Infrastructure as Code |
| [`devsecops`](#devsecops) | Full security toolchain | Security scanning & auditing |

---

## Image Details

### base

**Image:** `ghcr.io/toanvv42/devcontainer-images/base:latest`

Common development tools with basic security scanning capabilities.

| Tool | Version | Purpose |
|------|---------|---------|
| git | latest | Version control |
| curl, wget | latest | HTTP clients |
| jq | latest | JSON processing |
| gnupg | latest | GPG signing |
| **gitleaks** | 8.18.4 | Secret detection |
| **hadolint** | 2.12.0 | Dockerfile linting |

**Basic Usage:**

```bash
# Scan for secrets in current directory
gitleaks detect --source .

# Lint a Dockerfile
hadolint Dockerfile
```

---

### node

**Image:** `ghcr.io/toanvv42/devcontainer-images/node:latest`

| Tool | Version | Purpose |
|------|---------|---------|
| Node.js | 22 | JavaScript runtime |
| pnpm | latest | Package manager |
| corepack | enabled | Package manager management |

**Use when:** Building JavaScript/TypeScript applications, React, Vue, Next.js projects.

---

### python

**Image:** `ghcr.io/toanvv42/devcontainer-images/python:latest`

| Tool | Version | Purpose |
|------|---------|---------|
| Python | 3.12 | Python runtime |
| poetry | latest | Dependency management |

**Use when:** Python applications, Django, FastAPI, data science projects.

---

### ruby

**Image:** `ghcr.io/toanvv42/devcontainer-images/ruby:latest`

| Tool | Version | Purpose |
|------|---------|---------|
| Ruby | 3.1 | Ruby runtime |
| bundler | latest | Gem management |
| Database dev libs | latest | PostgreSQL, SQLite, MariaDB |

**Use when:** Ruby applications, gems development.

---

### rails

**Image:** `ghcr.io/toanvv42/devcontainer-images/rails:latest`

Extends ruby with Rails-specific dependencies.

| Tool | Purpose |
|------|---------|
| PostgreSQL client | Database connectivity |
| ImageMagick, Vips | Image processing |
| Node.js + Yarn | Asset pipeline |
| Redis tools | Background jobs |
| pdftk | PDF processing |

**Use when:** Ruby on Rails applications with full stack requirements.

---

### terraform

**Image:** `ghcr.io/toanvv42/devcontainer-images/terraform:latest`

| Tool | Version | Purpose |
|------|---------|---------|
| Terraform | 1.6.6 | Infrastructure as Code |
| SOPS | 3.8.1 | Secrets encryption |
| age | latest | Encryption |
| Kustomize | 5.3.0 | Kubernetes manifests |
| gcloud CLI | latest | GCP management |

**Basic Usage:**

```bash
# Initialize Terraform
terraform init

# Encrypt secrets with SOPS
sops -e secrets.yaml > secrets.enc.yaml

# Build Kubernetes manifests
kustomize build ./overlays/production
```

**Use when:** Managing cloud infrastructure, Kubernetes deployments.

---

### devsecops

**Image:** `ghcr.io/toanvv42/devcontainer-images/devsecops:latest`

Full security scanning toolchain for DevSecOps workflows.

#### IaC Security Tools

| Tool | Version | Purpose | Documentation |
|------|---------|---------|---------------|
| **Checkov** | 3.2.0 | Multi-framework IaC scanner | [checkov.io](https://www.checkov.io/) |
| **tfsec** | 1.28.10 | Terraform security scanner | [tfsec.dev](https://tfsec.dev/) |
| **Terraform** | 1.6.6 | Infrastructure as Code | [terraform.io](https://terraform.io/) |

```bash
# Scan Terraform code with Checkov
checkov -d . --framework terraform

# Scan with tfsec
tfsec .

# Generate baseline report
checkov -d . -o json > baseline.json
```

#### Container Security Tools

| Tool | Version | Purpose | Documentation |
|------|---------|---------|---------------|
| **Trivy** | 0.56.2 | Vulnerability scanner | [trivy docs](https://aquasecurity.github.io/trivy/) |
| **Hadolint** | 2.12.0 | Dockerfile linter | [hadolint](https://github.com/hadolint/hadolint) |

```bash
# Scan container image
trivy image nginx:latest

# Scan filesystem for vulnerabilities
trivy fs --scanners vuln .

# Scan for secrets
trivy fs --scanners secret .

# Lint Dockerfile
hadolint Dockerfile
```

#### Kubernetes Security Tools

| Tool | Version | Purpose | Documentation |
|------|---------|---------|---------------|
| **kubesec** | 2.14.0 | K8s manifest scanner | [kubesec.io](https://kubesec.io/) |
| **kube-linter** | 0.6.8 | K8s YAML linter | [kube-linter](https://github.com/stackrox/kube-linter) |
| **Kustomize** | 5.3.0 | K8s manifest management | [kustomize.io](https://kustomize.io/) |

```bash
# Scan Kubernetes manifest
kubesec scan deployment.yaml

# Lint Kubernetes manifests
kube-linter lint ./manifests/

# Build and scan Kustomize output
kustomize build . | kubesec scan /dev/stdin
```

#### SAST Tools

| Tool | Version | Purpose | Documentation |
|------|---------|---------|---------------|
| **Semgrep** | 1.90.0 | Static analysis | [semgrep.dev](https://semgrep.dev/) |

```bash
# Run with auto-detection
semgrep --config auto .

# Run OWASP Top 10 rules
semgrep --config "p/owasp-top-ten" .

# Run specific language rules
semgrep --config "p/python" .
```

#### Secrets Detection

| Tool | Version | Purpose | Documentation |
|------|---------|---------|---------------|
| **Gitleaks** | 8.18.4 | Secret detection | [gitleaks.io](https://gitleaks.io/) |
| **SOPS** | 3.8.1 | Secret encryption | [SOPS](https://github.com/getsops/sops) |

```bash
# Detect secrets in repo
gitleaks detect --source .

# Detect secrets in git history
gitleaks detect --source . --log-opts="--all"

# Encrypt file with SOPS
sops -e --age <public-key> secrets.yaml > secrets.enc.yaml

# Decrypt file
sops -d secrets.enc.yaml
```

#### Cloud CLIs

| Tool | Purpose |
|------|---------|
| **AWS CLI v2** | AWS management |
| **gcloud CLI** | GCP management |

```bash
# Configure AWS
aws configure

# Configure GCP
gcloud auth login
gcloud config set project <project-id>
```

**Use when:** Security scanning, vulnerability assessment, DevSecOps pipelines, compliance auditing.

---

## When to Use Each Image

```
Do you need security scanning?
├── Yes, full security toolchain → devsecops
├── Yes, basic secret detection → base (includes gitleaks, hadolint)
└── No
    ├── Working with Terraform/K8s? → terraform
    ├── Rails application? → rails
    ├── Ruby project? → ruby
    ├── Python project? → python
    ├── Node.js project? → node
    └── General purpose → base
```

---

## Pre-commit Integration

All images with security tools support pre-commit hooks:

```yaml
# .pre-commit-config.yaml
repos:
  - repo: https://github.com/gitleaks/gitleaks
    rev: v8.18.4
    hooks:
      - id: gitleaks

  - repo: https://github.com/hadolint/hadolint
    rev: v2.12.0
    hooks:
      - id: hadolint

  - repo: https://github.com/antonbabenko/pre-commit-terraform
    rev: v1.83.5
    hooks:
      - id: terraform_checkov
      - id: terraform_tfsec
```

---

## CI/CD Pipeline Examples

### GitLab CI Security Scanning

```yaml
security-scan:
  image: ghcr.io/toanvv42/devcontainer-images/devsecops:latest
  script:
    - gitleaks detect --source . --report-format sarif --report-path gitleaks.sarif
    - trivy fs --scanners vuln,secret --format sarif -o trivy.sarif .
    - checkov -d . -o sarif > checkov.sarif || true
  artifacts:
    reports:
      sast: [gitleaks.sarif, trivy.sarif, checkov.sarif]
```

### GitHub Actions

```yaml
- name: Security Scan
  uses: docker://ghcr.io/toanvv42/devcontainer-images/devsecops:latest
  with:
    args: |
      trivy fs --scanners vuln . &&
      gitleaks detect --source .
```

---

## Building Locally

```bash
# Build specific image
docker build -t devcontainer-devsecops ./devsecops

# Build with specific platform
docker build --platform linux/amd64 -t devcontainer-devsecops ./devsecops

# Test the image
docker run --rm -it devcontainer-devsecops trivy --version
```

---

## CI/CD

Images are automatically built and pushed to ghcr.io on:
- Push to `main` branch
- Pull request (build only, no push)
- Weekly schedule (Sundays at 00:00 UTC for security updates)

## Image Tags

- `latest` - Latest stable build from main
- `sha-<commit>` - Specific commit builds

---

## Version Pinning

All security tools use pinned versions for reproducibility. To update versions, modify the `ARG` values in the respective Dockerfile:

```dockerfile
ARG TRIVY_VERSION="0.56.2"
ARG CHECKOV_VERSION="3.2.0"
```

---

## Troubleshooting

### Trivy database update fails

```bash
# Clear cache and retry
trivy image --clear-cache
trivy image nginx:latest
```

### Checkov slow on large repos

```bash
# Use framework-specific scanning
checkov -d . --framework terraform --skip-path node_modules
```

### Gitleaks false positives

```bash
# Create .gitleaksignore file
echo "path/to/false/positive" >> .gitleaksignore
gitleaks detect --source .
```
