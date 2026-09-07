# VCF Offline Depot
## ESXi and vCenter Deployment Guide

**Release baseline:** Appliance 1.1.0 - distribution build 2026-09-07.1

**Documentation revision:** 1.9 — 7 September 2026

**Release status:** Version 1.1.0 maintenance release. Consult the signed release record for validation of this exact OVA. VCF Installer remains the supported integration scope; SDDC Manager and Fleet Manager qualification remains deferred.

**Audience:** Virtualization administrators deploying the appliance through the standalone ESXi Host Client or the vCenter vSphere Client

**Outcome:** A running appliance with verified networking, trusted HTTPS, administrator access, authenticated depot content, and a Connected VCF Installer endpoint

## Bootable ESX installer media

Choose **Install, including ESXi media** when downloading installation binaries. After the main VCF download, the appliance reads the release bill of materials and product catalog, then makes a separate VCF Download Tool request for the matching x86-64 bootable ESX ISO bundle ID. This includes installer media cataloged as PATCH instead of INSTALL. No separate manual ISO upload is required for an available entitled catalog entry.

The job requires the expected ISO filename, size, and SHA-256 checksum before reporting success. For VCF 9.1.1, the catalog entry is `VMware-VMvisor-Installer-9.1.1.0.25714478.x86_64.iso`. A missing, ambiguous, or corrupt ISO fails the job instead of accepting a zero-result request. Correct the cause and retry the same release; the tool reuses existing content. Look under the ESX_HOST component in **Depot Files**. A fresh activation code is required for each new job and is removed after use.

## Navigation in this build

The header provides **Authorize & Download**, **VCF Connections**, **Depot Files**, **Depot Management**, **Connection Logs**, and **Appliance Status**. During initial setup, **Prepare Depot** provides the guided steps for the Download Tool, authorization, Depot Accounts, HTTPS Certificate, and VCF Connections. After a saved endpoint becomes Connected, the setup workflow is hidden.

**Depot Management** remains available before and after setup. Its three cards open Download Tool, Depot Accounts, and HTTPS Certificate. Use **Back to Depot Management** to return. Maintenance does not require disconnecting or deleting a working endpoint. After rotating depot credentials or renewing the certificate, review affected clients in **VCF Connections** and verify authenticated retrieval.

**Depot Files** opens the **Depot File Management** page for browsing, uploading, and managing release files. **Appliance Status** reports health and effective settings. The administrator menu contains **Appliance management**, **Appliance updates**, and **Admin user management**; these pages leave the primary header links unhighlighted.
## Supported production scope

The supported integration scope is **VCF Installer**. See the signed release record for the checks performed on this build. The qualified workflow covers certificate trust, SSH access through the `vcf` account with root elevation, endpoint property configuration, service restart and stabilization, authenticated metadata retrieval, and representative binary retrieval.

SDDC Manager and Fleet Manager integration is planned for a later update. Do not configure those endpoint types in production until a release explicitly lists them as supported.

## Before you deploy

### Obtain and verify the release files

1. Obtain `VCF-Offline-Depot-1.1.0.ova`, its SHA-256 checksum, signature, release record, and release notes from the approved release location.
2. Calculate the SHA-256 hash of the OVA with an approved utility.
3. Compare the complete calculated hash with the published checksum.
4. Verify the checksum signature with the approved release public key.
5. Stop if the checksum, signature, source, or supported upgrade path cannot be verified.
6. Record the OVA filename, checksum, release date, and operator in the deployment record.

### Replace an existing appliance

This OVA creates a fresh appliance with a blank depot-data disk. It does not migrate accounts, endpoint configuration, certificates, binaries, or download history. Preserve the existing VM and data until replacement acceptance is complete. Use a separate test address or power off the old VM before reusing its IP address. Complete the setup workflows on the new appliance and verify endpoint retrieval before retiring the old VM.

Record appliance version **1.1.0**, build **2026-09-07.1**, and the supplied OVA SHA-256 in the deployment record.

### Confirm platform capacity

1. Use an ESX 9.0 or later host, standalone or managed by vCenter. This OVA uses virtual hardware version 22 and does not run on ESXi 8.x or earlier. The earlier qualification used ESX 9.1.0; it does not qualify this exact rebuilt image.
2. Reserve capacity for 4 vCPU and 8 GB memory.
3. Confirm the selected datastore can accommodate a 32 GiB operating-system disk and a 1 TiB thin-provisioned depot-data disk.
4. Confirm the datastore has enough ongoing capacity for the VCF release content you plan to retain.
5. Select an approved storage policy and disk format. Thin provisioning reduces initial allocation but still requires capacity monitoring.

### Prepare network and identity values

