# Tenant Discovery

The Tenant Governance Microsoft Graph APIs used in this document currently require delegated Microsoft Graph permissions. Application permissions are not supported for these Tenant Governance operations.

These samples use Connect-MgGraph -Scopes ... and are intended to run as a signed-in work or school user with the required delegated Microsoft Graph permission. The signed-in user must also hold a supported Microsoft Entra role for the operation being performed.

The Tenant Governance APIs shown here are Microsoft Graph /beta APIs. Microsoft Graph beta APIs are subject to change and are not supported for production use.

<details>
<summary>
    
## Enable 
</summary>

**Graph call**

Request
```
POST https://graph.microsoft.com/beta/directory/tenantGovernance/settings/enableRelatedTenants
```
Response
```
HTTP/1.1 204 No Content
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

Request
```
GET https://graph.microsoft.com/beta/directory/tenantGovernance/settings
```
Response
```
{
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#directory/tenantGovernance/settings/$entity",
    "@microsoft.graph.tips": "Use $select to choose only the properties your app needs, as this can lead to performance improvements. For example: GET directory/tenantGovernance/settings?$select=canReceiveInvitations,isRelatedTenantsEnabled",
    "isRelatedTenantsEnabled": true,
    "canReceiveInvitations": true
}
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

Request
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
Response
```
HTTP/1.1 201 Created
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

* Name: MultiTenantMonitor
* Assign API (application) permissions
  * Directory.ReadWrite.All (19dbc75e-c2e2-444c-a770-ec69d8559fc7)
  * Policy.Read.All (246dd0d5-5bd0-4def-940b-0421030a5b68)
  * (any others depending on what actions should be done)
* Grant admin consent

**Graph call**

<details>
<summary>

**1 - Create the multi-tenant application registration**

</summary>

Request
```
POST https://graph.microsoft.com/v1.0/applications
{
  "displayName": "TGMultiTenantMonitor",
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
Response
```
HTTP/1.1 201 Created

{
    "@odata.context": "https://graph.microsoft.com/v1.0/$metadata#applications/$entity",
    "id": "856fffc7-df69-4838-8d5d-9fe6d7c79ace",
    "deletedDateTime": null,
    "appId": "ac3a269f-f7c9-4827-8fb7-3906798a335a",
    "applicationTemplateId": null,
    "disabledByMicrosoftStatus": null,
    "createdByAppId": "de8bc8b5-d9f9-48b1-a8ad-b748da725064",
    "createdDateTime": "2026-06-24T17:22:38.5362737Z",
    "displayName": "TGMultiTenantMonitor",
    "description": null,
    "groupMembershipClaims": null,
    "identifierUris": [],
    "isDeviceOnlyAuthSupported": null,
    "isDisabled": null,
    "isFallbackPublicClient": null,
    "nativeAuthenticationApisEnabled": null,
    "notes": null,
    "publisherDomain": "MngEnv688692.onmicrosoft.com",
    "serviceManagementReference": null,
    "signInAudience": "AzureADMultipleOrgs",
    "tags": [],
    "tokenEncryptionKeyId": null,
    "uniqueName": null,
    "samlMetadataUrl": null,
    "defaultRedirectUri": null,
    "certification": null,
    "optionalClaims": null,
    "servicePrincipalLockConfiguration": null,
    "requestSignatureVerification": null,
    "addIns": [],
    "api": {
        "acceptMappedClaims": null,
        "knownClientApplications": [],
        "requestedAccessTokenVersion": null,
        "oauth2PermissionScopes": [],
        "preAuthorizedApplications": []
    },
    "appRoles": [],
    "info": {
        "logoUrl": null,
        "marketingUrl": null,
        "privacyStatementUrl": null,
        "supportUrl": null,
        "termsOfServiceUrl": null
    },
    "keyCredentials": [],
    "parentalControlSettings": {
        "countriesBlockedForMinors": [],
        "legalAgeGroupRule": "Allow"
    },
    "passwordCredentials": [],
    "publicClient": {
        "redirectUris": []
    },
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
    ],
    "verifiedPublisher": {
        "displayName": null,
        "verifiedPublisherId": null,
        "addedDateTime": null
    },
    "web": {
        "homePageUrl": null,
        "logoutUrl": null,
        "redirectUris": [],
        "implicitGrantSettings": {
            "enableAccessTokenIssuance": false,
            "enableIdTokenIssuance": false
        },
        "redirectUriSettings": []
    },
    "spa": {
        "redirectUris": []
    }
}
```

</details>

<details>
<summary>

**2 - Create the service principal in the home tenant**

</summary>

Request
```
POST https://graph.microsoft.com/v1.0/servicePrincipals

{
  "appId": "<appId-from-created-application>"
}
```
Response
```
{
    "@odata.context": "https://graph.microsoft.com/v1.0/$metadata#servicePrincipals/$entity",
    "id": "9c1af20a-ce5c-4213-9f57-1162eb422287",
    "deletedDateTime": null,
    "accountEnabled": true,
    "alternativeNames": [],
    "appDisplayName": "TGMultiTenantMonitor",
    "appDescription": null,
    "appId": "ac3a269f-f7c9-4827-8fb7-3906798a335a",
    "applicationTemplateId": null,
    "appOwnerOrganizationId": "<tenant_id>",
    "appRoleAssignmentRequired": false,
    "createdByAppId": "de8bc8b5-d9f9-48b1-a8ad-b748da725064",
    "createdDateTime": null,
    "description": null,
    "disabledByMicrosoftStatus": null,
    "displayName": "TGMultiTenantMonitor",
    "homepage": null,
    "isDisabled": null,
    "loginUrl": null,
    "logoutUrl": null,
    "notes": null,
    "notificationEmailAddresses": [],
    "preferredSingleSignOnMode": null,
    "preferredTokenSigningKeyThumbprint": null,
    "replyUrls": [],
    "servicePrincipalNames": [
        "ac3a269f-f7c9-4827-8fb7-3906798a335a"
    ],
    "servicePrincipalType": "Application",
    "signInAudience": "AzureADMultipleOrgs",
    "tags": [],
    "tokenEncryptionKeyId": null,
    "samlSingleSignOnSettings": null,
    "addIns": [],
    "appRoles": [],
    "info": {
        "logoUrl": null,
        "marketingUrl": null,
        "privacyStatementUrl": null,
        "supportUrl": null,
        "termsOfServiceUrl": null
    },
    "keyCredentials": [],
    "oauth2PermissionScopes": [],
    "passwordCredentials": [],
    "resourceSpecificApplicationPermissions": [],
    "verifiedPublisher": {
        "displayName": null,
        "verifiedPublisherId": null,
        "addedDateTime": null
    }
}
```

</details>

<details>
<summary>

**3 - Get the Microsoft Graph service principal**

</summary>

Request
```
GET https://graph.microsoft.com/v1.0/servicePrincipals?$filter=appId eq '00000003-0000-0000-c000-000000000000'&$select=id,displayName,appId
Authorization: Bearer <access_token>
```
Response
```
{
    "@odata.context": "https://graph.microsoft.com/v1.0/$metadata#servicePrincipals(id,displayName,appId)",
    "value": [
        {
            "id": "94b568db-9c17-4d88-a762-ca99dffc5bad",
            "displayName": "Microsoft Graph",
            "appId": "00000003-0000-0000-c000-000000000000"
        }
    ]
}
```

</details>

<details>
<summary>

**4 - Grant admin consent for Directory.ReadWrite.All**

</summary>

Request
```
POST https://graph.microsoft.com/v1.0/servicePrincipals/<client-service-principal-object-id>/appRoleAssignments

{
  "principalId": "<client-service-principal-object-id>",
  "resourceId": "<microsoft-graph-service-principal-object-id>",
  "appRoleId": "19dbc75e-c2e2-444c-a770-ec69d8559fc7"
}
```
Response
```
{
    "@odata.context": "https://graph.microsoft.com/v1.0/$metadata#appRoleAssignments/$entity",
    "id": "CvIanFzOE0KfVxFi60Iih4rxo3O5LgFBr8RATDWsxME",
    "deletedDateTime": null,
    "appRoleId": "19dbc75e-c2e2-444c-a770-ec69d8559fc7",
    "createdDateTime": "2026-06-24T17:38:43.9742561Z",
    "principalDisplayName": "TGMultiTenantMonitor",
    "principalId": "9c1af20a-ce5c-4213-9f57-1162eb422287",
    "principalType": "ServicePrincipal",
    "resourceDisplayName": "Microsoft Graph",
    "resourceId": "94b568db-9c17-4d88-a762-ca99dffc5bad"
}
```

</details>

<details>
<summary>

**5 - Grant admin consent for Policy.Read.All**

</summary>

Request
```
POST https://graph.microsoft.com/v1.0/servicePrincipals/<client-service-principal-object-id>/appRoleAssignments

{
  "principalId": "<client-service-principal-object-id>",
  "resourceId": "<microsoft-graph-service-principal-object-id>",
  "appRoleId": "246dd0d5-5bd0-4def-940b-0421030a5b68"
}
```
Response
```
{
    "@odata.context": "https://graph.microsoft.com/v1.0/$metadata#appRoleAssignments/$entity",
    "id": "CvIanFzOE0KfVxFi60Iih_roYR6wZORDiiH5mUEq78c",
    "deletedDateTime": null,
    "appRoleId": "246dd0d5-5bd0-4def-940b-0421030a5b68",
    "createdDateTime": "2026-06-24T17:39:28.836073Z",
    "principalDisplayName": "TGMultiTenantMonitor",
    "principalId": "9c1af20a-ce5c-4213-9f57-1162eb422287",
    "principalType": "ServicePrincipal",
    "resourceDisplayName": "Microsoft Graph",
    "resourceId": "94b568db-9c17-4d88-a762-ca99dffc5bad"
}
```

</details>

<details>
<summary>

**Graph call using PowerShell**

</summary>

```
# ================================
# CONFIGURATION / VARIABLES
# ================================

# Pipeline-provided authentication values
$tenantId     = $env:TENANT_ID
$clientId     = $env:CLIENT_ID
$clientSecret = $env:CLIENT_SECRET

# App registration to create
$appDisplayName = "TGMultiTenantMonitor"
$signInAudience = "AzureADMultipleOrgs"

# Microsoft Graph resource app ID
$graphAppId = "00000003-0000-0000-c000-000000000000"

# Microsoft Graph application permission IDs
$directoryReadWriteAllAppRoleId = "19dbc75e-c2e2-444c-a770-ec69d8559fc7" # Directory.ReadWrite.All
$policyReadAllAppRoleId         = "246dd0d5-5bd0-4def-940b-0421030a5b68" # Policy.Read.All

# Graph endpoints
$applicationsUri      = "https://graph.microsoft.com/v1.0/applications"
$servicePrincipalsUri = "https://graph.microsoft.com/v1.0/servicePrincipals"

# ================================
# VALIDATE REQUIRED VARIABLES
# ================================

if ([string]::IsNullOrWhiteSpace($tenantId)) {
    throw "TENANT_ID environment variable is missing."
}

if ([string]::IsNullOrWhiteSpace($clientId)) {
    throw "CLIENT_ID environment variable is missing."
}

if ([string]::IsNullOrWhiteSpace($clientSecret)) {
    throw "CLIENT_SECRET environment variable is missing."
}

# ================================
# CONNECT TO MICROSOFT GRAPH
# ================================

$secureClientSecret = ConvertTo-SecureString $clientSecret -AsPlainText -Force

$clientSecretCredential = New-Object System.Management.Automation.PSCredential(
    $clientId,
    $secureClientSecret
)

Connect-MgGraph `
    -TenantId $tenantId `
    -ClientSecretCredential $clientSecretCredential `
    -NoWelcome

# ================================
# 1. CREATE MULTI-TENANT APP REGISTRATION
# ================================

$appBody = @{
    displayName    = $appDisplayName
    signInAudience = $signInAudience

    requiredResourceAccess = @(
        @{
            resourceAppId = $graphAppId

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
    -Uri $applicationsUri `
    -Body $appBody `
    -ContentType "application/json"

if (-not $app.id -or -not $app.appId) {
    throw "Application registration creation failed."
}

# ================================
# 2. CREATE SERVICE PRINCIPAL FOR THE APP
# ================================

$spBody = @{
    appId = $app.appId
} | ConvertTo-Json

$clientSp = Invoke-MgGraphRequest `
    -Method POST `
    -Uri $servicePrincipalsUri `
    -Body $spBody `
    -ContentType "application/json"

if (-not $clientSp.id) {
    throw "Service principal creation failed."
}

# ================================
# 3. GET MICROSOFT GRAPH SERVICE PRINCIPAL
# ================================

$graphSpUri = "$servicePrincipalsUri?`$filter=appId eq '$graphAppId'&`$select=id,displayName,appId,appRoles"

$graphSpResponse = Invoke-MgGraphRequest `
    -Method GET `
    -Uri $graphSpUri

$graphSp = $graphSpResponse.value[0]

if (-not $graphSp.id) {
    throw "Microsoft Graph service principal was not found."
}

# ================================
# 4. GRANT ADMIN CONSENT - Directory.ReadWrite.All
# ================================

$directoryGrantBody = @{
    principalId = $clientSp.id
    resourceId  = $graphSp.id
    appRoleId   = $directoryReadWriteAllAppRoleId
} | ConvertTo-Json

$directoryGrant = Invoke-MgGraphRequest `
    -Method POST `
    -Uri "$servicePrincipalsUri/$($clientSp.id)/appRoleAssignments" `
    -Body $directoryGrantBody `
    -ContentType "application/json"

if (-not $directoryGrant.id) {
    throw "Admin consent grant failed for Directory.ReadWrite.All."
}

# ================================
# 5. GRANT ADMIN CONSENT - Policy.Read.All
# ================================

$policyGrantBody = @{
    principalId = $clientSp.id
    resourceId  = $graphSp.id
    appRoleId   = $policyReadAllAppRoleId
} | ConvertTo-Json

$policyGrant = Invoke-MgGraphRequest `
    -Method POST `
    -Uri "$servicePrincipalsUri/$($clientSp.id)/appRoleAssignments" `
    -Body $policyGrantBody `
    -ContentType "application/json"

if (-not $policyGrant.id) {
    throw "Admin consent grant failed for Policy.Read.All."
}

# ================================
# 6. DISCONNECT
# ================================

Disconnect-MgGraph | Out-Null
```

</details>

<details>
<summary>

**Microsoft Graph PowerShell SDK cmdlets**

</summary>

```
# ================================
# CONFIGURATION / VARIABLES
# ================================

# App registration to create
$appDisplayName = "TGMultiTenantMonitor"
$signInAudience = "AzureADMultipleOrgs"

# Microsoft Graph resource app ID
$graphAppId = "00000003-0000-0000-c000-000000000000"

# Microsoft Graph application permission IDs
$directoryReadWriteAllAppRoleId = "19dbc75e-c2e2-444c-a770-ec69d8559fc7" # Directory.ReadWrite.All
$policyReadAllAppRoleId         = "246dd0d5-5bd0-4def-940b-0421030a5b68" # Policy.Read.All

# ================================
# CONNECT TO MICROSOFT GRAPH
# ================================

Connect-MgGraph -Scopes `
    "Application.ReadWrite.All", `
    "AppRoleAssignment.ReadWrite.All", `
    "Application.Read.All"

# ================================
# 1. CREATE MULTI-TENANT APPLICATION REGISTRATION
# ================================

$app = New-MgApplication `
    -DisplayName $appDisplayName `
    -SignInAudience $signInAudience `
    -RequiredResourceAccess @(
        @{
            ResourceAppId  = $graphAppId
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

# ================================
# 2. CREATE SERVICE PRINCIPAL
# ================================

$clientSp = New-MgServicePrincipal -AppId $app.AppId

# ================================
# 3. GET MICROSOFT GRAPH SERVICE PRINCIPAL
# ================================

$graphSp = Get-MgServicePrincipal `
    -Filter "appId eq '$graphAppId'" `
    -Property "id,displayName,appId,appRoles"

# ================================
# 4. GRANT ADMIN CONSENT - Directory.ReadWrite.All
# ================================

New-MgServicePrincipalAppRoleAssignment `
    -ServicePrincipalId $clientSp.Id `
    -PrincipalId $clientSp.Id `
    -ResourceId $graphSp.Id `
    -AppRoleId $directoryReadWriteAllAppRoleId

# ================================
# 5. GRANT ADMIN CONSENT - Policy.Read.All
# ================================

New-MgServicePrincipalAppRoleAssignment `
    -ServicePrincipalId $clientSp.Id `
    -PrincipalId $clientSp.Id `
    -ResourceId $graphSp.Id `
    -AppRoleId $policyReadAllAppRoleId

# ================================
# 6. DISCONNECT
# ================================

Disconnect-MgGraph | Out-Null
```

</details>

</details>

<details>
<summary>

## Create a Governance policy template

</summary>

* Assign Delegated administration
  * Global Administrator (Entra role) → TG-Global Administrator (Role-assignable group)
  * User Administrator (Entra role) → TG-User Administrator (Role-assignable group)
* Select Multitenant application
  * TGMultiTenantMonitor

<details>
<summary>

**Graph call**

</summary>

Request
```
POST https://graph.microsoft.com/beta/directory/tenantGovernance/governancePolicyTemplates

{
  "displayName": "TGMultiTenantMonitor Policy Template",
  "description": "Delegates Global Administrator and User Administrator through role-assignable groups and provisions the TGMultiTenantMonitor multi-tenant application.",
  "multiTenantApplicationsToProvision": [
    {
      "appId": "<TGMultiTenantMonitor-appId-client-id>",
      "objectId": "<TGMultiTenantMonitor-service-principal-object-id-in-governing-tenant>",
      "displayName": "TGMultiTenantMonitor",
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
Response
```
{
    "@context": "https://graph.microsoft.com/beta/$metadata#directory/tenantGovernance/governancePolicyTemplates/$entity",
    "id": "b37cfdeb-d8e2-404a-8a9d-47cd50229ed6",
    "displayName": "TGMultiTenantMonitor Policy Template",
    "description": "Delegates Global Administrator and User Administrator through role-assignable groups and provisions the TGMultiTenantMonitor multi-tenant application.",
    "createdDateTime": "2026-06-24T18:59:17.7913078Z",
    "lastModifiedDateTime": "2026-06-24T18:59:17.7913085Z",
    "version": "1",
    "multiTenantApplicationsToProvision": [
        {
            "appId": "ac3a269f-f7c9-4827-8fb7-3906798a335a",
            "objectId": "856fffc7-df69-4838-8d5d-9fe6d7c79ace",
            "displayName": "TGMultiTenantMonitor",
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
            "groupDisplayName": "TG-Global Administrator",
            "roleTemplates": [
                {
                    "id": "62e90394-69f5-4237-9190-012177145e10",
                    "name": "Global Administrator"
                }
            ],
            "group": {
                "id": "551da5d3-a9e8-49e3-b137-4959c46bd360"
            }
        },
        {
            "groupDisplayName": "TG-User Administrator",
            "roleTemplates": [
                {
                    "id": "fe930be7-5e62-47db-91af-98c3a49a38b1",
                    "name": "User Administrator"
                }
            ],
            "group": {
                "id": "bc6f6f2a-6e5c-4f30-bfb5-4578f9fa8e47"
            }
        }
    ]
}
```

</details>

<details>
<summary>

**Graph call using PowerShell**

</summary>

```
# ================================
# CONFIGURATION / VARIABLES
# ================================

# Graph scopes required to create the governance policy template
$connectScopes = @(
    "TenantGovernance-PolicyTemplate.ReadWrite.All",
    "Group.Read.All",
    "Application.Read.All",
    "Directory.Read.All"
)

# Governance policy template
$policyTemplateDisplayName = "TGMultiTenantMonitor Policy Template"
$policyTemplateDescription = "Delegates Global Administrator and User Administrator through role-assignable groups and provisions the TGMultiTenantMonitor multi-tenant application."

# Microsoft Graph resource app ID
$graphAppId = "00000003-0000-0000-c000-000000000000"

# Microsoft Graph application permission IDs
$directoryReadWriteAllId = "19dbc75e-c2e2-444c-a770-ec69d8559fc7" # Directory.ReadWrite.All
$policyReadAllId         = "246dd0d5-5bd0-4def-940b-0421030a5b68" # Policy.Read.All

# Entra role template IDs
$globalAdministratorRoleTemplateId = "62e90394-69f5-4237-9190-012177145e10" # Global Administrator
$userAdministratorRoleTemplateId   = "fe930be7-5e62-47db-91af-98c3a49a38b1" # User Administrator

# Role-assignable group object IDs
$tgGlobalAdministratorGroupObjectId = "<TG-Global-Administrator-group-object-id>"
$tgUserAdministratorGroupObjectId   = "<TG-User-Administrator-group-object-id>"

# Multitenant application details
$multiTenantApplicationDisplayName = "TGMultiTenantMonitor"
$multiTenantApplicationAppId       = "<TGMultiTenantMonitor-app-id-client-id>"
$multiTenantEnterpriseAppObjectId  = "<TGMultiTenantMonitor-enterprise-app-service-principal-object-id>"

# Tenant Governance endpoint
$governancePolicyTemplatesUri = "https://graph.microsoft.com/beta/directory/tenantGovernance/governancePolicyTemplates"

# ================================
# CONNECT TO MICROSOFT GRAPH
# ================================

Connect-MgGraph -Scopes $connectScopes

# ================================
# CREATE GOVERNANCE POLICY TEMPLATE
# ================================

$body = @{
    displayName = $policyTemplateDisplayName
    description = $policyTemplateDescription

    multiTenantApplicationsToProvision = @(
        @{
            appId       = $multiTenantApplicationAppId
            objectId    = $multiTenantEnterpriseAppObjectId
            displayName = $multiTenantApplicationDisplayName

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
                id = $tgGlobalAdministratorGroupObjectId
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
                id = $tgUserAdministratorGroupObjectId
            }
        }
    )
} | ConvertTo-Json -Depth 20

$template = Invoke-MgGraphRequest `
    -Method POST `
    -Uri $governancePolicyTemplatesUri `
    -Body $body `
    -ContentType "application/json"
```

</details>

<details>
<summary>

**Microsoft Graph PowerShell SDK cmdlets**

</summary>

```
# ================================
# CONNECT TO MICROSOFT GRAPH
# ================================

Import-Module Microsoft.Graph.Beta.Identity.DirectoryManagement

Connect-MgGraph -Scopes `
    "TenantGovernance-PolicyTemplate.ReadWrite.All", `
    "Application.Read.All", `
    "Directory.Read.All"

# ================================
# VARIABLES / CONSTANTS
# ================================

# Governance policy template
$templateDisplayName = "TGMultiTenantMonitor Policy Template"
$templateDescription = "Delegates Global Administrator and User Administrator through role-assignable groups and provisions the TGMultiTenantMonitor multi-tenant application."

# Role-assignable group object IDs
$tgGlobalAdminGroupId = "<TG-Global-Administrator-group-object-id>"
$tgUserAdminGroupId   = "<TG-User-Administrator-group-object-id>"

# Multi-tenant application
$multiTenantApplicationObjectId    = "<TGMultiTenantMonitor-application-object-id>"
$multiTenantApplicationAppId       = "<TGMultiTenantMonitor-application-client-id-appId>"
$multiTenantApplicationDisplayName = "TGMultiTenantMonitor"

# Microsoft Graph resource app ID
$graphAppId = "00000003-0000-0000-c000-000000000000"

# Microsoft Graph application permission IDs
$directoryReadWriteAllId = "19dbc75e-c2e2-444c-a770-ec69d8559fc7" # Directory.ReadWrite.All
$policyReadAllId         = "246dd0d5-5bd0-4def-940b-0421030a5b68" # Policy.Read.All

# Entra role template IDs
$globalAdministratorRoleTemplateId = "62e90394-69f5-4237-9190-012177145e10" # Global Administrator
$userAdministratorRoleTemplateId   = "fe930be7-5e62-47db-91af-98c3a49a38b1" # User Administrator

# ================================
# RESOLVE SERVICE PRINCIPAL
# ================================

$servicePrincipal = Get-MgBetaServicePrincipal `
    -Filter "appId eq '$multiTenantApplicationAppId'" `
    -Property "id,appId,displayName"

# ================================
# CREATE GOVERNANCE POLICY TEMPLATE
# ================================

$params = @{
    displayName = $templateDisplayName
    description = $templateDescription

    multiTenantApplicationsToProvision = @(
        @{
            appId       = $multiTenantApplicationAppId
            objectId    = $servicePrincipal.Id
            displayName = $multiTenantApplicationDisplayName

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
                id = $tgGlobalAdminGroupId
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
                id = $tgUserAdminGroupId
            }
        }
    )
}

$template = New-MgBetaDirectoryTenantGovernancePolicyTemplate `
    -BodyParameter $params
```

</details>

</details>

# Tenant Governance Relationship

<details>
<summary>

## Enable tenant to receive governance invitations.

</summary>

**Graph call**

Request
```
PATCH https://graph.microsoft.com/beta/directory/tenantGovernance/settings
Content-Type: application/json

{
  "canReceiveInvitations": true
}
```

Response
```
{
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#directory/tenantGovernance/settings/$entity",
    "isRelatedTenantsEnabled": true,
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

Request
```
POST https://graph.microsoft.com/beta/directory/tenantGovernance/governanceInvitations

{
  "governingTenantId": "aaaabbbb-0000-cccc-1111-dddd2222eeee"
}
```

Response
```
{
    "@context": "https://graph.microsoft.com/beta/$metadata#directory/tenantGovernance/governanceInvitations/$entity",
    "id": "5445a1a4-7ed3-485f-b2d2-51634b619d8f",
    "governingTenantId": "<tenant_id>",
    "governedTenantId": "<tenant_id>",
    "governingTenantName": "<tenant_name>",
    "governedTenantName": "<tenant_name>",
    "createdDateTime": "2026-06-24T19:49:41.7819391Z",
    "expirationDateTime": "2026-07-24T19:49:41.7819391Z"
}
```

**Graph call using PowerShell**
```
Connect-MgGraph -Scopes "TenantGovernance-Relationship.ReadWrite.All"

$governingTenantId = "aaaabbbb-0000-cccc-1111-dddd2222eeee"

$body = @{
    governingTenantId = $governingTenantId
} | ConvertTo-Json

$invitation = Invoke-MgGraphRequest `
    -Method POST `
    -Uri "https://graph.microsoft.com/beta/directory/tenantGovernance/governanceInvitations" `
    -Body $body `
    -ContentType "application/json"
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

Request
```
POST https://graph.microsoft.com/beta/directory/tenantGovernance/governanceRequests

{
  "governedTenantId": "bbbbcccc-1111-dddd-2222-eeee3333ffff",
  "governancePolicyTemplate@odata.bind": "https://graph.microsoft.com/beta/directory/tenantGovernance/governancePolicyTemplates/d3d3d3d3-eeee-ffff-aaaa-b4b4b4b4b4b4"
}
```

<details>
<summary>

Response :point_left:

</summary>

```
{
    "@context": "https://graph.microsoft.com/beta/$metadata#directory/tenantGovernance/governanceRequests/$entity",
    "id": "44b02e44-eafc-46cb-8798-a49873039c5a",
    "governingTenantId": "<tenant_id>",
    "governingTenantName": "<tenant_name>",
    "governedTenantId": "<tenant_id>",
    "governedTenantName": "<tenant_name>",
    "expirationDateTime": "2026-07-08T20:19:21.4287835Z",
    "requestDateTime": "2026-06-24T20:19:21.4287831Z",
    "status": "pending",
    "policySnapshot": {
        "policyId": "b37cfdeb-d8e2-404a-8a9d-47cd50229ed6",
        "version": 1,
        "multiTenantApplicationsToProvision": [
            {
                "appId": "ac3a269f-f7c9-4827-8fb7-3906798a335a",
                "displayName": "TGMultiTenantMonitor",
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
                "groupDisplayName": "TG-Global Administrator",
                "groupId": "551da5d3-a9e8-49e3-b137-4959c46bd360",
                "roleTemplates": [
                    {
                        "id": "62e90394-69f5-4237-9190-012177145e10",
                        "name": "Global Administrator"
                    }
                ]
            },
            {
                "groupDisplayName": "TG-User Administrator",
                "groupId": "bc6f6f2a-6e5c-4f30-bfb5-4578f9fa8e47",
                "roleTemplates": [
                    {
                        "id": "fe930be7-5e62-47db-91af-98c3a49a38b1",
                        "name": "User Administrator"
                    }
                ]
            }
        ]
    },
    "governancePolicyTemplate": {
        "id": "https://graph.microsoft.com/beta/directory/tenantGovernance/policyTemplates/b37cfdeb-d8e2-404a-8a9d-47cd50229ed6"
    }
}
```

</details>

**Graph call using PowerShell**
```
Connect-MgGraph -Scopes "TenantGovernance-Relationship.ReadWrite.All"

$governedTenantId = "bbbbcccc-1111-dddd-2222-eeee3333ffff"
$governancePolicyTemplateId = "d3d3d3d3-eeee-ffff-aaaa-b4b4b4b4b4b4"

$body = @{
    governedTenantId = $governedTenantId
    "governancePolicyTemplate@odata.bind" = "https://graph.microsoft.com/beta/directory/tenantGovernance/governancePolicyTemplates/$governancePolicyTemplateId"
} | ConvertTo-Json

$governanceRequest = Invoke-MgGraphRequest `
    -Method POST `
    -Uri "https://graph.microsoft.com/beta/directory/tenantGovernance/governanceRequests" `
    -Body $body `
    -ContentType "application/json"
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

## List Governance request in Governed tenant

</summary>

**Graph call**

Request
```
GET https://graph.microsoft.com/beta/directory/tenantGovernance/governanceRequests?$filter=status eq 'pending' and governingTenantId eq 'bbbbcccc-1111-dddd-2222-eeee3333ffff'
```

<details>
<summary>

Response :point_left:

</summary>

```
{
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#directory/tenantGovernance/governanceRequests",
    "@microsoft.graph.tips": "Use $select to choose only the properties your app needs, as this can lead to performance improvements. For example: GET directory/tenantGovernance/governanceRequests?$select=expirationDateTime,governedTenantId",
    "value": [
        {
            "id": "44b02e44-eafc-46cb-8798-a49873039c5a",
            "governingTenantId": "<tenant_id>",
            "governingTenantName": "<tenant_name>",
            "governedTenantId": "<tenant_id>",
            "governedTenantName": "<tenant_name>",
            "expirationDateTime": "2026-07-08T20:19:21.4287835Z",
            "requestDateTime": "2026-06-24T20:19:21.4287831Z",
            "status": "pending",
            "policySnapshot": {
                "policyId": "b37cfdeb-d8e2-404a-8a9d-47cd50229ed6",
                "version": 1,
                "multiTenantApplicationsToProvision": [
                    {
                        "appId": "ac3a269f-f7c9-4827-8fb7-3906798a335a",
                        "displayName": "TGMultiTenantMonitor",
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
                        "groupId": "551da5d3-a9e8-49e3-b137-4959c46bd360",
                        "groupDisplayName": "TG-Global Administrator",
                        "roleTemplates": [
                            {
                                "id": "62e90394-69f5-4237-9190-012177145e10",
                                "name": "Global Administrator"
                            }
                        ]
                    },
                    {
                        "groupId": "bc6f6f2a-6e5c-4f30-bfb5-4578f9fa8e47",
                        "groupDisplayName": "TG-User Administrator",
                        "roleTemplates": [
                            {
                                "id": "fe930be7-5e62-47db-91af-98c3a49a38b1",
                                "name": "User Administrator"
                            }
                        ]
                    }
                ]
            }
        }
    ]
}
```

</details>

**Graph call using PowerShell**
```
Connect-MgGraph -Scopes "TenantGovernance-Request.Read.All"

$governingTenantId = "bbbbcccc-1111-dddd-2222-eeee3333ffff"
$status = "pending"

$governanceRequests = Invoke-MgGraphRequest `
    -Method GET `
    -Uri "https://graph.microsoft.com/beta/directory/tenantGovernance/governanceRequests?`$filter=status eq '$status' and governingTenantId eq '$governingTenantId'"
```

**Microsoft Graph PowerShell SDK cmdlets**
```
Import-Module Microsoft.Graph.Beta.Identity.DirectoryManagement

Connect-MgGraph -Scopes "TenantGovernance-Request.Read.All"

$governingTenantId = "bbbbcccc-1111-dddd-2222-eeee3333ffff"
$status = "pending"

$governanceRequests = Get-MgBetaDirectoryTenantGovernanceGovernanceRequest `
    -Filter "status eq '$status' and governingTenantId eq '$governingTenantId'"
```

</details>

<details>
<summary>

## Accept Governance request in Governed tenant

</summary>

**Graph call**

Request
```
PATCH https://graph.microsoft.com/beta/directory/tenantGovernance/governanceRequests/aaaaaaaa-0000-1111-2222-bbbbbbbbbbbb

{
  "status": "accepted"
}
```

<details>
<summary>

Response :point_left:

</summary>

```
{
    "@context": "https://graph.microsoft.com/beta/$metadata#directory/tenantGovernance/governanceRequests/$entity",
    "id": "44b02e44-eafc-46cb-8798-a49873039c5a",
    "governingTenantId": "<tenant_id>",
    "governingTenantName": "<tenant_name>",
    "governedTenantId": "<tenant_id>",
    "governedTenantName": "<tenant_name>",
    "expirationDateTime": "2026-07-08T20:19:21.4287835Z",
    "requestDateTime": "2026-06-24T20:19:21.4287831Z",
    "status": "accepted",
    "policySnapshot": {
        "policyId": "b37cfdeb-d8e2-404a-8a9d-47cd50229ed6",
        "version": 1,
        "multiTenantApplicationsToProvision": [
            {
                "appId": "ac3a269f-f7c9-4827-8fb7-3906798a335a",
                "displayName": "TGMultiTenantMonitor",
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
                "groupDisplayName": "TG-Global Administrator",
                "groupId": "551da5d3-a9e8-49e3-b137-4959c46bd360",
                "roleTemplates": [
                    {
                        "id": "62e90394-69f5-4237-9190-012177145e10",
                        "name": "Global Administrator"
                    }
                ]
            },
            {
                "groupDisplayName": "TG-User Administrator",
                "groupId": "bc6f6f2a-6e5c-4f30-bfb5-4578f9fa8e47",
                "roleTemplates": [
                    {
                        "id": "fe930be7-5e62-47db-91af-98c3a49a38b1",
                        "name": "User Administrator"
                    }
                ]
            }
        ]
    }
}
```

</details>

**Graph call using PowerShell**
```
Connect-MgGraph -Scopes "TenantGovernance-Request.ReadWrite.All"

$governanceRequestId = "aaaaaaaa-0000-1111-2222-bbbbbbbbbbbb"
$status = "accepted"

$body = @{
    status = $status
} | ConvertTo-Json

$governanceRequest = Invoke-MgGraphRequest `
    -Method PATCH `
    -Uri "https://graph.microsoft.com/beta/directory/tenantGovernance/governanceRequests/$governanceRequestId" `
    -Body $body `
    -ContentType "application/json"
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

<details>
<summary>

## Terminate Governance relationship

</summary>

Terminate a governance relationship in two ways, depending on whether the governing tenant or the governed tenant initiates the termination.
| Initiated by | Process |
|---|---|
| Governing tenant | Sends a termination request to the governed tenant. The governed tenant must confirm to complete termination. |
| Governed tenant	| Directly terminates the relationship. The governing tenant doesn't need to take any action. |

<details>
<summary>

### List Governance relationships

</summary>

**Graph call**

Request
```
GET https://graph.microsoft.com/beta/directory/tenantGovernance/governanceRelationships?$filter=status eq 'active' and governingTenantId eq 'aaaabbbb-0000-cccc-1111-dddd2222eeee' and governedTenantId eq 'bbbbcccc-1111-dddd-2222-eeee3333ffff'
```

<details>
<summary>

Response :point_left:

</summary>

```
{
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#directory/tenantGovernance/governanceRelationships",
    "@microsoft.graph.tips": "Use $select to choose only the properties your app needs, as this can lead to performance improvements. For example: GET directory/tenantGovernance/governanceRelationships?$select=createdType,creationDateTime",
    "value": [
        {
            "createdType": "approvedByAdmin",
            "creationDateTime": "2026-06-24T21:16:11.6117884Z",
            "id": "cd4ff218-c860-4727-b479-0f06f66bff95",
            "status": "active",
            "governingTenantId": "<tenant_id>",
            "governedTenantId": "<tenant_id>",
            "governingTenantName": "<tenant_name>",
            "governedTenantName": "<tenant_name>",
            "policySnapshot": {
                "policyId": "b37cfdeb-d8e2-404a-8a9d-47cd50229ed6",
                "version": 1,
                "multiTenantApplicationsToProvision": [
                    {
                        "appId": "ac3a269f-f7c9-4827-8fb7-3906798a335a",
                        "displayName": "TGMultiTenantMonitor",
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
                        "groupId": "551da5d3-a9e8-49e3-b137-4959c46bd360",
                        "groupDisplayName": "TG-Global Administrator",
                        "roleTemplates": [
                            {
                                "id": "62e90394-69f5-4237-9190-012177145e10",
                                "name": "Global Administrator"
                            }
                        ]
                    },
                    {
                        "groupId": "bc6f6f2a-6e5c-4f30-bfb5-4578f9fa8e47",
                        "groupDisplayName": "TG-User Administrator",
                        "roleTemplates": [
                            {
                                "id": "fe930be7-5e62-47db-91af-98c3a49a38b1",
                                "name": "User Administrator"
                            }
                        ]
                    }
                ]
            }
        }
    ]
}
```

</details>

**Graph call using PowerShell**
```
Connect-MgGraph -Scopes "TenantGovernance-Relationship.Read.All"

$status = "active"
$governingTenantId = "aaaabbbb-0000-cccc-1111-dddd2222eeee"
$governedTenantId = "bbbbcccc-1111-dddd-2222-eeee3333ffff"

$governanceRelationships = Invoke-MgGraphRequest `
    -Method GET `
    -Uri "https://graph.microsoft.com/beta/directory/tenantGovernance/governanceRelationships?`$filter=status eq '$status' and governingTenantId eq '$governingTenantId' and governedTenantId eq '$governedTenantId'"
```

**Microsoft Graph PowerShell SDK cmdlets**
```
Import-Module Microsoft.Graph.Beta.Identity.DirectoryManagement

Connect-MgGraph -Scopes "TenantGovernance-Relationship.Read.All"

$status = "active"
$governingTenantId = "aaaabbbb-0000-cccc-1111-dddd2222eeee"
$governedTenantId = "bbbbcccc-1111-dddd-2222-eeee3333ffff"

$governanceRelationships = Get-MgBetaDirectoryTenantGovernanceGovernanceRelationship `
    -Filter "status eq '$status' and governingTenantId eq '$governingTenantId' and governedTenantId eq '$governedTenantId'"
```

</details>

<details>
<summary>

### Initiate termination as the governing tenant

</summary>

**Graph call**

Request
```
PATCH https://graph.microsoft.com/beta/directory/tenantGovernance/governanceRelationships/aaaaaaaa-0000-1111-2222-bbbbbbbbbbbb

{
  "status": "terminationRequestedByGoverningTenant"
}
```
<details>
<summary>

Response :point_left:

</summary>

```
{
    "@context": "https://graph.microsoft.com/beta/$metadata#directory/tenantGovernance/governanceRelationships/$entity",
    "createdType": "approvedByAdmin",
    "creationDateTime": "2026-06-24T21:16:11.6117884Z",
    "id": "cd4ff218-c860-4727-b479-0f06f66bff95",
    "status": "terminationRequestedByGoverningTenant",
    "governingTenantId": "<tenant_id>",
    "governedTenantId": "<tenant_id>",
    "governingTenantName": "<tenant_name>",
    "governedTenantName": "<tenant_name>",
    "policySnapshot": {
        "policyId": "b37cfdeb-d8e2-404a-8a9d-47cd50229ed6",
        "version": 1,
        "multiTenantApplicationsToProvision": [
            {
                "appId": "ac3a269f-f7c9-4827-8fb7-3906798a335a",
                "displayName": "TGMultiTenantMonitor",
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
                "groupDisplayName": "TG-Global Administrator",
                "groupId": "551da5d3-a9e8-49e3-b137-4959c46bd360",
                "roleTemplates": [
                    {
                        "id": "62e90394-69f5-4237-9190-012177145e10",
                        "name": "Global Administrator"
                    }
                ]
            },
            {
                "groupDisplayName": "TG-User Administrator",
                "groupId": "bc6f6f2a-6e5c-4f30-bfb5-4578f9fa8e47",
                "roleTemplates": [
                    {
                        "id": "fe930be7-5e62-47db-91af-98c3a49a38b1",
                        "name": "User Administrator"
                    }
                ]
            }
        ]
    }
}
```

</details>

**Graph call using PowerShell**
```
Connect-MgGraph -Scopes "TenantGovernance-Relationship.ReadWrite.All"

$governanceRelationshipId = "aaaaaaaa-0000-1111-2222-bbbbbbbbbbbb"
$status = "terminationRequestedByGoverningTenant"

$body = @{
    status = $status
} | ConvertTo-Json

$governanceRelationship = Invoke-MgGraphRequest `
    -Method PATCH `
    -Uri "https://graph.microsoft.com/beta/directory/tenantGovernance/governanceRelationships/$governanceRelationshipId" `
    -Body $body `
    -ContentType "application/json"
```

**Microsoft Graph PowerShell SDK cmdlets**
```
Import-Module Microsoft.Graph.Beta.Identity.DirectoryManagement

Connect-MgGraph -Scopes "TenantGovernance-Relationship.ReadWrite.All"

$governanceRelationshipId = "aaaaaaaa-0000-1111-2222-bbbbbbbbbbbb"
$status = "terminationRequestedByGoverningTenant"

$body = @{
    status = $status
}

$governanceRelationship = Update-MgBetaDirectoryTenantGovernanceGovernanceRelationship `
    -GovernanceRelationshipId $governanceRelationshipId `
    -BodyParameter $body
```

</details>

<details>
<summary>

### Terminate relationship from the governed tenant

</summary>

**Graph call**

Request
```
PATCH https://graph.microsoft.com/beta/directory/tenantGovernance/governanceRelationships/aaaaaaaa-0000-1111-2222-bbbbbbbbbbbb

{
  "status": "terminated"
}
```

<details>
<summary>
	
Response :point_left:

</summary>

```
{
    "@context": "https://graph.microsoft.com/beta/$metadata#directory/tenantGovernance/governanceRelationships/$entity",
    "createdType": "approvedByAdmin",
    "creationDateTime": "2026-06-24T21:16:11.6117884Z",
    "id": "cd4ff218-c860-4727-b479-0f06f66bff95",
    "status": "terminated",
    "governingTenantId": "<tenant_id>",
    "governedTenantId": "<tenant_id>",
    "governingTenantName": "<tenant_name>",
    "governedTenantName": "<tenant_name>",
    "policySnapshot": {
        "policyId": "b37cfdeb-d8e2-404a-8a9d-47cd50229ed6",
        "version": 1,
        "multiTenantApplicationsToProvision": [
            {
                "appId": "ac3a269f-f7c9-4827-8fb7-3906798a335a",
                "displayName": "TGMultiTenantMonitor",
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
                "groupDisplayName": "TG-Global Administrator",
                "groupId": "551da5d3-a9e8-49e3-b137-4959c46bd360",
                "roleTemplates": [
                    {
                        "id": "62e90394-69f5-4237-9190-012177145e10",
                        "name": "Global Administrator"
                    }
                ]
            },
            {
                "groupDisplayName": "TG-User Administrator",
                "groupId": "bc6f6f2a-6e5c-4f30-bfb5-4578f9fa8e47",
                "roleTemplates": [
                    {
                        "id": "fe930be7-5e62-47db-91af-98c3a49a38b1",
                        "name": "User Administrator"
                    }
                ]
            }
        ]
    }
}
```
</details>
		
**Graph call using PowerShell**
```
Connect-MgGraph -Scopes "TenantGovernance-Relationship.ReadWrite.All"

$governanceRelationshipId = "aaaaaaaa-0000-1111-2222-bbbbbbbbbbbb"
$status = "terminated"

$body = @{
    status = $status
} | ConvertTo-Json

$governanceRelationship = Invoke-MgGraphRequest `
    -Method PATCH `
    -Uri "https://graph.microsoft.com/beta/directory/tenantGovernance/governanceRelationships/$governanceRelationshipId" `
    -Body $body `
    -ContentType "application/json"
```

**Microsoft Graph PowerShell SDK cmdlets**
```
Import-Module Microsoft.Graph.Beta.Identity.DirectoryManagement

Connect-MgGraph -Scopes "TenantGovernance-Relationship.ReadWrite.All"

$governanceRelationshipId = "aaaaaaaa-0000-1111-2222-bbbbbbbbbbbb"
$status = "terminated"

$body = @{
    status = $status
}

$governanceRelationship = Update-MgBetaDirectoryTenantGovernanceGovernanceRelationship `
    -GovernanceRelationshipId $governanceRelationshipId `
    -BodyParameter $body
```
</details>

</details>
