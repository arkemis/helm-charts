# backend

Backend application for Arkemis

## Installation

```bash
helm repo add arkemis https://arkemis.github.io/helm-charts
helm install my-backend arkemis/backend
```

## Values

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| configMap.env | object | `{}` | Key-value pairs injected as environment variables via ConfigMap |
| containerSecurityContext.allowPrivilegeEscalation | bool | `false` | Disallow privilege escalation |
| containerSecurityContext.capabilities.drop | list | `["ALL"]` | Linux capabilities to drop |
| containerSecurityContext.readOnlyRootFilesystem | bool | `true` | Mount root filesystem as read-only |
| containerSecurityContext.runAsNonRoot | bool | `true` | Require non-root user |
| externalSecrets | list | `[]` | List of ExternalSecret definitions |
| extraEnvVars | list | `[]` | Additional environment variables |
| extraVolumeMounts | list | `[]` | Additional volume mounts |
| extraVolumes | list | `[]` | Additional volumes |
| fullnameOverride | string | `""` | Override the fully qualified app name |
| headlessService.enabled | bool | `true` | Enable headless service for pod discovery |
| image.pullPolicy | string | `"Always"` | Image pull policy |
| image.pullSecrets | list | `[]` | List of image pull secret names |
| image.registry | string | `"ghcr.io"` | Container image registry |
| image.repository | string | `""` | Container image repository |
| image.tag | string | `""` | Container image tag (defaults to chart appVersion) |
| ingress.annotations | object | `{}` | Additional ingress annotations (merged with chart defaults) |
| ingress.certIssuer | string | `"cert-manager-global"` | cert-manager ClusterIssuer name |
| ingress.enabled | bool | `true` | Enable ingress |
| ingress.hosts | list | `[]` | List of ingress hostnames |
| ingress.ingressClassName | string | `"nginx"` | Ingress class name |
| internalIngress.enabled | bool | `false` | Enable internal ingress with IP whitelisting |
| internalIngress.whitelistSourceRange | string | `""` | Comma-separated CIDR ranges allowed to access internal endpoints |
| kubernetesClusterDomain | string | `"cluster.local"` | Kubernetes cluster domain |
| livenessProbe.enabled | bool | `true` | Enable liveness probe |
| livenessProbe.failureThreshold | int | `6` | Failures before restarting |
| livenessProbe.initialDelaySeconds | int | `10` | Delay before first probe |
| livenessProbe.path | string | `"/lib/health/ready"` | HTTP path for liveness check |
| livenessProbe.periodSeconds | int | `10` | Interval between probes |
| livenessProbe.port | string | `"http"` | Port name or number for liveness check |
| livenessProbe.successThreshold | int | `1` | Successes before marking healthy |
| livenessProbe.timeoutSeconds | int | `5` | Probe timeout |
| nameOverride | string | `""` | Override the chart name |
| namespace | string | `""` | Namespace to deploy resources into (also creates the namespace if set) |
| namespaceLabels | object | `{}` | Labels to apply to the created namespace |
| podAnnotations | object | `{}` | Additional pod annotations |
| podLabels | object | `{}` | Additional pod labels |
| podSecurityContext.fsGroup | int | `1001` | Group ID for filesystem access |
| podSecurityContext.runAsNonRoot | bool | `true` | Require non-root user |
| readinessProbe.enabled | bool | `true` | Enable readiness probe |
| readinessProbe.failureThreshold | int | `3` | Failures before marking unready |
| readinessProbe.initialDelaySeconds | int | `5` | Delay before first probe |
| readinessProbe.path | string | `"/lib/health/ready"` | HTTP path for readiness check |
| readinessProbe.periodSeconds | int | `10` | Interval between probes |
| readinessProbe.port | string | `"http"` | Port name or number for readiness check |
| readinessProbe.successThreshold | int | `1` | Successes before marking ready |
| readinessProbe.timeoutSeconds | int | `5` | Probe timeout |
| replicaCount | int | `1` | Number of pod replicas |
| resources.limits.memory | string | `"512Mi"` | Memory limit |
| resources.requests.cpu | string | `"10m"` | CPU request |
| resources.requests.memory | string | `"256Mi"` | Memory request |
| secret.data | object | `{}` | Arbitrary key-value pairs rendered as a Kubernetes Secret (values are base64-encoded automatically) |
| secretStore.auth.role | string | `""` | Kubernetes auth role for vault |
| secretStore.enabled | bool | `false` | Enable SecretStore and ServiceAccount for vault integration |
| secretStore.name | string | `""` | SecretStore name |
| secretStore.server | string | `""` | Vault/OpenBao server URL |
| service.port | int | `4000` | Service and container port |
| service.type | string | `"ClusterIP"` | Kubernetes service type |
| startupProbe.enabled | bool | `true` | Enable startup probe |
| startupProbe.failureThreshold | int | `30` | Failures before marking unhealthy |
| startupProbe.initialDelaySeconds | int | `5` | Delay before first probe |
| startupProbe.path | string | `"/lib/health/start"` | HTTP path for startup check |
| startupProbe.periodSeconds | int | `10` | Interval between probes |
| startupProbe.port | string | `"http"` | Port name or number for startup check |
| startupProbe.successThreshold | int | `1` | Successes before marking healthy |
| startupProbe.timeoutSeconds | int | `5` | Probe timeout |

## Maintainers

| Name | Email | Url |
| ---- | ------ | --- |
| Arkemis S.r.l. |  | <https://github.com/arkemishub/helm-charts> |
