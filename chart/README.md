# Ankra Agent

![Version: 1.0.234](https://img.shields.io/badge/Version-1.0.234-informational?style=flat-square)
![Type: application](https://img.shields.io/badge/Type-application-informational?style=flat-square)
![AppVersion: 1.0.234](https://img.shields.io/badge/AppVersion-1.0.234-informational?style=flat-square)

The Ankra Agent is a Kubernetes agent that enables seamless integration between your Kubernetes clusters and the Ankra platform, providing monitoring, management, and automation capabilities for your cloud-native infrastructure.

## Overview

Ankra Agent is a Go service that runs inside your Kubernetes cluster and acts as a bridge between your infrastructure and the Ankra platform. It's built for reliable scheduling and task execution, optimized for low resource consumption while maintaining high performance.

## Features

- 🔄 **Real-time Monitoring**: Continuous cluster health and resource monitoring with built-in metrics collection
- 🛡️ **Secure Communication**: End-to-end encrypted connection to Ankra platform via HTTPS with token-based authentication
- 🎯 **Smart Scheduling**: NATS-powered scheduler for efficient task execution with configurable worker pools
- 📊 **Resource Management**: Memory-bounded by default with configurable requests and limits suitable for production workloads
- 🔐 **RBAC Ready**: Built-in ClusterRole and ServiceAccount configuration with least-privilege access
- 🚀 **Easy Deployment**: Simple Helm installation with minimal configuration via OCI registry
- ❤️ **Health Checks**: Liveness and readiness probes on `/livez` and `/readyz` for reliability
- 🔄 **High Availability**: Support for multiple replicas with pod affinity and topology spread constraints
- 📈 **Prometheus Integration**: Built-in metrics endpoint for observability
- 🐹 **Small Footprint**: A single static Go binary with no runtime or interpreter to ship, sized for a 100Mi request

## Quick Start

```bash
helm upgrade \
  --install ankra-agent oci://registry.ankra.cloud/ankra/ankra-agent \
  --version 1.0.234 \
  --set config.token=your-ankra-token-here \
  --namespace=ankra \
  --create-namespace
```

## Prerequisites

- Kubernetes 1.31+
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
    memory: 200Mi
  requests:
    memory: 100Mi
```

The agent is a single static Go binary, so it is sized on memory only — no CPU
limit is set by default, which keeps it from being throttled while reconciling.

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

topologySpreadConstraints:
  - maxSkew: 1
    topologyKey: topology.kubernetes.io/zone
    whenUnsatisfiable: ScheduleAnyway
    labelSelector:
      matchLabels:
        app.kubernetes.io/name: ankra-agent
```

## Values Reference

| Parameter | Description | Default |
|-----------|-------------|---------|
| `config.ankra_url` | Ankra platform URL | `https://platform.ankra.app` |
| `config.token` | Your Ankra agent token | `""` |
| `log_level` | Agent log level (`DEBUG`, `INFO`, `WARNING`, `ERROR`) | `INFO` |
| `nats_worker_max_workers` | Maximum concurrent NATS command workers | `15` |
| `replica_count` | Number of agent replicas | `1` |
| `image.repository` | Agent image repository | `registry.ankra.cloud/ankra/agent` |
| `image.tag` | Image tag (empty = use appVersion) | `stable` |
| `resources.requests.memory` | Memory request | `100Mi` |
| `resources.limits.memory` | Memory limit | `200Mi` |

See the [full documentation](https://github.com/ankraio/ankra-agent-chart/blob/main/README.md) for complete configuration options.

## Security & Compliance

Ankra is a delivery and management plane, not a hosting provider: it does not sit in your data path, and clusters keep serving traffic even if the platform is unreachable (only reconciliation pauses). See the [shared responsibility model](https://docs.ankra.ai/security/shared-responsibility) for how this narrows Ankra's footprint in your SOC 2 / ISO 27001 scope.

- **Connectivity**: the agent makes outbound HTTPS connections to the Ankra platform only. No inbound access to your cluster is required; the bundled Service is a `ClusterIP` used for health and metrics probes.
- **Credentials**: the agent authenticates with a per-cluster token. Supply it via `config.token`, or reference a pre-existing Kubernetes Secret with `config.existing_secret_name` / `config.secret_key` so no token material passes through Helm values.
- **Permissions**: the agent runs with a cluster-wide `ClusterRole` granting full API access, which it needs to deliver and reconcile arbitrary resources. It adds no new compliance boundary — it is one privileged identity inside a boundary you already govern. Review it in [agent compliance](https://docs.ankra.ai/security/agent-compliance) and lock it down further with the [hardening guide](https://docs.ankra.ai/security/agent-hardening).
- **Audit**: actions performed through Ankra are recorded in the organisation audit log and exportable as CSV/JSON — see [audit export](https://docs.ankra.ai/security/audit-export).
- **Certifications**: current certification and compliance status is published at [ankra.ai/trust](https://ankra.ai/trust).

### Verifying release signatures

Releases ship with a PGP provenance file. Verify a downloaded chart against the [Ankra Helm signing key](https://raw.githubusercontent.com/ankraio/ankra-agent-chart/main/pgp/ankra-helm-signing.asc) (fingerprint `B4B5 3D74 0742 3346 DA8D 6648 1EE5 3FF7 E023 7237`):

```bash
curl -sSLO https://raw.githubusercontent.com/ankraio/ankra-agent-chart/main/pgp/ankra-helm-signing.asc
gpg --dearmor ankra-helm-signing.asc
helm fetch --prov https://github.com/ankraio/ankra-agent-chart/releases/download/v1.0.234/ankra-agent-1.0.234.tgz
helm verify --keyring ankra-helm-signing.asc.gpg ankra-agent-1.0.234.tgz
```

## Support

- 📚 Documentation: [https://docs.ankra.ai](https://docs.ankra.ai)
- 🐛 Issues: [GitHub Issues](https://github.com/ankraio/ankra-agent-chart/issues)
- 📧 Email: hello@ankra.io

## License

Apache License 2.0
