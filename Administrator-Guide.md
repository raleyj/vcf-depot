# VCF Offline Depot
## Administrator Guide

**Release baseline:** Appliance 1.0.0 - distribution build 2026-09-05.2

**Documentation revision:** 1.8 — 5 September 2026

**Release status:** The previously agreed VCF Installer-only 1.0 GA acceptance work and R01 remain complete. This rebuilt OVA is ready for manual replacement deployment testing; fresh deployment and VCF Installer acceptance of this exact image are pending. SDDC Manager and Fleet Manager qualification (T04) remains deferred.

**Audience:** Appliance administrators managing access, content, certificates, VCF Installer connections, configuration, and updates

**Security model:** All GUI users are administrators; depot content requires a separate depot account

## Navigation in this build

During initial setup, open **Prepare Depot** and use its sidebar for Download Tool, Authorize & Download Binaries, Depot Accounts, HTTPS Certificate, and VCF Connections. Complete tool installation, depot accounts, and certificate activation before finishing the endpoint connection.

Once a saved connection reports **Connected**, Prepare Depot and its setup sidebar are hidden. The main navigation then shows **Authorize & Download**, **Endpoint Connections**, **Offline Depot**, **Connection Logs**, and **Health Status**. A completed setup opens Health Status. Direct links to setup-only pages can return to Health Status; this build does not expose a separate post-setup maintenance entry for those pages. Do not remove a working connection record merely to reveal setup controls.

Use the administrator account menu for **Appliance management**, **Appliance updates**, and **Admin user management**. Health Status displays effective settings and service health; Appliance management changes settings. In the compact management layout, Identity and Timezone sit side by side above Management network and DNS and time; cards stack on smaller screens. Management network, Upload and verify a release, and Create administrator use normal card borders without the former blue top accent.

## Administration boundaries

Use the management GUI under `/admin/` for routine administration. Use vCenter or ESXi for VM lifecycle operations and console visibility.

The VM console provides a restricted recovery menu instead of an Ubuntu login. Use the separate console recovery password to recover web access or authorize a restart. Inbound SSH is disabled. Retain the Ubuntu maintenance credential only for approved break-glass support; it does not provide a supported remote login path.

The supported integration scope is **VCF Installer**; this exact rebuilt image still requires deployment acceptance. SDDC Manager and Fleet Manager integration is planned for a later qualified update.

## Sign in and navigate

**Why you may need this:** Use this procedure whenever you begin an administrative session or need to locate a management function safely.

1. Open `https://<appliance-fqdn>/admin/` from an authorized workstation.
2. Verify the browser shows the expected FQDN and trusted certificate.
3. Enter a named web administrator username and password.
4. Press Enter or select **Sign in**.
5. Confirm the top-right account menu shows the signed-in username.
6. Use the initial setup sidebar until a connection reaches Connected, then use the main navigation described above.
7. Use the account menu for Appliance management, Appliance updates, Admin user management, and Sign out.

## Understand the account types

**Why you may need this:** Choosing the correct account prevents failed sign-ins and avoids granting GUI access to depot clients or using the break-glass account for routine work.

| Account | Purpose | Managed from |
|---|---|---|
| Web administrator | Full GUI administration | Admin user management |
| Console recovery password | Recover web credentials or authorize a restart | Restricted VM console menu |
| Ubuntu maintenance account | Reserved break-glass support | Approved support process; no public shell login |
| Depot account | Read-only authenticated content retrieval | Depot Accounts |
| VCF endpoint credential | Authenticate to the supported VCF Installer | VCF Connections workflow |

A depot account cannot sign in to the GUI. A GUI administrator session does not authorize depot file retrieval.

## Manage web administrators

### Create an administrator

**Why you may need this:** Create a named account when a new operator needs access or when a second administrator is required for redundancy and recovery.

