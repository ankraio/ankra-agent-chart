# Ankra Agent Helm Chart

Official Helm chart for deploying the Ankra Agent to Kubernetes clusters.

## Overview

The Ankra Agent enables seamless integration between your Kubernetes clusters and the Ankra platform, providing monitoring, management, and automation capabilities.

## Prerequisites

- Kubernetes 1.19+
- Helm 3.0+
- An Ankra account with an agent token (sign up at [https://platform.ankra.app](https://platform.ankra.app))

## Quick Start

### Installation

1. Add the Ankra Helm repository:

```bash
helm repo add ankra https://ankra-io.github.io/ankra-agent-chart
helm repo update
```

2. Create a namespace for the agent:

```bash
kubectl create namespace ankra-system
```

3. Install the chart with your Ankra token:

```bash
helm install ankra-agent ankra/ankra-agent \
  --namespace ankra-system \
  --set config.token="your-ankra-token-here"
```

### Installation from source

Clone this repository and install directly:

```bash
git clone https://github.com/ankra-io/ankra-agent-chart.git
cd ankra-agent-chart
helm install ankra-agent ./chart \
  --namespace ankra-system \
  --set config.token="your-ankra-token-here"
```

## Configuration

### Basic Configuration

| Parameter | Description | Default |
|-----------|-------------|---------|
| `config.ankra_url` | Ankra platform URL | `https://platform.ankra.app` |
| `config.token` | Your Ankra agent token | `""` |
| `log_level` | Agent log level (DEBUG, INFO, WARNING, ERROR, CRITICAL) | `INFO` |
| `replica_count` | Number of agent replicas | `1` |

### Advanced Configuration

| Parameter | Description | Default |
|-----------|-------------|---------|
| `nats_worker_max_workers` | Maximum NATS worker threads | `15` |
| `terminationGracePeriodSeconds` | Graceful shutdown timeout | `600` |
| `image.repository` | Agent image repository | `registry.ankra.cloud/ankra/agent` |
| `image.tag` | Agent image tag | `stable` |
| `image.pull_policy` | Image pull policy | `Always` |
| `resources.limits.memory` | Memory limit | `200Mi` |
| `resources.requests.memory` | Memory request | `100Mi` |

### Using an Existing Secret

For production deployments, store your token in a Kubernetes secret:

```bash
kubectl create secret generic ankra-token \
  --namespace ankra-system \
  --from-literal=token=your-ankra-token-here
```

Then reference it in your values:

```yaml
config:
  existing_secret_name: "ankra-token"
  secret_key: "token"
```

### Example Configurations

#### Development Environment

```yaml
# values-dev.yaml
log_level: "DEBUG"
replica_count: 1
resources:
  limits:
    memory: 150Mi
  requests:
    memory: 75Mi
```

Install with:
```bash
helm install ankra-agent ./chart -f values-dev.yaml \
  --set config.token="your-token"
```

#### Production Environment

```yaml
# values-prod.yaml
log_level: "INFO"
replica_count: 2
terminationGracePeriodSeconds: 900
nats_worker_max_workers: 20

config:
  existing_secret_name: "ankra-token"
  secret_key: "token"

resources:
  limits:
    memory: 400Mi
  requests:
    memory: 200Mi

affinity:
  podAntiAffinity:
    preferredDuringSchedulingIgnoredDuringExecution:
    - weight: 100
      podAffinityTerm:
        labelSelector:
          matchExpressions:
          - key: app.kubernetes.io/name
            operator: In
            values:
            - ankra-agent
        topologyKey: kubernetes.io/hostname
```

## Upgrading

To upgrade the agent to a new version:

```bash
helm repo update
helm upgrade ankra-agent ankra/ankra-agent --namespace ankra-system
```

## Uninstalling

To uninstall the agent:

```bash
helm uninstall ankra-agent --namespace ankra-system
```

To also remove the namespace:

```bash
kubectl delete namespace ankra-system
```

## Troubleshooting

### Check Agent Status

```bash
kubectl get pods -n ankra-system
kubectl logs -n ankra-system -l app.kubernetes.io/name=ankra-agent
```

### Common Issues

**Agent not connecting:**
- Verify your token is correct
- Check network connectivity to `platform.ankra.app`
- Review logs for connection errors

**High memory usage:**
- Adjust `resources.limits.memory` based on your cluster size
- Consider reducing `nats_worker_max_workers`

**Pod not starting:**
- Check image pull secrets if using a private registry
- Verify RBAC permissions are correctly configured

## Support

- Documentation: [https://docs.ankra.io](https://docs.ankra.io)
- Issues: [GitHub Issues](https://github.com/ankra-io/ankra-agent-chart/issues)
- Email: hello@ankra.io

## License

Apache License 2.0 - See [LICENSE](LICENSE) for details.