| Item | Requirement | Example |
|---|---|---|
| VM name | Unique inventory name | VCF-Offline-Depot |
| Port group | Management network reachable by administrators and VCF Installer | Management Network |
| Hostname / FQDN | One hostname field; DNS labels up to 63 characters, complete FQDN up to 253 | offline-depot.example.com |
| FQDN | Forward-resolvable appliance name | offline-depot.example.com |
| IPv4 mode | DHCP or static | Static |
| IPv4 address and prefix | Unused management address | 192.0.2.10/24 |
| Default gateway | Router for the management subnet | 192.0.2.1 |
| DNS servers | One or two resolver IP addresses, separated by a comma | 192.0.2.53,192.0.2.54 |
| NTP servers | Up to two comma-separated hostnames or IP addresses | 192.0.2.53,192.0.2.54 |

The example addresses above are documentation placeholders. Replace them with your own allocated network values. DNS records are managed outside the appliance.

1. Create or reserve the appliance DNS record before using static addressing.
2. Confirm the chosen port group can reach DNS, NTP, administrators, the VCF Installer, and approved vendor download endpoints.
3. Allow TCP 443 from administrators and the VCF Installer to the appliance.
4. Allow TCP 80 only when the HTTP-to-HTTPS redirect is required.
5. Keep console access available for first-boot and network recovery.

### Prepare credentials

1. Create a unique initial web administrator username and password. All three deployment passwords must contain 15-256 characters, without control characters.
2. Create a console recovery password that differs from both the web administrator and restricted Ubuntu maintenance passwords. Invalid passwords prevent first boot from applying the network configuration.
3. Store all credentials in an approved password manager.
4. Obtain the VCF Installer endpoint sign-in credential, `vcf` SSH credential, and root elevation password through the approved process.
5. Do not place passwords or access tokens in the deployment record.

## Path A - Deploy through vCenter

### Start the deployment wizard

1. Sign in to the vSphere Client.
2. Select the destination datacenter, cluster, resource pool, host, or VM folder.
3. Right-click the destination and select **Deploy OVF Template**.
4. Select **Local file**, choose the verified OVA, and select **Next**.
5. Enter the approved VM name and select the destination folder.
6. Select the target compute resource and wait for compatibility validation to complete.
7. Resolve every compatibility error before continuing.

### Select storage and network

1. Review the OVA details and confirm the expected publisher, version, virtual hardware, and two virtual disks.
2. Accept the deployment details only when they match the release record.
3. Select the approved storage policy and datastore.
4. Select the approved disk format. Use thin provisioning when required by the storage plan.
5. Map the OVA management network to the prepared management port group.
6. Confirm the selected port group is the intended production network.

### Enter OVF properties

1. Enter the initial web administrator username.
2. Enter and confirm the web administrator password (15-256 characters).
3. Set a separate **Console recovery password** (15-256 characters) and store it in your password vault. It must differ from both the web and maintenance passwords. Keep the Ubuntu maintenance credential restricted to break-glass support; routine console access uses the recovery menu.
4. Set the Ubuntu maintenance password (15-256 characters) and retain it only for approved break-glass support. No Ubuntu shell login is exposed by the console.
5. Enter the appliance hostname or complete FQDN in the single hostname field.
6. Select **dhcp** or **static** from the **IPv4 network mode** dropdown.
7. For static mode, enter the IPv4 address, prefix length, default gateway, and DNS servers as one complete configuration. Enter no more than two DNS server IP addresses, separated by a comma (for example, **192.0.2.53,192.0.2.54**). Spaces around the comma are allowed. In DHCP mode, leave DNS blank to use the DHCP-provided resolvers, or enter one or two IPs to override them. First boot rejects a third address or a list without commas; correct the deployment properties before retrying.
8. Optionally enter one or two NTP server hostnames or IP addresses, separated by a comma (for example, **192.0.2.53,192.0.2.54**). First boot rejects a third entry or invalid separators.
9. Review every property for typing errors. No default password exists.
10. On **Ready to complete**, verify compute, storage, network mapping, disk format, and customization values.
11. Select **Finish** and monitor the deployment task.
12. Do not power on the VM until the deployment task completes successfully.

## Path B - Deploy through a standalone ESXi host

### Start the ESXi deployment wizard

1. Sign in to the ESXi Host Client at `https://<esxi-host>/ui`.
2. In the navigator, select **Virtual Machines**.
3. Select **Create / Register VM**.
4. Select **Deploy a virtual machine from an OVF or OVA file**, then select **Next**.
5. Enter the approved VM name.
6. Drag the verified OVA into the upload area or select it from the local workstation.
7. Wait for the upload validation to complete before selecting **Next**.

### Select storage and deployment options

