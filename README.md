# Proxmox Golden Image Automation

Automated Linux VM provisioning and lifecycle management using Proxmox, Cloud-Init, CoreDNS, and NXLog.

## Overview

This project documents a homelab workflow for creating reusable Linux golden-image templates in Proxmox.

The goal is to make cloned virtual machines boot with a consistent baseline configuration, register themselves in DNS, and optionally bootstrap required monitoring/logging components with minimal manual work.

## Documentation

### Infrastructure Automation

- [Linux Template Automation](docs/linux-template-automation.md)

  Automated first-boot provisioning workflow using Cloud-Init, DNS registration, and logging agent bootstrap automation.

- [Template Automation Scripts](docs/template-automation-scripts.md)

  Sanitized overview of the first-boot automation scripts used for DNS registration, DNS configuration, and logging agent bootstrap.

### DNS

- [CoreDNS Auto-Registration](docs/coredns-auto-registration.md)

  Automated DNS registration workflow that maintains forward and reverse DNS records for newly provisioned Linux virtual machines.

- [Homelab DNS Architecture](docs/homelab-dns-architecture.md)

  Design decisions, troubleshooting, and lessons learned from migrating away from `.local` and implementing automated DNS registration.

### Security & PKI

- [Offline Root Certificate Authority](docs/offline-root-ca.md)

  Design and implementation of an offline PKI trust hierarchy used to secure internal services and TLS communications.

### Troubleshooting Case Studies

- [Case Study: Migrating from .local to .internal](docs/case-study-local-domain-migration.md)

  Investigation and resolution of cross-platform DNS issues caused by mDNS handling of the `.local` domain.

## What This Demonstrates

- Linux system administration
- Proxmox VM template management
- Cloud-Init automation
- DNS registration workflows
- SSH key-based automation
- Repeatable infrastructure provisioning
- Technical documentation

## Core Workflow

1. Build a clean Linux VM.
2. Install and configure Cloud-Init.
3. Configure DHCP-based networking.
4. Add DNS auto-registration logic.
5. Add optional software bootstrap logic.
6. Clean machine-specific identity.
7. Convert the VM into a Proxmox template.
8. Clone the template to create new ready-to-use VMs.

## Technologies Used

- Proxmox VE
- Cloud-Init
- CoreDNS
- Linux
- SSH
- Bash
- NXLog
- DHCP
- DNS

## Supported Template Targets

This workflow has been used for Linux distributions such as:

- Ubuntu 22.04 / 24.04
- RHEL 9
- Rocky Linux 9
- Debian 13
- SUSE Leap

## Repository Status

This repository is currently being documented and will be expanded with:

- Architecture overview
- Example scripts
- Template preparation checklist
- Clone validation checklist
- Screenshots
- Lessons learned

## Notes

This repository is based on personal homelab work and does not include customer data, proprietary information, or confidential production details.
