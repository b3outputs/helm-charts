![lint](https://github.com/b3outputs/helm-charts/actions/workflows/ci.yaml/badge.svg) ![release](https://github.com/b3outputs/helm-charts/actions/workflows/release-charts.yaml/badge.svg)

# Generic App Chart

- [Get repo](#get-repo)
- [Install chart](#install-chart)
- [Uninstall chart](#uninstall-chart)
- [Using examples](#using-examples)
  - [Simple use](#simple-use)
  - [Pratical use](#pratical-use)
- [Configuration](#configuration)
  - [Chart parameters](#chart-parameters)

# Get repo

```bash
helm repo add b3o https://helm.b3o.io
helm repo update
```

# Install chart:

```bash
helm install [RELEASE_NAME] b3o/generic-app
```

*See [configuration](#configuration) below.*

# Uninstall chart

```bash
helm uninstall [RELEASE_NAME]
```

# Using examples

## Simple use

```bash
helm install webserver --set image.name=nginx b3o/generic-app
```

The above command will deploy a `Deployment` and `Service` resources.  

We can check this with the below command:

```bash
helm get manifest webserver
```

Manifest output:

```yaml
---
# Source: generic-app/templates/service.yaml
apiVersion: v1
kind: Service
metadata:
  name: webserver
  labels:
    app: webserver
spec:
  selector:
    app: webserver
  ports:
  - targetPort: 80
    port: 80
---
# Source: generic-app/templates/deployment.yaml
kind: Deployment
apiVersion: apps/v1
metadata:
  name: webserver
  labels:
    app: webserver 
spec:
  replicas: 
  selector:
    matchLabels:
      app: webserver
  template:
    metadata:
      labels:
        app: webserver
    spec:
      containers:
        - name: webserver
          imagePullPolicy: Always
          image: "nginx:latest"
          ports:
          - containerPort: 80
          resources:
            requests:
            limits:
```

## Pratical use

In the example, we are going to execute the Wordpress application + MySQL Database with the same Chart!

**Database:**

```bash
helm install database \
    --set image.name=mariadb \
    --set variables.nonSecret.MYSQL_RANDOM_ROOT_PASSWORD=yes \
    --set variables.nonSecret.MYSQL_DATABASE=wordpress \
    --set variables.nonSecret.MYSQL_USER=username \
    --set variables.secret.MYSQL_PASSWORD=password \
    --set app.containerPort=3306 \
    --set app.servicePort=3306 \
    b3o/generic-app
```

**Wordpress:**

```bash
helm install wordpress \
    --set image.name=wordpress \
    --set variables.nonSecret.WORDPRESS_DB_HOST=database \
    --set variables.nonSecret.WORDPRESS_DB_NAME=wordpress \
    --set variables.nonSecret.WORDPRESS_DB_USER=username \
    --set variables.secret.WORDPRESS_DB_PASSWORD=password \
    b3o/generic-app
```

Check with `kubectl port-forward`:

```bash
kubectl port-forward service/wordpress 8080:80
```

Access `http://localhost:8080` and enjoy!

> **Note:** For external access using *NGINX Ingress Controller* and SSL with *Cert-Manager* see the [Chart Parameters](#chart-parameters) area.

# Configuration

## Show values

```shell
helm show values b3o/generic-app
```

## Chart Parameters

| Variable | Description | Default value |
|---|---|---|
| `app.servicePort` | Service expose port | `80` |
| `app.containerPort` | Container expose port | `80` |
| `deployment.memoryRequest` | Minimal memory request for application | `null` |
| `deployment.cpuRequest` | Minimal cpu request for application | `null` |
| `deployment.memoryLimit` | Maximum memory request for application | `null` |
| `deployment.cpuLimit` | Maximum cpu request for application | `null` |
| `strategy.type` | Specifies the strategy used to replace old Pods by new ones | `RollingUpdate` |
| `strategy.rollingUpdate.maxUnavailable` | Deployment ensures that only a certain number of Pods are down while they are being updated. By default, it ensures that at least 75% of the desired number of Pods are up (25% max unavailable) | `25%` |
| `strategy.rollingUpdate.maxSurge` | Deployment also ensures that only a certain number of Pods are created above the desired number of Pods. By default, it ensures that at most 125% of the desired number of Pods are up (25% max surge). | `25%` |
| `image.repository.name` | Repository of image | `null` |
| `image.repository.isPrivate` | If repository is private | `false` |
| `image.repository.secretName` | If `image.repository.isPrivate` is `true` to define secret name with auth for repo | `null` |
| `image.name` | Name of image | `containous/whoami` |
| `image.tag` | Tag of image |  `latest` |
| `image.pullPolicy` | Policy of download of image | `Always` |
| `ingress.enabled` | Enable ingress resource (for nginx ingress controller) | `false` |
| `ingress.hostName` | Define FQDN access for web application (to configure DNS) | `null`
| `ingress.sslRedirect` | Enable redirect for ssl (for nginx ingress controller) | `false` |
| `ingress.nginxController` | If use nginx linke ingress controller | `false` |
| `ingress.certManager.enabled` | Enable cert-manager annotation | `false` |
| `ingress.certManager.clusterIssuerName` | Cluster Issuer Name for cert-manager | `letsencrypt` |
| `variables.secret` | To create **SECRET** variables on deployment | `{}` |
| `variables.nonSecret` | To create **NON SECRET** variables on deployment | `{}` |
| `hpa.enabled` | Enable horizontal pod autoscaler | `false` |
| `hpa.minReplicas` | Minimal replicas in execution | `1` |
| `hpa.maxReplicas` | Maximum replicas in execution | `2` |
| `hpa.avgCpuUtilization` | Percentage cpu value that will trigger horizontal scaling | `50` |
| `startupProbe.enabled` | Enable startup probe | `false` |
| `startupProbe.httpGet.path` | Path for get http probe | `/` |
| `startupProbe.periodSeconds` | How often (in seconds) to perform the probe | `10` |
| `startupProbe.failureThreshold` | When a probe fails, Kubernetes will try failureThreshold times before giving up | `20` |
| `startupProbe.timeoutSeconds` | Number of seconds after which the probe times out | `1` |
| `readinessProbe.enabled` | Enable readiness probe | `false` |
| `readinessProbe.httpGet.path` | Path for get http probe | `/` |
| `readinessProbe.initialDelaySeconds` | Tells the kubelet that it should wait N seconds | `20` |
| `readinessProbe.periodSeconds` | How often (in seconds) to perform the probe | `30` |
| `readinessProbe.failureThreshold` | When a probe fails, Kubernetes will try failureThreshold times before giving up. In case of readiness probe the Pod will be marked Unready | `2` |
| `readinessProbe.timeoutSeconds` | Number of seconds after which the probe times out | `3` |
| `livenessProbe.enabled` | Enable liveness probe | `false` |
| `livenessProbe.httpGet.path` | Path for get http probe | `/` |
| `livenessProbe.initialDelaySeconds` | Tells the kubelet that it should wait N seconds | `20` |
| `livenessProbe.periodSeconds` | How often (in seconds) to perform the probe | `60` |
| `livenessProbe.failureThreshold` | When a probe fails, Kubernetes will try failureThreshold times before giving up. Giving up in case of liveness probe means restarting the container | `1` |
| `livenessProbe.timeoutSeconds` | Number of seconds after which the probe times out | `3` |

More details regarding the probes (startup, readiness and liveness) can be found in the official documentation here:  
https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/
