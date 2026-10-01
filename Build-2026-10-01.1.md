# VCF Offline Depot 1.2

Appliance version **1.2.0**, distribution build **2026-10-01.1**, for **VCF 9.1 and newer**. Workflow testing is demonstrated on VCF 9.1.1; verify compatibility before adopting later releases.

## Changes

- **All remaining binaries** on Authorize & Download requests each component supported by the installed VCF Download Tool for the selected release, including Operations for Logs. It removes the initial-deployment-only filter and requests available component versions, while reusing existing files.
- **Automatic Avi downloads** use the selected release's bill of materials and product catalog to resolve exact bundle IDs. This handles the Download Tool's missing NSX_ALB component option. Every selected Avi file is verified by size and SHA-256; missing or ambiguous metadata fails clearly. VCF 9.1.1.0 maps to Avi 32.1.3.25665611.
- **Correct repeated-download results** count ALREADY_DOWNLOADED files as well as new successes. A job can complete without transferring new binaries.
- **Safer failed-job cleanup** preserves files verified by product-catalog checksums, including Avi OVAs without a neighboring depot-manifest YAML. Cross-filesystem quarantine copies before removing the source, and cleanup errors produce a terminal failure rather than crashing the management service and leaving a stale running status.
- **Updated guides** at revision 2.1 cover the new option, automatic Avi mapping, repeated downloads and validation scope.

Existing ESX bootable ISO verification, VCF Installer retrieval controls, certificate and depot account management, restricted service identity, and network recovery remain available.

## Download scope

All remaining binaries applies to the selected VCF release. It does not mean every historical VCF release or every Broadcom product. Availability depends on the installed Download Tool, release metadata, and entitlement. This option can use substantially more storage than Install. Use the normal activation code; no manual Avi bundle ID is needed.

## Validation

The live VCF 9.1.1 workflow transferred and verified the Avi 32.1.3 controller OVA. Existing Operations for Logs files passed catalog size and SHA-256 checks. The repeated-download false failure was repaired and the live API reported Completed with healthy services and working authenticated HTTPS retrieval.

Automated regression tests cover BOM selection, missing and corrupt files, repeated-download counts, checksum retention, cross-filesystem cleanup and Installer download tracking. See the signed release-record.json for fresh-deployment and reboot checks of the exact OVA. A new entitled bulk download and VCF Installer retrieval are not repeated during fresh-OVA smoke qualification.

## Deployment

Deploy with 4 vCPUs, 8 GiB memory, a 32 GiB OS disk, and a new 1 TiB thin-provisioned depot disk. DHCP and Etc/UTC are defaults. Supply distinct deployment credentials, the official Linux AMD64 VCF Download Tool, Broadcom entitlement, and certificates appropriate for your environment.

This OVA creates a fresh appliance. It does not migrate existing configuration, credentials or binaries. No Broadcom download tool, activation code, downloaded VCF binaries, or deployment credentials are included. This release does not replace your running appliance automatically.

Verify the signed SHA256SUMS using an independently trusted copy of the release public key before deployment. This community appliance's lab qualification is not vendor support certification.
