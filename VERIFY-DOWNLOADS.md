# Verify downloads

Appliance **1.1.0**, build **2026-09-07.1**. Download the OVA, its `.ova.sha256` and `.ova.sha256.sig` files, and `release-signing-key.pub.pem` from the [release](https://github.com/raleyj/vcf-depot/releases/tag/v1.1.0).

Establish trust in the signing key independently. Public key SPKI SHA-256:

```text
312d7a9bf4df037fadbe50b8cd8e876e66db66ce40ca079e5700b068db222329
```

```sh
openssl pkey -pubin -in release-signing-key.pub.pem -outform DER -out release-signing-key.der
openssl dgst -sha256 release-signing-key.der
openssl dgst -sha256 -verify release-signing-key.pub.pem -signature VCF-Offline-Depot-1.1.0.ova.sha256.sig VCF-Offline-Depot-1.1.0.ova.sha256
sha256sum -c VCF-Offline-Depot-1.1.0.ova.sha256
```

OVA size: **1,243,740,160 bytes**. Expected SHA-256:

```text
a3720c4ae9b3eb6e89dc733e1aeb3a8115f8cb35e987eed8642817b13757d724
```

On Windows, compare `Get-FileHash -Algorithm SHA256 .\VCF-Offline-Depot-1.1.0.ova` with that value. OpenSSL also verifies detached signatures on Windows.

`SHA256SUMS` and its signature cover all release assets, including the revision 1.9 guides ZIP and the release record:

```sh
openssl dgst -sha256 -verify release-signing-key.pub.pem -signature SHA256SUMS.sig SHA256SUMS
sha256sum -c SHA256SUMS
```

Read `release-record.json` for the completed tests and their scope. Integrity verification confirms the signed files; deployment still requires configuration and acceptance in your environment.
