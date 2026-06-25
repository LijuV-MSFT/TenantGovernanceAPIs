# Tenant Governance APIs

This repository contains Microsoft Graph API and Microsoft Graph PowerShell examples for working with Microsoft Entra Tenant Governance.

The examples show how to use Tenant Governance APIs to:

- Enable tenant discovery
- Read Tenant Governance settings
- Create role-assignable groups for delegated administration
- Create a multi-tenant application for monitoring or management
- Create and update governance policy templates
- Create governance invitations and governance requests
- Accept governance requests
- Query and terminate governance relationships

## Authentication and permissions

Tenant Governance Microsoft Graph APIs currently use delegated Microsoft Graph permissions. The Tenant Governance API permission tables list application permissions as not supported for the Tenant Governance operations used in these examples.

This means the examples are intended to be run in a delegated user context, such as:

```powershell
Connect-MgGraph -Scopes "<required delegated permission>"
```

The signed-in user must also have a supported Microsoft Entra role for the operation. For many Tenant Governance operations, Microsoft lists **Tenant Governance Administrator** as the least privileged supported role. Some operations also support **Tenant Governance Relationship Administrator**.

> [!IMPORTANT]
> Do not use app-only authentication or client credentials for these Tenant Governance examples unless the specific Microsoft Graph API permission table for that operation explicitly lists application permissions as supported.

## Microsoft Graph beta API notice

The Tenant Governance APIs used in these examples are under the Microsoft Graph `/beta` endpoint.

Microsoft Graph beta APIs are subject to change and are not supported for production use.

## Repository contents

| File | Description |
| --- | --- |
| `TenantGovernance.md` | End-to-end Tenant Governance walkthrough and sample Microsoft Graph calls. |

## Prerequisites

- Microsoft Graph PowerShell SDK
- Access to Microsoft Graph `/beta` APIs
- A signed-in work or school account with the required delegated Graph permissions
- A supported Microsoft Entra role for the Tenant Governance operation being performed

## Key references

- Tenant Governance overview:  
  https://learn.microsoft.com/en-us/graph/api/resources/tenantgovernanceservices-tenantgovernance-overview?view=graph-rest-beta

- Tenant Governance settings:  
  https://learn.microsoft.com/en-us/graph/api/resources/tenantgovernanceservices-tenantgovernancesetting?view=graph-rest-beta

- Update Tenant Governance settings:  
  https://learn.microsoft.com/en-us/graph/api/tenantgovernanceservices-tenantgovernancesetting-update?view=graph-rest-beta

- Enable related tenants:  
  https://learn.microsoft.com/en-us/graph/api/tenantgovernanceservices-tenantgovernancesetting-enablerelatedtenants?view=graph-rest-beta

- Governance policy template resource:  
  https://learn.microsoft.com/en-us/graph/api/resources/tenantgovernanceservices-governancepolicytemplate?view=graph-rest-beta

- Create governance policy template:  
  https://learn.microsoft.com/en-us/graph/api/tenantgovernanceservices-post-governancepolicytemplates?view=graph-rest-beta

- Update governance policy template:  
  https://learn.microsoft.com/en-us/graph/api/tenantgovernanceservices-governancepolicytemplate-update?view=graph-rest-beta

- Governance invitation resource:  
  https://learn.microsoft.com/en-us/graph/api/resources/tenantgovernanceservices-governanceinvitation?view=graph-rest-beta

- Create governance invitation:  
  https://learn.microsoft.com/en-us/graph/api/tenantgovernanceservices-post-governanceinvitations?view=graph-rest-beta

- Governance request resource:  
  https://learn.microsoft.com/en-us/graph/api/resources/tenantgovernanceservices-governancerequest?view=graph-rest-beta

- Create governance request:  
  https://learn.microsoft.com/en-us/graph/api/tenantgovernanceservices-post-governancerequests?view=graph-rest-beta

- List governance requests:  
  https://learn.microsoft.com/en-us/graph/api/tenantgovernanceservices-list-governancerequests?view=graph-rest-beta

- Update governance request:  
  https://learn.microsoft.com/en-us/graph/api/tenantgovernanceservices-governancerequest-update?view=graph-rest-beta

- Governance relationship resource:  
  https://learn.microsoft.com/en-us/graph/api/resources/tenantgovernanceservices-governancerelationship?view=graph-rest-beta

- List governance relationships:  
  https://learn.microsoft.com/en-us/graph/api/tenantgovernanceservices-list-governancerelationships?view=graph-rest-beta

- Update governance relationship:  
  https://learn.microsoft.com/en-us/graph/api/tenantgovernanceservices-governancerelationship-update?view=graph-rest-beta

- Microsoft Graph PowerShell SDK:  
  https://learn.microsoft.com/en-us/powershell/microsoftgraph/overview

- Use Microsoft Graph PowerShell with beta APIs:  
  https://learn.microsoft.com/en-us/powershell/microsoftgraph/use-beta

- Microsoft Graph delegated and application permissions:  
  https://learn.microsoft.com/en-us/graph/auth/auth-concepts#delegated-and-application-permissions
