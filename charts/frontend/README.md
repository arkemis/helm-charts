# frontend

Frontend application for Arkemis

## Installation

```bash
helm repo add arkemis https://arkemis.github.io/helm-charts
helm install my-frontend arkemis/frontend
```

## Routing

Traffic is exposed through Gateway API (`gateway.*`). Ready-made values files live in
[`examples/`](examples).

```bash
helm install my-frontend arkemis/frontend -f examples/gateway-values.yaml
```

### Gateway API layout

Everything under `gateway.*` renders standard Gateway API and cert-manager resources, except
`gateway.nginx.*`, which holds NGINX Gateway Fabric extensions. On another controller, set
`gateway.nginx.enabled: false`; routes, listeners and certificates stay as they are.

- `gateway.listenerSet.create: true` renders a `ListenerSet` with one HTTPS listener per entry in
  `gateway.hostnames`, attached to the shared Gateway, plus one cert-manager `Certificate` covering all of
  them. Enable it in exactly one release per set of hostnames (e.g. the backend).
- Every release's `HTTPRoute` attaches to the ListenerSet named by `gateway.listenerSet.name` (its own
  when it creates one), so a frontend and backend sharing hostnames set the same `name`.

### Prerequisites for `gateway.enabled`

- Gateway API CRDs v1.5+ (for `ListenerSet`) and cert-manager installed in the cluster.
- A shared `Gateway`, **not** created by this chart, whose `allowedListeners` admits ListenerSets
  from the release namespace, and the ClusterIssuer named in `gateway.listenerSet.certIssuer`.
- NGINX Gateway Fabric for the `gateway.nginx.*` extensions.
- DNS records for `gateway.hostnames` pointing at the Gateway address (or external-dns with the
  `gateway-httproute` source).

### Upgrading from 0.4.x or earlier

The Ingress (`ingress.*`) was removed in 0.6.0. Set the Gateway API equivalents
below; the old keys are ignored.

