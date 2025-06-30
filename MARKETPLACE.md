# Kairos Factory Action - Marketplace Description

## Short Description
Build Kairos immutable images and artifacts within your release pipeline with ease. Supports multiple architectures, Kubernetes distributions, and security scanning.

## Long Description
The Kairos Factory Action simplifies building Kairos immutable images and bootable artifacts (ISO, RAW, VHD, etc.) for various platforms and architectures. Perfect for CI/CD pipelines that need to create secure, reproducible infrastructure images.

### Key Features:
- **Multi-platform builds**: Support for amd64 and arm64 architectures
- **Kubernetes integration**: Built-in support for K3s and K0s distributions
- **Multiple artifact types**: Generate ISO, RAW, VHD, GCE, and TAR artifacts
- **Security scanning**: Integrated Grype and Trivy vulnerability scanning
- **Digital signing**: Cosign integration for artifact signing
- **Trusted boot**: UKI support for secure boot environments
- **Flexible versioning**: Automatic git-based or manual semver versioning
- **Registry publishing**: Push to any container registry
- **Rich reporting**: GitHub Summary integration with artifact links

### Use Cases:
- Building infrastructure images for Kubernetes clusters
- Creating secure boot environments with trusted boot
- Generating multi-architecture releases
- Automated security scanning and signing
- CI/CD pipelines for edge computing deployments

### Supported Models:
- Generic x86_64/ARM64 builds
- Raspberry Pi 3/4 builds
- NVIDIA Jetson AGX Orin builds

### Quick Start:
```yaml
- name: Build Kairos image
  uses: kairos-io/kairos-factory-action@v1
  with:
    version: "auto"
    base_image: "ubuntu:24.04"
    kubernetes_distro: "k3s"
    artifacts: "iso"
    security_checks: "grype,trivy"
    sign_artifacts: true
```

Perfect for teams building immutable infrastructure, edge computing solutions, or secure Kubernetes environments. Built with security and reproducibility in mind.

## Categories
- Docker
- Security
- Infrastructure
- CI/CD
- Kubernetes

## Keywords
kairos, immutable, infrastructure, kubernetes, k3s, k0s, security, signing, cosign, grype, trivy, iso, raw, trusted-boot, edge-computing, multi-arch, arm64, amd64 