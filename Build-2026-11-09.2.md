# VCF Offline Depot 1.2.1

Build 2026-11-09.2 is a candidate release for VCF 9.0 and later.

## What is new

- Adds Software Depot Publishing under Depot Management for transferring one exact BOM-mapped binary or OCI artifact bundle to a VCF Operations Software Depot.
- Discovers and pins the Software Depot and VCF Operations TLS certificate fingerprints before credentials are stored or sent.
- Validates the exact four-part VCF release, BOM component version, bundle ID, local file size, and SHA-256 checksum before publishing.
- Runs publishing as a durable background job with sanitized logs, restart reconciliation, progress reporting, and safe retry.
- Checks remote inventory before uploading to prevent duplicate publication, then verifies the exact non-partial bundle after upload. When a remote inventory row reports OCI image counts, the downloaded and total counts must match; OVA-only artifact rows are accepted only when the exact bundle is present and not partial.
- Prepares the restricted publishing working directory through the privileged boundary before every job, preventing a root-owned directory from blocking a valid upload.
- Serializes Software Depot publishing against VCF release downloads and appliance updates.
- Retains the 1.2 release support for all remaining Download Tool binaries, exact Avi mapping, VCF 9.0 download and hosting, and VCF 9.1 or later Installer retrieval.

## Upgrade scope

The signed 1.2.1 update package accepts an existing 1.2.0 appliance. The 1.2.1 OVA is for a fresh deployment and does not migrate configuration, credentials, or depot binaries from another appliance.

## Operational notes

- Publishing requires a Software Depot endpoint, its paired VCF Operations endpoint, and an authorized VCF Operations account.
- A changed endpoint certificate blocks stored credential use until an administrator discovers and explicitly trusts the replacement fingerprint.
- Build 2026-11-09.2 includes the permission and remote-verification corrections found during the first live binary-publishing test. Final publication follows successful signed-update and isolated OVA qualification.
