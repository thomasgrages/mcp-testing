# IL6 MDI TASKORD - Proposed Edits for Azure Government Secret Parity

**Document Under Review:** IL6 - Microsoft Defender for Identity Deployment Configuration Guide Ver 1 Rel 1  
**Prepared for:** DCDC  
**Original Author:** Hannah Snesil, Cloud Solution Architect  
**Review Date:** 3/19/2026  
**Reviewer:** Thomas Grages  
**Reference Source:** Azure Government Secret MDI Documentation (review.learn.microsoft.com)

---

## Executive Summary

This document details proposed edits to the MDI TASKORD deployment guide to align it with the Azure Government Secret (IL6) environment. The current guide contains several references to commercial and GCC High/DoD endpoints that are incorrect for Azure Government Secret. Additionally, feature parity gaps documented in the official Gov Secret MDI page are not reflected in the TASKORD.

All findings are prioritized P0 (Critical), P1 (Important), or P2 (Moderate).

---

## P0 - CRITICAL CHANGES

### 1. Add Azure Government Secret Portal URL Throughout Document

**Current State:** The document references "Microsoft Defender XDR" portal multiple times but never specifies the correct URL for Azure Government Secret.

**Proposed Edit:** Add the following portal URLs wherever the document directs the user to the Microsoft Defender portal:

| Environment | Microsoft Defender Portal | Sensor (Agent) Endpoint |
|---|---|---|
| Azure Government Secret | `https://security.microsoft.scloud` | `<your-workspace-name>sensorapi.atp.azure.microsoft.scloud` |
| DoD | `security.microsoft.us` | `<your-workspace-name>sensorapi.atp.azure.us` |
| GCC-H | `security.microsoft.us` | `<your-workspace-name>sensorapi.atp.azure.us` |
| GCC | `security.microsoft.com` | `<your-workspace-name>sensorapi.gcc.atp.azure.com` |

**Affected Sections:**
- Section 2.1 (MDI Prerequisites)
- Section 2.10.1 (Download the MDI sensor - "In Microsoft Defender XDR, go to Settings > Identities")
- Section 2.11 (Configure Microsoft Defender for Identity sensor settings - "In Microsoft Defender XDR, go to Settings > Identities > Sensors")
- Section 2.12.2 (Verify latest available sensor version - "Microsoft Defender XDR Settings > Identities > About page")

---

### 2. Correct Sensor API URL in Section 2.3.1

**Current Text (Section 2.3.1 - Enable access to Defender for Identity service URLs in the proxy server):**

> To enable access to Defender for Identity, make sure to allow traffic to the sensor URL, using the following syntax: `<your-workspace-name>sensorapi.atp.azure.com`. For example, `contoso-corpsensorapi.atp.azure.us`.

**Proposed Replacement:**

> To enable access to Defender for Identity in the Azure Government Secret environment, make sure to allow traffic to the sensor URL, using the following syntax: `<your-workspace-name>sensorapi.atp.azure.microsoft.scloud`.
>
> **Note:** The `.atp.azure.com` endpoint is for commercial environments and `.atp.azure.us` is for GCC-H/DoD. Azure Government Secret uses the `.microsoft.scloud` domain exclusively.

---

### 3. Correct SSL Wildcard URLs in Port Tables (Tables 5 and 6)

**Current Text (Table 5 - Required ports, Internet ports row):**

| Protocol | Transport | Port | From | To |
|---|---|---|---|---|
| SSL (*.atp.azure.us) Alternately, configure access through a proxy. | TCP | 443 | Defender for Identity sensor | Defender for Identity cloud service |

**Proposed Replacement:**

| Protocol | Transport | Port | From | To |
|---|---|---|---|---|
| SSL (*.atp.azure.microsoft.scloud) | TCP | 443 | Defender for Identity sensor | Defender for Identity cloud service |

**Same change required in Table 6 (multi-forest scenarios):**

| Protocol | Transport | Port | To/From | Direction |
|---|---|---|---|---|
| SSL (*.atp.azure.microsoft.scloud) | TCP | 443 | Defender for Identity cloud service | Outbound |

