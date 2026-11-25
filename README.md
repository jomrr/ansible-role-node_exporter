# Ansible Role: node_exporter

![GitHub](https://img.shields.io/github/license/jomrr/ansible-role-node_exporter)
![GitHub last commit](https://img.shields.io/github/last-commit/jomrr/ansible-role-node_exporter)
![GitHub issues](https://img.shields.io/github/issues-raw/jomrr/ansible-role-node_exporter)
[![dev](https://img.shields.io/github/actions/workflow/status/jomrr/ansible-role-node_exporter/dev.yml?branch=dev&label=dev)](https://github.com/jomrr/ansible-role-node_exporter/actions/workflows/dev.yml?query=branch%3Adev)
[![main](https://img.shields.io/github/actions/workflow/status/jomrr/ansible-role-node_exporter/main.yml?branch=main&label=main)](https://github.com/jomrr/ansible-role-node_exporter/actions/workflows/main.yml?query=branch%3Amain)

Install and configure Prometheus Node Exporter for Linux host metrics.

## Purpose

Publish Linux host metrics at `http://127.0.0.1:9100/metrics` by default. Set
the listen address for direct remote scraping over HTTP or HTTPS. Select
collectors by name.

## Requirements

- AlmaLinux package installations use EPEL. The role installs epel-release from
  AlmaLinux Extras.
- Binary installations download release assets over HTTPS. With `latest`, the
  managed host resolves the version through GitHub's latest-release link.

## Dependencies

```yaml
collections:
  - name: community.general
    version: '>=12.0.0'
  - name: ansible.posix
    version: '>=2.0.0'
  - name: community.crypto
    version: '>=3.0.0'
  - name: containers.podman
    version: '>=1.7.0'
```

## Role Variables

### `node_exporter_backup`

Type: `bool`. Required: `false`.

Back up managed files before replacing their contents.

Default:

```yaml
node_exporter_backup: true
```

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

TCP listener for metrics in host:port, [IPv6]:port or :port form.

Default:

```yaml
node_exporter_listen_address: 127.0.0.1:9100
```

### `node_exporter_web_config`

Type: `dict`. Required: `false`.

Native exporter-toolkit web configuration. An empty mapping serves HTTP without
authentication.

Default:

```yaml
node_exporter_web_config: {}
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
- `/etc/node_exporter/web-config.ansible.yml`
- `/usr/local/libexec/node_exporter/node_exporter-<version>`
- `/usr/local/bin/node_exporter`

## Check Mode

Run check mode on a provisioned installation. A first installation or a new
binary release requires a normal run to create its files.

## Service Behavior

Both modes use the same collector and listener settings in
`/etc/node_exporter/node_exporter.env`. The role enables the distribution's
service name at boot and runs it as the unprivileged node_exporter user. Changes
to the executable or configuration restart the service. Reapplying the same
configuration and release leaves the running process in place.

## Security Notes

- The default listener accepts unauthenticated HTTP scrapes from local
  processes. Binding a network interface makes host metrics available to clients
  that can reach that interface. Listener and authentication settings apply
  independently in both installation modes.
- Set `node_exporter_web_config` to native exporter-toolkit YAML for HTTPS,
  bcrypt Basic Auth or mutual TLS. Use HTTPS with Basic Auth to protect
  credentials in transit. For mutual TLS, set `client_auth_type:
  RequireAndVerifyClientCert` and a trusted client CA. Reference certificate and
  key files by absolute paths readable by the node_exporter service account.
  Store inventory secrets in Ansible Vault.
- Ansible renders the web configuration directly. Node Exporter reads it at
  service startup and on scrape requests. The service group can read the managed
  file. The role suppresses its contents in task output and diffs. Set the
  mapping to `{}` to restore HTTP without authentication and remove the active
  web configuration.
- Only root can read the managed environment file. The system service manager
  reads the file before starting the exporter.
- Node Exporter runs with a read-only system filesystem, an empty capability
  bounding set and restricted namespace creation. Host mounts, including those
  beneath home and temporary directories, stay visible with the service user's
  normal read permissions.
- The service allows IPv4 and IPv6 sockets for scrapes, Unix sockets for the
  system D-Bus and Netlink sockets for kernel network statistics. Set
  `node_exporter_collectors.systemd: true` to export systemd unit metrics.

## Operational Notes

- Run `tasks_from: install` to install the selected exporter and create the
  `node_exporter` system user and group. You can then grant that user a read ACL
  on a TLS private key before applying the full role. On Debian the package
  enables its vendor unit for the next boot.
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
  gather_facts: false
  roles:
    - role: jomrr.node_exporter
```

### Follow upstream releases and enable the processes collector

```yaml
- name: Export process metrics locally
  hosts: monitored
  gather_facts: false
  roles:
    - role: jomrr.node_exporter
      node_exporter_install: binary
      node_exporter_collectors:
        processes: true
```

### Scrape a monitoring interface over HTTPS with Basic Auth

```yaml
- name: Export host metrics to remote Prometheus
  hosts: monitored
  gather_facts: false
  roles:
    - role: jomrr.node_exporter
      node_exporter_listen_address: '192.0.2.10:9100'
      node_exporter_web_config:
        tls_server_config:
          cert_file: /etc/monitoring/tls/server.crt
          key_file: /etc/monitoring/tls/server.key
        basic_auth_users:
          prometheus: "{{ vault_node_exporter_scraper_bcrypt }}"
```

## References

- [Exporter Toolkit web configuration](https://github.com/prometheus/exporter-toolkit/blob/master/docs/web-configuration.md)
- [Node Exporter downloads](https://prometheus.io/download/#node_exporter)
- [Latest upstream release](https://github.com/prometheus/node_exporter/releases/latest)
- [Node Exporter collectors](https://github.com/prometheus/node_exporter#collectors)

## Author

[Jonas Mauer](https://github.com/jomrr)

## License

This project is licensed under the MIT License.
See [LICENSE](LICENSE) for the full license text.

Copyright (c) 2026 Jonas Mauer.
