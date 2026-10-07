# VCF Offline Depot 1.2

**Appliance 1.2.0 - build 2026-10-07.1**

Download the deployable OVA, signed checksums and updated guides from the [latest qualified release](https://github.com/raleyj/vcf-depot/releases/tag/v1.2.0-build.2026-10-07.1). GitHub source archives do not contain the appliance.

The appliance provides authenticated HTTPS depot storage, Broadcom VCF Download Tool workflows, matching bootable ESX media, certificate and account management, and VCF Installer connection and release-retrieval controls.

## What is new

- VCF 9.0.x binary download and hosting, with exact release and BOM matching. Automatic VCF Installer retrieval remains limited to VCF 9.1 and later.
- All remaining binaries requests supported install, upgrade, patch and artifact content for the selected release, including Operations for Logs. It reuses existing content.
- Automatic Avi bundle selection where Avi is part of the selected BOM, with size and SHA-256 verification.
- Explicit VCF 9.0.2 exclusions when exact catalog entries are unavailable: OSA Witness, ESA Witness, VMware Tools, VMRC, Live Recovery and DSM. Obtain these separately from Broadcom if required. A later exact catalog publication takes precedence over the exclusion.
- Supervisor, VKS and Velero coverage for VCF 9.0.2 through SHA-256 verification of their containing vCenter package. This is not a separate service deployment or individual container-image audit.
- Clear completion scope: supported automatic downloads only. Unexpected mapping gaps, failed transfers and checksum errors still fail the job. The observed Private AI HTTP 403 is not silently excluded.

## Guides

- [Deployment Guide](VCF-Offline-Depot-Deployment-Guide.docx)
- [Quick Start Guide](VCF-Offline-Depot-Quick-Start-Guide.docx)
- [Administrator Guide](VCF-Offline-Depot-Administrator-Guide.docx)
- [Troubleshooting Guide](VCF-Offline-Depot-Troubleshooting-Guide.docx)
- [Release notes](Release-Notes-1.2.0.md)
- [Download verification instructions](VERIFY-DOWNLOADS.md)

Guides are revision 2.2 and are included in the OVA. Download SHA256SUMS, SHA256SUMS.sig and release-signing-key.pub.pem from the release page. Establish trust in the signing key independently, then verify:

```sh
openssl dgst -sha256 -verify release-signing-key.pub.pem -signature SHA256SUMS.sig SHA256SUMS
sha256sum --check --ignore-missing SHA256SUMS
```

Public-key SPKI SHA256: `312d7a9bf4df037fadbe50b8cd8e876e66db66ce40ca079e5700b068db222329`.

## Requirements and qualification

Deploy with 4 vCPUs, 8 GiB RAM, a 32 GiB OS disk and a new thin-provisioned 1 TiB depot disk. Supply distinct deployment passwords, the supported Linux VCF Download Tool, Broadcom entitlement and appropriate certificates. The OVA contains no VCF binaries or deployment credentials. Importing it creates a fresh appliance and does not migrate an existing depot.

The exact OVA passed fresh DHCP deployment, administrator login, automatic content startup, restricted service identity, authenticated HTTPS range retrieval, 338 runtime file hash checks and reboot persistence. Regression results: 185 JavaScript tests passed on Windows (one platform-specific skip), and 31 JavaScript plus 13 Python tests passed on the installed Linux runtime. The isolated test network reported unsynchronized system time; verify NTP after deployment.

Earlier live testing covered Avi and Operations for Logs on VCF 9.1.1 and VCF 9.0.2 download/catalog diagnosis. A full entitled download and VCF Installer retrieval were not repeated on this fresh OVA. See the signed release-record.json for the exact qualification scope. Later VCF releases require compatibility verification. This is a community appliance, not vendor support certification.
