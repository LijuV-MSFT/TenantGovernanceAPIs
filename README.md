# Tenant Discovery

<details>
<summary>
    
## Enable 
</summary>

**Graph call**
```
POST https://graph.microsoft.com/beta/directory/tenantGovernance/settings/enableRelatedTenants
```
**Graph call using PowerShell**
```
Connect-MgGraph -Scopes "TenantGovernance-Setting.ReadWrite.All"
Invoke-MgGraphRequest `
    -Method POST `
    -Uri "https://graph.microsoft.com/beta/directory/tenantGovernance/settings/enableRelatedTenants"
```
**Microsoft Graph PowerShell SDK cmdlets**
```
Import-Module Microsoft.Graph.Beta.Identity.DirectoryManagement
Enable-MgBetaDirectoryTenantGovernanceSettingRelatedTenant
```
    
</details>

<details>
<summary>
    
## Read

</summary>

**Graph call**
```
GET https://graph.microsoft.com/beta/directory/tenantGovernance/settings
```
**Graph call using PowerShell**
```
Connect-MgGraph -Scopes "TenantGovernance-Setting.Read.All"
Invoke-MgGraphRequest `
    -Method GET `
    -Uri https://graph.microsoft.com/beta/directory/tenantGovernance/settings
```
**Microsoft Graph PowerShell SDK cmdlets**
```
Import-Module Microsoft.Graph.Beta.Identity.DirectoryManagement
Get-MgBetaDirectoryTenantGovernanceSetting
```

</details>

# Governance policy Template

<details>
<summary>
    
## Create role-assignable group

</summary>

**Graph call**
```
POST https://graph.microsoft.com/v1.0/groups
{
    "description": "Global Administrator for Governed tenants",
    "displayName": "TG-Global Administrator",
    "isAssignableToRole": true,
    "mailEnabled": false,
    "mailNickname": "TGGlobalAdministrator",
    "securityEnabled": true
}
```
**Graph call using PowerShell**
```
Connect-MgGraph -Scopes "Group.ReadWrite.All", "RoleManagement.ReadWrite.Directory"

$body = @{
    displayName        = "TG-Global Administrator"
    description        = "Global Administrator for Governed tenants"
    mailEnabled        = $false
    securityEnabled    = $true
    mailNickname       = "TGGlobalAdministrator"
    isAssignableToRole = $true
} | ConvertTo-Json

$group = Invoke-MgGraphRequest `
    -Method POST `
    -Uri "https://graph.microsoft.com/v1.0/groups" `
    -Body $body `
    -ContentType "application/json"
```
**Microsoft Graph PowerShell SDK cmdlets**
```
Connect-MgGraph -Scopes `
    "Group.ReadWrite.All", `
    "RoleManagement.ReadWrite.Directory"

$group = New-MgGroup `
    -DisplayName "TG-Global Administrator" `
    -Description "Global Administrator for Governed tenants" `
    -MailEnabled:$false `
    -SecurityEnabled:$true `
    -MailNickName "TGGlobalAdministrator" `
    -IsAssignableToRole:$true
```
</details>

<details>
<summary>

## Create multi-tenant application

</summary>

* Name: HCMultiTenantMonitor
* Assign API (application) permissions
  * Directory.ReadWrite.All (19dbc75e-c2e2-444c-a770-ec69d8559fc7)
  * Policy.Read.All (246dd0d5-5bd0-4def-940b-0421030a5b68)
  * (any others depending on what actions should be done)
* Grant admin consent

**Graph call**