1. Select the approved datastore and verify that sufficient free capacity is available.
2. Review the deployment options.
3. Map the appliance network to the prepared ESXi port group.
4. Select the approved disk provisioning format.
5. Disable automatic power-on if the wizard offers that option.
6. Review the summary and begin deployment.
7. Monitor the recent tasks pane until OVA upload and deployment complete.

### Enter or verify OVF properties

1. Open the deployed VM and select **Edit settings**.
2. Open **VM Options** and locate **vApp Options** or the OVF property view presented by the host version.
3. Confirm fields exist for the web administrator username and password, console recovery password, Ubuntu maintenance password, hostname/FQDN, IPv4 mode, address, prefix, gateway, DNS, and NTP.
4. Enter the same values described in the vCenter OVF-properties procedure.
5. Save the settings and reopen them to confirm the values were retained.
6. Do not power on the appliance if the standalone host does not expose all required OVF properties. Use vCenter or another approved deployment method that preserves those properties.

## Power on and complete first boot

1. Open the VM console before first power-on.
2. Power on the appliance.
3. Leave the console open while first-boot initialization validates the supplied properties, configures networking and time, initializes the blank depot-data disk, and starts appliance services. The depot content container starts automatically with the management service; no manual container start should be required.
4. Do not interrupt initialization or repeatedly reboot the VM.
5. Wait for the console to report completion and display the expected hostname, management address, and GUI URL.
6. If initialization reports a specific failure, record it and correct the cause before retrying.

### Verify the guest from the console

1. Confirm the console shows the appliance version, hostname, and expected management address.
2. Select **Show service status** and confirm nginx, management, and UFW are active.
3. Confirm VMware Tools reports normally in vCenter or ESXi.
4. Open the management GUI and use **Appliance Status** to check route, DNS, NTP, synchronization, and services.
5. Do not attempt an Ubuntu login. The console offers a restricted recovery menu, and inbound SSH is disabled.


## Complete appliance setup

### Sign in and verify configuration

1. From an authorized workstation, open `https://<appliance-fqdn>/admin/`.
2. If the temporary certificate is active, compare its presented identity with the console before proceeding.
3. Sign in with the web administrator created during deployment.
4. Open **Appliance management**.
5. Compare hostname, FQDN, IPv4 mode, address, prefix, gateway, DNS, NTP, synchronization state, and timezone with the deployment record.
6. Correct discrepancies before loading content or configuring VCF Installer.
7. Create and test a second named administrator before handover.

## Prepare for the six setup workflows

Confirm the appliance has completed first boot and you can sign in at `https://<appliance-fqdn>/admin/` with a named web administrator.

Have the following available:

1. Supported Linux AMD64 VCF Download Tool archive.
2. Broadcom entitlement and a current activation code for this Software Depot ID.
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
4. Open **Appliance Status**, select **Refresh status**, and verify the timezone under **Time synchronization**.
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

1. Open **Authorize & Download** (or the authorization step in Prepare Depot during initial setup).
2. Select **Get Software Depot ID** and copy the identifier.
3. Register that identifier through Broadcom and obtain an entitled activation code.
4. Enter the activation code in the protected download field when starting the selected release download.

The ACTIVATED badge records a previous successful download. It does not replace the current activation code required for each new job. The appliance does not retain the activation code after the job.

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
11. Open **Depot Files**.
12. Select **Refresh inventory** once.
13. Wait for the green **UPDATED** badge containing the refresh date and time. File count, total size, incomplete count, and free space appear beside the badge. UPDATED confirms that inventory refreshed; it does not certify a complete release download or successful endpoint retrieval.
14. Confirm the expected release, file count, and size appear.

**Success:** The download completes, inventory recognizes the release, and a representative metadata file and binary object are present.

**If it fails:** Check entitlement, vendor connectivity, DNS, NTP, free space, and the displayed file-level error. Stop and investigate repeated checksum failures.

## 4. Depot Account Creation

**Why:** VCF Installer needs a dedicated read-only credential to retrieve protected depot content over HTTPS.

**You need:** A unique username and strong password stored in the approved password manager.

1. Open **Depot Management > Depot Accounts** (or **Prepare Depot > Depot Accounts** during initial setup).
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

1. Open **Depot Management > HTTPS Certificate** (or **Prepare Depot > HTTPS Certificate** during initial setup).
2. Enter the final FQDN as the common name.
3. Add every DNS name and IP address that clients will use.
4. Review the requested SAN list.
5. Select **Generate CSR**.
6. Wait for the local success status.
7. Download the pending CSR.
8. Submit the CSR to the approved certificate authority.
9. Request a server certificate that permits TLS server authentication.

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

1. Open **VCF Connections**.
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

