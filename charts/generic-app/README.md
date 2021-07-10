![lint](https://github.com/b3outputs/helm-charts/actions/workflows/ci.yaml/badge.svg) ![release](https://github.com/b3outputs/helm-charts/actions/workflows/release-charts.yaml/badge.svg)

# Generic App Chart

Add chart:

```bash
$ helm repo add b3o https://b3outputs.github.io/helm/
```

Install chart:

```bash
$ helm install b3o/generic-chart
```

# Chart Parameters

TODO - Add all variables

| Variable | Description | Default value |
|---|---|---|
| app.servicePort | Service expose port | 80 |
| app.containerPort | Container expose port | 80 |
| deployment.memoryRequest | Minimal memory request for application | 50Mi |
| deployment.cpuRequest | Minimal cpu request for application | 25m |
| deployment.memoryLimit | Maximum memory request for application | 100Mi |
| deployment.cpuLimit | Maximum cpu request for application | 75m |