**1 - Create the multi-tenant application registration**
```
POST https://graph.microsoft.com/v1.0/applications
Content-Type: application/json
Authorization: Bearer <access_token>
{
  "displayName": "HCMultiTenantMonitor",
  "signInAudience": "AzureADMultipleOrgs",
  "requiredResourceAccess": [
    {
      "resourceAppId": "00000003-0000-0000-c000-000000000000",
      "resourceAccess": [
      {
          "id": "19dbc75e-c2e2-444c-a770-ec69d8559fc7",
          "type": "Role"
        },
        {
          "id": "246dd0d5-5bd0-4def-940b-0421030a5b68",
          "type": "Role"
        }
      ]
    }
  ]
}
```
**2 — Create the service principal in the home tenant**
```
POST https://graph.microsoft.com/v1.0/servicePrincipals
Content-Type: application/json
Authorization: Bearer <access_token>

{
  "appId": "<appId-from-created-application>"
}
```
**3 — Get the Microsoft Graph service principal**
```
GET https://graph.microsoft.com/v1.0/servicePrincipals?$filter=appId eq '00000003-0000-0000-c000-000000000000'&$select=id,displayName,appId,appRoles
Authorization: Bearer <access_token>
```
**4 — Grant admin consent for Directory.ReadWrite.All**
```
POST https://graph.microsoft.com/v1.0/servicePrincipals/<client-service-principal-object-id>/appRoleAssignments
Content-Type: application/json
Authorization: Bearer <access_token>

{
  "principalId": "<client-service-principal-object-id>",
  "resourceId": "<microsoft-graph-service-principal-object-id>",
  "appRoleId": "19dbc75e-c2e2-444c-a770-ec69d8559fc7"
}
```
**5 — Grant admin consent for Policy.Read.All**
```
POST https://graph.microsoft.com/v1.0/servicePrincipals/<client-service-principal-object-id>/appRoleAssignments
Content-Type: application/json
Authorization: Bearer <access_token>

{
  "principalId": "<client-service-principal-object-id>",
  "resourceId": "<microsoft-graph-service-principal-object-id>",
  "appRoleId": "246dd0d5-5bd0-4def-940b-0421030a5b68"
}
```
**Graph call using PowerShell**
```
Connect-MgGraph -Scopes `
    "Application.ReadWrite.All", `
    "AppRoleAssignment.ReadWrite.All", `
    "Application.Read.All"

# ================================
# Constants
# ================================
$graphAppId = "00000003-0000-0000-c000-000000000000"

$directoryReadWriteAllAppRoleId = "19dbc75e-c2e2-444c-a770-ec69d8559fc7"
$policyReadAllAppRoleId         = "246dd0d5-5bd0-4def-940b-0421030a5b68"

# ================================
# 1. Create multi-tenant app registration
# ================================
$appBody = @{
    displayName    = "HCMultiTenantMonitor"
    signInAudience = "AzureADMultipleOrgs"
    requiredResourceAccess = @(
        @{
            resourceAppId  = $graphAppId
            resourceAccess = @(
                @{
                    id   = $directoryReadWriteAllAppRoleId
                    type = "Role"
                },
                @{
                    id   = $policyReadAllAppRoleId
                    type = "Role"
                }
            )
        }
    )
} | ConvertTo-Json -Depth 10

$app = Invoke-MgGraphRequest `
    -Method POST `
    -Uri "https://graph.microsoft.com/v1.0/applications" `
    -Body $appBody `
    -ContentType "application/json"

$app | Select-Object id, appId, displayName, signInAudience

# ================================
# 2. Create service principal for the app
# ================================
$spBody = @{
    appId = $app.appId
} | ConvertTo-Json

$clientSp = Invoke-MgGraphRequest `
    -Method POST `
    -Uri "https://graph.microsoft.com/v1.0/servicePrincipals" `
    -Body $spBody `
    -ContentType "application/json"

$clientSp | Select-Object id, appId, displayName

# ================================
# 3. Get Microsoft Graph service principal
# ================================
$graphSpResponse = Invoke-MgGraphRequest `
    -Method GET `
    -Uri "https://graph.microsoft.com/v1.0/servicePrincipals?`$filter=appId eq '$graphAppId'&`$select=id,displayName,appId,appRoles"

$graphSp = $graphSpResponse.value[0]

$graphSp | Select-Object id, displayName, appId

