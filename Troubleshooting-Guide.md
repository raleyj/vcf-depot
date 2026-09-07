# VCF Offline Depot
## Troubleshooting and Appliance Update Guide

**Release baseline:** Appliance 1.1.0 - distribution build 2026-09-07.1

**Documentation revision:** 1.9 — 7 September 2026

**Release status:** Version 1.1.0 maintenance release. Consult the signed release record for validation of this exact OVA. VCF Installer remains the supported integration scope; SDDC Manager and Fleet Manager qualification remains deferred.

**Audience:** Appliance administrators diagnosing failures, restoring service, or installing a future signed release

**Use:** Follow the symptom procedures in order, preserve evidence before changing state, and use the update workflow for every future appliance release

## Bootable ESX installer media

Choose **Install, including ESXi media** when downloading installation binaries. After the main VCF download, the appliance reads the release bill of materials and product catalog, then makes a separate VCF Download Tool request for the matching x86-64 bootable ESX ISO bundle ID. This includes installer media cataloged as PATCH instead of INSTALL. No separate manual ISO upload is required for an available entitled catalog entry.

The job requires the expected ISO filename, size, and SHA-256 checksum before reporting success. For VCF 9.1.1, the catalog entry is `VMware-VMvisor-Installer-9.1.1.0.25714478.x86_64.iso`. A missing, ambiguous, or corrupt ISO fails the job instead of accepting a zero-result request. Correct the cause and retry the same release; the tool reuses existing content. Look under the ESX_HOST component in **Depot Files**. A fresh activation code is required for each new job and is removed after use.

## Navigation in this build

The header provides **Authorize & Download**, **VCF Connections**, **Depot Files**, **Depot Management**, **Connection Logs**, and **Appliance Status**. During initial setup, **Prepare Depot** provides the guided steps for the Download Tool, authorization, Depot Accounts, HTTPS Certificate, and VCF Connections. After a saved endpoint becomes Connected, the setup workflow is hidden.

**Depot Management** remains available before and after setup. Its three cards open Download Tool, Depot Accounts, and HTTPS Certificate. Use **Back to Depot Management** to return. Maintenance does not require disconnecting or deleting a working endpoint. After rotating depot credentials or renewing the certificate, review affected clients in **VCF Connections** and verify authenticated retrieval.

**Depot Files** opens the **Depot File Management** page for browsing, uploading, and managing release files. **Appliance Status** reports health and effective settings. The administrator menu contains **Appliance management**, **Appliance updates**, and **Admin user management**; these pages leave the primary header links unhighlighted.
## Safety rules

1. Use the management GUI first when it is available.
2. Keep VM console access available before network, certificate, authentication, or update work.
3. Record the appliance version, time, symptom, and last known good state before making changes.
4. Run one disruptive operation at a time.
5. Do not repeatedly reboot, restart services, reapply endpoint configuration, or retry an update while an operation is active.
6. Never disable TLS validation, authentication, update signatures, or package checks to make a failure pass.
7. Do not edit application files, certificate files, VCF Installer properties, or update history manually unless an approved recovery procedure explicitly requires it.
8. Redact passwords, tokens, cookies, authorization headers, and private keys from screenshots and logs.

## Start with these checks

### Check the GUI

1. Open `https://<appliance-fqdn>/admin/` from an authorized workstation.
2. Confirm the browser resolves the expected address.
3. Inspect the served certificate identity and expiration.
4. Sign in with a named administrator.
5. Review the current status message on the affected page.
6. Open detailed results instead of relying only on a red or blocked summary.
7. Record the exact error, phase, timestamp, and affected endpoint or job.

### Check the VM console

1. Open the console from vCenter or the ESXi Host Client.
2. Confirm the appliance menu shows the expected version, hostname, and management address.
3. Select **Show service status** and record the status of nginx, management, and UFW.
4. Compare the address with the deployment record and VMware Tools guest information.
5. If web access is unavailable because of credentials, use the password-recovery procedure in this guide. No Ubuntu login is required.

## GUI cannot be reached

1. Confirm the VM is powered on and VMware Tools reports a guest address.
2. Verify that the VM network adapter is connected and mapped to the intended port group.
3. From the console, confirm the expected IP address and prefix are present.
4. Confirm the default route points to the intended gateway.
5. Confirm the configured DNS server resolves the appliance FQDN.
6. From an authorized workstation, test IP reachability and TCP 443.
7. Test both the FQDN and IP address to distinguish DNS from routing or service failure.
8. Use **Show service status** on the VM console and record any inactive service.
9. Capture service state and logs before an approved restart.
10. If an appliance restart is approved, use the authenticated console restart option, then retest HTTPS, authentication, depot access, and VCF connectivity.

