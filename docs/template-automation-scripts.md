# Template Automation Scripts

## Overview

This document describes the automation scripts used by the Linux template workflow.

The scripts are executed during first boot through cloud-init and provide automated infrastructure onboarding for newly cloned virtual machines.

The automation framework performs three primary functions:

1. DNS configuration
2. DNS registration
3. Logging agent installation and bootstrap

The goal is to minimize manual provisioning work while ensuring newly deployed systems are operational immediately after deployment.

---

## dns-auto-register.sh

### Purpose

Automatically registers a virtual machine hostname and IP address in DNS during first boot.

### Key Features

* Waits for network connectivity
* Detects the active network interface dynamically
* Detects the primary IPv4 address dynamically
* Refreshes SSH host key information automatically
* Registers hostname and address information
* Operates non-fatally if DNS registration is unavailable

### Workflow

```text
Virtual Machine
      |
      v
Detect Network
      |
      v
Determine Hostname
      |
      v
Determine IP Address
      |
      v
Connect to Registration Service
      |
      v
Update DNS Records
```

### Design Considerations

#### Dynamic Interface Detection

Different Linux distributions may use different network interface naming conventions.

Examples include:

* ens18
* eth0
* enp1s0

The script identifies the active interface dynamically instead of relying on hard-coded values.

#### Non-Fatal Registration

The registration process is intentionally non-blocking.

Virtual machine provisioning should continue even if DNS services are temporarily unavailable.

#### Dynamic Host Key Refresh

SSH host key information is refreshed automatically to simplify template maintenance and avoid stale trust relationships.

---

## nxlog-ee-bootstrap.sh

### Purpose

Installs and enables a logging agent automatically during first boot.

### Key Features

* Distribution-aware package selection
* Automatic package discovery
* Version-agnostic operation
* Idempotent execution
* Support for RPM and DEB based distributions

### Supported Platforms

* RHEL
* Rocky Linux
* Oracle Linux
* Ubuntu
* Debian
* SUSE Linux Enterprise

### Workflow

```text
Detect Operating System
            |
            v
Locate Matching Package
            |
            v
Download Package
            |
            v
Install Package
            |
            v
Enable Service
            |
            v
Create Installation Marker
```

### Design Considerations

#### Version-Agnostic Deployment

The bootstrap process automatically discovers the latest compatible package version.

This reduces maintenance effort and eliminates the need for frequent script updates.

#### Idempotent Operation

Repeated executions should not reinstall software unnecessarily.

The script validates installation state before performing actions.

#### Multi-Distribution Support

A single automation framework supports multiple Linux distributions while minimizing duplication of logic.

---

## Cloud-Init Integration

Cloud-init serves as the orchestration layer for first-boot automation.

### Execution Order

```text
Cloud-Init
    |
    +--> DNS Configuration
    |
    +--> DNS Registration
    |
    +--> Logging Agent Bootstrap
```

This sequencing ensures networking is fully functional before dependent automation tasks execute.

---

## Security Considerations

Environment-specific values are intentionally omitted from this documentation.

Examples include:

* Internal IP addresses
* Hostnames
* Usernames
* SSH key locations
* Repository URLs
* Enrollment endpoints

The objective of this repository is to document architectural concepts and automation techniques without exposing environment-specific implementation details.

---

## Lessons Learned

### Prefer Dynamic Discovery

Automatically discovering interfaces, addresses, and operating system details improves portability across distributions.

### Keep Provisioning Resilient

Supporting services should not become hard dependencies during provisioning workflows.

### Design for Reusability

Version-agnostic and distribution-aware automation significantly reduces long-term maintenance effort.

### Automate Early

Infrastructure automation becomes increasingly valuable as environments grow in size and complexity.

