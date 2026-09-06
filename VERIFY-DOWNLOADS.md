# Verify downloads

For appliance 1.0.0, distribution build **2026-09-05.2**.

Download the OVA, `VCF-Offline-Depot-1.0.0.ova.sha256`, its `.sig` file, and `release-signing-key.pub.pem` from the release assets. The detached signature authenticates the checksum file. Establish trust in the signing key independently; a key downloaded alongside an artifact is not by itself an independent trust anchor.

Public key SPKI SHA-256:

```text
312d7a9bf4df037fadbe50b8cd8e876e66db66ce40ca079e5700b068db222329
```

Using OpenSSL and a SHA-256 utility from the directory containing the downloads:

```sh
openssl pkey -pubin -in release-signing-key.pub.pem -outform DER -out release-signing-key.der
openssl dgst -sha256 release-signing-key.der
openssl dgst -sha256 -verify release-signing-key.pub.pem -signature VCF-Offline-Depot-1.0.0.ova.sha256.sig VCF-Offline-Depot-1.0.0.ova.sha256
sha256sum -c VCF-Offline-Depot-1.0.0.ova.sha256
```

Expected OVA SHA-256 (1,241,835,520 bytes):

```text
42e8be88ad1e90d0ba51e976d34d86b714f6f4da00bc0f27dde5d4b46ac6f0ef
```

On Windows, compare the result of `Get-FileHash -Algorithm SHA256 .\VCF-Offline-Depot-1.0.0.ova` with the checksum above. OpenSSL can verify the detached signature on Windows as well.

The companion `VCF-Offline-Depot-Build-2026-09-05.2-Guides-Rev-1.8.zip` has SHA-256:

```text
8334ebe3aa99cfed6d7a9773bb260df5b3bb02e137a4b41301b0e6ff8bcad9f4
```

Its `.zip.sha256` file and internal `SHA256SUMS` detect corruption; these documentation checksums are unsigned. The guides ZIP contains the four revision 1.8 operational guides and build notes. The unchanged roadmap revision 3.6 is supplied separately.

Successful integrity verification does not establish fresh-deployment qualification. This exact OVA is a prerelease awaiting manual deployment and VCF Installer acceptance testing.
