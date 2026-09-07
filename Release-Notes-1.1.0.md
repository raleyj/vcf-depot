# VCF Offline Depot 1.1.0

Distribution build 2026-09-07.1. Maintenance release following 1.0.0 build 2026-09-05.2.

## Changes

- Install downloads make a separate catalog-selected request for the bootable ESX installer ISO and verify its size and SHA-256 checksum before success.
- New permanent Depot Management page provides Download Tool, Depot Accounts, and HTTPS Certificate maintenance after initial setup.
- Header labels now match VCF Connections and Appliance Status. Depot Files opens Depot File Management.
- Administrator-menu pages do not highlight unrelated header pages. All three maintenance pages use a consistent Back to Depot Management button.
- Updated operational guides revision 1.9 describe the new navigation and download behavior.

## Deployment

Deploy VCF-Offline-Depot-1.1.0.ova as a fresh appliance. The OVA does not migrate configuration or downloaded content from an existing depot. Preserve the existing appliance until the replacement is configured and validated. No Broadcom binaries, activation codes, or environment credentials are distributed.

The image retains the existing 4 vCPU, 8 GiB RAM, 32 GiB OS disk, blank 1 TiB data disk, DHCP default, and Etc/UTC timezone default. Configure DNS, time, trusted certificates, depot accounts, and endpoint access for your environment.

## Validation scope

See release-record.json and SHA256SUMS for the exact packaged artifact and completed automated checks. VCF Installer is the supported integration target; Fleet Manager and SDDC Manager qualification remains deferred. No live appliance or existing endpoint configuration is changed during this release build.
