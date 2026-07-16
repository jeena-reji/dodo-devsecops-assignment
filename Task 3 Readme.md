# Task 3 — Service Mesh & Zero-Trust Security with Istio

## Objective

The objective of this task is to implement a service mesh using Istio and enforce zero-trust security between workloads.

The implementation includes:

- Istio service mesh installation
- Workload sidecar injection
- Mutual TLS (mTLS) enforcement
- Identity-based authorization using Istio AuthorizationPolicy
- Kubernetes NetworkPolicy defense-in-depth
- Workload identity verification using SPIFFE identities

---

# Architecture

```
                 +----------------+
                 |  curl client   |
                 | allowed-client |
                 +-------+--------+
                         |
                         |
                  mTLS + Identity
                         |
                         v

              +--------------------+
              |   Istio Gateway    |
              +--------------------+

                         |
                         |
              +----------+----------+
              |
              v

       +----------------------+
       |     ledger-api       |
       |                      |
       |  Flask Application   |
       |  Envoy Sidecar       |
       +----------------------+

              ^
              |
              |
       +------+-------+
       |
       | attacker pod |
       |
       | blocked by   |
       | AuthorizationPolicy
       |
       +--------------+

```

---

# Environment

| Component | Version |
|---|---|
| Kubernetes | v1.31.6 |
| Istio | 1.28.10 |
| Namespace | ledger |
| Application | ledger-api |
| Container Runtime | Docker |

---

# 1. Istio Installation

Istio was installed using `istioctl`.

Installation verification:

```bash
istioctl version
```

Expected:

```
client version: 1.28.10
```

---

# 2. Enable Istio Injection

The ledger namespace was configured for automatic Envoy sidecar injection.

Command:

```bash
kubectl get namespace ledger --show-labels
```

Output:

```
ledger Active istio-injection=enabled
```

## Screenshot



# 3. Workloads Added to Mesh

The application and test workloads were deployed with Istio sidecars.

Verification:

```bash
kubectl get pods -n ledger
```

Output:

```
NAME                          READY
ledger-api                    2/2
allowed-client                2/2
attacker                      2/2
```

The `2/2` status confirms:

- Application container
- Istio Envoy proxy sidecar

are running.


---

# 4. Mutual TLS Enforcement

A PeerAuthentication policy was created with STRICT mode.

Command:

```bash
kubectl get peerauthentication -n ledger
```

Output:

```
NAME      MODE
default   STRICT
```

STRICT mode ensures:

- All workload-to-workload communication requires mTLS
- Plaintext traffic is rejected
- Workload identity is verified using certificates

---

# 5. Workload Certificates and Trust Model

Istio uses `istiod` as the Certificate Authority (CA).

When a workload joins the mesh:

1. Istiod issues an X.509 workload certificate.
2. Envoy sidecar stores and uses the certificate.
3. Certificates are automatically rotated before expiry.
4. Workload identity is represented using SPIFFE.

Example identity:

```
spiffe://cluster.local/ns/ledger/sa/default
```

Trust root:

```
Istio Root CA
        |
        |
      istiod
        |
        |
 Workload Certificates
```

---

# 6. Zero-Trust Authorization

## Default Deny Policy

A default deny policy was applied.

Policy:

```yaml
apiVersion: security.istio.io/v1
kind: AuthorizationPolicy
metadata:
  name: default-deny
  namespace: ledger
spec:
  {}
```

This ensures workloads cannot communicate unless explicitly allowed.

---

# 7. Identity-Based Allow Policy

Access is granted using workload identity instead of IP addresses.

Policy:

```yaml
apiVersion: security.istio.io/v1
kind: AuthorizationPolicy
metadata:
  name: allow-ledger-api
  namespace: ledger

spec:
  selector:
    matchLabels:
      app: ledger-api

  action: ALLOW

  rules:
  - from:
    - source:
        principals:
        - cluster.local/ns/ledger/sa/default
```

The policy allows only workloads with the approved SPIFFE identity.

---

# 8. Authorization Testing

## Unauthorized Workload Test

An attacker workload was created:

```
attacker pod
```

Request:

```bash
curl http://ledger-api/health
```

Response:

```
RBAC: access denied
```

Result:

✅ Unauthorized workload blocked




---

## Authorized Workload Test

An allowed workload using the correct service identity was tested.

Command:

```bash
curl http://ledger-api/health
```

Response:

```json
{
  "status": "ok"
}
```

Result:

✅ Authorized workload allowed


---

# 9. Kubernetes NetworkPolicy Defense-in-Depth

Istio provides identity-based security at the service layer.

Kubernetes NetworkPolicy provides network-level isolation.

## Default Deny NetworkPolicy

```yaml
apiVersion: networking.k8s.io/v1

kind: NetworkPolicy

metadata:
  name: ledger-default-deny
  namespace: ledger

spec:
  podSelector: {}

  policyTypes:
  - Ingress
```

Apply:

```bash
kubectl apply -f network-policy.yaml
```

Verification:

```bash
kubectl get networkpolicy -n ledger
```

---

# Security Layer Comparison

| Layer | Protection |
|-|-|
| Kubernetes NetworkPolicy | Controls network connectivity between pods |
| Istio mTLS | Encrypts service communication and verifies workload identity |
| Istio AuthorizationPolicy | Controls who can access which service |

Together they provide:

- Network isolation
- Encryption
- Identity verification
- Least privilege access

---

# 10. Verification Summary

| Requirement | Status |
|-|-|
| Istio installed | Completed |
| Namespace injection enabled | Completed |
| Workloads in mesh | Completed |
| mTLS STRICT enabled | Completed |
| Workload identity enabled | Completed |
| Default deny authorization | Completed |
| Identity-based allow policy | Completed |
| Unauthorized access blocked | Completed |
| Authorized access allowed | Completed |
| NetworkPolicy configured | Completed |

---

# Conclusion

The ledger-api application is now protected using a zero-trust service mesh architecture.

Security controls implemented:

- Istio service mesh
- STRICT mutual TLS
- SPIFFE workload identity
- AuthorizationPolicy based access control
- Kubernetes NetworkPolicy isolation

The architecture follows zero-trust principles by removing IP-based trust and enforcing authenticated workload-to-workload communication.