---

### 4. Correct Browser Connectivity Test URL in Section 2.4.1

**Current Text (Section 2.4.1):**

> Browse to the following URL: `https://<your_workspace_name>sensorapi.atp.azure.com/tri/sensor/api/ping`. Replace `<your_workspace_name>` with the name of your Defender for Identity workspace.

**Proposed Replacement:**

> Browse to the following URL: `https://<your_workspace_name>sensorapi.atp.azure.microsoft.scloud/tri/sensor/api/ping`. Replace `<your_workspace_name>` with the name of your Defender for Identity workspace.

---

### 5. Correct PowerShell Connectivity Test in Section 2.4.2

**Current Text (Section 2.4.2):**

> Test-MDISensorApiConnection -BypassConfiguration -SensorApiUrl 'https://contososensorapi.atp.azure.us'

**Proposed Replacement:**

> For Azure Government Secret, use the following command to test connectivity:
>
> `Test-NetConnection <your-workspace-name>sensorapi.atp.azure.microsoft.scloud -port 443`
>
> MDI endpoint to validate:
>
> `https://<your-workspace-name>sensorapi.atp.azure.microsoft.scloud`

---

### 6. Correct Connectivity Disclaimer in Section 2.1.3

**Current Text (Section 2.1.3 - Connectivity Requirements):**

> Disclaimer: You may have to allow *atp.azure.us through your proxy instead *.atp.azure.com.

**Proposed Replacement:**

> **Important:** For Azure Government Secret (IL6) environments, all proxy and firewall rules must allow `*.atp.azure.microsoft.scloud` instead of `*.atp.azure.com` (commercial) or `*.atp.azure.us` (GCC-H/DoD).

---

### 7. Correct Service Tag File References

**Current Text (Section 2.1.3 and Section 2.3.2):**

> download the Azure IP Ranges and Service Tags – Public Cloud file and use the AzureAdvancedThreatProtection service tag

**Proposed Replacement:**

> You can also use the IP address ranges in the Azure service tag `AzureAdvancedThreatProtection` to enable access to Defender for Identity. For more information about service tags, see the Virtual Network Service Tags public Microsoft documentation.
>
> **Note:** For Azure Government Secret, use the service tag file appropriate to the Gov Secret cloud environment rather than the Public Cloud file.

---

## P1 - IMPORTANT CHANGES

### 8. NEW SECTION: Feature Parity with Commercial (Add After Executive Summary or Before Appendix A)

**Proposed New Section:**

> ## Feature Parity with Commercial
>
> Defender for Identity for Azure Government Secret customers doesn't have complete parity with the commercial offering. While the goal is to deliver all commercial features and functionality to Azure Government Secret customers, there are some capabilities not yet available.
>
> ### Features Available in Azure Government Secret
>
> | Category | Feature | Available |
> |---|---|---|
> | Alerts | Native MDI alerts | Yes |
> | Alerts | Streaming MDI alerts | Yes |
> | Alerts | Alert Exclusion/Tuning | Yes |
> | Experiences | Global Search | Yes |
> | Experiences | Identities page (Inventory) | Yes |
> | Experiences | ITDR dashboard | Yes |
> | Experiences | ITDR deployment health widget on homepage | Yes |
> | Horizontals | URBAC support | Yes |
> | Horizontals | Action center | Yes |
> | Horizontals | Unified Audit | Yes |
> | Hunting | Identity/Events tables | Yes |
> | Hunting | IdentityInfo table | Yes |
> | Posture | ISPMs | Yes |
> | Reports | Download and Schedule MDI reports | Yes |
> | Response | Identity response actions | Yes |
> | Deployment | Version 3 Sensor | Yes |
> | Deployment | Public APIs (Graph) | Yes |
> | Deployment | Sensor management | Yes |
> | Settings | Notifications for health issues | Yes |
> | Settings | Syslog forwarding (MDI only) | Yes |
> | Settings | Global exclusions | Yes |
> | Settings | Tagging (Honeytoken, Sensitive, Exchange server) | Yes |
> | Settings | VPN (Radius accounting) | Yes |
> | Settings | Alert thresholds tuning | Yes |
>
> ### Known Gaps (NOT Available in Azure Government Secret)
>
> The following capabilities are **not currently available** in the Azure Government Secret environment. DAOs should plan their deployment accordingly and not expect these features to function:
>
> | Feature | Status |
> |---|---|
> | LSDA Connectivity | Not Available |
> | RAW/FIM partner integration | Not Available |
> | Entra ID alerts | Not Available |
> | Copilot Identity skills | Not Available |
> | Multi-tenants support | Not Available |
> | Attack path includes identity data | Not Available |
> | Identity criticality management | Not Available |
> | Identity immune capabilities | Not Available |
> | Metrics and initiatives | Not Available |
> | Attack distribution identity exclusions | Not Available |
>
> **Important:** The absence of Entra ID alerts means that detections relying on Entra ID signal integration will not trigger in the Azure Government Secret environment. DAOs should implement alternative monitoring for Entra ID-related identity threats.