# ================================
# 4. Grant admin consent - Directory.ReadWrite.All
# ================================
$directoryGrantBody = @{
    principalId = $clientSp.id
    resourceId  = $graphSp.id
    appRoleId   = $directoryReadWriteAllAppRoleId
} | ConvertTo-Json

Invoke-MgGraphRequest `
    -Method POST `
    -Uri "https://graph.microsoft.com/v1.0/servicePrincipals/$($clientSp.id)/appRoleAssignments" `
    -Body $directoryGrantBody `
    -ContentType "application/json"

# ================================
# 5. Grant admin consent - Policy.Read.All
# ================================
$policyGrantBody = @{
    principalId = $clientSp.id
    resourceId  = $graphSp.id
    appRoleId   = $policyReadAllAppRoleId
} | ConvertTo-Json

Invoke-MgGraphRequest `
    -Method POST `
    -Uri "https://graph.microsoft.com/v1.0/servicePrincipals/$($clientSp.id)/appRoleAssignments" `
    -Body $policyGrantBody `
    -ContentType "application/json"
```
**Microsoft Graph PowerShell SDK cmdlets**
```
Connect-MgGraph -Scopes `
    "Application.ReadWrite.All", `
    "AppRoleAssignment.ReadWrite.All", `
    "Application.Read.All"

# ================================
# Constants
# ================================
$graphAppId = "00000003-0000-0000-c000-000000000000"

$directoryReadWriteAllAppRoleId = "19dbc75e-c2e2-444c-a770-ec69d8559fc7"
$policyReadAllAppRoleId         = "246dd0d5-5bd0-4def-940b-0421030a5b68"

# ================================
# 1. Create multi-tenant application registration
# ================================
$app = New-MgApplication `
    -DisplayName "HCMultiTenantMonitor" `
    -SignInAudience "AzureADMultipleOrgs" `
    -RequiredResourceAccess @(
        @{
            ResourceAppId = $graphAppId
            ResourceAccess = @(
                @{
                    Id   = $directoryReadWriteAllAppRoleId
                    Type = "Role"
                },
                @{
                    Id   = $policyReadAllAppRoleId
                    Type = "Role"
                }
            )
        }
    )

$app | Select-Object Id, AppId, DisplayName, SignInAudience

# ================================
# 2. Create service principal
# ================================
$clientSp = New-MgServicePrincipal -AppId $app.AppId

$clientSp | Select-Object Id, AppId, DisplayName

# ================================
# 3. Get Microsoft Graph service principal
# ================================
$graphSp = Get-MgServicePrincipal `
    -Filter "appId eq '$graphAppId'" `
    -Property "id,displayName,appId,appRoles"

$graphSp | Select-Object Id, DisplayName, AppId

# ================================
# 4. Grant admin consent - Directory.ReadWrite.All
# ================================
New-MgServicePrincipalAppRoleAssignment `
    -ServicePrincipalId $clientSp.Id `
    -PrincipalId $clientSp.Id `
    -ResourceId $graphSp.Id `
    -AppRoleId $directoryReadWriteAllAppRoleId

# ================================
# 5. Grant admin consent - Policy.Read.All
# ================================
New-MgServicePrincipalAppRoleAssignment `
    -ServicePrincipalId $clientSp.Id `
    -PrincipalId $clientSp.Id `
    -ResourceId $graphSp.Id `
    -AppRoleId $policyReadAllAppRoleId
```

</details>

<details>
<summary>

## Create a Governance policy template

</summary>

* Assign Delegated administration
  * Global Administrator (Entra role) → TG-Global Administrator (Role-assignable group)
  * User Administrator (Entra role) → TG-User Administrator (Role-assignable group)
* Select Multitenant application
  * HCMultiTenantMonitor

**Graph call**
```
POST https://graph.microsoft.com/beta/directory/tenantGovernance/governancePolicyTemplates
Content-Type: application/json
Authorization: Bearer <token>

