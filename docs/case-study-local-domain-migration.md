# Case Study: Migrating from .local to .internal

## Overview

This case study documents a DNS issue encountered within a homelab environment that used the `.local` domain for internal name resolution.

The issue initially appeared to be an intermittent DNS problem but was ultimately traced to Multicast DNS (mDNS) behaviour on client systems.

The resolution involved migrating the environment from `.local` to `.internal` and updating the supporting DNS infrastructure.

---

## Problem

The homelab environment originally used the `.local` domain for internal systems.

Examples included:

```text
platform.local
dns.local
server01.local
```

Internal DNS records were hosted on CoreDNS and appeared to function correctly from Linux systems.

However, hostname resolution was inconsistent from macOS clients.

---

## Symptoms

Observed symptoms included:

* Hostnames resolving successfully from Linux systems
* Hostnames failing to resolve from macOS systems
* Intermittent lookup behaviour
* Inconsistent results between different client platforms
* DNS troubleshooting becoming increasingly difficult

The issue appeared random because the same hostname could resolve successfully from one system while failing from another.

---

## Investigation

Initial troubleshooting focused on the DNS infrastructure itself.

Areas investigated included:

* CoreDNS configuration
* Forward DNS zones
* Reverse DNS zones
* Network connectivity
* Resolver configuration

CoreDNS was functioning correctly and serving the expected records.

Network connectivity was also confirmed to be healthy.

Attention then shifted toward client-side resolver behaviour.

---

## Findings

Testing revealed that macOS treated `.local` differently from standard DNS domains.

Rather than forwarding all queries to the configured DNS server, some lookups were being processed through Multicast DNS (mDNS).

This behaviour resulted in DNS queries bypassing the intended authoritative DNS server.

The issue was not caused by CoreDNS.

The root cause was the special handling of `.local` by operating systems.

---

## Root Cause

The `.local` domain is reserved for Multicast DNS.

Many operating systems implement special resolver behaviour for `.local` names and may attempt mDNS resolution before consulting traditional DNS infrastructure.

Using `.local` for an internal DNS domain created a conflict between:

* Authoritative DNS
* Multicast DNS

This conflict produced inconsistent behaviour across platforms.

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
platform.internal
dns.internal
server01.internal
```

The migration included:

* Updating DNS zones
* Updating host records
* Updating resolver configurations
* Updating automation workflows
* Validating hostname resolution across platforms

---

## Validation

After migration:

* Linux clients resolved hostnames consistently
* macOS clients resolved hostnames consistently
* DNS queries reached the authoritative DNS server as expected
* Forward and reverse lookups functioned normally
* DNS-related troubleshooting complexity was reduced significantly

The migration eliminated the inconsistent behaviour previously observed.

---

## Lessons Learned

### Domain Selection Matters

A seemingly minor naming decision can have long-term operational consequences.

Selecting an appropriate internal domain should be considered early in infrastructure design.

### Cross-Platform Testing Is Important

Infrastructure that appears healthy on one operating system may behave differently on another.

Testing across multiple platforms exposed the issue.

### Understand Reserved Domains

Special-use domains often have operating-system-specific behaviour.

Understanding these reservations can prevent future troubleshooting effort.

### Root Cause Is Not Always Where Symptoms Appear

The symptoms initially suggested a DNS server problem.

The actual issue was resolver behaviour on client systems.

Identifying the true root cause required validating assumptions at each layer of the system.