---

### 9. ADD: Specify Version 3 Sensor in Section 2.10

**Current State:** Section 2.10 (Download and install the MDI sensor) does not specify which sensor version is supported in Azure Government Secret.

**Proposed Edit:** Add the following note to Section 2.10:

> **Note:** Azure Government Secret supports the MDI Version 3 Sensor. Ensure you download the latest sensor package from the Azure Government Secret Defender portal (`https://security.microsoft.scloud`).

---

### 10. NEW SECTION: How to Create a Defender Support Request (Add Before Appendix A)

**Proposed New Section:**

> ## Creating a Defender Support Request
>
> For detailed instructions on how to create a Defender support case in the Azure Government Secret environment, see the Microsoft Defender Support Request process. Support requests for MDI issues in Azure Government Secret should be filed through the appropriate classified support channel.

---

### 11. Correct AD FS Events Section Mislabeling (Section 2.5.2)

**Current Text (Section 2.5.2):**

> **Required Active Directory Federation Services (AD FS) events**
>
> The following events are required for Active Directory Certificate Services (AD CS) servers:

**Issue:** The heading says "AD FS events" but the body lists AD CS events. The actual AD FS events are missing.

**Proposed Edit:** Split into two properly labeled subsections:

> **Required Active Directory Federation Services (AD FS) events**
>
> [Add the correct AD FS required events - reference the current Microsoft Learn documentation for the complete list]
>
> **Required Active Directory Certificate Services (AD CS) events**
>
> The following events are required for Active Directory Certificate Services (AD CS) servers:
> - 4870: Certificate Services revoked a certificate
> - 4882: The security permissions for Certificate Services changed
> - 4885: The audit filter for Certificate Services changed
> - 4887: Certificate Services approved a certificate request and issued a certificate
> - 4888: Certificate Services denied a certificate request
> - 4890: The certificate manager settings for Certificate Services changed
> - 4896: One or more rows have been deleted from the certificate database

---

### 12. Add 90-Day Feature Lag Disclaimer

**Proposed Edit:** Add to Executive Summary or new Feature Parity section:

> **Note:** New feature releases, including preview features, documented in "What's new with Defender for Identity" will be available in Azure Government Secret within 90 days of release in the commercial environment. Preview features may not be supported in the Azure Government Secret environment.

---

## P2 - MODERATE CHANGES

### 13. Add Stricter Dynamic Memory Requirement (Section 2.2.3)

**Current Text (Table 7 - Dynamic memory requirements):**

Generic guidance about enabling/disabling dynamic memory per hypervisor.

**Proposed Edit:** Add the following Important callout after Table 7:

> **Important:** When running as a virtual machine in the Azure Government Secret environment, all memory must be allocated to the virtual machine at all times. Dynamic memory allocation is not supported for MDI sensors in IL6 environments.

---

### 14. Add Gov Secret Documentation to Appendix C

**Current State:** All Appendix C URLs reference commercial documentation only.

