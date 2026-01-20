# Ansible Role: Prometheus Exporter

Generic Ansible role for installing Prometheus exporters from GitHub releases.

## Features

- Downloads and installs any Prometheus exporter from GitHub releases
- Creates system user/group for security
- Generates systemd service with security hardening
- Supports version checking to avoid unnecessary reinstalls
- Configurable for different GitHub organizations and URL patterns

## Requirements

- Ansible >= 2.12
- Target system: Debian/Ubuntu with systemd

## Role Variables

### Required Variables

```yaml
prometheus_exporter_name: "elasticsearch_exporter"  # Name of the exporter
prometheus_exporter_version: "1.10.0"               # Version to install
prometheus_exporter_listen_port: "9114"             # Port to listen on
```

### Optional Variables

```yaml
# GitHub source
prometheus_exporter_github_org: "prometheus-community"  # GitHub organization
prometheus_exporter_github_repo: "{{ prometheus_exporter_name }}"  # Repository name

# Listen configuration
prometheus_exporter_listen_address: "127.0.0.1"  # Address to bind to

# System user/group
prometheus_exporter_system_user: "elasticsearch_exporter"
prometheus_exporter_system_group: "elasticsearch_exporter"

# Service configuration
prometheus_exporter_service_enabled: true
prometheus_exporter_service_state: stopped  # stopped or started

# Extra command line arguments
prometheus_exporter_extra_args:
  - "--es.uri=http://localhost:9200"

# Environment variables
prometheus_exporter_env_vars:
  ES_USERNAME: "elastic"
  ES_PASSWORD: "changeme"

# Custom download URL (for non-standard release formats)
prometheus_exporter_download_url: "https://custom-url/exporter.tar.gz"

# Custom web listen flag (some exporters use different flags)
prometheus_exporter_web_listen_flag: "--web.listen-address"
```

## Example Usage

### Installing elasticsearch_exporter

```yaml
- hosts: elasticsearch
  roles:
    - role: prometheus-exporter
      vars:
        prometheus_exporter_name: "elasticsearch_exporter"
        prometheus_exporter_version: "1.10.0"
        prometheus_exporter_listen_port: "9114"
        prometheus_exporter_extra_args:
          - "--es.uri=http://localhost:9200"
```

### Installing postfix_exporter (kumina)

```yaml
- hosts: mailservers
  roles:
    - role: prometheus-exporter
      vars:
        prometheus_exporter_name: "postfix_exporter"
        prometheus_exporter_version: "0.4.1"
        prometheus_exporter_listen_port: "9154"
        prometheus_exporter_github_org: "kumina"
        prometheus_exporter_extra_args:
          - "--postfix.showq_path=/var/spool/postfix/public/showq"
```

### Multiple exporters in one playbook

```yaml
- hosts: all
  tasks:
    - name: Install elasticsearch_exporter
      ansible.builtin.include_role:
        name: prometheus-exporter
      vars:
        prometheus_exporter_name: "elasticsearch_exporter"
        prometheus_exporter_version: "1.10.0"
        prometheus_exporter_listen_port: "9114"

    - name: Install postfix_exporter
      ansible.builtin.include_role:
        name: prometheus-exporter
      vars:
        prometheus_exporter_name: "postfix_exporter"
        prometheus_exporter_version: "0.4.1"
        prometheus_exporter_listen_port: "9154"
        prometheus_exporter_github_org: "kumina"
```

## Supported Exporters

This role can install any exporter that follows the standard Prometheus release format:

| Exporter | GitHub Org | Port | Notes |
|----------|------------|------|-------|
| elasticsearch_exporter | prometheus-community | 9114 | |
| postfix_exporter | kumina | 9154 | |
| rabbitmq_exporter | kbudde | 9419 | |
| redis_exporter | oliver006 | 9121 | |
| nginx_exporter | nginxinc | 9113 | |
| blackbox_exporter | prometheus | 9115 | |
| snmp_exporter | prometheus | 9116 | |

## License

MIT

## Author

Rheinwerk
