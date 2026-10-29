# AD DS troubleshooting evidence and safe fault-injection plan

Updated 29 October 2026. Compiled with AI assistance from existing public README evidence; **new fault tests are not run**. No connection to Windows, Azure or AD was made for this update.

## Recorded lab evidence (historical)
The README documents DC-1 (Windows Server 2022), Client-1 (Windows 10), common Azure VNet, a static private IP for the controller, Client-1 pointing to the controller for DNS, Windows Firewall ICMPv4 allowance, a successful private-IP ping, AD DS promotion, domain join and tested domain sign-in. A user-generation PowerShell script is credited to an external author; this is not all-original automation.

A **separate** repository, [azure-network-protocols](https://github.com/LawrenceDavy/azure-network-protocols), records file-share access denial to a normal user and later ACCOUNTANTS group membership followed by another access attempt. Its current title promises network traffic/NSG/Wireshark but contents are mostly file-share ACL walkthrough: correct that mismatch separately before featuring it as packet-analysis evidence. This existing access-control exercise is not a fresh deliberate break/fix case.

## Planned fault A: wrong client DNS - NOT RUN
Business symptom: a domain-joined user cannot discover the domain controller or access directory-dependent services.

1. On a disposable Client-1, record current settings and successful lookup using PowerShell:
    ipconfig /all
    Resolve-DnsName -Name _ldap._tcp.dc._msdcs.mydomain.com -Type SRV
    nltest /dsgetdc:mydomain.com
2. Save the original client DNS setting and preserve local-admin/rollback access.
3. Deliberately set the **client** to a resolver that does not host the lab AD zone; do not change the controller. Restart/renew DNS settings only as needed.
4. Repeat the exact tests and record what fails. Ping by IP may still work and is not adequate evidence of DNS health.
5. Compare actual DNS server addresses with the design. Restore the previous controller DNS setting through the same interface you changed.
6. Confirm restored SRV lookup and controller discovery; capture successful domain login where possible.

## Planned fault B: test-share permissions - NOT RUN AS NEW TEST
Create an isolated share with separate SMB share and NTFS permissions. Document unauthorized user denial, add only the appropriate test security group, sign out/in to refresh the user's token, verify authorized access and continued denial to an unrelated user, then revoke permissions and clean up. Record actual ACCESS DENIED text and effective group membership. Never grant Domain Admin membership merely to make access work.

## Evidence table to complete after a real session
Date / setup: NOT RECORDED.
Known-good DNS and auth results: NOT RECORDED.
Misconfiguration and actual error: NOT RUN.
Tests used to isolate cause: NOT RUN.
Fix, rollback and independent post-fix validation: NOT RUN.
Screenshots or logs with sensitive data removed: NOT CAPTURED.
Azure teardown / cost: NOT VERIFIED.

## Security and production differences
A single-controller training domain is not high-availability AD. Broad RDP access for Domain Users and overly privileged Domain Admin accounts are not production-safe defaults. Do not publish passwords, tenant data or real identities.

Original evidence: [AD README](README.md). This file is a validation plan, **not new proof of completed troubleshooting**.
