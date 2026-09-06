# VCF Offline Depot
## Quick Start Guide

**Release baseline:** Appliance 1.0.0 - distribution build 2026-09-05.2

**Documentation revision:** 1.8 — 5 September 2026

**Release status:** The previously agreed VCF Installer-only 1.0 GA acceptance work and R01 remain complete. This rebuilt OVA is ready for manual replacement deployment testing; fresh deployment and VCF Installer acceptance of this exact image are pending. SDDC Manager and Fleet Manager qualification (T04) remains deferred.

**Audience:** Administrators completing initial appliance setup after deployment

**Goal:** Install and authorize the VCF Download Tool, download content, secure depot access, install a production certificate, and connect the supported VCF Installer

## Navigation in this build

During initial setup, open **Prepare Depot** and use its sidebar for Download Tool, Authorize & Download Binaries, Depot Accounts, HTTPS Certificate, and VCF Connections. Complete tool installation, depot accounts, and certificate activation before finishing the endpoint connection.

Once a saved connection reports **Connected**, Prepare Depot and its setup sidebar are hidden. The main navigation then shows **Authorize & Download**, **Endpoint Connections**, **Offline Depot**, **Connection Logs**, and **Health Status**. A completed setup opens Health Status. Direct links to setup-only pages can return to Health Status; this build does not expose a separate post-setup maintenance entry for those pages. Do not remove a working connection record merely to reveal setup controls.

Use the administrator account menu for **Appliance management**, **Appliance updates**, and **Admin user management**. Health Status displays effective settings and service health; Appliance management changes settings. In the compact management layout, Identity and Timezone sit side by side above Management network and DNS and time; cards stack on smaller screens. Management network, Upload and verify a release, and Create administrator use normal card borders without the former blue top accent.

## Before you start

Confirm the appliance has completed first boot and you can sign in at `https://<appliance-fqdn>/admin/` with a named web administrator.

Have the following available:

1. Supported Linux AMD64 VCF Download Tool archive.
2. Broadcom entitlement, access token or supported authorization credential, and activation code.
3. Selected VCF release and sufficient depot-data capacity.
4. A unique depot-account username and password.
5. Final appliance FQDN and required certificate SANs.
6. Access to an approved certificate authority.
7. VCF Installer endpoint credential, `vcf` SSH credential, root elevation password, HTTPS identity, and SSH host-key reference.

Complete the six workflows in order.

### Set the appliance timezone

1. Open the administrator account menu and select **Appliance management**.
2. In **Timezone**, choose an installed region and city, such as **America/New_York**, or **Etc/UTC**.
3. Select **Save timezone** and confirm the current-timezone message matches your selection.
4. Open **Health Status**, select **Refresh status**, and verify the timezone under **Time synchronization**.
5. Record the setting. After the next planned reboot, verify it is retained.

The sealed OVA defaults to **Etc/UTC**. Saving the timezone is independent of **Apply configuration**: it does not apply pending identity or network edits, change the NTP server list, or restart NTP. NTP synchronizes the clock independently of the timezone. Configure up to two NTP sources in **DNS and time**, then use **Apply configuration** for those changes.

The inventory UPDATED badge uses the browser workstation's local date and time. Changing the appliance timezone does not change the workstation timezone; UTC audit timestamps remain UTC. Record the timezone when correlating events.


## 1. Install VCF Download Tool

**Why:** The appliance needs the supported vendor tool before it can authorize access or download entitled VCF content.

**You need:** The verified Linux AMD64 VCF Download Tool archive from the approved vendor source.

1. Sign in to the appliance GUI.
2. Open **Prepare Depot**, then **Download Tool** in the setup sidebar.
3. Confirm no tool installation or download job is already running.
4. Select the VCF Download Tool archive.
5. Start the upload and installation.
6. Wait while the appliance validates the archive and extracts it safely.
7. Confirm the page displays the installed tool version.
8. Confirm the tool status shows that it is available for use.

**Success:** The expected tool version is displayed without an archive, architecture, launcher, or extraction error.

**If it fails:** Verify the archive source and checksum. Use the Linux AMD64 package and do not alter or repackage the vendor archive.