Do not repeatedly reboot. Reboots can remove transient evidence and complicate incomplete filesystem or update work.

## Administrator sign-in fails

1. Confirm you are using a web administrator account rather than a depot or Ubuntu account.
2. Verify Caps Lock, keyboard layout, username spelling, and the correct appliance FQDN.
3. Retry in a new private browser session to eliminate a stale session or cookie.
4. Confirm the appliance clock is correct.
5. Ask another named administrator to verify that the account remains enabled.
6. Use the supported Admin user management password-reset workflow after verifying the requester's identity.
7. If no web administrator can sign in, use the console recovery procedure below. Do not edit the credential database manually.

## Depot requests return 401 or 403

1. Confirm the client is using a depot account, not a GUI administrator.
2. Confirm the request uses HTTPS and the intended depot URL.
3. Verify the depot account exists and has not been removed or rotated.
4. Re-enter the current password through an approved credential prompt.
5. Test a small metadata object directly.

```
curl --cacert depot-ca.pem -u depotuser https://depot.example/PROD/metadata/productVersionCatalog/v1/productVersionCatalog.json
```

6. Confirm an anonymous HTTPS request returns 401. This is expected and proves content is protected.
7. If direct retrieval succeeds but VCF Installer fails, update the selected depot credential in the connection workflow and retest.
8. After rotation, confirm the old credential fails and the new credential succeeds.

## Certificate warning or TLS failure

1. Confirm the workstation and appliance clocks are correct.
2. Confirm DNS resolves the name used in the browser or VCF Installer.
3. Inspect the served certificate subject, SANs, issuer, expiration, and fingerprint.
4. Confirm the certificate contains the exact FQDN used by clients.
5. Confirm the server presents the required intermediate certificates.
6. Confirm the issuing root is trusted by the administrator workstation and VCF Installer.
7. Compare the served fingerprint with the value displayed on the appliance.
8. If the certificate is wrong or expired, generate a new CSR and activate a correctly signed chain.
9. Reconnect with a new browser session and rerun depot and VCF Installer checks.

Never bypass certificate validation as a permanent workaround.

## DNS or NTP failure

1. Open **Appliance management** and record configured and effective values.
2. Confirm the DNS server is not mistakenly set to the default gateway unless that gateway is an approved resolver.
3. Review the effective DNS servers in **Appliance management**. If the GUI is unavailable, ask the network administrator to verify resolver reachability.
4. Resolve the appliance FQDN, VCF Installer FQDN, NTP names, and required vendor endpoints.
5. Confirm the default route and firewall permit the required traffic.
6. Open **Appliance Status**, select **Refresh status**, and review NTP synchronization and the current time source.
7. Correct DNS before diagnosing an NTP hostname failure.
8. Correct time before diagnosing certificates, signed updates, or token expiration.
9. Retest the original workflow after time synchronization becomes active.

`getaddrinfo EAI_AGAIN` means name resolution could not complete. Verify the configured resolver, search behavior, and endpoint record from the appliance.

### Set the appliance timezone

1. Open the administrator account menu and select **Appliance management**.
2. In **Timezone**, choose an installed region and city, such as **America/New_York**, or **Etc/UTC**.
3. Select **Save timezone** and confirm the current-timezone message matches your selection.
4. Open **Appliance Status**, select **Refresh status**, and verify the timezone under **Time synchronization**.
5. Record the setting. After the next planned reboot, verify it is retained.

The sealed OVA defaults to **Etc/UTC**. Saving the timezone is independent of **Apply configuration**: it does not apply pending identity or network edits, change the NTP server list, or restart NTP. NTP synchronizes the clock independently of the timezone. Configure up to two NTP sources in **DNS and time**, then use **Apply configuration** for those changes.

The inventory UPDATED badge uses the browser workstation's local date and time. Changing the appliance timezone does not change the workstation timezone; UTC audit timestamps remain UTC. Record the timezone when correlating events.

## Timezone or timestamp appears incorrect

1. Compare the selected timezone in Appliance management with Time synchronization in Appliance Status.
2. If the list is unavailable, select **Reload current settings** and wait for the installed timezone choices to load. Record any error if the selector stays disabled.
3. Choose a listed timezone and use **Save timezone**, then verify its success message. Apply configuration does not save this separate selection.
4. If the inventory badge differs from the appliance's local time, check the workstation timezone. Its date and time are browser-local; UTC audit records remain UTC.
5. If NTP is not synchronized, investigate DNS, reachability, and configured NTP sources. Changing timezone does not repair synchronization.

