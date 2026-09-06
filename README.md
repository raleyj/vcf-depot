# VCF Offline Depot

A dedicated Ubuntu appliance that simplifies VMware Cloud Foundation offline depot setup, content downloads, HTTPS configuration, depot accounts, and VCF Installer connection configuration.

## Download

**Appliance 1.0.0 · build 2026-09-05.2 · release**

Download the **OVA and revision 1.8 guides** from the [release page](https://github.com/raleyj/vcf-depot/releases/tag/v1.0.0-build.2026-09-05.2). The OVA is approximately 1.16 GiB. GitHub's automatically generated source archives do not contain the appliance.

The maintainer completed manual OVA testing before the final minor changes and approved this build for release. Build VM checks and package verification passed. This approval supersedes the earlier pending-test status in the revision 1.8 guides and packaged build supplement. Read the [build notes](Build-2026-09-05.2.md) and [download verification instructions](VERIFY-DOWNLOADS.md).

## Guides

| Guide | Read online | Word download |
|---|---|---|
| Deployment | [Markdown](Deployment-Guide.md) | [Word](VCF-Offline-Depot-Deployment-Guide.docx) |
| Quick Start | [Markdown](Quick-Start-Guide.md) | [Word](VCF-Offline-Depot-Quick-Start-Guide.docx) |
| Administration | [Markdown](Administrator-Guide.md) | [Word](VCF-Offline-Depot-Administrator-Guide.docx) |
| Troubleshooting | [Markdown](Troubleshooting-Guide.md) | [Word](VCF-Offline-Depot-Troubleshooting-Guide.docx) |
| Roadmap | Historical baseline, revision 3.6 | [Word](VCF-Offline-Depot-Roadmap.docx) |

The four operational guides are revision **1.8** and supersede earlier companion guides, including those embedded in the OVA. The roadmap remains revision 3.6; its historical completion status does not qualify this rebuilt OVA.

## Requirements

- ESXi 9.0 or later with virtual hardware version 22 support; vCenter or ESXi OVA deployment.
- 4 vCPUs, 8 GiB RAM, 32 GiB OS disk, and a new 1 TiB thin-provisioned data disk. Plan physical storage for downloaded content.
- Working DNS, NTP, and network access required by the selected download and installer workflow.
- A supported Linux AMD64 VCF Download Tool and appropriate Broadcom entitlement/authorization, obtained separately.
- A certificate authority to sign the appliance CSR, plus VCF Installer endpoint and SSH credentials for connection setup.

DHCP is the default. Static configuration requires the complete network settings. DNS and NTP properties accept at most two comma-separated entries each. All three deployment passwords must contain 15–256 characters; the recovery password must differ from the other two.

## Setup workflow

1. Deploy the Offline Depot Appliance.
2. Upload the VCF Download Tool.
3. Authorize the tool with Broadcom.
4. Download the required VCF binaries.
5. Generate a CSR, obtain a signed HTTPS certificate from your CA, and activate it.
6. Configure a depot user account.
7. Provide the VCF Installer login, `vcf` SSH login, and root elevation credentials in the appliance UI.
8. Test and configure the VCF Installer connection, verifying the endpoint identities shown by the appliance.

Follow the Quick Start Guide for the detailed sequence. Never put deployment credentials in GitHub issues.

## Included in this build

- Compact management forms and consistent status badges, including the inventory update date and time.
- Configurable system timezone, independent of the NTP server list, with persistence across reboots.
- Automatic depot content-container startup with the management service.
- Current administrator navigation and VCF 9.1.1 release selection.

## Scope and known limitations

VCF Installer is the supported integration scope. SDDC Manager and Fleet Manager qualification remains deferred. After a saved connection reports Connected, setup-only pages are hidden; this build has no separate post-setup maintenance entry for those pages. Complete tool, account, and certificate setup before completing the endpoint connection.

The OVA deploys a fresh appliance. It does not migrate accounts, configuration, certificates, downloaded binaries, or history from an existing appliance. Preserve the existing appliance and data during replacement testing, and avoid duplicate IP addresses.

This repository distributes the appliance and its documentation. It does not include the separately obtained VCF Download Tool or VCF binary downloads. For problems, [open an issue](https://github.com/raleyj/vcf-depot/issues) with the build number, sanitized reproduction steps, and relevant errors.
