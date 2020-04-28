---
title: Aangepaste rollen toewijzen met Azure PowerShell-Azure AD | Microsoft Docs
description: Leden van een aangepaste rol van Azure AD-beheerder beheren met Azure PowerShell.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a2096b7899039e7a9d3455bc0c6fb3ec84ebd1a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74025323"
---
# <a name="assign-custom-roles-with-resource-scope-using-powershell-in-azure-active-directory"></a>Aangepaste rollen met resource bereik toewijzen met behulp van Power shell in Azure Active Directory

In dit artikel wordt beschreven hoe u een roltoewijzing maakt op organisatie bereik in Azure Active Directory (Azure AD). Voor het toewijzen van een rol in het bereik van de organisatie wordt toegang verleend via de Azure AD-organisatie. Zie [een aangepaste rol maken en deze toewijzen aan](roles-create-custom.md)de hand van een resource bereik voor het maken van een roltoewijzing met een bereik van één Azure AD-resource. In dit artikel wordt gebruikgemaakt van de [Azure Active Directory module Power shell versie 2](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#directory_roles) .

Zie [beheerders rollen toewijzen in azure Active Directory](directory-assign-admin-roles.md)voor meer informatie over Azure AD-beheerders rollen.

## <a name="required-permissions"></a>Vereiste machtigingen

Maak verbinding met uw Azure AD-Tenant met behulp van een algemeen beheerders account om rollen toe te wijzen of te verwijderen.

## <a name="prepare-powershell"></a>Power shell voorbereiden

Installeer de Azure AD Power shell-module vanuit het [PowerShell Gallery](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17). Importeer vervolgens de Azure AD Power shell preview-module met de volgende opdracht:

``` PowerShell
import-module azureadpreview
```

Als u wilt controleren of de module gereed is voor gebruik, gaat u naar de versie die wordt geretourneerd door de volgende opdracht:

``` PowerShell
get-module azureadpreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azureadpreview               {Add-AzureADMSAdministrati...}
```

U kunt nu beginnen met het gebruik van de cmdlets in de module. Zie voor een volledige beschrijving van de cmdlets in de Azure AD-module de online referentie documentatie voor [Azure ad preview-module](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17).

## <a name="assign-a-role-to-a-user-or-service-principal-with-resource-scope"></a>Een rol toewijzen aan een gebruiker of Service-Principal met resource bereik

1. Open de Azure AD preview Power shell-module.
1. Meld u aan door de opdracht `Connect-AzureAD`uit te voeren.
1. Maak een nieuwe rol met behulp van het volgende Power shell-script.

``` PowerShell
## Assign a role to a user or service principal with resource scope
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'cburl@f128.info'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Application Support Administrator'"

# Get app registration and construct resource scope for assignment.
$appRegistration = Get-AzureADApplication -Filter "displayName eq 'f/128 Filter Photos'"
$resourceScope = '/' + $appRegistration.objectId

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope $resourceScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
```

Gebruik de [cmdlet Get-AzureADMSServicePrincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0)om de rol toe te wijzen aan een Service-Principal in plaats van een gebruiker.

## <a name="operations-on-roledefinition"></a>Bewerkingen op RoleDefinition

Role definition-objecten bevatten de definitie van de ingebouwde of aangepaste rol, samen met de machtigingen die worden verleend door die roltoewijzing. In deze resource worden zowel aangepaste roldefinities als ingebouwde directoryRoles (die worden weer gegeven in roleDefinition-equivalent formulier) weer gegeven. Momenteel kan een Azure AD-organisatie Maxi maal 30 unieke aangepaste RoleDefinitions gedefinieerd.

### <a name="create-operations-on-roledefinition"></a>Bewerkingen maken op RoleDefinition

``` PowerShell
# Basic information
$description = "Can manage credentials of application registrations"
$displayName = "Application Registration Credential Administrator"
$templateId = (New-Guid).Guid

# Set of actions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/standard/read",
    "microsoft.directory/applications/credentials/update"
)
$rolePermissions = @{'allowedResourceActions'= $allowedResourceAction}

# Create new custom admin role
$customAdmin = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="read-operations-on-roledefinition"></a>Lees bewerkingen op RoleDefinition

``` PowerShell
# Get all role definitions
Get-AzureADMSRoleDefinitions

# Get single role definition by objectId
Get-AzureADMSRoleDefinition -Id 86593cfc-114b-4a15-9954-97c3494ef49b

# Get single role definition by templateId
Get-AzureADMSRoleDefinition -Filter "templateId eq 'c4e39bd9-1100-46d3-8c65-fb160da0071f'"
```

### <a name="update-operations-on-roledefinition"></a>Bewerkingen voor het bijwerken van RoleDefinition

``` PowerShell
# Update role definition
# This works for any writable property on role definition. You can replace display name with other
# valid properties.
Set-AzureADMSRoleDefinition -Id c4e39bd9-1100-46d3-8c65-fb160da0071f -DisplayName "Updated DisplayName"
```

### <a name="delete-operations-on-roledefinition"></a>Verwijder bewerkingen op RoleDefinition

``` PowerShell
# Delete role definition
Remove-AzureADMSRoleDefinitions -Id c4e39bd9-1100-46d3-8c65-fb160da0071f
```

## <a name="operations-on-roleassignment"></a>Bewerkingen op RoleAssignment

Roltoewijzingen bevatten informatie die een bepaalde beveiligingsprincipal (een gebruiker of een toepassings service-principal) koppelt aan een roldefinitie. Indien nodig kunt u een bereik van één Azure AD-resource voor de toegewezen machtigingen toevoegen.  Het beperken van het bereik van machtigingen wordt ondersteund voor ingebouwde en aangepaste rollen.

### <a name="create-operations-on-roleassignment"></a>Bewerkingen maken op RoleAssignment

``` PowerShell
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'cburl@f128.info'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Application Support Administrator'"

# Get app registration and construct resource scope for assignment.
$appRegistration = Get-AzureADApplication -Filter "displayName eq 'f/128 Filter Photos'"
$resourceScope = '/' + $appRegistration.objectId

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope $resourceScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
```

### <a name="read-operations-on-roleassignment"></a>Lees bewerkingen op RoleAssignment

``` PowerShell
# Get role assignments for a given principal
Get-AzureADMSRoleAssignment -Filter "principalId eq '27c8ca78-ab1c-40ae-bd1b-eaeebd6f68ac'"

# Get role assignments for a given role definition 
Get-AzureADMSRoleAssignment -Filter "roleDefinitionId eq '355aed8a-864b-4e2b-b225-ea95482e7570'"
```

### <a name="delete-operations-on-roleassignment"></a>Verwijder bewerkingen op RoleAssignment

``` PowerShell
# Delete role assignment
Remove-AzureADMSRoleAssignment -Id 'qiho4WOb9UKKgng_LbPV7tvKaKRCD61PkJeKMh7Y458-1'
```

## <a name="next-steps"></a>Volgende stappen

- Deel met ons op het [forum Azure AD-beheerders rollen](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
- Zie [beheerders rollen toewijzen](directory-assign-admin-roles.md)voor meer informatie over rollen en toewijzingen van Azure AD-beheerdersrol.
- Zie voor standaard gebruikers machtigingen een [vergelijking van de standaard machtigingen voor gast-en gebruikers rechten](../fundamentals/users-default-permissions.md).