1. Open the account menu.
2. Select **Admin user management**.
3. In the add-user section, enter a unique named username.
4. Enter and confirm a temporary strong password that meets the displayed policy.
5. Select **Create administrator**.
6. Confirm the success status appears beside the action.
7. Open a private browser session and test the new account.
8. Have the account owner replace the temporary password through the approved process.
9. Record the account owner and creation date without recording the password.

### Change your password

**Why you may need this:** Change your password when it expires, may have been exposed, or must be rotated under organizational policy.

1. Open **Admin user management**.
2. Locate **Change my password**.
3. Enter the new password.
4. Confirm the new password.
5. Select **Change my password**. Your active sessions are signed out.
6. Confirm the success status.
7. Sign out and test the new password in a new browser session.
8. Update the approved password manager.

### Reset another administrator's password

**Why you may need this:** Reset another account when its owner cannot sign in and the request has been independently authorized.

1. Verify the requester's identity and authorization.
2. Sign in with a separate named administrator.
3. Open **Admin user management**.
4. Select the target username.
5. Enter and confirm a unique temporary password.
6. Select the password-reset action.
7. Confirm the success status.
8. Have the user sign in and replace the temporary password.
9. Record the actor, target account, time, and result without recording the password.

### Disable or enable an administrator

**Why you may need this:** Disable access temporarily during leave, investigation, or role changes; enable it again when the approved access requirement returns.

1. Open **Admin user management**.
2. Select the target account.
3. Review the account owner and current state.
4. Select **Disable** or **Enable**.
5. Confirm the action when prompted.
6. Verify the displayed state changes.
7. Test the intended access state.

The appliance prevents disabling the current administrator or the last enabled administrator.

### Remove an administrator

**Why you may need this:** Remove an account when the owner no longer administers the appliance and retention policy does not require the record to remain active.

1. Confirm retention policy permits removal.
2. Sign in with a different named administrator.
3. Open **Admin user management**.
4. Select the account to remove.
5. Verify the username and owner.
6. Select **Remove administrator**.
7. Confirm the removal.
8. Verify the account no longer appears and cannot sign in.

The current administrator and last enabled administrator cannot be removed.

### Configure password policy

**Why you may need this:** Adjust the policy to meet the organization's current password standard and prevent administrators from choosing weak credentials.

1. Open **Admin user management**.
2. Locate password-policy settings.
3. Set the minimum length between 8 and 128 characters.
4. Enable the approved upper-case, lower-case, number, and symbol requirements.
5. Enable the rule preventing the username from appearing in the password when required.
6. Save the policy.
7. Test one compliant and one rejected password.
8. Record the approved policy.

### Configure session timeout

**Why you may need this:** Set the timeout to reduce exposure from unattended browser sessions while allowing administrators enough time to complete normal work.

1. Open **Admin user management**.
2. Locate session-timeout settings.
3. Enter a value between 5 and 1,440 minutes.
4. Select **Save security policy**.
5. Confirm the status shows the new value.
6. Test that an expired session returns to sign-in without exposing protected content.

Long-running server-side downloads continue after the browser session expires.

## Review appliance configuration

**Why you may need this:** Review effective settings after deployment, a network change, an incident, or a restart to confirm the appliance is using the intended configuration.

1. Open the account menu.
2. Select **Appliance management**.
3. Review the editable identity, IPv4, DNS, NTP, and timezone settings. Open **Health Status** and select **Refresh status** to review effective values, synchronization, services, storage, and HTTPS identity.
4. Compare the values with the deployment record and DNS/IPAM systems.
5. Resolve any unexplained difference before making another change.

### Change hostname or FQDN

**Why you may need this:** Change appliance identity when DNS naming standards, network ownership, or the assigned production name changes.

1. Schedule a change window.
2. Confirm VM console access works.
3. Create the required forward and reverse DNS records.
4. Confirm the active certificate or planned replacement contains the new FQDN.
5. Open **Appliance management**.
6. Enter the new short hostname and complete FQDN.
7. Review the proposed values.
8. Apply the change.
9. Reconnect using the new FQDN.
10. Verify DNS, HTTPS identity, administrator login, depot access, and VCF Installer reachability.
11. Replace the certificate if its SANs do not contain the new identity.

