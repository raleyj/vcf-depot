# VCF Offline Depot for VCF 9.1 and newer

**Appliance 1.1.0 · final build 2026-09-13.1**

Download the deployable OVA and updated guides from the [latest qualified release](https://github.com/raleyj/vcf-depot/releases/tag/v1.1.0-build.2026-09-13.1). GitHub source archives do not contain the appliance. This build supersedes the September 7 OVA while retaining version 1.1.0.

The appliance provides authenticated HTTPS depot storage, Broadcom VCF Download Tool workflows including matching bootable ESX media, certificate and account management, and VCF Installer connection and release-retrieval controls. It is intended for VCF 9.1 and newer, with workflow testing demonstrated on VCF 9.1.1. Verify compatibility before adopting later VCF releases.

## Guides

- [Deployment Guide](VCF-Offline-Depot-Deployment-Guide.docx)
- [Quick Start Guide](VCF-Offline-Depot-Quick-Start-Guide.docx)
- [Administrator Guide](VCF-Offline-Depot-Administrator-Guide.docx)
- [Troubleshooting Guide](VCF-Offline-Depot-Troubleshooting-Guide.docx)
- [Release notes](Release-Notes-1.1.0.md)

Guides are revision 2.0 and are included in the OVA. Download the OVA, SHA256SUMS, SHA256SUMS.sig, and release-signing-key.pub.pem from the release page. Establish trust in the signing key independently before verifying the signature:

```sh
openssl dgst -sha256 -verify release-signing-key.pub.pem -signature SHA256SUMS.sig SHA256SUMS
sha256sum --check --ignore-missing SHA256SUMS
```

Public-key SPKI SHA256: `312d7a9bf4df037fadbe50b8cd8e876e66db66ce40ca079e5700b068db222329`.

## Requirements and qualification

Deploy with 4 vCPUs, 8 GiB RAM, a 32 GiB OS disk, and a new thin-provisioned 1 TiB depot disk. Supply your own distinct deployment passwords, supported Linux VCF Download Tool, Broadcom entitlement, and environment certificates. The OVA contains no downloaded VCF binaries or credentials. Importing a new OVA does not migrate an existing depot.

The exact OVA passed fresh DHCP deployment, administrator login, automatic content startup, restricted service identity, authenticated HTTPS range retrieval, payload hash validation, and reboot persistence. The live final runtime also passed Broadcom metadata refresh with existing binaries retained. See the signed release-record.json for the exact validation scope.
