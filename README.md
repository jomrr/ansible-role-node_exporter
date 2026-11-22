# Ansible Role: node_exporter

![GitHub](https://img.shields.io/github/license/jomrr/ansible-role-node_exporter)
![GitHub last commit](https://img.shields.io/github/last-commit/jomrr/ansible-role-node_exporter)
![GitHub issues](https://img.shields.io/github/issues-raw/jomrr/ansible-role-node_exporter)
[![dev](https://img.shields.io/github/actions/workflow/status/jomrr/ansible-role-node_exporter/dev.yml?branch=dev&label=dev)](https://github.com/jomrr/ansible-role-node_exporter/actions/workflows/dev.yml?query=branch%3Adev)
[![main](https://img.shields.io/github/actions/workflow/status/jomrr/ansible-role-node_exporter/main.yml?branch=main&label=main)](https://github.com/jomrr/ansible-role-node_exporter/actions/workflows/main.yml?query=branch%3Amain)

Install and configure Prometheus Node Exporter for Linux host metrics.

## Purpose

This role is intended to install Prometheus Node Exporter and configure host
metrics. Implementation is pending. The listed platforms and dependencies are
planned until the role has been implemented and tested. CI badges link to
workflows and do not establish validation of this initial README.

## Dependencies

```yaml
collections:
  - name: community.general
    version: '>=12.0.0'
  - name: ansible.posix
    version: '>=2.0.0'
```

## Role Variables

No role variable source was detected by the README generator.

## Supported Platforms

| OS Family | Distribution | Version | Container Image |
| --------- | ------------ | ------- | --------------- |
| RedHat | AlmaLinux | latest | [jomrr/molecule-almalinux:latest](https://hub.docker.com/r/jomrr/molecule-almalinux) |
| Debian | Debian | latest | [jomrr/molecule-debian:latest](https://hub.docker.com/r/jomrr/molecule-debian) |
| RedHat | Fedora | latest | [jomrr/molecule-fedora:latest](https://hub.docker.com/r/jomrr/molecule-fedora) |
| Suse | OpenSuse Leap | latest | [jomrr/molecule-opensuse-leap:latest](https://hub.docker.com/r/jomrr/molecule-opensuse-leap) |
| Suse | OpenSuse Tumbleweed | latest | [jomrr/molecule-opensuse-tumbleweed:latest](https://hub.docker.com/r/jomrr/molecule-opensuse-tumbleweed) |
| Debian | Ubuntu | latest | [jomrr/molecule-ubuntu:latest](https://hub.docker.com/r/jomrr/molecule-ubuntu) |

## Author

[Jonas Mauer](https://github.com/jomrr)

## License

This project is licensed under the MIT License.
See [LICENSE](LICENSE) for the full license text.

Copyright (c) 2026 Jonas Mauer.
