# Kairos Factory Action 🏭

> [!CAUTION]
> This Action is in early development and with the help of AI, wait until a stable release before relying on it's API

Build Kairos immutable images and artifacts within your release pipeline with ease. This GitHub Action simplifies the process of creating Kairos container images and bootable formats (ISO, RAW, VHD, etc.) for various platforms and architectures.

## ✨ Features

- **Multi-platform support**: Build for `amd64` and `arm64` architectures
- **Multiple base images**: Support for Ubuntu, OpenSUSE, and other distributions
- **Kubernetes integration**: Built-in support for K3s and K0s distributions
- **Artifact generation**: Create ISO, RAW, VHD, GCE, and TAR artifacts
- **Security scanning**: Integrated Grype and Trivy vulnerability scanning
- **Digital signing**: Cosign integration for artifact signing
- **Trusted boot**: Support for UKI (Unified Kernel Image) builds
- **Flexible versioning**: Automatic git-based versioning or manual semver
- **Registry publishing**: Push to any container registry
- **GitHub Summary**: Rich build summaries with artifact links

## 🚀 Quick Start

### Basic Usage

```yaml
- name: Build Kairos image
  uses: kairos-io/kairos-factory-action@v1
  with:
    version: "1.0.0"
    base_image: "ubuntu:24.04"
    model: "generic"
    artifacts: "iso"
    summary_artifacts: true
```

### With Kubernetes

```yaml
- name: Build Kairos with K3s
  uses: kairos-io/kairos-factory-action@v1
  with:
    version: "auto"
    base_image: "ubuntu:24.04"
    kubernetes_distro: "k3s"
    kubernetes_version: "v1.28.0"
    artifacts: "iso,raw"
    security_checks: "grype,trivy"
    sign_artifacts: true
```

## 🏗️ Dual-Structure Pattern

This action follows a dual-structure pattern that provides flexibility for different use cases:

### Option 1: Reusable Workflow (Recommended for Consumers)

Use the reusable workflow for better GitHub UI visibility and simpler configuration:

```yaml
jobs:
  build:
    uses: kairos-io/kairos-factory-action/.github/workflows/reusable-factory.yaml@main
    with:
      version: "v1.0.0"
      iso: true
      summary_artifacts: true
      push_repository: "ghcr.io/user/repo"
      push_repository_username: ${{ secrets.GITHUB_TOKEN }}
      push_repository_password: ${{ secrets.GITHUB_TOKEN }}

**Benefits:**
- ✅ Expanded step visibility in GitHub UI
- ✅ Marketplace publishing support
- ✅ Boolean inputs for individual artifact types and security checks
- ✅ Simplified configuration

### Option 2: Composite Action (Advanced Use Cases)

Use the composite action directly for maximum control:

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: kairos-io/kairos-factory-action@v1
        with:
          version: "v1.0.0"
          artifacts: "iso,raw"
          security_checks: "grype,trivy"
          sign_artifacts: true
```

**Benefits:**
- ✅ Direct control over all inputs
- ✅ Comma-separated lists for artifacts and security checks
- ✅ Advanced customization options

### Input Compatibility

Both approaches support the same functionality. The reusable workflow automatically converts boolean inputs to the appropriate format for the composite action:

| Reusable Workflow | Composite Action | Description |
|-------------------|------------------|-------------|
| `iso: true` | `artifacts: "iso"` | Generate ISO artifact |
| `raw: true, iso: true` | `artifacts: "iso,raw"` | Generate multiple artifacts |
| `cosign: true` | `sign_artifacts: true` | Sign artifacts with cosign |
| `grype: true` | `security_checks: "grype"` | Run Grype security scan |

## 📋 Inputs

### Working Directory

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `workdir` | Working directory where the Kairos source lives (must contain images/) | ❌ | `.` |

### Core Build Parameters

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `version` | Version for the build (semver or "auto" for git describe) | ✅ | - |
| `base_image` | Base image to use (e.g., ubuntu:24.04, opensuse/leap:15.6) | ❌ | `ubuntu:24.04` |
| `model` | Target model (generic, rpi3, nvidia-jetson-agx-orin) | ❌ | `generic` |
| `arch` | Target architecture (amd64, arm64) | ❌ | `amd64` |
| `kubernetes_distro` | Kubernetes distribution (k3s, k0s) | ❌ | - |
| `kubernetes_version` | Kubernetes version (auto or specific version) | ❌ | `auto` |
| `trusted_boot` | Enable trusted boot support | ❌ | `false` |

### Artifact Generation

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `artifacts` | Comma-separated artifacts (iso,raw,vhd,gce,tar) | ❌ | `iso` |
| `output_format` | Output format override (auto, docker, oci) | ❌ | `auto` |

### Security & Signing

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `security_checks` | Comma-separated checks (cosign,grype,trivy) | ❌ | - |
| `sign_artifacts` | Sign generated artifacts with cosign | ❌ | `false` |

