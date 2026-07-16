# Task 1 – Deploy & Harden the Workload

## Objective

The objective of this task is to deploy the `ledger-api` application on a local Kubernetes cluster and transform it from an insecure deployment into a production-ready workload by implementing Kubernetes security best practices.

The application was deployed on a local Kind Kubernetes cluster and secured using RBAC, Sealed Secrets, Kubernetes Security Context, resource management, health probes, and Kyverno admission policies.

---

# Architecture

```
                    +----------------+
                    |    Ingress     |
                    +-------+--------+
                            |
                            v
                    +----------------+
                    | Ledger Service |
                    +-------+--------+
                            |
                            v
                +------------------------+
                | Ledger API Deployment  |
                +------------------------+
                  |        |        |
                  |        |        |
                  |        |        |
                  v        v        v
             ConfigMap  SealedSecret  ServiceAccount
                                      |
                                      v
                                   RBAC(Role)
                                      |
                                      v
                                RoleBinding

                     Kyverno Admission Policies
                                |
                                v
                      Kubernetes API Server
```

---

# Components

| Component | Purpose |
|----------|---------|
| Deployment | Deploys the ledger-api application |
| Service | Exposes the application inside the cluster |
| Ingress | Routes external traffic to the application |
| ConfigMap | Stores non-sensitive application configuration |
| Sealed Secret | Stores encrypted application secrets securely |
| ServiceAccount | Provides a dedicated identity for the application |
| Role | Grants minimum required permissions |
| RoleBinding | Associates the ServiceAccount with the Role |
| Kyverno Policy | Enforces Kubernetes security policies |

---

# Security Improvements

The original deployment contained several security issues including plaintext secrets, default Kubernetes ServiceAccount usage, lack of resource limits, missing health probes, and containers running without security restrictions.

The following improvements were implemented:

- Dedicated Kubernetes ServiceAccount
- Least privilege RBAC
- Secrets encrypted using Bitnami Sealed Secrets
- Configurations externalized using ConfigMap
- Container runs as a non-root user
- Read-only root filesystem enabled
- Linux capabilities dropped
- Privilege escalation disabled
- RuntimeDefault Seccomp profile enabled
- Resource requests and limits configured
- Liveness Probe configured
- Readiness Probe configured
- Ingress resource added
- Kyverno admission policy to prevent insecure deployments

---

# Security Decisions

## ConfigMap

Application configuration was separated from the container image to improve maintainability and support environment-specific configuration without rebuilding images.

---

## Sealed Secrets

Instead of storing plaintext credentials inside Git, Bitnami Sealed Secrets were used. Secrets are encrypted before being committed to the repository and automatically decrypted inside the Kubernetes cluster by the Sealed Secrets controller.

---

## RBAC

A dedicated ServiceAccount was created for the application. RBAC permissions were restricted to the minimum required permissions following the Principle of Least Privilege.

---

## Security Context

The application is configured to:

- Run as a non-root user
- Disable privilege escalation
- Drop all Linux capabilities
- Use RuntimeDefault Seccomp Profile
- Enable a read-only root filesystem

These settings significantly reduce the attack surface of the container.

---

## Health Probes

Liveness and Readiness probes ensure Kubernetes can detect unhealthy containers and route traffic only to healthy application instances.

---

## Resource Management

CPU and memory requests and limits prevent resource starvation and improve cluster stability.

---

## Kyverno Policy

Kyverno admission policies enforce secure deployment practices by rejecting workloads that:

- Run as the root user
- Use the `latest` image tag

This ensures security standards are enforced before workloads are deployed.

---

# Validation

The deployment was validated using the following Kubernetes commands.

```bash
kubectl get pods -n payments

kubectl get svc -n payments

kubectl get ingress -n payments

kubectl get sealedsecrets -n payments

kubectl get secret -n payments

kubectl describe deployment ledger-api -n payments

kubectl get clusterpolicy
```

---

# Screenshots

The following screenshots are included as proof of implementation.

- Running Pods
- Services
- Ingress
- Sealed Secret
- Deployment
- Kyverno Policy
- Resource Limits
- GitHub Repository

---

# Design Decisions

The deployment was designed using Kubernetes security best practices with a defense-in-depth approach.

Key design decisions include:

- Externalized configuration
- Encrypted secret management
- Least privilege access control
- Admission policy enforcement
- Container hardening
- Resource governance
- Health monitoring

These improvements collectively transform the original insecure deployment into a significantly more secure and production-ready Kubernetes workload.

---

# Future Improvements

Given additional time, the following enhancements would be implemented:

- External Secrets Operator with a centralized secret store
- Pod Security Standards (Restricted)
- Image signature verification using Cosign
- OPA Gatekeeper policy enforcement
- Continuous runtime security monitoring using Falco