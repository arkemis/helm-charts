# Backend Helm Chart

This Helm chart deploys the backend application to Kubernetes.

## Parameters

The following table lists the configurable parameters of the backend chart and their default values.

| Parameter | Description | Default |
|-----------|-------------|---------|
| `kubernetesClusterDomain` | Kubernetes cluster domain | `cluster.local` |
| `namespace` | Kubernetes namespace | `""` |
| `certIssuer` | Certificate issuer name | `""` |
| `deployment.replicas` | Number of backend pods | `1` |
| `deployment.image` | Backend image | `""` |
| `deployment.imagePullSecret` | Image pull secret name | `""` |
| `service.ports` | List of service ports | `[{port: 4000, protocol: TCP, targetPort: 4000}]` |
| `configMap.env` | Environment variables | `{}` |
| `internalIngress.enabled` | Enable internal ingress | `false` |
| `db.host` | Database host | `""` |
| `db.name` | Database name | `""` |
| `db.password` | Database password | `""` |
| `db.port` | Database port | `"5432"` |
| `db.user` | Database user | `postgres` |
| `ingress.hosts` | List of ingress hosts | `[]` |
| `gcloudServiceKey.enabled` | Enable Google Cloud service key | `false` |