### Change IPv4 configuration

**Why you may need this:** Change addressing when moving the appliance to another network, replacing a temporary DHCP lease, or correcting an invalid static configuration.

1. Schedule a change window and confirm VM console access.
2. Record the current address, prefix, gateway, DNS, and rollback values.
3. Pre-stage DNS, routing, firewall, and monitoring changes.
4. Open **Appliance management**.
5. Select DHCP or static mode.
6. For static mode, enter address, prefix, gateway, and DNS as one complete configuration.
7. Review the values and apply the change.
8. Expect the current browser connection to close if the address changes.
9. Reconnect using the effective address or FQDN.
10. Verify route, DNS, NTP, HTTPS, depot retrieval, and VCF Installer connectivity.

### Change DNS or NTP

**Why you may need this:** Update these services when resolvers or time sources change, names stop resolving, time loses synchronization, or certificate and token validation fails.

1. Open **Appliance management**.
2. Record the current configured and effective values.
3. In **DNS and time**, enter one or two comma-separated DNS resolver IPv4 addresses. Do not enter a gateway unless it is an approved resolver.
4. Enter up to two comma-separated NTP hostnames or IP addresses.
5. Select **Apply configuration**.
6. Verify DNS resolution for the appliance, VCF Installer, NTP sources, and required vendor endpoints.
7. Verify synchronization becomes active and the displayed time is correct.
8. Rerun certificate, update, or token workflows that previously failed because of time or resolution.

### Set the appliance timezone

1. Open the administrator account menu and select **Appliance management**.
2. In **Timezone**, choose an installed region and city, such as **America/New_York**, or **Etc/UTC**.
3. Select **Save timezone** and confirm the current-timezone message matches your selection.
4. Open **Health Status**, select **Refresh status**, and verify the timezone under **Time synchronization**.
5. Record the setting. After the next planned reboot, verify it is retained.

The sealed OVA defaults to **Etc/UTC**. Saving the timezone is independent of **Apply configuration**: it does not apply pending identity or network edits, change the NTP server list, or restart NTP. NTP synchronizes the clock independently of the timezone. Configure up to two NTP sources in **DNS and time**, then use **Apply configuration** for those changes.

The inventory UPDATED badge uses the browser workstation's local date and time. Changing the appliance timezone does not change the workstation timezone; UTC audit timestamps remain UTC. Record the timezone when correlating events.

## Manage depot accounts

These account procedures require the initial setup pages. Once setup is complete, this build hides those pages; arrange a supported maintenance procedure before attempting post-setup account changes.


### Create a depot account

**Why you may need this:** Create a dedicated client identity when connecting a new VCF Installer or separating access by environment or owner.

1. During initial setup, open **Prepare Depot**, then **Depot Accounts**.
2. Select the account-creation section.
3. Enter a unique client username.
4. Enter and confirm a strong password.
5. Select the confirmation checkbox, then select **Save depot account**.
6. Confirm the account appears in the current-account list.
7. Store the credential in the approved password manager.
8. Test retrieval of a small metadata object.
9. Assign the account to the intended VCF Installer connection.

### Rotate a depot password

**Why you may need this:** Rotate the credential on schedule, after possible exposure, or when responsibility for the connected endpoint changes.

1. Identify every client using the account.
2. During initial setup, open **Prepare Depot**, then **Depot Accounts** and select the account.
3. Enter and confirm the new password.
4. Apply the rotation.
5. Update the VCF Installer connection with the new credential.
6. Test metadata and representative binary retrieval.
7. Confirm the old password no longer works.
8. Update the password manager and change record.

### Remove a depot account

**Why you may need this:** Remove access when an endpoint is retired, an account is replaced, or a client should no longer retrieve depot content.

