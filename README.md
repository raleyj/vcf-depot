# VCF Offline Depot 1.2.1

**Appliance 1.2.1 - build 2026-11-09.2**

Download the deployable OVA, signed update, checksums, and guides from the [latest qualified release](https://github.com/raleyj/vcf-depot/releases/tag/v1.2.1-build.2026-11-09.2). GitHub source archives do not contain the appliance.

The appliance downloads and hosts VCF 9.0.x content and supports automated VCF Installer configuration and retrieval for VCF 9.1 and later. It also provides exact BOM-mapped publishing of verified binary and artifact bundles to a VCF Operations Software Depot.

## What is new

- Software Depot Publishing under Depot Management for one exact BOM-mapped bundle at a time.
- Certificate discovery and SHA-256 pinning before credentials are stored or transmitted.
- Recursive local size and SHA-256 verification before publishing.
- Remote inventory reconciliation before and after upload, with duplicate publication prevention.
- Correct handling of complete OVA-only artifact rows that do not report an OCI image count.
- Restricted working-directory preparation through the privileged service boundary.
- Durable background publishing jobs with sanitized logs and safe retry.

The final build was validated with a real 3.94 GiB Avi artifact upload. A follow-up reconciliation verified the exact remote bundle as complete and skipped duplicate transfer. The exact OVA also passed fresh deployment, first boot, administrator login, 351 runtime hash checks, 1 TiB data-disk initialization, and reboot persistence.

## Guides

- [Deployment Guide](VCF-Offline-Depot-Deployment-Guide.docx)
- [Quick Start Guide](VCF-Offline-Depot-Quick-Start-Guide.docx)
- [Administrator Guide](VCF-Offline-Depot-Administrator-Guide.docx)
- [Troubleshooting Guide](VCF-Offline-Depot-Troubleshooting-Guide.docx)
- [Release notes](Release-Notes-1.2.1.md)
- [Download verification instructions](VERIFY-DOWNLOADS.md)

## Verify downloads

```sh
openssl dgst -sha256 -verify release-signing-key.pub.pem -signature SHA256SUMS.sig SHA256SUMS
sha256sum --check --ignore-missing SHA256SUMS
```

The OVA is for a fresh deployment. Use the signed update package for an existing appliance. This is a community appliance, not vendor support certification.