**Proposed Edit:** Add the following row to the beginning of the Appendix C table:

| Topic | URL |
|---|---|
| Microsoft Defender for Identity Azure Government Secret | https://review.learn.microsoft.com/en-us/microsoft-government-secret/azure/azure-government-secret/services/security/defender-for-identity (internal - requires authentication) |

---

### 15. ExpressRoute BGP Community Value Caveat (Section 2.1.3)

**Current Text:**

> ExpressRoute can be configured to forward MDI sensor traffic over customer's express route. To route network traffic destined to the Defender for Identity cloud servers use ExpressRoute Microsoft peering and add the Microsoft Defender for Identity (12076:5220) service BGP community to your route filter.

**Proposed Edit:**

> ExpressRoute can be configured to forward MDI sensor traffic over customer's express route. To route network traffic destined to the Defender for Identity cloud servers use ExpressRoute Microsoft peering and add the Microsoft Defender for Identity (12076:5220) service BGP community to your route filter.
>
> **Note:** The BGP community value `12076:5220` is documented for commercial ExpressRoute peering and is not explicitly listed in the Azure Government national clouds BGP community table. DAOs should validate this BGP community value is available in their Azure Government Secret ExpressRoute peering using the `Get-AzBgpServiceCommunity` PowerShell cmdlet before relying on this routing method. The proxy/firewall approach using `*.atp.azure.microsoft.scloud` is recommended as the primary connectivity method for IL6.

---

### 16. Clarify gMSA Domain Placeholder (Section 2.7)

**Current Text:**

```powershell
$DNS = 'MDIgMSA.<domain.mil>'
```

**Proposed Edit:** Add a note clarifying:

> **Note:** Replace `<domain.mil>` with your organization's SIPRNet domain. Azure Government Secret operates on classified networks connected to SIPRNet.

---

### 17. Add GitHub Sizing Tool Access Note (Section 2.2.6)

**Current Text:**

> Link to sizing tool: GitHub - microsoft/Microsoft-Defender-for-Identity-Sizing-Tool

**Proposed Edit:**

> Link to sizing tool: GitHub - microsoft/Microsoft-Defender-for-Identity-Sizing-Tool
>
> **Note:** GitHub may not be accessible from IL6/SIPRNet networks. Obtain the sizing tool through approved channels or download it from an unclassified network and transfer via approved cross-domain solution.

---

## Summary of All Proposed Edits

| # | Priority | Section | Change Description |
|---|---|---|---|
| 1 | P0 | Throughout | Add `security.microsoft.scloud` portal URL |
| 2 | P0 | 2.3.1 | Change sensor URL to `.atp.azure.microsoft.scloud` |
| 3 | P0 | Tables 5 & 6 | Change SSL wildcard to `*.atp.azure.microsoft.scloud` |
| 4 | P0 | 2.4.1 | Change browser test URL to `.microsoft.scloud` |
| 5 | P0 | 2.4.2 | Change PowerShell test to Gov Secret endpoint |
| 6 | P0 | 2.1.3 | Update connectivity disclaimer for Gov Secret |
| 7 | P0 | 2.1.3, 2.3.2 | Correct service tag file reference |
| 8 | P1 | New section | Add Feature Parity table with known gaps |
| 9 | P1 | 2.10 | Specify Version 3 Sensor support |
| 10 | P1 | New section | Add Defender support request guidance |
| 11 | P1 | 2.5.2 | Fix AD FS/AD CS event section mislabeling |
| 12 | P1 | Exec Summary | Add 90-day feature lag disclaimer |
| 13 | P2 | 2.2.3 | Add stricter dynamic memory requirement |
| 14 | P2 | Appendix C | Add Gov Secret doc link |
| 15 | P2 | 2.1.3 | Add ExpressRoute BGP caveat |
| 16 | P2 | 2.7 | Clarify SIPRNet domain placeholder |
| 17 | P2 | 2.2.6 | Add GitHub accessibility note for IL6 |

---

*End of Proposed Edits Document*
