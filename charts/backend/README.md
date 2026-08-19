# backend

Backend application for Arkemis

## Installation

```bash
helm repo add arkemis https://arkemis.github.io/helm-charts
helm install my-backend arkemis/backend
```

## Routing

Traffic is exposed either through Gateway API (`gateway.*`, NGINX Gateway Fabric) or through the
**deprecated** Ingress (`ingress.*`, `internalIngress.*`). Ready-made values files live in
[`examples/`](examples).

```bash
helm install my-backend arkemis/backend -f examples/gateway-values.yaml
```

### Prerequisites for `gateway.enabled`

- Gateway API CRDs and [NGINX Gateway Fabric](https://docs.nginx.com/nginx-gateway-fabric/) installed
  in the cluster.
- A `Gateway` — **not** created by this chart — with an HTTPS listener whose `hostname` covers
  `gateway.hostnames`, and whose TLS certificate is issued there. The chart renders only the
  `HTTPRoute`; it no longer requests a certificate.
- external-dns configured with the `gateway-httproute` source if DNS records should follow the route
  (hostnames are read from `HTTPRoute.spec.hostnames`, not from an annotation).
- NGF snippets filters enabled (`--set nginxGateway.snippetsFilters.enable=true`) only if
  `gateway.internal.enabled` is used.

### Migrating from Ingress

`ingress.*` and `internalIngress.*` still work and are unchanged, but are deprecated. Both paths may
run side by side during the cutover — they render separate resources and do not conflict — so enable
`gateway`, verify traffic, then set `ingress.enabled: false`.

| Deprecated Ingress setting | Gateway API equivalent |
| -------------------------- | ---------------------- |
| `ingress.enabled` | `gateway.enabled` |
| `ingress.hosts` | `gateway.hostnames` |
| `ingress.ingressClassName` | `gateway.parentRefs` (the Gateway selects the controller) |
| `ingress.certIssuer` | none — the Gateway listener owns TLS |
| `internalIngress.whitelistSourceRange` (comma-separated string) | `gateway.internal.allowedCidrs` (list), rendered as an NGF `SnippetsFilter` |
| `nginx.ingress.kubernetes.io/rewrite-target` | `URLRewrite` filter on the `HTTPRoute` rule (`/api` → `/`, `/api/internal` → `/internal`) |
| `nginx.ingress.kubernetes.io/proxy-body-size` | `gateway.clientSettings.maxBodySize` (NGF `ClientSettingsPolicy`) |
| `nginx.ingress.kubernetes.io/proxy-read-timeout`, `proxy-send-timeout` | `gateway.timeouts.request`, `gateway.timeouts.backendRequest` |

## Values

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| configMap.env | object | `{}` | Key-value pairs injected as environment variables via ConfigMap |
| containerSecurityContext.allowPrivilegeEscalation | bool | `false` | Disallow privilege escalation |
| containerSecurityContext.capabilities.drop | list | `["ALL"]` | Linux capabilities to drop |
| containerSecurityContext.readOnlyRootFilesystem | bool | `true` | Mount root filesystem as read-only |
| containerSecurityContext.runAsNonRoot | bool | `true` | Require non-root user |
| createNamespace | bool | `false` | Whether to create the namespace resource |
| externalSecrets | list | `[]` | List of ExternalSecret definitions |
| extraEnvVars | list | `[]` | Additional environment variables |
| extraVolumeMounts | list | `[]` | Additional volume mounts |
| extraVolumes | list | `[]` | Additional volumes |
| fullnameOverride | string | `""` | Override the fully qualified app name |
| gateway.annotations | object | `{}` | Additional HTTPRoute annotations |
| gateway.clientSettings.bodyTimeout | string | `"60s"` | Client request body read timeout |
| gateway.clientSettings.enabled | bool | `true` | Enable the NGF ClientSettingsPolicy targeting the HTTPRoute |
| gateway.clientSettings.maxBodySize | string | `"15m"` | Maximum client request body size |
| gateway.enabled | bool | `false` | Enable HTTPRoute (Gateway API). Replaces the deprecated `ingress` |
| gateway.hostnames | list | `[]` | List of hostnames served by the HTTPRoute (empty matches every hostname of the Gateway listener) |
| gateway.internal.allowedCidrs | list | `[]` | CIDR ranges allowed to reach `/api/internal`. Empty means deny all |
| gateway.internal.enabled | bool | `false` | Enable the `/api/internal` route restricted by an NGF SnippetsFilter IP allowlist. Requires NGF installed with snippets filters enabled |
| gateway.parentRefs | list | `[{"name":"nginx","namespace":"nginx-gateway","sectionName":"https"}]` | Gateways to attach the HTTPRoute to (name, namespace, sectionName). The Gateway itself, its TLS listener and its certificate are not managed by this chart |
| gateway.timeouts | object | `{"backendRequest":"900s","request":"900s"}` | Per-rule HTTPRoute timeouts (`request`, `backendRequest`); set to `null` to use NGINX defaults |
| headlessService.enabled | bool | `true` | Enable headless service for pod discovery |
| image.pullPolicy | string | `"Always"` | Image pull policy |
| image.pullSecrets | list | `[]` | List of image pull secret names |
| image.registry | string | `"ghcr.io"` | Container image registry |
| image.repository | string | `""` | Container image repository |
| image.tag | string | `""` | Container image tag (defaults to chart appVersion) |
| ingress.annotations | object | `{}` | DEPRECATED (use `gateway.annotations`): Additional ingress annotations (merged with chart defaults) |
| ingress.certIssuer | string | `"cert-manager-global"` | DEPRECATED (the Gateway listener owns TLS): cert-manager ClusterIssuer name |
| ingress.enabled | bool | `true` | DEPRECATED (use `gateway.enabled`): Enable ingress |
| ingress.hosts | list | `[]` | DEPRECATED (use `gateway.hostnames`): List of ingress hostnames |
| ingress.ingressClassName | string | `"nginx"` | DEPRECATED (use `gateway.parentRefs`): Ingress class name |
| internalIngress.enabled | bool | `false` | DEPRECATED (use `gateway.internal.enabled`): Enable internal ingress with IP whitelisting |
| internalIngress.whitelistSourceRange | string | `""` | DEPRECATED (use `gateway.internal.allowedCidrs`): Comma-separated CIDR ranges allowed to access internal endpoints |
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
| namespace | string | `""` | Namespace to deploy resources into |
| namespaceLabels | object | `{}` | Labels to apply to the created namespace |
| podAnnotations | object | `{}` | Additional pod annotations |
| podLabels | object | `{}` | Additional pod labels |
| podSecurityContext.fsGroup | int | `65534` | Group ID for filesystem access |
| podSecurityContext.runAsGroup | int | `65534` | Group ID to run as |
| podSecurityContext.runAsNonRoot | bool | `true` | Require non-root user |
| podSecurityContext.runAsUser | int | `65534` | User ID to run as |
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
| secretStore.caProvider | object | `{}` | CA provider for TLS verification (type, name, key) |
| secretStore.enabled | bool | `false` | Enable SecretStore and ServiceAccount for vault integration |
| secretStore.name | string | `""` | SecretStore name |
| secretStore.server | string | `""` | Vault/OpenBao server URL |
| service.port | int | `4000` | Service and container port |
| service.type | string | `"ClusterIP"` | Kubernetes service type |
| serviceAccount.automount | bool | `false` | Mount the ServiceAccount token into the pod. Required by GKE Workload Identity |
| serviceAccount.name | string | `""` | Name of an existing ServiceAccount to run the pod as. Empty uses the namespace default. The ServiceAccount is not created here: GKE Workload Identity needs it annotated with a Google service account email that only the infrastructure layer knows |
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
