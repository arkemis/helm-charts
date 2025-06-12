# Frontend Helm Chart

This Helm chart deploys the frontend application to Kubernetes.

## Parameters

The following table lists the configurable parameters of the frontend chart and their default values.

| Parameter | Description | Default |
|-----------|-------------|---------|
| `kubernetesClusterDomain` | Kubernetes cluster domain | `""` |
| `namespace` | Kubernetes namespace | `""` |
| `certIssuer` | Certificate issuer name | `""` |
| `deployment.replicas` | Number of frontend pods | `1` |
| `deployment.repository` | Frontend image repository | `""` |
| `deployment.tag` | Frontend image tag | `""` |
| `deployment.imagePullSecret` | Image pull secret name | `""` |
| `service.ports` | List of service ports | `[{port: 3000, protocol: TCP, targetPort: 3000}]` |
| `configMap.env` | Environment variables | `{}` |
| `ingress.hosts` | List of ingress hosts | `[]` |