1. Confirm no VCF Installer or other approved client still uses the account.
2. During initial setup, open **Prepare Depot**, then **Depot Accounts**.
3. Select the username.
4. Review the account identity.
5. Select **Remove account**.
6. Confirm the removal.
7. Verify the credential returns HTTP 401.
8. Remove the corresponding password-manager entry according to policy.

Account removal does not recall files already downloaded by a client.

## Install or replace the VCF Download Tool

This GUI workflow is available during initial setup. The Download Tool page is hidden after a connection reaches Connected; do not assume a post-setup tool replacement entry is available in this build.


**Why you may need this:** Install the tool on a new appliance or replace it when Broadcom releases a supported version required for newer VCF content.

1. Obtain the supported Linux AMD64 VCF Download Tool archive from the approved vendor source.
2. Verify its source and checksum according to policy.
3. During initial setup, open **Prepare Depot**, then **Download Tool**.
4. Select the tool archive.
5. Start the upload and installation.
6. Wait for archive validation and safe extraction to complete.
7. Confirm the displayed tool version and activation state.
8. Stop if the archive contains unsupported paths, links, special files, an unsupported launcher, or an incompatible architecture.
9. Test Software Depot ID retrieval before starting a release download.

## Authorize and download binaries

**Why you may need this:** Use this workflow to prove entitlement and populate the offline depot with the metadata and binaries required by a selected VCF release.

1. Open **Authorize & Download Binaries** under Prepare Depot, or **Authorize & Download** after setup.
2. Confirm the active VCF Download Tool version.
3. Enter a short-lived access token or use the supported vendor credential exchange.
4. Retrieve and verify the Software Depot ID when required.
5. Enter the Broadcom activation code through the protected field.
6. Select the intended VCF release and download type.
7. Confirm entitlement and available storage.
8. Select **Start release download**.
9. Confirm the page moves to **Download activity**.
10. Monitor current file, completed files, transferred size, rate, and errors.
11. If interrupted, correct the cause and retry the same job. Verified files should be retained.
12. Refresh offline inventory after completion.
13. Validate expected metadata and at least one binary object.

Do not retain tokens, vendor passwords, or activation credentials in screenshots, logs, or browser storage.

## Refresh and review offline content

**Why you may need this:** Refresh after downloads, content removal, storage recovery, or an unexpected inventory result so the GUI matches the files currently on disk.

1. Open **Offline Depot**.
2. Confirm no appliance update or filesystem maintenance is running.
3. Select **Refresh inventory** once.
4. Confirm the button and local status show an active scan.
5. Wait for the green **UPDATED** badge containing the refresh date and time. File count, total size, incomplete count, and free space appear beside the badge. UPDATED confirms that inventory refreshed; it does not certify a complete release download or successful endpoint retrieval.
6. Compare release count, file count, and size with the expected download manifest.
7. Review datastore and filesystem capacity.
8. If the scan does not finish, do not start another scan; use the Troubleshooting and Appliance Update Guide.

## Manage HTTPS certificates

The following certificate controls are on the initial setup pages. After setup is complete, review the active HTTPS identity in Health Status; certificate replacement requires a supported maintenance procedure because the setup page is hidden.


### Review and export the active certificate

**Why you may need this:** Review certificate identity and expiration during routine checks, troubleshooting, audits, or before distributing the issuing CA to a client.

1. During initial setup, open **Prepare Depot**, then **HTTPS Certificate**.
2. Expand the current-certificate section.
3. Review subject, SANs, issuer, validity, fingerprint, and chain.
4. Download the certificate chain, server certificate, or root CA when required.
5. Store exported public certificate material according to policy.

The private key is not exported by this workflow.

### Generate a CSR

**Why you may need this:** Generate a CSR when replacing the temporary certificate, renewing an expiring certificate, or adding a new DNS name or IP SAN.

