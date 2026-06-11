# CoreDNS Auto-Registration

## Overview

This project documents an automated DNS registration workflow built around CoreDNS and Linux virtual machine templates.

The goal is to eliminate manual DNS administration when new virtual machines are deployed by automatically creating and updating DNS records during first boot.

## Problem Statement

In many lab and infrastructure environments, administrators manually create DNS records whenever a new server is deployed.

While manageable at small scale, this approach becomes increasingly difficult as the number of systems grows.

Common challenges include:

* Missing DNS records
* Incorrect IP assignments
* Stale records after rebuilds
* Missing reverse lookup entries
* Additional administrative overhead

The objective was to automate DNS registration so that cloned virtual machines become reachable by hostname immediately after deployment.

## Architecture

```text
Virtual Machine
      |
      | First Boot
      v
Cloud-Init
      |
      v
DNS Registration Script
      |
      | SSH
      v
DNS Registration Service
      |
      v
CoreDNS
      |
      +--> A Record
      |
      +--> PTR Record
```

A cloud-init workflow invokes a DNS registration script during first boot.

The script discovers the machine hostname and active IPv4 address before securely sending the information to a DNS registration service.

The registration service updates both forward and reverse DNS zones.

## Registration Workflow

The registration process performs the following actions:

1. Wait for network connectivity.
2. Detect the active network interface.
3. Determine the primary IPv4 address.
4. Collect the system hostname.
5. Establish a secure SSH connection.
6. Submit registration data.
7. Update forward DNS records.
8. Update reverse DNS records.
9. Reload DNS configuration.

The process is designed to be non-blocking so virtual machine startup is not dependent on DNS availability.

## Design Decisions

### Dynamic Interface Detection

Linux distributions use different network interface naming conventions.

Examples include:

* ens18
* eth0
* enp1s0

The registration process determines the active interface dynamically instead of relying on hard-coded values.

### Automated Reverse DNS

Many environments maintain forward DNS records while neglecting reverse lookup zones.

The automation updates both record types to ensure forward and reverse lookups remain consistent.

### SSH-Based Registration

The solution uses SSH to communicate with the registration service.

Benefits include:

* Encryption in transit
* Strong authentication
* Minimal infrastructure requirements
* Simplicity of deployment

### Non-Fatal Execution

DNS registration is treated as a convenience service rather than a critical dependency.

If registration fails, the virtual machine continues booting normally.

This improves resilience during maintenance windows and infrastructure testing.

## Benefits

The solution provides several advantages:

* Consistent DNS registration
* Reduced manual administration
* Automatic reverse lookup management
* Improved repeatability
* Faster virtual machine provisioning
* Reduced configuration drift

## Lessons Learned

### DNS Should Be Automated Early

The value of automation increases significantly as the number of systems grows.

Automating DNS registration early avoids operational overhead later.

### Forward and Reverse Records Matter

Automating only forward records often creates troubleshooting challenges.

Maintaining both record types improves operational consistency.

### Reliability Is More Important Than Perfection

Provisioning workflows should continue operating even when supporting services experience temporary issues.

Treating DNS registration as non-critical improved overall system reliability.

## Future Enhancements

Potential future improvements include:

* API-based registration workflows
* Certificate-based authentication
* Multi-zone support
* Automatic stale-record cleanup
* Integration with infrastructure-as-code workflows

