# Changelog

All notable changes to the Ankra Agent Helm Chart will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

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

[Unreleased]: https://github.com/ankra-io/ankra-agent-chart/compare/v1.0.232...HEAD
[1.0.232]: https://github.com/ankra-io/ankra-agent-chart/compare/v0.0.1...v1.0.232
[0.0.1]: https://github.com/ankra-io/ankra-agent-chart/releases/tag/v0.0.1