1. During initial setup, open **Prepare Depot**, then **HTTPS Certificate**.
2. Enter the final common name.
3. Add every required DNS name and IP address as a SAN.
4. Review the requested identity.
5. Select **Generate CSR**.
6. Wait for the local success status.
7. Download the pending CSR.
8. Submit it to the approved certificate authority.

Only one CSR can remain pending. Download or discard it before generating another.

### Activate a signed certificate

**Why you may need this:** Activate the signed response from the certificate authority so browsers and VCF Installer can trust the appliance's production HTTPS identity.

1. Confirm the signed server certificate matches the pending CSR.
2. Obtain the intermediate certificates and issuing root CA.
3. During initial setup, open **Prepare Depot**, then **HTTPS Certificate**.
4. Upload the signed server certificate, intermediates, and root.
5. Select the option to replace the active certificate.
6. Select **Convert and activate**.
7. Wait for chain validation, installation, and web-service reload.
8. Reconnect in a new browser session.
9. Verify the served identity, SANs, chain, fingerprint, and expiration.
10. Test administrator login, authenticated depot retrieval, and VCF Installer trust.

The previous certificate and key are retained as a protected backup by the supported workflow.

## Configure a VCF Installer connection

### Add and test the connection

**Why you may need this:** Add a connection when onboarding a VCF Installer or retest it after credential, certificate, DNS, network, or endpoint changes.

1. Open **Endpoint Connections**.
2. Add a named connection.
3. Select **VCF Installer** as the endpoint type.
4. Enter the endpoint FQDN or IP and HTTPS port.
5. Enter the endpoint sign-in credential.
6. Enter SSH username `vcf` and its password.
7. Enter the root password used for approved elevation.
8. Retrieve and approve the endpoint certificate using the procedure below, or provide the private CA certificate files when required.
9. Independently verify and accept the detected SSH host key. HTTPS certificate approval and SSH host-key approval are separate checks.
10. Select an existing depot account and enter its current password.
11. Select **Test connection**.
12. Review each DNS, TCP, TLS, API, authentication, version, SSH, elevation, trust, and depot-access check.
13. Correct the first failed dependency and rerun the test.

### Retrieve and approve the VCF Installer certificate

**Why you may need this:** VCF Installer may present a self-signed server certificate rather than a certificate issued by a separate root CA. This workflow retrieves that certificate from the endpoint so you do not have to export and upload it manually.

1. Enter the endpoint FQDN and HTTPS port in **Endpoint Connections**.
2. Select **Test connection**. Certificate retrieval starts automatically; **Configure endpoint** uses the same approval flow. For VCF Installer, the normal form has no separate certificate section or retrieval button. Fleet Manager and SDDC Manager retain their visible certificate-trust section, uploads, and retrieval button.
3. If the endpoint is already trusted by the appliance, testing continues without a certificate approval prompt.
4. For an untrusted self-signed endpoint, review the pop-up showing the endpoint, subject, expiration, and SHA-256 fingerprint.
5. Compare the fingerprint with the endpoint console or another independently trusted source. Select **Cancel** if it does not match. Discovery itself does not send endpoint sign-in credentials.
6. Accept only the verified certificate. The approval is held in the current page session for that endpoint and port; it does not disable TLS validation or install a system-wide CA.
7. Continue testing or configuration. A valid self-signed server certificate does not require a separate root CA upload. Its hostname and validity dates must still match.
8. If the endpoint uses an untrusted private CA instead, manual certificate options appear. Upload its server certificate and issuing root CA there, then retry. Automatic approval does not replace that CA chain.

Test and Configure retrieve the current certificate each time. An unchanged certificate already approved in the current page session does not prompt again; a changed fingerprint requires new approval. Changing the endpoint, port, or uploaded certificate files clears the approval, as does reloading the page. Investigate an unexpected fingerprint before accepting it. TLS verification remains enabled for all credential-bearing requests.

### Configure the endpoint

