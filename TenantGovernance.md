Tenant Discovery
Enable 

Graph call
POST https://graph.microsoft.com/beta/directory/tenantGovernance/settings/enableRelatedTenants

Graph call using PowerShell
Connect-MgGraph -Scopes "TenantGovernance-Setting.ReadWrite.All"
Invoke-MgGraphRequest `
    -Method POST `
    -Uri "https://graph.microsoft.com/beta/directory/tenantGovernance/settings/enableRelatedTenants"

PowerShell SDK
Import-Module Microsoft.Graph.Beta.Identity.DirectoryManagement
Enable-MgBetaDirectoryTenantGovernanceSettingRelatedTenant


Read

Graph call
GET https://graph.microsoft.com/beta/directory/tenantGovernance/settings

Graph call using PowerShell
Connect-MgGraph -Scopes "TenantGovernance-Setting.Read.All"
Invoke-MgGraphRequest `
    -Method GET `
    -Uri https://graph.microsoft.com/beta/directory/tenantGovernance/settings

PowerShell SDK
Import-Module Microsoft.Graph.Beta.Identity.DirectoryManagement
Get-MgBetaDirectoryTenantGovernanceSetting



Tenant Governance Relationship
Create Governance policy template
Create role-assignable group

Graph call
POST https://graph.microsoft.com/v1.0/groups
{
    "description": "Global Administrator for Governed tenants",
    "displayName": " TG-Global Administrator ",
    "isAssignableToRole": true,
    "mailEnabled": false,
    "mailNickname": " TGGlobalAdministrator ",
    "securityEnabled": true
}

Graph call using PowerShell
Connect-MgGraph -Scopes "Group.ReadWrite.All"
$group = New-MgGroup -DisplayName "TG-Global Administrator" -Description "Global Administrator for Governed tenants" -MailEnabled:$false -SecurityEnabled -MailNickName "TGGlobalAdministrator" -IsAssignableToRole:$true


PowerShell SDK
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





















Graph call


Graph call using PowerShell


PowerShell SDK