{
  "displayName": "HCMultiTenantMonitor Policy Template",
  "description": "Delegates Global Administrator and User Administrator through role-assignable groups and provisions the HCMultiTenantMonitor multi-tenant application.",
  "multiTenantApplicationsToProvision": [
    {
      "appId": "<HCMultiTenantMonitor-appId-client-id>",
      "objectId": "<HCMultiTenantMonitor-service-principal-object-id-in-governing-tenant>",
      "displayName": "HCMultiTenantMonitor",
      "requiredResourceAccesses": [
        {
          "resourceAppId": "00000003-0000-0000-c000-000000000000",
          "permissions": [
            {
              "id": "19dbc75e-c2e2-444c-a770-ec69d8559fc7",
              "name": "Directory.ReadWrite.All",
              "type": "role"
            },
            {
              "id": "246dd0d5-5bd0-4def-940b-0421030a5b68",
              "name": "Policy.Read.All",
              "type": "role"
            }
          ]
        }
      ]
    }
  ],
  "delegatedAdministrationRoleAssignments": [
    {
      "roleTemplates": [
        {
          "id": "62e90394-69f5-4237-9190-012177145e10",
          "name": "Global Administrator"
        }
      ],
      "group": {
        "id": "<TG-Global-Administrator-group-object-id>"
      }
    },
    {
      "roleTemplates": [
        {
          "id": "fe930be7-5e62-47db-91af-98c3a49a38b1",
          "name": "User Administrator"
        }
      ],
      "group": {
        "id": "<TG-User-Administrator-group-object-id>"
      }
    }
  ]
}
```
**Graph call using PowerShell**
```
Connect-MgGraph -Scopes `
    "TenantGovernance-PolicyTemplate.ReadWrite.All", `
    "Group.Read.All", `
    "Application.Read.All", `
    "Directory.Read.All"
```
**Resolve groups, app, and service principal**
```
# Microsoft Graph resource app ID
$graphAppId = "00000003-0000-0000-c000-000000000000"

# Microsoft Graph application permission IDs
$directoryReadWriteAllId = "19dbc75e-c2e2-444c-a770-ec69d8559fc7"
$policyReadAllId         = "246dd0d5-5bd0-4def-940b-0421030a5b68"

# Entra role template IDs
$globalAdministratorRoleTemplateId = "62e90394-69f5-4237-9190-012177145e10"
$userAdministratorRoleTemplateId   = "fe930be7-5e62-47db-91af-98c3a49a38b1"
```
**Create the governance policy template**
```
$body = @{
    displayName = "HCMultiTenantMonitor Policy Template"
    description = "Delegates Global Administrator and User Administrator through role-assignable groups and provisions the HCMultiTenantMonitor multi-tenant application."

    multiTenantApplicationsToProvision = @(
        @{
            appId       = $app.appId
            objectId    = $servicePrincipal.id
            displayName = $app.displayName

            requiredResourceAccesses = @(
                @{
                    resourceAppId = $graphAppId
                    permissions   = @(
                        @{
                            id   = $directoryReadWriteAllId
                            name = "Directory.ReadWrite.All"
                            type = "role"
                        },
                        @{
                            id   = $policyReadAllId
                            name = "Policy.Read.All"
                            type = "role"
                        }
                    )
                }
            )
        }
    )

    delegatedAdministrationRoleAssignments = @(
        @{
            roleTemplates = @(
                @{
                    id   = $globalAdministratorRoleTemplateId
                    name = "Global Administrator"
                }
            )
            group = @{
                id = $tgGlobalAdminGroup.id
            }
        },
        @{
            roleTemplates = @(
                @{
                    id   = $userAdministratorRoleTemplateId
                    name = "User Administrator"
                }
            )
            group = @{
                id = $tgUserAdminGroup.id
            }
        }
    )
} | ConvertTo-Json -Depth 20

