# Linux Template Automation

## Overview

This project documents an automated Linux template workflow used in a Proxmox-based homelab environment.

The objective is to create reusable virtual machine templates that automatically perform initial configuration tasks when cloned, including:

* DNS configuration
* Host registration
* Logging agent installation
* Service bootstrap
* Infrastructure onboarding

The workflow was designed to reduce manual provisioning effort and provide a consistent deployment experience across multiple Linux distributions.

## Supported Platforms

The automation framework has been validated against:

* Ubuntu 22.04
* Ubuntu 24.04
* Rocky Linux 9
* RHEL 9
* Debian 13
* SUSE Leap 16

## Architecture

A newly cloned virtual machine performs the following sequence during first boot:

```text
Cloud-Init
    |
    +--> DNS Configuration
    |
    +--> DNS Registration
    |
    +--> Logging Agent Bootstrap
    |
    +--> Ready for Use
```

Cloud-init serves as the orchestration layer and invokes the required automation scripts during initial startup.

## DNS Registration Workflow

A registration script automatically:

1. Waits for network connectivity.
2. Detects the active network interface.
3. Identifies the primary IPv4 address.
4. Establishes a secure SSH connection to the DNS server.
5. Registers hostname and address information.
6. Continues boot even if registration fails.

The process is intentionally non-fatal so infrastructure availability is not dependent on DNS registration success.

### Design Considerations

#### Dynamic Interface Detection

Linux distributions often use different interface naming conventions.

Examples include:

* ens18
* eth0
* enp1s0

The automation dynamically determines the active interface rather than relying on hard-coded names.

#### Dynamic Host Key Refresh

The registration process refreshes the SSH host key of the DNS service at runtime.

This eliminates the need to pre-seed SSH known_hosts entries within golden images and reduces template maintenance requirements.

## Logging Agent Bootstrap

A separate bootstrap process installs and enables the logging agent automatically.

Key characteristics:

* Distribution-aware package selection
* Automatic package discovery
* Version-agnostic deployment
* Idempotent execution
* Support for RPM-based and DEB-based distributions

The bootstrap process detects the operating system and retrieves the most appropriate package bundle from an internal repository.

## Cloud-Init Integration

Cloud-init coordinates the first-boot workflow.

The execution order is:

1. DNS configuration
2. DNS registration
3. Logging agent bootstrap

This sequencing ensures networking is fully functional before dependent services execute.

## Lessons Learned

### Avoid Hard-Coded Interface Names

Different Linux distributions and virtual hardware configurations can expose different interface names.

Dynamic detection proved significantly more reliable.

### Treat Registration as Non-Critical

Infrastructure provisioning should succeed even when supporting services are temporarily unavailable.

Non-fatal registration improved reliability during maintenance windows and testing.

### Prefer Generic Bootstrap Logic

Version-specific installation scripts become difficult to maintain over time.

Automatic package discovery reduced maintenance overhead and simplified future upgrades.

## Future Enhancements

Planned improvements include:

* Infrastructure diagrams
* Template validation checklists
* Deployment screenshots
* Additional operating system coverage
* Terraform integration
* Monitoring and observability examples