## Depot service unavailable after first boot

The content container should start automatically with the management service in version 1.1.0. A manual container start is not an expected deployment step.

1. Confirm first boot completed and record the OVA build and checksum.
2. Review services and storage in **Appliance Status**, or use **Show service status** from the VM console if the GUI is unavailable.
3. Record the exact HTTP response. Anonymous HTTP 401 is expected authentication enforcement, not proof of a service failure.
4. Test a known downloaded metadata object with a depot account and trusted HTTPS identity.
5. If the service is unavailable, preserve service status, first-boot errors, and a support bundle from Appliance Status before an approved restart. Report the failure against this exact OVA.

## Inventory badge interpretation

Wait for the green **UPDATED** badge containing the refresh date and time. File count, total size, incomplete count, and free space appear beside the badge. UPDATED confirms that inventory refreshed; it does not certify a complete release download or successful endpoint retrieval.

A new appliance can report zero files and no incomplete files because its data disk is blank. If expected content is absent, check the download result and selected release before treating a successful inventory refresh as download completion.

## Depot-data disk or storage problem

1. Stop new downloads and inventory scans.
2. Determine whether pressure affects the operating-system disk, depot-data disk, or backing datastore.
3. Confirm the 1 TiB data disk remains attached to the VM.
4. Confirm the expected data filesystem is mounted.
5. Review free space and filesystem health without deleting files.
6. Confirm the datastore has capacity and no active storage alarm.
7. Do not format, initialize, or replace a disk that contains depot data.
8. Do not manually delete manifests, update backups, certificates, application state, or unknown files.
9. Expand storage or remove content only through an approved procedure.
10. Refresh inventory and verify retained releases after recovery.

## Inventory refresh does not complete

1. Confirm no download, update, certificate activation, or filesystem maintenance is running.
2. Wait for the active refresh to finish. Do not start duplicate scans.
3. Record the start time and any displayed job identifier.
4. Check data-disk mount health and available space.
5. Check management-service state and recent job logs.
6. Confirm the depot tree does not contain unexpected links, special files, or inaccessible paths.
7. Capture the failure details before restarting a service.
8. After recovery, run one refresh and compare release count, file count, and size with the expected content.

## Download fails or stops

1. Record the job identifier, selected release, current file, transferred size, and exact error.
2. Confirm vendor entitlement and the activation code is still valid.
3. Confirm the appliance can reach the required vendor endpoints.
4. Confirm DNS, NTP, and certificate validation are healthy.
5. Confirm sufficient data-disk and datastore capacity remains.
6. Correct the underlying connectivity, entitlement, or capacity issue.
7. Retry the same download. Verified files should be retained and only missing or invalid content downloaded again.
8. Stop and escalate repeated checksum failures, unexpected paths, or rapidly declining free space.
9. Refresh inventory after a successful retry and validate metadata plus one binary.

## VCF Installer connection is blocked

1. Open **VCF Connections** and select the affected VCF Installer.
2. Open the detailed connection result.
3. Resolve checks in dependency order: DNS, route, TCP, TLS identity and chain, API authentication, version detection, SSH, root elevation, depot authentication, and endpoint-originated retrieval.
4. Correct the first failed dependency before rerunning the test.
5. Verify the endpoint sign-in credential is current.
6. Verify SSH uses the `vcf` account. Direct root SSH is not expected.
7. Verify TCP 22 is reachable and the `vcf` credential works.
8. Verify the root password permits the approved elevation workflow.
9. Compare the detected SSH host key with an independently verified value.
10. Accept a changed host key only after confirming the endpoint identity and authorized key change.
11. Verify the selected depot account and password by retrieving a small object directly.
12. Rerun **Test connection** and retain the before-and-after results.

## Configure endpoint fails or remains in progress

1. Distinguish a passed connection test from a completed endpoint configuration.
2. Read the phase-specific status to identify property update, trust installation, service restart, stabilization wait, or post-change retrieval failure.
3. Do not click **Configure endpoint** again while services are restarting or the operation remains active.
4. Confirm SSH and root elevation still work.
5. Confirm the VCF Installer depot properties match the appliance URL, certificate trust, and selected depot account.
6. Confirm the required VCF Installer services restarted and became healthy.
7. Allow the displayed stabilization wait to complete.
8. Verify authenticated metadata and representative binary retrieval.
9. Treat the endpoint as complete only when the saved status is **Connected**.
10. If the operation fails, preserve the detailed phase result and service logs before retrying.

