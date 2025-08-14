# Kairos Factory Action 🏭

A GitHub Actions reusable workflow for building Kairos immutable images and artifacts with comprehensive security scanning, digital signing, and multi-platform support.

## ✨ Features

- **Multi-platform support**: Build for `amd64` and `arm64` architectures
- **Multiple base images**: Support for Ubuntu, OpenSUSE, and other distributions
- **Kubernetes integration**: Built-in support for K3s and K0s distributions
- **Artifact generation**: Create ISO, and RAW artifacts
- **Security scanning**: Integrated Grype and Trivy vulnerability scanning with SARIF support
- **Digital signing**: Cosign integration for artifact signing
- **Trusted boot**: Support for UKI/USI (Unified Kernel/System Image) builds
- **Flexible versioning**: Automatic git-based versioning or manual semver
- **Registry publishing**: Push to any container registry
- **GitHub Summary**: Rich build summaries with artifact links
- **Custom naming**: Flexible tag and artifact naming formats
- **Cloud config support**: Integration with cloud-init configurations

## 🚀 Quick Start

### Basic Usage

```yaml
jobs:
  build:
    uses: kairos-io/kairos-factory-action/.github/workflows/reusable-factory.yaml@main
    with:
      version: "v1.0.0"
      base_image: "ubuntu:24.04"
      model: "generic"
      iso: true
      summary_artifacts: true
```

### With Kubernetes

```yaml
jobs:
  build:
    uses: kairos-io/kairos-factory-action/.github/workflows/reusable-factory.yaml@main
    with:
      version: "auto"
      base_image: "ubuntu:24.04"
      kubernetes_distro: "k3s"
      kubernetes_version: "v1.28.0"
      iso: true
```

## 📋 Inputs

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

### Dockerfile Configuration

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `dockerfile_path` | Path to the Dockerfile to use for building | ❌ | [Downloads from Kairos repo](https://github.com/kairos-io/kairos/blob/master/images/Dockerfile) |
| `kairos_version` | Kairos version for fallback Dockerfile | ❌ | `v3.5.0` |

### Artifact Generation

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `iso` | Generate ISO artifact | ❌ | `false` |
| `raw` | Generate RAW disk image | ❌ | `false` |
| `compute_checksums` | Compute SHA256 checksums for artifacts | ❌ | `true` |
| `output_format` | Output format override (auto, docker, oci) | ❌ | `auto` |

### Security & Signing

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `grype` | Run Grype security scan | ❌ | `false` |
| `trivy` | Run Trivy security scan | ❌ | `false` |
| `grype_sarif` | Generate Grype SARIF report | ❌ | `false` |
| `trivy_sarif` | Generate Trivy SARIF report | ❌ | `false` |
| `cosign` | Sign generated artifacts with cosign | ❌ | `false` |

### Publishing

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `registry_domain` | Container registry domain to push to | ❌ | - |
| `registry_namespace` | Namespace/organization in the registry URL | ❌ | - |
| `registry_repository` | Repository name in the registry | ❌ | Uses flavor name |

### Output & Reporting

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `summary_artifacts` | Add artifact links to GitHub Summary | ❌ | `false` |
| `auroraboot_version` | Auroraboot version to use | ❌ | `latest` |
| `release` | Create a GitHub release for artifacts | ❌ | `false` |
| `list_release_artifacts` | List artifacts in GitHub summary | ❌ | `false` |

### Custom Naming

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `custom_tag_format` | Custom tag format using variables | ❌ | Default format |
| `custom_artifact_format` | Custom artifact filename format | ❌ | Auroraboot default |
| `custom_job_name_format` | Custom job name format | ❌ | Default format |
| `image_labels` | Image labels to add to the image | ❌ | - |

### Trusted Boot Configuration

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `keys_dir` | Path to trusted boot keys directory | ❌ | - |
| `sysext_dir` | Path to system extensions overlay | ❌ | - |
| `single_efi_cmdline` | Single EFI command line for trusted boot | ❌ | - |

### Cloud Configuration

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `cloud_config` | Cloud-config file path or URL | ❌ | - |

## 🔧 Supported Models

- **generic**: Standard x86_64/ARM64 builds
- **rpi3/**: Raspberry Pi 3/4 builds
- **nvidia-jetson-agx-orin**: NVIDIA Jetson AGX Orin builds

## 🔧 Supported Artifacts

- **iso**: Bootable ISO images (generic model only)
- **raw**: Raw disk images (most models except rpi3, generic, nvidia-jetson)

## 🔒 Security Features

- **Grype scanning**: Anchore's vulnerability scanner with JSON and SARIF output
- **Trivy scanning**: Aqua Security's comprehensive scanner with SARIF support
- **Cosign signing**: Sigstore's digital signing for images and artifacts
- **Trusted boot**: UKI support for secure boot with custom keys
- **SARIF integration**: Upload scan results to GitHub Security tab

## 📤 Outputs

| Output | Description |
|--------|-------------|
| `image_tag` | The generated image tag |
| `artifacts` | JSON array of generated artifacts |
| `summary` | Build summary for GitHub Summary |

## 🏗️ Examples

### Security-Focused Build with Trusted Boot

```yaml
jobs:
  build:
    uses: kairos-io/kairos-factory-action/.github/workflows/reusable-factory.yaml@main
    with:
      version: "auto"
      kubernetes_distro: "k3s"
      grype: true
      trivy: true
      grype_sarif: true
      trivy_sarif: true
      cosign: true
      trusted_boot: true
      keys_dir: "./keys"
      iso: true
      summary_artifacts: true
```

### Custom Dockerfile

```yaml
jobs:
  build:
    uses: kairos-io/kairos-factory-action/.github/workflows/reusable-factory.yaml@main
    with:
      dockerfile_path: "custom/path/Dockerfile"
      version: "auto"
      base_image: "ubuntu:24.04"
      model: "generic"
      summary_artifacts: true
```

### Specialized Models

```yaml
jobs:
  build-rpi:
    uses: kairos-io/kairos-factory-action/.github/workflows/reusable-factory.yaml@main
    with:
      version: "auto"
      model: "rpi4"
      arch: "arm64"
      kubernetes_distro: "k3s"
      raw: true
      summary_artifacts: true
```

### With Cloud Configuration

```yaml
jobs:
  build:
    uses: kairos-io/kairos-factory-action/.github/workflows/reusable-factory.yaml@main
    with:
      version: "auto"
      base_image: "ubuntu:24.04"
      kubernetes_distro: "k3s"
      iso: true
      cloud_config: "path/to/cloud-config.yml"
      summary_artifacts: true
```

## 🔧 Custom Naming Variables

The following variables can be used in custom tag, artifact, and job name formats:

- `$FLAVOR_RELEASE`: Base image flavor and release (e.g., ubuntu-24.04)
- `$VARIANT`: Build variant (core or standard)
- `$ARCH`: Target architecture (amd64 or arm64)
- `$MODEL`: Target model (generic, rpi3, etc.)
- `$VERSION`: Build version
- `$KUBERNETES_DISTRO`: Kubernetes distribution (k3s, k0s)
- `$KUBERNETES_VERSION`: Kubernetes version
- `$UKI`: UKI suffix if trusted boot is enabled
- `$COMMIT_SHA`: Git commit SHA

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