| Removed Ingress setting | Gateway API equivalent |
| -------------------------- | ---------------------- |
| `ingress.enabled` | `gateway.enabled` |
| `ingress.hosts` | `gateway.hostnames` |
| `ingress.ingressClassName` | `gateway.listenerSet.gateway` (the Gateway selects the controller) |
| `ingress.certIssuer` | `gateway.listenerSet.certIssuer` |
| `nginx.ingress.kubernetes.io/proxy-body-size` | `gateway.nginx.clientSettings.maxBodySize` (NGF `ClientSettingsPolicy`) |
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
| gateway.enabled | bool | `false` | Enable HTTPRoute (Gateway API) |
| gateway.hostnames | list | `[]` | Hostnames served by the HTTPRoute; with `listenerSet.create` also one HTTPS listener each and the certificate's DNS names |
| gateway.listenerSet.certIssuer | string | `"cert-manager-gateway"` | cert-manager ClusterIssuer for the created Certificate |
| gateway.listenerSet.create | bool | `false` | Create the ListenerSet and its cert-manager Certificate. Enable in exactly one release per set of hostnames; other releases attach to it by `name` |
| gateway.listenerSet.gateway | object | `{"name":"nginx","namespace":"nginx-gateway"}` | Shared Gateway the created ListenerSet attaches to |
| gateway.listenerSet.name | string | `""` | ListenerSet the HTTPRoute attaches to. Defaults to this release's own when `create` is true; required otherwise |
| gateway.nginx.clientSettings.bodyTimeout | string | `"60s"` | Client request body read timeout |
| gateway.nginx.clientSettings.enabled | bool | `true` | Enable the NGF ClientSettingsPolicy targeting the HTTPRoute |
| gateway.nginx.clientSettings.maxBodySize | string | `"15m"` | Maximum client request body size |
| gateway.nginx.enabled | bool | `true` | Render the NGINX Gateway Fabric extensions below. Set to `false` on any other Gateway API controller: their CRDs exist only where NGF is installed |
| gateway.timeouts | object | `{"backendRequest":"900s","request":"900s"}` | Per-rule HTTPRoute timeouts (`request`, `backendRequest`); set to `null` to use controller defaults |
| image.digest | string | `""` | Container image digest (takes precedence over tag) |
| image.pullPolicy | string | `"Always"` | Image pull policy |
| image.pullSecrets | list | `[]` | List of image pull secret names |
| image.registry | string | `"ghcr.io"` | Container image registry |
| image.repository | string | `""` | Container image repository |
| image.tag | string | `""` | Container image tag (defaults to chart appVersion) |
| kubernetesClusterDomain | string | `"cluster.local"` | Kubernetes cluster domain |
| livenessProbe.enabled | bool | `true` | Enable liveness probe |
| livenessProbe.failureThreshold | int | `6` | Failures before restarting |
| livenessProbe.initialDelaySeconds | int | `10` | Delay before first probe |
| livenessProbe.path | string | `"/"` | HTTP path for liveness check |
| livenessProbe.periodSeconds | int | `10` | Interval between probes |
| livenessProbe.port | string | `"http"` | Port name or number for liveness check |
| livenessProbe.successThreshold | int | `1` | Successes before marking healthy |
| livenessProbe.timeoutSeconds | int | `5` | Probe timeout |
| nameOverride | string | `""` | Override the chart name |
| namespace | string | `""` | Namespace to deploy resources into |
| namespaceLabels | object | `{}` | Labels to apply to the created namespace |
| podAnnotations | object | `{}` | Additional pod annotations |
| podLabels | object | `{}` | Additional pod labels |
| podSecurityContext.fsGroup | int | `1001` | Group ID for filesystem access |
| podSecurityContext.runAsGroup | int | `1001` | Group ID to run as |
| podSecurityContext.runAsNonRoot | bool | `true` | Require non-root user |
| podSecurityContext.runAsUser | int | `1001` | User ID to run as |
| readinessProbe.enabled | bool | `true` | Enable readiness probe |
| readinessProbe.failureThreshold | int | `3` | Failures before marking unready |
| readinessProbe.initialDelaySeconds | int | `5` | Delay before first probe |
| readinessProbe.path | string | `"/"` | HTTP path for readiness check |
| readinessProbe.periodSeconds | int | `10` | Interval between probes |
| readinessProbe.port | string | `"http"` | Port name or number for readiness check |
| readinessProbe.successThreshold | int | `1` | Successes before marking ready |
| readinessProbe.timeoutSeconds | int | `5` | Probe timeout |
| replicaCount | int | `1` | Number of pod replicas |
| resources.limits.memory | string | `"256Mi"` | Memory limit |
| resources.requests.cpu | string | `"5m"` | CPU request |
| resources.requests.memory | string | `"128Mi"` | Memory request |
| secret.data | object | `{}` | Arbitrary key-value pairs rendered as a Kubernetes Secret (values are base64-encoded automatically) |
| secretStore.auth.role | string | `""` | Kubernetes auth role for vault |
| secretStore.caProvider | object | `{}` | CA provider for TLS verification (type, name, key) |
| secretStore.enabled | bool | `false` | Enable SecretStore and ServiceAccount for vault integration |
| secretStore.name | string | `""` | SecretStore name |
| secretStore.server | string | `""` | Vault/OpenBao server URL |
| service.port | int | `3000` | Service and container port |
| service.type | string | `"ClusterIP"` | Kubernetes service type |
| startupProbe.enabled | bool | `true` | Enable startup probe |
| startupProbe.failureThreshold | int | `30` | Failures before marking unhealthy |
| startupProbe.initialDelaySeconds | int | `5` | Delay before first probe |
| startupProbe.path | string | `"/"` | HTTP path for startup check |
| startupProbe.periodSeconds | int | `10` | Interval between probes |
| startupProbe.port | string | `"http"` | Port name or number for startup check |
| startupProbe.successThreshold | int | `1` | Successes before marking healthy |
| startupProbe.timeoutSeconds | int | `5` | Probe timeout |

## Maintainers

| Name | Email | Url |
| ---- | ------ | --- |
| Arkemis S.r.l. |  | <https://github.com/arkemishub/helm-charts> |