SDDC Manager and Fleet Manager are not production-supported integration targets in this release. Do not modify those appliances to imitate the VCF Installer property model.

## Update package is rejected before installation

1. Confirm the package was obtained from the approved release location.
2. Confirm the package and signed metadata belong to the same target release.
3. Read the validation result for signature, trusted key, manifest, checksum, path, package structure, version, or compatibility failure.
4. Confirm the appliance clock is correct.
5. Confirm the current appliance version is a supported source for the target release.
6. Recalculate the package checksum and compare it with the published value.
7. Obtain a fresh package if upload or transfer corruption is suspected.
8. Do not rename package contents, edit metadata, bypass signature verification, or install loose files.

## VCF Installer certificate retrieval or approval fails

**Why you may need this:** A new or rebuilt VCF Installer may serve a different certificate, or a connection may report that no issuing root CA is available.

1. Confirm the endpoint FQDN, HTTPS port, DNS result, routing, and appliance time.
2. In **VCF Connections**, confirm the endpoint details and select **Test connection** to retrieve its current certificate automatically. If private-CA options are visible, clear obsolete manual certificate selections before retrying.
3. For a self-signed endpoint, compare the displayed SHA-256 fingerprint with an independently trusted source. Accept only a match. A self-signed server certificate may have no separate root CA; do not upload an unrelated CA to work around this.
4. If the endpoint uses an untrusted private CA, obtain its correct server certificate and issuing root CA from the endpoint owner and use the manual certificate options that appear.
5. Correct an expired certificate or hostname/SAN mismatch on the endpoint. Retrieval does not make an invalid identity acceptable.
6. If you cancel the approval, no endpoint sign-in credentials are sent by the discovery workflow. Retry only after confirming the identity.
7. After a certificate replacement, run **Test connection** again. The changed fingerprint triggers a new approval prompt. A page reload or endpoint change also clears the previous approval.
8. Rerun **Test connection**, then configure the endpoint only if a configuration change is required. Keep SSH host-key verification separate from HTTPS certificate approval.

Never disable certificate verification or accept an unexpected fingerprint to bypass a failed check. The appliance's own HTTPS certificate is managed separately under **HTTPS Certificate**.

## Step-by-step future appliance update

### 1. Review the future release

1. Read the release notes completely.
2. Confirm the currently installed version is listed as a supported source release.
3. Review new features, fixed issues, known issues, expected service restarts, configuration changes, and rollback limitations.
4. Confirm the release supports every production integration currently in use.
5. Obtain the signed update package, metadata, checksum, and signing information from the approved source. An OVA is for a fresh deployment; do not upload an OVA to **Appliance updates**. Follow the future release notes for its supported source versions.
6. Verify the published checksum and signing identity before the change window.

### 2. Prepare the appliance

1. Schedule an approved maintenance window.
2. Notify affected administrators and VCF Installer owners of the expected interruption.
3. Sign in and record the current appliance version.
4. Confirm no content download, inventory refresh, certificate activation, network change, or VCF endpoint configuration is running.
5. Confirm the management GUI, DNS, NTP, certificate, depot authentication, and VCF Installer connection are healthy.
6. Record the current inventory count, certificate fingerprint, connected endpoint status, and update history.
7. Confirm adequate free space exists on the operating-system disk, depot-data disk, and backing datastore.
8. Confirm console access works and the approved rollback owner is available.
9. Take a VM snapshot only when organizational policy and the release notes permit it. Do not treat a snapshot as a replacement for the appliance's signed update and rollback controls.

### 3. Upload and validate the update

1. Open the administrator account menu.
2. Select **Appliance updates**.
3. Review the current version and retained prior release.
4. In **Upload and verify a release**, select the future signed `offline-depot-update-<version>.tar.gz` package.
5. Upload the files.
6. Wait for validation to finish.
7. Confirm signature, trusted signing key, manifest, checksums, safe paths, package completeness, declared version, and source-version compatibility all pass.
8. Stop if any validation fails. Preserve the result and obtain a corrected release artifact.
9. Review the staged target version and release notes shown by the appliance.

### 4. Install the update

1. Confirm the appliance is still idle.
2. Select **Apply update**.
3. Accept the displayed maintenance and restart notice.
4. Monitor each phase: protected backup creation, payload application, service restart, health validation, and activation.
5. Keep the browser open, but use the VM console if the GUI temporarily disconnects.
6. Do not refresh repeatedly, upload another package, restart the VM, or manually restart services while the update is active.
7. Wait for the appliance to report success or automatic rollback.