$template = Invoke-MgGraphRequest `
    -Method POST `
    -Uri "https://graph.microsoft.com/beta/directory/tenantGovernance/governancePolicyTemplates" `
    -Body $body `
    -ContentType "application/json"

$template | Select-Object id, displayName, description, version
```
**Microsoft Graph PowerShell SDK cmdlets**
```
under construction
```

</details>

# Tenant Governance Relationship

<details>
<summary>

## Enable tenant to receive governance invitations.

</summary>

**Graph call**
```
PATCH https://graph.microsoft.com/beta/directory/tenantGovernance/settings
Content-Type: application/json

{
  "canReceiveInvitations": true
}
```

**Graph call using PowerShell**
```
Connect-MgGraph -Scopes "TenantGovernance-Setting.ReadWrite.All"

$body = @{
    canReceiveInvitations = $true
} | ConvertTo-Json

Invoke-MgGraphRequest `
    -Method PATCH `
    -Uri "https://graph.microsoft.com/beta/directory/tenantGovernance/settings" `
    -Body $body `
    -ContentType "application/json"
```

**Microsoft Graph PowerShell SDK cmdlets**
```
Import-Module Microsoft.Graph.Beta.Identity.DirectoryManagement

$params = @{
	canReceiveInvitations = $true
}

Update-MgBetaDirectoryTenantGovernanceSetting -BodyParameter $params
```

</details>

<details>
<summary>

## Send Invitation from Governed tenant

</summary>

**Graph call**
```
POST https://graph.microsoft.com/beta/directory/tenantGovernance/governanceInvitations
Content-Type: application/json

{
  "governingTenantId": "aaaabbbb-0000-cccc-1111-dddd2222eeee"
}
```

**Graph call using PowerShell**
```
```

**Microsoft Graph PowerShell SDK cmdlets**
```
Import-Module Microsoft.Graph.Beta.Identity.DirectoryManagement

$params = @{
	governingTenantId = "aaaabbbb-0000-cccc-1111-dddd2222eeee"
}

New-MgBetaDirectoryTenantGovernanceInvitation -BodyParameter $params
```

</details>

<details>
<summary>

## Send Governance request from Governing tenant

</summary>

**Graph call**
```
POST https://graph.microsoft.com/beta/directory/tenantGovernance/governanceRequests
Content-Type: application/json

{
  "governedTenantId": "bbbbcccc-1111-dddd-2222-eeee3333ffff",
  "governancePolicyTemplate@odata.bind": "https://graph.microsoft.com/beta/directory/tenantGovernance/governancePolicyTemplates/d3d3d3d3-eeee-ffff-aaaa-b4b4b4b4b4b4"
}
```

**Graph call using PowerShell**
```
```

**Microsoft Graph PowerShell SDK cmdlets**
```
Import-Module Microsoft.Graph.Beta.Identity.DirectoryManagement

$params = @{
	governedTenantId = "bbbbcccc-1111-dddd-2222-eeee3333ffff"
	"governancePolicyTemplate@odata.bind" = "https://graph.microsoft.com/beta/directory/tenantGovernance/governancePolicyTemplates/d3d3d3d3-eeee-ffff-aaaa-b4b4b4b4b4b4"
}

New-MgBetaDirectoryTenantGovernanceRequest -BodyParameter $params
```

</details>

<details>
<summary>

## Accept Governance request from Governed tenant

</summary>

**Graph call**
```
PATCH https://graph.microsoft.com/beta/directory/tenantGovernance/governanceRequests/aaaaaaaa-0000-1111-2222-bbbbbbbbbbbb
Content-Type: application/json

{
  "status": "accepted"
}
```

**Graph call using PowerShell**
```
```

**Microsoft Graph PowerShell SDK cmdlets**
```
Import-Module Microsoft.Graph.Beta.Identity.DirectoryManagement

$params = @{
	status = "accepted"
}

Update-MgBetaDirectoryTenantGovernanceRequest -GovernanceRequestId $governanceRequestId -BodyParameter $params
```

</details>