### Publishing

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `push_repository` | Repository to push to (e.g., ghcr.io/user/repo) | ❌ | - |
| `push_repository_username` | Username for authentication | ❌ | - |
| `push_repository_password` | Password/token for authentication | ❌ | - |

### Output & Reporting

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `summary_artifacts` | Add artifact links to GitHub Summary | ❌ | `false` |
| `auroraboot_version` | Auroraboot version to use | ❌ | `latest` |

## 📤 Outputs

| Output | Description |
|--------|-------------|
| `image_tag` | The generated image tag |
| `artifacts` | JSON array of generated artifacts |
| `summary` | Build summary for GitHub Summary |

## 🏗️ Examples

### Basic Build (Reusable Workflow)
```yaml
jobs:
  build:
    uses: kairos-io/kairos-factory-action/.github/workflows/build-factory.yaml@main
    with:
      version: "1.0.0"
      base_image: "ubuntu:24.04"
      model: "generic"
      iso: true
      summary_artifacts: true
```

### Basic Build (Composite Action)
```yaml
- name: Build Kairos image
  uses: kairos-io/kairos-factory-action@v1
  with:
    version: "1.0.0"
    base_image: "ubuntu:24.04"
    model: "generic"
    artifacts: "iso"
    summary_artifacts: true
```

### Building from Caller Repository
```yaml
# Use this when calling from the Kairos main repository or any repo with images/ directory
- name: Build Kairos image from source
  uses: kairos-io/kairos-factory-action@v1
  with:
    workdir: "."  # Points to the caller repository root (default)
    version: "auto"
    base_image: "ubuntu:24.04"
    model: "generic"
    arch: "amd64"
    artifacts: "iso,raw"
    summary_artifacts: true
```

### Multi-Architecture Release (Reusable Workflow)
```yaml
jobs:
  build:
    strategy:
      matrix:
        arch: [amd64, arm64]
    uses: kairos-io/kairos-factory-action/.github/workflows/reusable-factory.yaml@main
    with:
      version: "auto"
      arch: ${{ matrix.arch }}
      kubernetes_distro: "k3s"
      iso: true
      push_repository: "ghcr.io/${{ github.repository }}"
      push_repository_username: ${{ github.actor }}
      push_repository_password: ${{ secrets.GITHUB_TOKEN }}

### Multi-Architecture Release (Composite Action)
```yaml
jobs:
  build:
    strategy:
      matrix:
        arch: [amd64, arm64]
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: kairos-io/kairos-factory-action@v1
        with:
          version: "auto"
          arch: ${{ matrix.arch }}
          kubernetes_distro: "k3s"
          artifacts: "iso"
          push_repository: "ghcr.io/${{ github.repository }}"
          push_repository_username: ${{ github.actor }}
          push_repository_password: ${{ secrets.GITHUB_TOKEN }}
```

### Specialized Models
```yaml
# Raspberry Pi
- uses: kairos-io/kairos-factory-action@v1
  with:
    version: "auto"
    model: "rpi3"
    arch: "arm64"
    kubernetes_distro: "k3s"

# NVIDIA Jetson
- uses: kairos-io/kairos-factory-action@v1
  with:
    version: "auto"
    model: "nvidia-jetson-agx-orin"
    arch: "arm64"
    artifacts: "raw"
```

### Security-Focused Build
```yaml
- uses: kairos-io/kairos-factory-action@v1
  with:
    version: "auto"
    kubernetes_distro: "k3s"
    security_checks: "grype,trivy"
    sign_artifacts: true
    trusted_boot: true
```

## 🔧 Supported Models

- **generic**: Standard x86_64/ARM64 builds
- **rpi3**: Raspberry Pi 3/4 builds
- **nvidia-jetson-agx-orin**: NVIDIA Jetson AGX Orin builds

## 🔧 Supported Artifacts

- **iso**: Bootable ISO images (generic model only)
- **raw**: Raw disk images (most models except rpi3, generic, nvidia-jetson)
- **vhd**: Virtual Hard Disk images (coming soon)
- **gce**: Google Compute Engine images (coming soon)
- **tar**: TAR archives (coming soon)

## 🔒 Security Features

- **Grype scanning**: Anchore's vulnerability scanner
- **Trivy scanning**: Aqua Security's comprehensive scanner
- **Cosign signing**: Sigstore's digital signing
- **Trusted boot**: UKI support for secure boot

## 📚 Documentation

For more detailed information about Kairos, visit:
- [Kairos Documentation](https://kairos.io/)
- [Kairos GitHub Repository](https://github.com/kairos-io/kairos)

## 🤝 Contributing

We welcome contributions! Please see our [Contributing Guide](CONTRIBUTING.md) for details.

## 📄 License

This project is licensed under the Apache License 2.0 - see the [LICENSE](LICENSE) file for details.

---

**Built with ❤️ by the Kairos Community**