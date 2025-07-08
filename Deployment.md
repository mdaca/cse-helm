# Deployment Guide for CSE Helm Chart

This document explains key configuration settings in `values.yaml` and how to deploy the CSE Helm chart for various environments.

---

## Key Configuration Sections

### 1. CSE Application (`cse`)

- **replicaCount**: Number of CSE application pods to run.
- **image**: Docker image repository, tag, and pull policy.
- **env**: Environment variables passed into the container. Database credentials are injected securely via Kubernetes Secrets.
- **auth.spnego.enabled**:  
  - `true` to enable SPNEGO Kerberos authentication.  
  - `false` to disable it (useful for demo or simple auth).
- **auth.spnego.config.applicationUsers** and **applicationRoles**: Used only if SPNEGO is enabled, defines users and their roles.
- **podSecurityContext** and **securityContext**: Security-related settings to run pods as non-root with least privileges.
- **service**: Service type and port.
- **ingress**:  
  - Enable/disable ingress (`enabled`) and configure TLS certificates, hosts, and paths.
  - Set `className` to your ingress controller (e.g., `"nginx"`).
- **autoscaling**: Horizontal Pod Autoscaler config based on CPU and memory.
- **resources**: CPU and memory requests and limits for the pod.
- **livenessProbe** and **readinessProbe**: Health checks for Kubernetes.

### 2. Database (`database`)

- **enabled**: Set to `true` to deploy an embedded PostgreSQL with the app; set `false` if using an external database.
- **replicaCount**: Number of database replicas (usually 1 unless you manage HA Postgres).
- **image**: Docker image for the PostgreSQL container.
- **name, user, password**: Database name, username, and password (use Kubernetes Secret `existingSecret` for secure password management).
- **storage**: Persistent volume size and storage class.
- **resources**: CPU and memory requests/limits for the database pod.

---

## Example Deployment Scenarios

### A. Development/Demo Deployment

Use embedded PostgreSQL and disable SPNEGO:

```yaml
cse:
  auth:
    spnego:
      enabled: false

database:
  enabled: true
```

Helm command:

```bash
helm upgrade --install cse-demo ./chart/cse --namespace demo --values ./values-demo.yaml
```

---

### B. Production Deployment

Use external PostgreSQL, enable SPNEGO, and configure ingress:

```yaml
cse:
  auth:
    spnego:
      enabled: true
  ingress:
    enabled: true
    hosts:
      - host: cse.example.com
        paths:
          - path: /
            pathType: ImplementationSpecific

database:
  enabled: false
```

Set database connection via environment variables or Kubernetes secrets for external DB.

Helm command:

```bash
helm upgrade --install cse-prod ./chart/cse --namespace prod --values ./values-prod.yaml
```

---

## Additional Tips

- Credentials such as database passwords and application user secrets **must be securely stored** and managed before deployment. Common options include:  
  - GitLab CI/CD variables (secure environment variables)  
  - AWS Secrets Manager (or other cloud secret stores)  
  - Pre-created Kubernetes Secrets installed into the target cluster  

- Ensure these secrets are referenced properly in your Helm values (`existingSecret` or via environment variables).  
- Adjust resource requests and limits to fit your cluster's capacity.  
- Update ingress TLS certificates as needed for production environments.  
- Use autoscaling settings thoughtfully to balance performance and resource usage.
