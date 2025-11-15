# Ankra Agent

![Version: 1.0.3](https://img.shields.io/badge/Version-1.0.3-informational?style=flat-square)
![Type: application](https://img.shields.io/badge/Type-application-informational?style=flat-square)
![AppVersion: 1.0.232](https://img.shields.io/badge/AppVersion-1.0.232-informational?style=flat-square)

The Ankra Agent is a Kubernetes agent that enables seamless integration between your Kubernetes clusters and the Ankra platform, providing monitoring, management, and automation capabilities for your cloud-native infrastructure.

## Overview

Ankra Agent is designed to run inside your Kubernetes cluster and acts as a bridge between your infrastructure and the Ankra platform. It's built for reliable scheduling and task execution, optimized for low resource consumption while maintaining high performance.

## Features

- 🔄 **Real-time Monitoring**: Continuous cluster health and resource monitoring with built-in metrics collection
- 🛡️ **Secure Communication**: End-to-end encrypted connection to Ankra platform via HTTPS with token-based authentication
- 🎯 **Smart Scheduling**: NATS-powered scheduler for efficient task execution with configurable worker pools
- 📊 **Resource Management**: Optimized CPU and memory usage with configurable limits suitable for production workloads
- 🔐 **RBAC Ready**: Built-in ClusterRole and ServiceAccount configuration with least-privilege access
- 🚀 **Easy Deployment**: Simple Helm installation with minimal configuration via OCI registry
- ❤️ **Health Checks**: Comprehensive liveness, readiness, and startup probes for reliability
- 🔄 **High Availability**: Support for multiple replicas with pod anti-affinity and disruption budgets
- 📈 **Prometheus Integration**: Built-in metrics endpoint for observability
- 🐍 **Python Optimized**: Environment variables and settings optimized for Python runtime performance

## Quick Start

```bash
helm upgrade \
  --install ankra-agent oci://registry.ankra.cloud/ankra/ankra-agent \
  --version 1.0.232 \
  --set config.token=your-ankra-token-here \
  --namespace=ankra \
  --create-namespace
```

## Prerequisites

- Kubernetes 1.19+
- Helm 3.0+
- An Ankra account with agent token (sign up at [https://platform.ankra.app](https://platform.ankra.app))

## Configuration Highlights

### Basic Configuration

```yaml
config:
  ankra_url: "https://platform.ankra.app"
  token: "your-token"

log_level: "INFO"
replica_count: 1

resources:
  limits:
    cpu: 1000m
    memory: 512Mi
  requests:
    cpu: 200m
    memory: 256Mi
```

### Using Existing Secret

For production environments, store your token in a Kubernetes secret:

```bash
kubectl create secret generic ankra-token \
  --namespace ankra-system \
  --from-literal=token=your-token
```

Then reference it:

```yaml
config:
  existing_secret_name: "ankra-token"
  secret_key: "token"
```

### High Availability Setup

```yaml
replica_count: 2

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

pod_disruption_budget:
  enabled: true
  min_available: 1
```

## Values Reference

| Parameter | Description | Default |
|-----------|-------------|---------|
| `config.ankra_url` | Ankra platform URL | `https://platform.ankra.app` |
| `config.token` | Your Ankra agent token | `""` |
| `log_level` | Agent log level (DEBUG, INFO, WARNING, ERROR, CRITICAL) | `INFO` |
| `replica_count` | Number of agent replicas | `1` |
| `image.repository` | Agent image repository | `registry.ankra.cloud/ankra/agent` |
| `image.tag` | Image tag (empty = use appVersion) | `""` |
| `resources.limits.cpu` | CPU limit | `1000m` |
| `resources.limits.memory` | Memory limit | `512Mi` |

See the [full documentation](https://github.com/ankraio/ankra-agent-chart/blob/main/README.md) for complete configuration options.

## Support

- 📚 Documentation: [https://docs.ankra.io](https://docs.ankra.io)
- 🐛 Issues: [GitHub Issues](https://github.com/ankraio/ankra-agent-chart/issues)
- 📧 Email: hello@ankra.io

## License

Apache License 2.0
