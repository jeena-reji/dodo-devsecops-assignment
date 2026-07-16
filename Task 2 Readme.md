# Task 2 – Secure CI/CD Pipeline

## Objective

The objective of this task is to build a secure CI/CD pipeline that performs automated security checks, builds a container image, signs the image, and prepares it for deployment using GitOps principles.

The pipeline is implemented using GitHub Actions and integrates multiple DevSecOps tools to improve software supply chain security.

---

# Pipeline Architecture

```
Developer Push
        |
        v
GitHub Actions
        |
        +----------------------+
        | Checkout Repository  |
        +----------------------+
                    |
                    v
           Install Dependencies
                    |
                    v
              Semgrep (SAST)
                    |
                    v
         Gitleaks (Secret Scan)
                    |
                    v
       Trivy Filesystem Scan
                    |
                    v
          Build Docker Image
                    |
                    v
          Trivy Image Scan
                    |
                    v
            Push Image to GHCR
                    |
                    v
            Cosign Image Signing
                    |
                    v
         SLSA Provenance Generation
                    |
                    v
       ArgoCD Deployment (Bonus)
```

---

# Security Controls

| Tool | Purpose |
|------|---------|
| GitHub Actions | CI/CD automation |
| Semgrep | Static Application Security Testing (SAST) |
| Gitleaks | Detects hardcoded secrets |
| Trivy | Filesystem and container vulnerability scanning |
| GHCR | Container image registry |
| Cosign | Container image signing |
| SLSA Provenance | Supply chain attestation |
| ArgoCD | GitOps-based deployment (bonus) |

---

# Pipeline Stages

## Checkout

Retrieves the latest source code from the repository.

## Dependency Installation

Installs Python dependencies required by the application.

## Semgrep Scan

Performs static code analysis to detect insecure coding patterns and common security vulnerabilities.

## Gitleaks Scan

Scans the repository for hardcoded credentials, API keys, passwords, and other secrets.

## Trivy Filesystem Scan

Scans the project files for known vulnerabilities and security misconfigurations before building the container image.

## Docker Build

Builds the application container image.

## Trivy Image Scan

Scans the built container image for operating system and package vulnerabilities.

## Container Registry

Pushes the validated container image to GitHub Container Registry (GHCR).

## Cosign Signing

Signs the container image to provide integrity and authenticity verification.

## SLSA Provenance

Generates build provenance to improve software supply chain transparency.

**Note:** In a production environment, the attestation would use the image digest produced after the image is pushed to the container registry.

## ArgoCD

Represents the GitOps deployment stage. In a production environment, this step would invoke the ArgoCD API or CLI to synchronize the application after a successful build.

---

# Validation

The pipeline was validated using GitHub Actions workflow execution.

Security reports include:

- Semgrep findings
- Gitleaks findings
- Trivy vulnerability scan
- Container image scan
- Cosign image signature
- SLSA provenance generation

---

# Design Decisions

The pipeline follows a shift-left security approach by performing security checks before deployment.

Key design decisions include:

- Automated static application security testing
- Automated secret detection
- Vulnerability scanning before and after image creation
- Container image signing
- Supply chain provenance generation
- GitOps deployment approach

---

# Future Improvements

- Capture the Docker image digest automatically for SLSA attestations.
- Upload security reports to centralized dashboards.
- Integrate policy enforcement using OPA or Kyverno.
- Configure automatic ArgoCD synchronization with deployment manifests.