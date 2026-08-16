# Changelog

All notable changes to the Ankra Agent Helm Chart will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

## [1.0.234] - 2026-08-17

### Added
- Security & Compliance section in the chart README and repository README,
  linking the shared responsibility model, agent compliance review, hardening
  guide, audit export, and the trust centre
- PGP signing of release artifacts: `helm package --sign` in the release
  workflow (key from the `HELM_SIGNING_KEY` secret), `.prov` provenance file
  attached to each release, public key published at `pgp/ankra-helm-signing.asc`
  and declared via the `artifacthub.io/signKey` annotation

### Changed
- Documentation now describes the Go agent. The agent was rewritten from Python
  to Go, but the chart still advertised a "Python Optimized" runtime and resource
  settings tuned for a Python interpreter.
- `nats_worker_max_workers` is described as a concurrency bound rather than a
  count of "worker threads"; the agent schedules goroutines, not threads.

### Fixed
- `log_level` no longer offers `CRITICAL`. The agent maps `DEBUG`, `INFO`,
  `WARNING`/`WARN` and `ERROR`, and falls back to `INFO` for anything else, so
  `CRITICAL` silently produced *more* logging than `INFO` rather than less.
- `values.schema.json` now accepts `WARN`, which the agent has always understood
  but the schema rejected.
- Documented resource defaults match `values.yaml` (100Mi request, 200Mi limit,
  no CPU limit) instead of CPU and memory figures the chart never shipped.
- The upgrade command referenced a `ankra/ankra-agent` chart under a repo added
  as `ankra-agent`, so it could not resolve as written.
- `chart/README.md` stated Kubernetes 1.19+ while the root README stated 1.31+.

### Removed
- Documentation for a startup probe and a `pod_disruption_budget` value; neither
  is rendered by this chart.

## [1.0.233] - 2026-08-16

### Fixed
- Removed the invalid `artifacthub.io/alternativeName` annotation. Artifact Hub
  rejected every registration of 1.0.232 with "invalid alternative name (must be
  a subset or superset of the name)", so the package — and with it the chart
  README — never appeared on Artifact Hub. The annotation was dropped from `main`
  in July 2026 but no release carried the fix until now.

### Added
- JSON schema for chart values (`chart/values.schema.json`)
- Container image metadata for the agent image

## [1.0.232] - 2025-11-15

### Added
- Initial public release of the Ankra Agent Helm Chart
- Support for configuring agent via values
- RBAC resources (ClusterRole, ClusterRoleBinding, ServiceAccount)
- Deployment with configurable replicas
- Service configuration
- Secret management for agent tokens
- Configurable resource limits and requests
- Support for topology spread constraints
- Support for node selectors, tolerations, and affinity rules
- Configurable logging levels
- NATS worker configuration
- Graceful shutdown configuration

## [0.0.1] - 2025-11-15

### Added
- Initial chart structure
- Basic deployment configuration
- Service account and RBAC setup
- ConfigMap and Secret support
- Service definition
- Helm helpers and templates

[Unreleased]: https://github.com/ankraio/ankra-agent-chart/compare/v1.0.234...HEAD
[1.0.234]: https://github.com/ankraio/ankra-agent-chart/compare/v1.0.233...v1.0.234
[1.0.233]: https://github.com/ankraio/ankra-agent-chart/compare/v1.0.232...v1.0.233
[1.0.232]: https://github.com/ankraio/ankra-agent-chart/compare/v0.0.1...v1.0.232
[0.0.1]: https://github.com/ankraio/ankra-agent-chart/releases/tag/v0.0.1