**Success:** Setup navigation closes and the permanent header navigation remains available. The connection displays **Connected**, the installer trusts the appliance certificate, and authenticated metadata and binary retrieval succeed.

**If it fails:** Open the detailed result and resolve checks in dependency order. A passed connection test alone does not mean endpoint configuration completed.

SDDC Manager and Fleet Manager are not production-supported connection targets in this release.

## Verify final depot access

After Step 5 installs the production HTTPS certificate and Step 6 configures VCF Installer, repeat the account checks using the production FQDN and its trusted CA chain.

1. Confirm anonymous HTTPS requests to `/`, `/depot/`, and `/secure-depot/` return HTTP 401.
2. Confirm HTTP returns only a 308 redirect to HTTPS.
3. Confirm the selected depot account retrieves representative metadata and binary content over HTTPS.
4. Use a credential prompt or protected client configuration. Never put the password on a shared command line or disable TLS verification.

```
curl -I http://depot.example/
curl --cacert depot-ca.pem -I https://depot.example/
curl --cacert depot-ca.pem -u depotuser https://depot.example/PROD/metadata/productVersionCatalog/v1/productVersionCatalog.json
```

Replace the example hostname, CA file, account name, and content path with values for your deployment. In Step 4, before the production certificate is installed, use the independently verified certificate trust for the appliance's current HTTPS identity. Repeat the request after Step 5 with the new CA chain.

## Clear retained deployment secrets

1. Confirm the GUI and console credentials are stored and tested.
2. In vCenter or the ESXi Host Client, open the VM's vApp or OVF property settings.
3. Clear the retained values of the web, console recovery, and Ubuntu maintenance password deployment properties after initialization.
4. Do not remove unrelated vApp properties, the NTP property, or VMware Tools integration.
5. Save the VM settings.
6. Confirm administrator and console authentication still work. Changing original deployment properties is not a password-reset mechanism.

## Deployment acceptance checklist

| Check | Result | Evidence |
|---|---|---|
| OVA checksum, signature, and release identity verified | | |
| VM has expected CPU, memory, VMXNET3, and two disks | | |
| First boot completed without required-service failures | | |
| Hostname, FQDN, address, prefix, gateway, and DNS verified | | |
| NTP synchronization active and timezone saved independently | | |
| Planned reboot retains timezone and restarts depot automatically | | |
| HTTPS administrator login succeeds | | |
| Second named administrator login succeeds | | |
| Expected VCF Download Tool version installed | | |
| Tool authorization and entitlement succeed | | |
| Binary download completes and inventory shows expected release and content | | |
| Dedicated depot account created and tested | | |
| Production certificate chain and SANs trusted | | |
| Anonymous depot access rejected | | |
| Authenticated depot retrieval succeeds | | |
| VCF Installer status is Connected | | |
| VCF Installer metadata and binary retrieval succeed | | |
| Deployment password properties cleared | | |

Record vault references rather than passwords.

<!-- pagebreak -->

## Deployment troubleshooting

| Symptom | Likely cause | Action |
|---|---|---|
| OVA deployment is rejected | Unsupported virtual hardware, corrupt OVA, or insufficient permissions | Verify checksum, compatibility, permissions, and the selected compute target |
| Required OVF properties are missing in ESXi | Host Client version does not expose the complete property set | Do not power on; deploy through vCenter or another approved property-preserving method |
| First boot does not complete | Invalid property, network failure, disk initialization failure, or failed service | Preserve the console message and correct the named failure before retrying |
| GUI is unreachable | Wrong port group, address, route, DNS, or failed service | Check the console network state and select **Show service status** |
| FQDN does not resolve | Missing or incorrect DNS record | Correct forward DNS and verify it with the configured resolver |
| Time is not synchronized | NTP unreachable or its hostname cannot resolve | Verify DNS, routing, UDP 123 policy, and the NTP status in **Appliance Status** |
| Console recovery fails | Incorrect recovery password or retry delay | Use the separate recovery password and wait for the retry delay |
| HTTPS returns 401 | Expected without a depot account | Test with a valid depot account credential |
| Certificate warning persists | Wrong SAN, incomplete chain, untrusted root, or incorrect clock | Verify identity, chain, DNS, NTP, and the installed root CA |
| Depot disk is unavailable | Missing blank data disk, mount failure, or storage issue | Stop content operations and investigate before writing or formatting |
| VCF Installer configuration is blocked | A required preflight, SSH, trust, restart, or retrieval check failed | Open the detailed result, correct the first failed dependency, and rerun the workflow |
| Test passes but status is not Connected | Preflight passed but endpoint configuration has not completed | Run Configure endpoint and wait for restart and post-change verification |
