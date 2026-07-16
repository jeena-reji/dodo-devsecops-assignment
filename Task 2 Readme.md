# Task 2 – Secure CI/CD Pipeline

## Objective

The objective of this task is to implement a secure CI/CD pipeline build.yml using GitHub Actions that automates application security validation, container image creation, vulnerability scanning, image signing, and software supply chain attestation.

The pipeline follows DevSecOps and shift-left security principles by integrating security checks throughout the software delivery lifecycle.

---

# Pipeline Architecture

```
Developer Push / Pull Request
            |
            v
     GitHub Actions Workflow
            |
            v
+---------------------------+
| Checkout Repository       |
+---------------------------+
            |
            v
+---------------------------+
| Install Dependencies      |
+---------------------------+
            |
            v
+---------------------------+
| Semgrep SAST Scan         |
+---------------------------+
            |
            v
+---------------------------+
| Gitleaks Secret Scan      |
+---------------------------+
            |
            v
+---------------------------+
| Trivy Filesystem Scan     |
+---------------------------+
            |
            v
+---------------------------+
| Build Docker Image        |
+---------------------------+
            |
            v
+---------------------------+
| Trivy Container Scan      |
+---------------------------+
            |
            v
+---------------------------+
| Push Image to GHCR        |
+---------------------------+
            |
            v
+---------------------------+
| Cosign Image Signing      |
+---------------------------+
            |
            v
+---------------------------+
| SLSA Provenance           |
| Generation                |
+---------------------------+
            |
            v
+---------------------------+
| GitOps Deployment Trigger |
| (ArgoCD - Bonus)          |
+---------------------------+
```

---

# Technology Stack

| Tool | Purpose |
|------|---------|
| GitHub Actions | CI/CD workflow automation |
| Semgrep | Static Application Security Testing (SAST) |
| Gitleaks | Secret and credential detection |
| Trivy | Filesystem and container vulnerability scanning |
| Docker | Container image creation |
| GHCR | Container image registry |
| Cosign | Container image signing |
| SLSA | Software supply chain provenance |
| ArgoCD | GitOps deployment automation |

---

# Pipeline Workflow

## 1. Repository Checkout

The workflow starts by retrieving the latest source code from the GitHub repository.

Action used:

```
actions/checkout
```

---

## 2. Dependency Installation

Python dependencies required by the application are installed before security validation.

Example:

```
pip install -r app/requirements.txt
```

---

## 3. Semgrep SAST Scan

Semgrep performs static application security testing by analyzing source code for:

- Security vulnerabilities
- Unsafe coding patterns
- Common programming mistakes

This helps identify issues before application deployment.

---

## 4. Gitleaks Secret Detection

Gitleaks scans the repository for exposed sensitive information such as:

- Passwords
- API keys
- Tokens
- Credentials

A Gitleaks configuration file is used to manage allowed files and prevent false positives.

---

## 5. Trivy Filesystem Security Scan

Trivy scans the application source code and dependencies for:

- Known CVE vulnerabilities
- Vulnerable packages
- Security issues

The scan results are uploaded to GitHub Security using SARIF format.

---

## 6. Docker Image Build

After successful security checks, the application container image is created.

Example image:

```
ghcr.io/<organization>/ledger-api:v1
```

---

## 7. Trivy Container Image Scan

The generated Docker image is scanned for vulnerabilities including:

- OS package vulnerabilities
- Application dependency vulnerabilities
- Critical and high severity issues

Results are uploaded to GitHub Code Scanning.

---

## 8. Push Image to GitHub Container Registry

After validation, the container image is pushed to GHCR.

Registry:

```
ghcr.io
```

The workflow authenticates using GitHub Actions token.

---

## 9. Container Image Signing using Cosign

Cosign is used to sign the container image.

Purpose:

- Verify image authenticity
- Prevent image tampering
- Improve software supply chain security

---

## 10. SLSA Provenance Generation

The pipeline generates SLSA build provenance for the container image.

The attestation contains:

- Build information
- Source repository details
- Workflow information
- Image digest

The provenance is generated after pushing the image and uses the generated image digest.

---

## 11. GitOps Deployment Trigger

The pipeline includes an ArgoCD deployment stage.

In a production environment:

- The workflow would call ArgoCD CLI/API
- ArgoCD would synchronize Kubernetes manifests
- The application would be deployed automatically

---

# Security Reports

The pipeline generates security reports for:

- Semgrep SAST analysis
- Gitleaks secret detection
- Trivy filesystem scanning
- Trivy container image scanning

Reports are uploaded to GitHub Security through SARIF integration.

---

# Security Implementation Highlights

The pipeline implements:

✅ Shift-left security approach  
✅ Automated source code security scanning  
✅ Secret detection before deployment  
✅ Dependency vulnerability scanning  
✅ Container vulnerability scanning  
✅ Secure image publishing  
✅ Container image signing  
✅ Supply chain attestation  
✅ GitOps deployment readiness  

---

# Validation

The pipeline was successfully validated using GitHub Actions.

Successful execution includes:

```
✓ Semgrep Scan
✓ Gitleaks Scan
✓ Trivy Filesystem Scan
✓ Docker Image Build
✓ Trivy Image Scan
✓ GHCR Image Push
✓ Cosign Image Signing
✓ SLSA Provenance Generation
✓ GitOps Deployment Trigger
```

---

# Design Decisions

The following design decisions were implemented:

- Security checks are performed before container deployment.
- Vulnerability scanning is included at both source and image levels.
- Container images are signed before deployment.
- SLSA provenance improves software supply chain visibility.
- SARIF reports integrate security findings into GitHub Security.

---

# Future Improvements

Possible enhancements:

- Implement automatic vulnerability remediation workflows.
- Add Kubernetes admission policies using Kyverno or OPA Gatekeeper.
- Enable automatic ArgoCD synchronization.
- Add runtime security monitoring.
- Integrate security dashboards for centralized reporting.
- Implement image verification before Kubernetes deployment.

---

# Conclusion

This DevSecOps pipeline provides a secure automated delivery workflow by combining CI/CD automation with security controls throughout the software lifecycle.

The implementation improves code security, container security, and software supply chain integrity while maintaining deployment automation using GitOps principles.
