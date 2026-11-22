# Ansible Role: node_exporter

![GitHub](https://img.shields.io/github/license/jomrr/ansible-role-node_exporter)
![GitHub last commit](https://img.shields.io/github/last-commit/jomrr/ansible-role-node_exporter)
![GitHub issues](https://img.shields.io/github/issues-raw/jomrr/ansible-role-node_exporter)
[![dev](https://img.shields.io/github/actions/workflow/status/jomrr/ansible-role-node_exporter/dev.yml?branch=dev&label=dev)](https://github.com/jomrr/ansible-role-node_exporter/actions/workflows/dev.yml?query=branch%3Adev)
[![main](https://img.shields.io/github/actions/workflow/status/jomrr/ansible-role-node_exporter/main.yml?branch=main&label=main)](https://github.com/jomrr/ansible-role-node_exporter/actions/workflows/main.yml?query=branch%3Amain)

Install and configure Prometheus Node Exporter for Linux host metrics.

## Purpose

Publish Linux host metrics at `http://127.0.0.1:9100/metrics` by default. Set
the listener to a reachable address for a remote Prometheus server and select
collectors by name.

## Requirements

- AlmaLinux package installations use EPEL. The role installs epel-release from
  AlmaLinux Extras.
- Binary installations need HTTPS access to GitHub release assets. Selecting
  latest also requires access to api.github.com from the managed host.

## Dependencies

```yaml
collections:
  - name: community.general
    version: '>=12.0.0'
  - name: ansible.posix
    version: '>=2.0.0'
  - name: containers.podman
    version: '>=1.7.0'
```

## Role Variables

### `node_exporter_install`

Type: `str`. Required: `false`.

Install the distribution package or the upstream binary.

Default:

```yaml
node_exporter_install: package
```

### `node_exporter_version`

Type: `str`. Required: `false`.

Binary release to install. Use latest to follow stable releases or a numeric
major.minor.patch to pin a release.

Default:

```yaml
node_exporter_version: latest
```

### `node_exporter_listen_address`

Type: `str`. Required: `false`.

TCP listener for HTTP metrics in host:port, [IPv6]:port or :port form.

Default:

```yaml
node_exporter_listen_address: 127.0.0.1:9100
```

### `node_exporter_collectors`

Type: `dict`. Required: `false`.

Collector names mapped to boolean switches. Unspecified collectors retain the
executable's defaults.

Default:

```yaml
node_exporter_collectors: {}
```

## Managed Files

- `/etc/node_exporter/node_exporter.env`
- `/usr/local/libexec/node_exporter/node_exporter-<version>`
- `/usr/local/bin/node_exporter`

## Check Mode

Run check mode on a provisioned installation. A first installation or a new
binary release requires a normal run to create its files.

## Service Behavior

Both modes use the same collector and listener settings in
`/etc/node_exporter/node_exporter.env`. The role enables the distribution's
service name at boot and runs it as the unprivileged node_exporter user. Changes
to the executable or configuration restart the service.

## Operational Notes

- Debian and Ubuntu (Universe) use `prometheus-node-exporter`. Fedora and
  AlmaLinux (EPEL) use `node-exporter`. openSUSE Leap and Tumbleweed use
  `golang-github-prometheus-node_exporter`. The package manager handles
  distribution updates.
- With `node_exporter_install: binary`, each run resolves `latest` to a concrete
  release, verifies its archive against that release's SHA-256 manifest, and
  points `/usr/local/bin/node_exporter` to the versioned executable. Earlier
  releases stay available for explicit pins. Switching back to package mode
  removes the role's local command link.
- The systemd unit in `/etc/systemd/system/` is named
  `prometheus-node_exporter.service` on openSUSE and
  `prometheus-node-exporter.service` elsewhere.
- The endpoint serves metrics over HTTP. Limit access to the Prometheus scraper
  when using a network listener.

## Supported Platforms

| OS Family | Distribution | Version | Container Image |
| --------- | ------------ | ------- | --------------- |
| RedHat | AlmaLinux | latest | [jomrr/molecule-almalinux:latest](https://hub.docker.com/r/jomrr/molecule-almalinux) |
| Debian | Debian | latest | [jomrr/molecule-debian:latest](https://hub.docker.com/r/jomrr/molecule-debian) |
| RedHat | Fedora | latest | [jomrr/molecule-fedora:latest](https://hub.docker.com/r/jomrr/molecule-fedora) |
| Suse | OpenSuse Leap | latest | [jomrr/molecule-opensuse-leap:latest](https://hub.docker.com/r/jomrr/molecule-opensuse-leap) |
| Suse | OpenSuse Tumbleweed | latest | [jomrr/molecule-opensuse-tumbleweed:latest](https://hub.docker.com/r/jomrr/molecule-opensuse-tumbleweed) |
| Debian | Ubuntu | latest | [jomrr/molecule-ubuntu:latest](https://hub.docker.com/r/jomrr/molecule-ubuntu) |

## Example Playbook

### Install the distribution package

```yaml
- name: Export host metrics
  hosts: monitored
  gather_facts: true
  roles:
    - role: jomrr.node_exporter
```

### Follow upstream releases and enable the processes collector

```yaml
- name: Export metrics on the monitoring network
  hosts: monitored
  gather_facts: true
  roles:
    - role: jomrr.node_exporter
      node_exporter_install: binary
      node_exporter_listen_address: '192.0.2.10:9100'
      node_exporter_collectors:
        processes: true
```

## References

- [Node Exporter downloads](https://prometheus.io/download/#node_exporter)
- [Latest upstream release](https://github.com/prometheus/node_exporter/releases/latest)
- [Node Exporter collectors](https://github.com/prometheus/node_exporter#collectors)

## Author

[Jonas Mauer](https://github.com/jomrr)

## License

This project is licensed under the MIT License.
See [LICENSE](LICENSE) for the full license text.

Copyright (c) 2026 Jonas Mauer.
