![lint](https://github.com/b3outputs/helm-charts/actions/workflows/ci.yaml/badge.svg) ![release](https://github.com/b3outputs/helm-charts/actions/workflows/release-charts.yaml/badge.svg)

# Generic App Chart

# Get repo

```bash
helm repo add b3o https://b3outputs.github.io/helm/
helm repo update
```

# Install chart:

```bash
helm install [RELEASE_NAME] b3o/generic-chart
```

*See [configuration](#configuration) below.*

# Uninstall chart


```bash
helm uninstall [RELEASE_NAME]
```

# Configuration

```shell
helm show values b3o/generic-chart
```

## Chart Parameters

TODO - Add all variables

| Variable | Description | Default value |
|---|---|---|
| `app.servicePort` | Service expose port | `80` |
| `app.containerPort` | Container expose port | `80` |
| `deployment.memoryRequest` | Minimal memory request for application | `50Mi` |
| `deployment.cpuRequest` | Minimal cpu request for application | `25m` |
| `deployment.memoryLimit` | Maximum memory request for application | `100Mi` |
| `deployment.cpuLimit` | Maximum cpu request for application | `75m` |
| `strategy.type` | Specifies the strategy used to replace old Pods by new ones | `RollingUpdate` |
| `strategy.rollingUpdate.maxUnavailable` | Deployment ensures that only a certain number of Pods are down while they are being updated. By default, it ensures that at least 75% of the desired number of Pods are up (25% max unavailable) | `25%` |
| `strategy.rollingUpdate.maxSurge` | Deployment also ensures that only a certain number of Pods are created above the desired number of Pods. By default, it ensures that at most 125% of the desired number of Pods are up (25% max surge). | `25%` |
| `image.repository` | Repository of image | `registry.hub.docker.com` |
| `image.name` | Name of image | `containous/whoami` |
| `image.tag` | Tag of image |  `latest` |
| `image.pullPolicy` | Policy of download of image | `Always` |