### 5. Validate the updated appliance

1. Sign in through a new browser session.
2. Confirm the displayed appliance version matches the target release.
3. Confirm no required service is failed.
4. Verify hostname, FQDN, address, route, DNS, NTP, and timezone.
5. Verify the active HTTPS certificate and administrator login.
6. Confirm administrator accounts, depot accounts, settings, and certificate material were retained.
7. Refresh inventory and compare it with the pre-update record.
8. Confirm anonymous HTTPS access remains rejected.
9. Confirm authenticated metadata and representative binary retrieval.
10. Test the production VCF Installer connection and confirm its saved status remains **Connected**.
11. Review update history and confirm the result, source version, target version, and timestamp are recorded.
12. Remove a temporary VM snapshot only after the validation period and according to policy.

### 6. Close the change

1. Record the package identity, checksum, source version, target version, operator, start and end times, and validation results.
2. Record any automatic or manual rollback.
3. Record residual issues and their owners.
4. Notify affected owners that service has returned.
5. Retain sanitized update and validation evidence according to policy.

## Update fails and automatically rolls back

1. Allow the automatic rollback to finish without interruption.
2. Sign in after services recover.
3. Confirm the appliance reports the prior version.
4. Review update history and the failing health-check detail.
5. Verify network, DNS, NTP, certificate, administrator login, accounts, inventory, depot retrieval, and VCF Installer status.
6. Confirm the protected prior runtime was restored completely.
7. Preserve logs and the rejected package metadata.
8. Do not retry until the failure cause is understood or a corrected package is available.

## Manual rollback to the retained release

Use manual rollback only when the new release is active but unhealthy and the retained prior release is listed as available.

1. Open **Appliance updates**.
2. Review the active and retained release versions.
3. Confirm no other operation is active.
4. Select the retained prior release.
5. Review the rollback warning and expected service restart.
6. Authorize the rollback during an approved maintenance window.
7. Monitor restoration, restart, and health validation.
8. Sign in and confirm the prior version is active.
9. Repeat the complete post-update validation checklist.
10. Record the reason, operator, timestamps, result, and follow-up owner.

Escalate if backup creation failed, both releases are unhealthy, rollback history is incomplete, or the restored state differs from the pre-update record.

## Evidence to collect before escalation

| Field | Required information | Secret handling |
|---|---|---|
| Appliance identity | Version, build, OVA checksum, source revision | No secrets |
| Incident window | Timezone, start time, last known good, detection time | No secrets |
| Impact | GUI, depot, download, certificate, update, storage, network, or VCF connection | Minimize customer data |
| Configuration | Sanitized hostname, network, DNS, NTP, certificate fingerprint | Remove credentials and keys |
| Failure | Exact message, phase, job or update identifier, endpoint type | Redact secrets |
| Logs | Narrow time window covering the failure | Redact tokens and headers |
| Actions | Restarts, changes, retry, update, or rollback | Use vault references |
| Current state | Healthy, degraded, isolated, rolled back, or unavailable | No secrets |

## Return-to-service checklist

1. Confirm the approved appliance version is active.
2. Confirm no required service is failed.
3. Verify hostname, FQDN, address, route, DNS, NTP, and timezone.
4. Verify the trusted HTTPS certificate and administrator login.
5. Confirm anonymous depot access is rejected.
6. Confirm authenticated metadata and representative binary retrieval.
7. Refresh inventory and compare expected content.
8. Confirm the production VCF Installer status is **Connected**.
9. Confirm update and audit history are intact.
10. Record approval, residual risk, monitoring plan, and follow-up owner.

## Reset a web password from the VM console

Use this procedure when no web administrator can sign in. It does not require an Ubuntu login and does not change depot client passwords.

1. Open the appliance console in vCenter or the ESXi Host Client.
2. Select **Reset web administrator password**.
3. Enter the separate console recovery password supplied during deployment.
4. Select the existing administrator account by number.
5. Enter the new web password twice. The configured password policy applies.
6. Type **RESET** to confirm. The management service briefly restarts; all web sessions are invalidated. The depot download service is not restarted.
7. Sign in through the browser using the new password.
8. Review administrator accounts and retain the recovery password in the approved password vault.

Failed recovery attempts are delayed, and the delay persists across menu restarts and appliance reboots. Inputs time out after 60 seconds. The recovery password cannot be displayed or retrieved. If it is lost, another web administrator can still manage web accounts; there is no unauthenticated recovery bypass. Protect hypervisor console and VM configuration permissions because a hypervisor administrator controls the appliance disks and boot environment.