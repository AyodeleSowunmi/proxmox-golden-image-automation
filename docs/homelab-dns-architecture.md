# Homelab DNS Architecture

## Overview

This document describes the DNS architecture used within the homelab environment and the design decisions that led to its current implementation.

The primary goals were:

* Reliable hostname resolution
* Automated DNS registration
* Forward and reverse lookup support
* Cross-platform compatibility
* Minimal administrative overhead

The environment uses CoreDNS as the authoritative DNS service for internal systems.

---

## Initial Approach

The original implementation used a custom `.local` domain for internal name resolution.

Example:

```text
server01.local
dns01.local
platform.local
```

At first this appeared to work correctly across most Linux systems.

However, problems began to appear when macOS systems were introduced.

---

## The Problem

The `.local` domain is reserved for Multicast DNS (mDNS).

Many operating systems automatically treat `.local` domains as mDNS domains rather than forwarding queries to traditional DNS servers.

This created inconsistent name resolution behaviour across devices.

Symptoms included:

* Hostnames resolving on Linux but not macOS
* Intermittent DNS lookups
* DNS queries bypassing the intended DNS server
* Difficult-to-diagnose troubleshooting scenarios

The issue was not with CoreDNS itself but with the special handling of `.local` by operating systems.

---

## Investigation

Troubleshooting focused on:

* DNS query paths
* Resolver configuration
* mDNS behaviour
* Cross-platform testing

Testing demonstrated that macOS frequently attempted mDNS resolution instead of querying the authoritative DNS server.

This behaviour was expected according to the mDNS specification.

---

## Solution

The environment was migrated from:

```text
.local
```

to:

```text
.internal
```

Examples:

```text
server01.internal
platform.internal
dns01.internal
```

This removed conflicts with mDNS and restored predictable DNS behaviour across operating systems.

---

## Current Architecture

```text
Client Systems
       |
       v
    CoreDNS
       |
       +--> Forward Zone
       |
       +--> Reverse Zone
```

CoreDNS provides:

* Forward DNS records
* Reverse DNS records
* Internal name resolution
* Dynamic record updates through automation

---

## Automated Registration

New virtual machines register automatically during first boot.

The workflow performs:

1. Network initialization
2. IP address discovery
3. Hostname collection
4. DNS registration
5. Forward record creation
6. Reverse record creation

This removes the need for manual DNS administration when deploying new systems.

---

## Design Decisions

### Use a Dedicated Internal Domain

Avoiding `.local` prevents conflicts with mDNS and improves cross-platform compatibility.

### Maintain Reverse DNS

Both forward and reverse records are created automatically.

This improves troubleshooting and operational consistency.

### Automate DNS Registration

Manual DNS management becomes increasingly difficult as environments grow.

Automation improves consistency and reduces administrative effort.

### Keep DNS Independent

Systems continue functioning even if DNS registration temporarily fails.

This improves resilience during maintenance and testing.

---

## Lessons Learned

### Domain Selection Matters

Choosing an inappropriate internal domain can create long-term operational challenges.

Researching reserved and special-use domains before deployment is important.

### Cross-Platform Testing Is Essential

Behaviour that appears correct on Linux may differ on macOS or Windows.

Validation across multiple operating systems revealed issues that would otherwise have remained hidden.

### Automate Early

DNS automation becomes more valuable as infrastructure grows.

Implementing automation early reduces future maintenance effort.

---

## Future Enhancements

Potential future improvements include:

* DNS monitoring and alerting
* DNS change auditing
* API-based record management
* High-availability DNS services
* Infrastructure-as-code integration