**Why you may need this:** Configure the endpoint when it must begin using this appliance or when its depot URL, trust, or selected depot account has changed.

1. Confirm all required connection checks pass.
2. Schedule an approved VCF Installer change window.
3. Review the proposed depot URL, trust, account, and property changes.
4. Select the authorization checkbox.
5. Select **Configure endpoint**.
6. Monitor property update, trust installation, service restart, stabilization wait, and post-change retrieval status.
7. Do not reapply configuration while the operation is active.
8. Confirm the saved status becomes **Connected**.
9. Confirm the VCF Installer retrieves representative metadata and one representative binary.
10. Record the endpoint version, time, account name, and result without recording secrets.

A passed connection test alone does not mean the endpoint has been configured.

Do not configure SDDC Manager or Fleet Manager in production with this release.

## Review connection logs

**Why you may need this:** Review logs when a test, configuration, or retrieval fails and you need the exact phase, status, and sanitized error for diagnosis.

1. Open **Connection Logs**.
2. Select the relevant time range or endpoint.
3. Locate the connection test, configuration, or retrieval request.
4. Review the phase, status code, duration, and sanitized failure detail.
5. Correlate the entry with the local status shown on Endpoint Connections.
6. Export or capture only the evidence required for troubleshooting.
7. Verify passwords, tokens, authorization headers, and private keys are absent before sharing evidence.

## Apply a future appliance update

**Why you may need this:** Apply a signed update to install fixes, security improvements, new supported integrations, or compatibility changes without redeploying the appliance.

1. Read the target release notes and confirm the installed version is a supported source.
2. Schedule a maintenance window.
3. Confirm no download, inventory scan, certificate activation, network change, or endpoint configuration is running.
4. Confirm console access and adequate operating-system, data-disk, and datastore space.
5. Open the account menu and select **Appliance updates**.
6. In **Upload and verify a release**, upload the approved signed `offline-depot-update-<version>.tar.gz` package.
7. Wait for signature, trusted-key, manifest, checksum, path, package-completeness, version, and compatibility validation.
8. Stop if any validation fails.
9. Review the staged target version and release notes.
10. Select **Apply update** and accept the restart notice.
11. Monitor protected backup, payload application, service restart, health validation, and activation.
12. Do not reboot, refresh repeatedly, restart services, or upload another package while the update is active.
13. Sign in through a new browser session after completion.
14. Verify version, services, networking, certificate, accounts, inventory, depot retrieval, and VCF Installer Connected status.
15. Review update history and retain the result.

Use the Troubleshooting and Appliance Update Guide for the complete pre-update checklist, failure recovery, automatic rollback, and manual rollback procedures.

## Administrative handover

**Why you may need this:** Complete handover when ownership changes or deployment closes so the next administrator receives verified configuration, access, and support information.

1. Record the appliance version and OVA checksum.
2. Record hostname, FQDN, address, gateway, DNS, NTP, and timezone.
3. Record certificate issuer, fingerprint, and expiration.
4. Record administrator and depot-account owners without passwords.
5. Record the connected VCF Installer and its selected depot account.
6. Record inventory date, datastore owner, update level, and open exceptions.
7. Confirm another named administrator can sign in and use the Deployment Guide and Troubleshooting and Appliance Update Guide.
8. Use vault references instead of placing credentials in the handover record.


## Firewall defaults

**Why you may need this:** Use these defaults when arranging network access or troubleshooting a blocked connection.

Only TCP 443 (HTTPS) and TCP 80 (HTTPS redirect) are allowed inbound. Application and Docker backend ports are private. Inbound SSH remains disabled. Outbound DNS, time synchronization, entitled downloads, and VCF endpoint connections remain available. Place the appliance on a controlled management network and restrict source addresses at the network level.

Use the Troubleshooting Guide's console recovery procedure if web credentials are lost. Store the separate recovery password in a vault and restrict vCenter/ESXi console permissions.