## 2. Authorize VCF Download Tool

**Why:** Authorization proves that the organization is entitled to retrieve protected VCF software from the vendor depot.

**You need:** An approved short-lived access token or supported vendor credential, plus the Broadcom activation code when required.

1. Open **Authorize & Download Binaries** under Prepare Depot, or **Authorize & Download** after setup.
2. Confirm the expected VCF Download Tool version is active.
3. Expand the authorization section.
4. Enter the short-lived access token or complete the supported credential exchange.
5. Retrieve the Software Depot ID when the workflow requires it.
6. Compare the displayed Depot ID with the intended account or entitlement.
7. Enter the Broadcom activation code in the protected field.
8. Select the authorization action.
9. Wait for the local authorization result.
10. Confirm the green **ACTIVATED** status is shown before starting the release download.

**Success:** Authorization completes and the release-selection controls become available without an entitlement or token error.

**If it fails:** Confirm the clock is synchronized, the appliance can resolve and reach the vendor endpoints, and the token or activation code is current. Do not place credentials in screenshots or logs.

## 3. Download binaries

**Why:** This populates the offline depot with the metadata and binary files required by the selected VCF release.

**You need:** Successful authorization, the intended VCF release and download type, and sufficient data-disk and datastore capacity.

1. Remain on the authorization and download page.
2. Confirm the authorization status is valid.
3. Select the intended VCF release; this build includes VCF 9.1.1 in the release choices. Confirm your entitlement and required content before downloading.
4. Select the required download type or product selection.
5. Review the selection and available storage.
6. Select **Start release download**.
7. Confirm the page moves to **Download activity**.
8. Monitor current file, completed files, transferred size, rate, and errors.
9. Leave the job running until it reports completion.
10. If interrupted, correct the cause and retry the same job. Verified files should be retained.
11. Open **Offline Depot**.
12. Select **Refresh inventory** once.
13. Wait for the green **UPDATED** badge containing the refresh date and time. File count, total size, incomplete count, and free space appear beside the badge. UPDATED confirms that inventory refreshed; it does not certify a complete release download or successful endpoint retrieval.
14. Confirm the expected release, file count, and size appear.

**Success:** The download completes, inventory recognizes the release, and a representative metadata file and binary object are present.

**If it fails:** Check entitlement, vendor connectivity, DNS, NTP, free space, and the displayed file-level error. Stop and investigate repeated checksum failures.

## 4. Depot Account Creation

**Why:** VCF Installer needs a dedicated read-only credential to retrieve protected depot content over HTTPS.

**You need:** A unique username and strong password stored in the approved password manager.

1. During initial setup, open **Prepare Depot**, then **Depot Accounts**.
2. Locate the account-creation section.
3. Enter the depot username.
4. Enter and confirm the password.
5. Select the confirmation checkbox, then select **Save depot account**.
6. Confirm the account appears in the current-account list.
7. Store the credential in the approved password manager.
8. Test a small metadata request with the new account.

```
curl --cacert depot-ca.pem -u depotuser https://depot.example/PROD/metadata/productVersionCatalog/v1/productVersionCatalog.json
```

9. Confirm the authenticated request succeeds.
10. Confirm the same request without credentials returns HTTP 401.

**Success:** The account retrieves protected content, while anonymous HTTPS access remains rejected.

**If it fails:** Re-enter the username and password, confirm the account exists, and verify the request uses the appliance's trusted HTTPS name.

## 5. HTTPS Certificate generation and upload

**Why:** A certificate signed by the approved CA allows browsers and VCF Installer to verify the appliance's production HTTPS identity.

**You need:** Final appliance FQDN, every required DNS name or IP SAN, and access to the approved certificate authority.

**Generate and submit the CSR**

1. During initial setup, open **Prepare Depot**, then **HTTPS Certificate**.
2. Enter the final FQDN as the common name.
3. Add every DNS name and IP address that clients will use.
4. Review the requested SAN list.
5. Select **Generate CSR**.
6. Wait for the local success status.
7. Download the pending CSR.
8. Submit the CSR to the approved certificate authority.
9. Request a server certificate that permits TLS server authentication.

