---
title: Aangepaste rollen toewijzen met Azure PowerShell - Azure AD | Microsoft Documenten
description: Beheer leden van een aangepaste Azure AD-beheerdermet Azure PowerShell.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74025323"
---
# <a name="assign-custom-roles-with-resource-scope-using-powershell-in-azure-active-directory"></a>Aangepaste rollen toewijzen met resourcebereik met PowerShell in Azure Active Directory

In dit artikel wordt beschreven hoe u een roltoewijzing maakt op een breder bereik voor de hele organisatie in Azure Active Directory (Azure AD). Door een rol toe te kent bij het bereik voor de hele organisatie, wordt toegang toegekend in de Azure AD-organisatie. Als u een roltoewijzing wilt maken met een bereik van één Azure AD-bron, raadpleegt u [Hoe u een aangepaste rol maakt en deze toewijzen op resourcebereik](roles-create-custom.md). In dit artikel wordt de [Azure Active Directory PowerShell-module voor versie 2](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#directory_roles) gebruikt.

Zie [Beheerdersrollen toewijzen in Azure Active Directory](directory-assign-admin-roles.md)voor meer informatie over Azure AD-beheerdersrollen.

## <a name="required-permissions"></a>Vereiste machtigingen

Maak verbinding met uw Azure AD-tenant met behulp van een algemeen beheerdersaccount om rollen toe te wijzen of te verwijderen.

## <a name="prepare-powershell"></a>PowerShell voorbereiden

Installeer de Azure AD PowerShell-module in de [PowerShell-galerie](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17). Importeer vervolgens de Azure AD PowerShell-voorbeeldmodule met de volgende opdracht:

``` PowerShell
import-module azureadpreview
```

Als u wilt controleren of de module klaar is voor gebruik, moet u de versie die met de volgende opdracht wordt geretourneerd, koppelen aan de versie die hier wordt vermeld:

``` PowerShell
get-module azureadpreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azureadpreview               {Add-AzureADMSAdministrati...}
```

Nu u beginnen met het gebruik van de cmdlets in de module. Zie de online referentiedocumentatie voor [Azure AD-previewmodule](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17)voor een volledige beschrijving van de cmdlets in de Azure AD-module.

## <a name="assign-a-role-to-a-user-or-service-principal-with-resource-scope"></a>Een rol toewijzen aan een gebruiker of serviceprincipal met resourcebereik

1. Open de PowerShell-module van Azure AD-voorbeeld.
1. Meld u aan door `Connect-AzureAD`de opdracht uit te voeren .
1. Maak een nieuwe rol met het volgende PowerShell-script.

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

Als u de rol wilt toewijzen aan een serviceprincipal in plaats van aan een gebruiker, gebruikt u de [cmdlet Get-AzureADMSServicePrincipal.](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0)

## <a name="operations-on-roledefinition"></a>Bewerkingen op het terrein van Roldefinitie

Roldefinitieobjecten bevatten de definitie van de ingebouwde of aangepaste rol, samen met de machtigingen die door die roltoewijzing worden verleend. Deze bron geeft zowel aangepaste roldefinities als ingebouwde mapRollen weer (die worden weergegeven in de equivalente vorm van roleDefinition). Vandaag de dag kan een Azure AD-organisatie maximaal 30 unieke aangepaste roledefinities hebben gedefinieerd.

### <a name="create-operations-on-roledefinition"></a>Bewerkingen maken op roledefinition

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

### <a name="read-operations-on-roledefinition"></a>Lees Bewerkingen op RoleDefinition

``` PowerShell
# Get all role definitions
Get-AzureADMSRoleDefinitions

# Get single role definition by objectId
Get-AzureADMSRoleDefinition -Id 86593cfc-114b-4a15-9954-97c3494ef49b

# Get single role definition by templateId
Get-AzureADMSRoleDefinition -Filter "templateId eq 'c4e39bd9-1100-46d3-8c65-fb160da0071f'"
```

### <a name="update-operations-on-roledefinition"></a>Bewerkingen bijwerken op roledefinition

``` PowerShell
# Update role definition
# This works for any writable property on role definition. You can replace display name with other
# valid properties.
Set-AzureADMSRoleDefinition -Id c4e39bd9-1100-46d3-8c65-fb160da0071f -DisplayName "Updated DisplayName"
```

### <a name="delete-operations-on-roledefinition"></a>Bewerkingen op RoleDefinition verwijderen

``` PowerShell
# Delete role definition
Remove-AzureADMSRoleDefinitions -Id c4e39bd9-1100-46d3-8c65-fb160da0071f
```

## <a name="operations-on-roleassignment"></a>Bewerkingen op roleassignment

Roltoewijzingen bevatten informatie die een bepaalde beveiligingsprincipal (een hoofdvoor gebruiker of toepassingsservice) koppelt aan een roldefinitie. Indien nodig u een bereik van één Azure AD-bron toevoegen voor de toegewezen machtigingen.  Het beperken van het bereik van machtigingen wordt ondersteund voor ingebouwde en aangepaste rollen.

### <a name="create-operations-on-roleassignment"></a>Bewerkingen maken op roleassignment

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

### <a name="read-operations-on-roleassignment"></a>Bewerkingen lezen op roleassignment

``` PowerShell
# Get role assignments for a given principal
Get-AzureADMSRoleAssignment -Filter "principalId eq '27c8ca78-ab1c-40ae-bd1b-eaeebd6f68ac'"

# Get role assignments for a given role definition 
Get-AzureADMSRoleAssignment -Filter "roleDefinitionId eq '355aed8a-864b-4e2b-b225-ea95482e7570'"
```

### <a name="delete-operations-on-roleassignment"></a>Bewerkingen bij roleassignment verwijderen

``` PowerShell
# Delete role assignment
Remove-AzureADMSRoleAssignment -Id 'qiho4WOb9UKKgng_LbPV7tvKaKRCD61PkJeKMh7Y458-1'
```

## <a name="next-steps"></a>Volgende stappen

- Deel met ons op het [Azure AD-forum voor beheerfuncties](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
- Zie [Beheerdersrollen toewijzen](directory-assign-admin-roles.md)voor meer informatie over rollen en azure AD-beheerdersroltoewijzingen .
- Zie een vergelijking van [standaardmachtigingen voor gasten en leden](../fundamentals/users-default-permissions.md)voor standaardgebruikersmachtigingen voor standaardgebruikers.
