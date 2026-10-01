# Verify VCF Offline Depot downloads

Current appliance version **1.2.0**, final build **2026-10-01.1**. Obtain the OVA, SHA256SUMS, SHA256SUMS.sig, and release-signing-key.pub.pem from the [qualified release](https://github.com/raleyj/vcf-depot/releases/tag/v1.2.0-build.2026-10-01.1).

Establish trust in the release public key independently before checking signatures. Expected public-key SPKI SHA256:

`312d7a9bf4df037fadbe50b8cd8e876e66db66ce40ca079e5700b068db222329`

```sh
openssl pkey -pubin -in release-signing-key.pub.pem -outform DER -out release-signing-key.der
openssl dgst -sha256 release-signing-key.der
openssl dgst -sha256 -verify release-signing-key.pub.pem -signature SHA256SUMS.sig SHA256SUMS
sha256sum --check --ignore-missing SHA256SUMS
```

Require a successful signature check and an OK checksum result for every file you intend to use. On Windows, use PowerShell `Get-FileHash -Algorithm SHA256` and compare the result with the corresponding entry in the verified SHA256SUMS file.

The OVA also has a standalone `.ova.sha256` checksum and `.ova.sha256.sig` signature. Either signed checksum route verifies the OVA. SHA256SUMS additionally covers the release record and guides. A checksum obtained alongside an untrusted download without validating its signature does not establish authenticity.

Read release-record.json for the exact artifact and qualification scope. GitHub's automatic source archives do not contain the appliance. Importing the OVA creates a fresh deployment and does not migrate configuration or binaries.
