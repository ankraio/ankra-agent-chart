# Contributing to Ankra Agent Helm Chart

Thank you for your interest in contributing to the Ankra Agent Helm Chart! We welcome contributions from the community.

## Table of Contents

- [Code of Conduct](#code-of-conduct)
- [How Can I Contribute?](#how-can-i-contribute)
- [Development Setup](#development-setup)
- [Submitting Changes](#submitting-changes)
- [Style Guidelines](#style-guidelines)
- [Testing](#testing)

## Code of Conduct

This project adheres to the Contributor Covenant [Code of Conduct](CODE_OF_CONDUCT.md). By participating, you are expected to uphold this code.

## How Can I Contribute?

### Reporting Bugs

Before creating bug reports, please check the existing issues to avoid duplicates. When creating a bug report, include:

- **Clear title and description**
- **Steps to reproduce** the issue
- **Expected behavior** vs. **actual behavior**
- **Environment details** (Kubernetes version, Helm version, etc.)
- **Logs or error messages** if applicable

### Suggesting Enhancements

Enhancement suggestions are tracked as GitHub issues. When creating an enhancement suggestion, include:

- **Clear title and description** of the enhancement
- **Use cases** that explain why this would be useful
- **Examples** of how it would work

### Pull Requests

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Make your changes
4. Test your changes thoroughly
5. Commit your changes (`git commit -m 'Add some amazing feature'`)
6. Push to your branch (`git push origin feature/amazing-feature`)
7. Open a Pull Request

## Development Setup

### Prerequisites

- [Kubernetes cluster](https://kubernetes.io/docs/setup/) (minikube, kind, or cloud provider)
- [Helm 3.0+](https://helm.sh/docs/intro/install/)
- [kubectl](https://kubernetes.io/docs/tasks/tools/)
- [helm-unittest](https://github.com/helm-unittest/helm-unittest) (optional, for testing)

### Local Development

1. Clone your fork:
```bash
git clone https://github.com/YOUR-USERNAME/ankra-agent-chart.git
cd ankra-agent-chart
```

2. Install the chart locally:
```bash
helm install ankra-agent ./chart \
  --namespace ankra-system \
  --create-namespace \
  --set config.token="test-token" \
  --dry-run --debug
```

3. Lint the chart:
```bash
helm lint ./chart
```

4. Test template rendering:
```bash
helm template ankra-agent ./chart --debug
```

## Submitting Changes

### Pull Request Guidelines

- **Keep PRs focused**: One feature or fix per PR
- **Update documentation**: Update README.md and values.yaml comments if needed
- **Follow Helm best practices**: See [Helm Chart Best Practices](https://helm.sh/docs/chart_best_practices/)
- **Test your changes**: Ensure the chart installs successfully
- **Update CHANGELOG**: Add an entry to CHANGELOG.md describing your changes

### Commit Message Guidelines

Follow the [Conventional Commits](https://www.conventionalcommits.org/) specification:

- `feat:` new feature
- `fix:` bug fix
- `docs:` documentation changes
- `style:` formatting, missing semicolons, etc.
- `refactor:` code refactoring
- `test:` adding tests
- `chore:` maintenance tasks

Examples:
```
feat: add support for custom annotations
fix: correct service account name reference
docs: update installation instructions
```

## Style Guidelines

### Helm Chart Conventions

- Use **lowercase with underscores** for values (`snake_case`)
- Provide **default values** for all parameters
- Add **comments** to values.yaml explaining each option
- Use **consistent indentation** (2 spaces)
- Follow [Helm template best practices](https://helm.sh/docs/chart_best_practices/templates/)

### YAML Style

- Use 2-space indentation
- Use `""` for strings that may contain special characters
- Use `{}` and `[]` for empty objects and arrays
- Keep lines under 120 characters when possible

### Template Guidelines

- Use `{{- }}` to control whitespace
- Include common labels using `_helpers.tpl`
- Use `quote` function for string values from user input
- Validate required values with `required` function

Example:
```yaml
{{- if .Values.config.token }}
- name: TOKEN
  value: {{ .Values.config.token | quote }}
{{- end }}
```

## Testing

### Manual Testing

Test your changes with different configurations:

```bash
# Test with minimal values
helm install test-minimal ./chart --set config.token="test"

# Test with custom values
helm install test-custom ./chart -f examples/values-prod.yaml

# Test upgrade
helm upgrade test-minimal ./chart --set config.token="test"

# Verify deployment
kubectl get all -n ankra-system
kubectl logs -n ankra-system -l app.kubernetes.io/name=ankra-agent
```

### Automated Testing

If you have helm-unittest installed:

```bash
helm unittest ./chart
```

### Pre-commit Checklist

Before submitting your PR:

- [ ] Chart lints without errors (`helm lint ./chart`)
- [ ] Chart installs successfully
- [ ] Documentation is updated
- [ ] CHANGELOG.md is updated
- [ ] Commit messages follow conventions
- [ ] All values have comments in values.yaml
- [ ] Template rendering works (`helm template` succeeds)

## Questions?

Feel free to:
- Open an issue for discussion
- Email us at hello@ankra.io
- Check our [documentation](https://docs.ankra.io)

Thank you for contributing! 🎉