<!-- pagebreak -->

### Upload and activate the signed certificate

1. Obtain the signed server certificate, intermediate CA certificates, and issuing root CA.
2. Return to **HTTPS Certificate**.
3. Upload the signed server certificate.
4. Upload the intermediate certificates when present.
5. Upload the root CA.
6. Select the option to replace the active certificate.
7. Select **Convert and activate**.
8. Wait for chain validation, installation, and web-service reload.
9. Open a new browser session using the appliance FQDN.
10. Verify the subject, SANs, issuer, fingerprint, expiration, and complete chain.

**Success:** The browser trusts the appliance FQDN and the certificate details match the approved certificate request.

**If it fails:** Confirm the signed certificate matches the pending CSR, the SAN contains the exact FQDN, the complete CA chain is supplied, and appliance time is correct.

## 6. VCF connection setup

**Why:** This configures the supported VCF Installer to trust the appliance and retrieve depot content with the selected depot account.

**You need:** VCF Installer endpoint address, HTTPS sign-in credential, `vcf` SSH credential, root elevation password, independently verifiable endpoint certificate fingerprint and SSH host key, and the depot account created in Step 4.

### Add and test the connection

1. Open **Endpoint Connections**.
2. Add a named connection.
3. Select **VCF Installer** as the endpoint type.
4. Enter the endpoint FQDN or IP address and HTTPS port.
5. Enter the endpoint sign-in username and password.
6. Enter SSH username `vcf` and its password.
7. Enter the root password used for approved elevation.
8. Select the depot account created in Step 4.
9. Enter the current depot-account password.
10. For VCF Installer, select **Test connection**. The appliance retrieves the endpoint HTTPS certificate automatically and displays a trust pop-up if approval is needed. No separate certificate section or retrieval button is required.
11. If prompted for a self-signed certificate, compare its SHA-256 fingerprint with the endpoint console or another trusted source, then accept only a match. No separate root CA is needed.
12. If the endpoint uses an untrusted private CA, the manual certificate options appear. Supply its server certificate and issuing root CA there, then retry. Already trusted endpoints need no additional approval.
13. Verify and accept the detected SSH host key separately. Cancel either approval if the identity cannot be confirmed.
14. Review DNS, TCP, TLS, API, authentication, version, SSH, elevation, trust, and depot-access checks.
15. Correct the first failed dependency and rerun the test until all required checks pass.

Self-signed certificate approval lasts for the current page session and endpoint. Changing the endpoint or certificate inputs clears it. Certificate discovery does not send endpoint sign-in credentials, and TLS verification remains enabled.

### Configure and verify the endpoint

1. Schedule an approved VCF Installer change window.
2. Review the proposed depot URL, trust, account, and property changes.
3. Select the authorization checkbox.
4. Select **Configure endpoint**.
5. Monitor the phase-specific status while properties are updated, trust is installed, installer services restart, and stabilization completes.
6. Do not submit the configuration again while the operation is active.
7. Wait for post-change metadata and binary retrieval checks.
8. Confirm the saved endpoint status becomes **Connected**.
9. Confirm the VCF Installer retrieves representative metadata and one representative binary with the selected depot account.

**Success:** Setup navigation closes and Health Status becomes the landing page. The connection displays **Connected**, the installer trusts the appliance certificate, and authenticated metadata and binary retrieval succeed.

**If it fails:** Open the detailed result and resolve checks in dependency order. A passed connection test alone does not mean endpoint configuration completed.

SDDC Manager and Fleet Manager are not production-supported connection targets in this release.

## Quick completion checklist

| Step | Complete when | Result |
|---|---|---|
| VCF Download Tool installed | Expected tool version is active | |
| Tool authorized | Entitlement and authorization succeed | |
| Binaries downloaded | Inventory shows expected release and content | |
| Depot account created | Authenticated retrieval succeeds; anonymous returns 401 | |
| HTTPS certificate activated | Browser trusts the production FQDN and chain | |
| VCF connection configured | VCF Installer status is Connected and retrieval succeeds | |
