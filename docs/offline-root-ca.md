# Offline Root Certificate Authority

## Overview

This project documents the design and implementation of an offline Root Certificate Authority (CA) used within a homelab environment.

The objective was to establish a trusted certificate hierarchy for internal services while minimizing exposure of the root signing key.

The Root CA remains powered off except when signing subordinate certificates, reducing the attack surface and protecting the trust anchor of the environment.

## Goals

The project was designed to:

* Establish an internal trust hierarchy
* Eliminate browser certificate warnings for internal services
* Improve TLS security across the environment
* Separate root trust from operational systems
* Provide hands-on experience with PKI administration

## Architecture

```text
                    Root CA
                  (Offline)
                       |
                       |
                       v
              Server Certificates
                       |
       --------------------------------
       |                              |
       v                              v
 Internal Services             Logging Platform
```

The Root CA is used only for certificate signing operations.

Operational services receive certificates signed by the Root CA and present those certificates during TLS connections.

## Design Decisions

### Offline Root CA

The Root CA is intentionally isolated from day-to-day operations.

Benefits include:

* Reduced attack surface
* Improved key protection
* Separation of duties
* Better alignment with PKI best practices

The CA system remains powered off except when certificate signing activities are required.

### Long-Lived Root Certificate

The Root CA certificate uses a longer validity period than operational certificates.

This reduces the need for frequent trust-anchor replacement while still allowing shorter lifetimes for service certificates.

### Separate Service Certificates

Each service receives its own certificate rather than sharing a common certificate across multiple systems.

Benefits include:

* Improved security isolation
* Easier certificate replacement
* Simplified troubleshooting
* Reduced operational risk

## Certificate Workflow

The certificate issuance process follows these steps:

1. Generate a private key.
2. Create a certificate signing request (CSR).
3. Transfer the CSR to the offline CA.
4. Sign the CSR using the Root CA.
5. Install the signed certificate.
6. Distribute the Root CA certificate to trusted systems.
7. Validate certificate trust.

## Trust Distribution

Client systems must trust the Root CA certificate before internal TLS services can be validated successfully.

Trust was distributed to client systems used for administration and testing.

This enabled secure access to internal services without browser or application trust warnings.

## Operational Considerations

### Backup Strategy

The Root CA private key represents the trust anchor of the environment.

Backup procedures should:

* Protect confidentiality
* Prevent unauthorized access
* Support disaster recovery

### Key Protection

The Root CA private key should remain encrypted and accessible only to authorized administrators.

Compromise of the Root CA private key would compromise trust across the entire environment.

### Certificate Renewal

Operational certificates should be renewed before expiration to avoid service disruption.

Maintaining shorter certificate lifetimes for operational systems limits risk while preserving a stable root trust anchor.

## Lessons Learned

### PKI Is Simpler Than It Appears

While certificate management is often viewed as complex, a small internal PKI can be implemented using straightforward processes and careful operational practices.

### Protect the Root Key

The security of the environment ultimately depends on the security of the Root CA private key.

Protecting the root key is more important than protecting any individual service certificate.

### Trust Distribution Matters

Certificate issuance is only part of the solution.

Ensuring client systems trust the Root CA is equally important for a successful deployment.

## Future Enhancements

Potential future improvements include:

* Intermediate certificate authorities
* Automated certificate renewal
* Certificate revocation workflows
* Internal certificate inventory management
* Infrastructure-as-code integration

