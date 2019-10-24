---
title: Toegang tot Azure-resources beheren met RBAC en Azure PowerShell | Microsoft Docs
description: Meer informatie over het beheren van toegang tot Azure-resources voor gebruikers, groepen en toepassingen met behulp van RBAC (op rollen gebaseerd toegangs beheer) en Azure PowerShell. U vindt hier instructies voor het weergeven van toegang, het verlenen van toegang en het intrekken van toegang.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 9e225dba-9044-4b13-b573-2f30d77925a9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/11/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 5a6ed66efa0f73f957c3acb048136a5328f9c264
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/22/2019
ms.locfileid: "72750168"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-azure-powershell"></a>Toegang tot Azure-resources beheren met RBAC en Azure PowerShell

[Op rollen gebaseerd toegangsbeheer (RBAC)](overview.md) is de manier waarop u de toegang tot Azure-resources beheert. In dit artikel wordt beschreven hoe u de toegang beheert voor gebruikers, groepen en toepassingen met RBAC en Azure PowerShell.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

Als u de toegang wilt beheren, hebt u een van de volgende opties nodig:

* [Power shell in Azure Cloud Shell](/azure/cloud-shell/overview)
* [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="list-roles"></a>Rollen opvragen

### <a name="list-all-available-roles"></a>Alle beschik bare rollen weer geven

Gebruik [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition)om de RBAC-rollen weer te geven die beschikbaar zijn voor toewijzing en om de bewerkingen te controleren waartoe ze toegang verlenen.

```azurepowershell
Get-AzRoleDefinition | FT Name, Description
```

```Example
AcrImageSigner                                    acr image signer
AcrQuarantineReader                               acr quarantine data reader
AcrQuarantineWriter                               acr quarantine data writer
API Management Service Contributor                Can manage service and the APIs
API Management Service Operator Role              Can manage service but not the APIs
API Management Service Reader Role                Read-only access to service and APIs
Application Insights Component Contributor        Can manage Application Insights components
Application Insights Snapshot Debugger            Gives user permission to use Application Insights Snapshot Debugge...
Automation Job Operator                           Create and Manage Jobs using Automation Runbooks.
Automation Operator                               Automation Operators are able to start, stop, suspend, and resume ...
...
```

### <a name="list-a-specific-role"></a>Een specifieke rol weer geven

Gebruik [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition)om een specifieke rol weer te geven.

```azurepowershell
Get-AzRoleDefinition <role_name>
```

```Example
PS C:\> Get-AzRoleDefinition "Contributor"

Name             : Contributor
Id               : b24988ac-6180-42a0-ab88-20f7382dd24c
IsCustom         : False
Description      : Lets you manage everything except access to resources.
Actions          : {*}
NotActions       : {Microsoft.Authorization/*/Delete, Microsoft.Authorization/*/Write,
                   Microsoft.Authorization/elevateAccess/Action}
DataActions      : {}
NotDataActions   : {}
AssignableScopes : {/}
```

## <a name="list-a-role-definition"></a>Een roldefinitie weer geven

### <a name="list-a-role-definition-in-json-format"></a>Een roldefinitie in JSON-indeling weer geven

Gebruik [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition)om een ROLDEFINITIE in JSON-indeling weer te geven.

```azurepowershell
Get-AzRoleDefinition <role_name> | ConvertTo-Json
```

```Example
PS C:\> Get-AzRoleDefinition "Contributor" | ConvertTo-Json

{
  "Name": "Contributor",
  "Id": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "IsCustom": false,
  "Description": "Lets you manage everything except access to resources.",
  "Actions": [
    "*"
  ],
  "NotActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action",
    "Microsoft.Blueprint/blueprintAssignments/write",
    "Microsoft.Blueprint/blueprintAssignments/delete"
  ],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

### <a name="list-actions-of-a-role"></a>Acties van een rol weer geven

Gebruik [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition)om de acties voor een specifieke rol weer te geven.

```azurepowershell
Get-AzRoleDefinition <role_name> | FL Actions, NotActions
```

```Example
PS C:\> Get-AzRoleDefinition "Contributor" | FL Actions, NotActions

Actions    : {*}
NotActions : {Microsoft.Authorization/*/Delete, Microsoft.Authorization/*/Write,
             Microsoft.Authorization/elevateAccess/Action,
             Microsoft.Blueprint/blueprintAssignments/write...}
```

```azurepowershell
(Get-AzRoleDefinition <role_name>).Actions
```

```Example
PS C:\> (Get-AzRoleDefinition "Virtual Machine Contributor").Actions

Microsoft.Authorization/*/read
Microsoft.Compute/availabilitySets/*
Microsoft.Compute/locations/*
Microsoft.Compute/virtualMachines/*
Microsoft.Compute/virtualMachineScaleSets/*
Microsoft.DevTestLab/schedules/*
Microsoft.Insights/alertRules/*
Microsoft.Network/applicationGateways/backendAddressPools/join/action
Microsoft.Network/loadBalancers/backendAddressPools/join/action
...
```

## <a name="list-access"></a>Toegang opvragen

In RBAC kunt u de roltoewijzingen weer geven om toegang weer te geven.

### <a name="list-role-assignments-for-a-user"></a>Roltoewijzingen voor een gebruiker weergeven

Gebruik [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment)om alle rollen weer te geven die aan een opgegeven gebruiker zijn toegewezen.

```azurepowershell
Get-AzRoleAssignment -SignInName <email_or_userprincipalname>
```

```Example
PS C:\> Get-AzRoleAssignment -SignInName isabella@example.com | FL DisplayName, RoleDefinitionName, Scope

DisplayName        : Isabella Simonsen
RoleDefinitionName : BizTalk Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
```

Gebruik [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment)om alle rollen weer te geven die zijn toegewezen aan een opgegeven gebruiker en de rollen die zijn toegewezen aan de groepen waartoe de gebruiker behoort.

```azurepowershell
Get-AzRoleAssignment -SignInName <email_or_userprincipalname> -ExpandPrincipalGroups
```

```Example
Get-AzRoleAssignment -SignInName isabella@example.com -ExpandPrincipalGroups | FL DisplayName, RoleDefinitionName, Scope
```

### <a name="list-role-assignments-at-a-resource-group-scope"></a>Roltoewijzingen weer geven in een bereik van een resource groep

Als u alle roltoewijzingen wilt weer geven in een bereik van een resource groep, gebruikt u [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment).

```azurepowershell
Get-AzRoleAssignment -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> Get-AzRoleAssignment -ResourceGroupName pharma-sales | FL DisplayName, RoleDefinitionName, Scope

DisplayName        : Alain Charon
RoleDefinitionName : Backup Operator
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales

DisplayName        : Isabella Simonsen
RoleDefinitionName : BizTalk Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales

DisplayName        : Alain Charon
RoleDefinitionName : Virtual Machine Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
```

### <a name="list-role-assignments-at-a-subscription-scope"></a>Roltoewijzingen in een abonnements bereik weer geven

Gebruik [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment)om alle roltoewijzingen in een abonnements bereik weer te geven. Als u de abonnements-ID wilt ophalen, kunt u deze vinden op de Blade **abonnementen** in de Azure portal of u kunt [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription)gebruiken.

```azurepowershell
Get-AzRoleAssignment -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> Get-AzRoleAssignment -Scope /subscriptions/00000000-0000-0000-0000-000000000000
```

### <a name="list-role-assignments-at-a-management-group-scope"></a>Roltoewijzingen in een beheer groeps bereik weer geven

Gebruik [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment)om alle roltoewijzingen in een beheer groeps bereik weer te geven. Als u de beheer groep-ID wilt ophalen, kunt u deze vinden op de Blade **beheer groepen** in de Azure portal of u kunt [Get-AzManagementGroup](/powershell/module/az.resources/get-azmanagementgroup)gebruiken.

```azurepowershell
Get-AzRoleAssignment -Scope /providers/Microsoft.Management/managementGroups/<group_id>
```

```Example
PS C:\> Get-AzRoleAssignment -Scope /providers/Microsoft.Management/managementGroups/marketing-group
```

### <a name="list-role-assignments-for-classic-service-administrator-and-co-administrators"></a>Roltoewijzingen voor de klassieke service beheerder en mede beheerders weer geven

Gebruik [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment)om roltoewijzingen voor de klassieke abonnements beheerder en mede beheerders weer te geven.

```azurepowershell
Get-AzRoleAssignment -IncludeClassicAdministrators
```

## <a name="grant-access"></a>Toegang verlenen

In RBAC verleent u toegang door een roltoewijzing te maken.

### <a name="search-for-object-ids"></a>Zoeken naar object-Id's

Als u een rol wilt toewijzen, moet u het object (gebruiker, groep of toepassing) en het bereik identificeren.

Als u de abonnements-ID wilt ophalen, kunt u deze vinden op de Blade **abonnementen** in de Azure portal of u kunt [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription)gebruiken.

Gebruik [Get-AzADUser](/powershell/module/az.resources/get-azaduser)om de object-id voor een Azure AD-gebruiker op te halen.

```azurepowershell
Get-AzADUser -StartsWith <string_in_quotes>
```

Gebruik [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup)om de object-id voor een Azure AD-groep op te halen.

```azurepowershell
Get-AzADGroup -SearchString <group_name_in_quotes>
```

Gebruik [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal)om de object-id voor een Azure AD-Service-Principal of-toepassing op te halen.

```azurepowershell
Get-AzADServicePrincipal -SearchString <service_name_in_quotes>
```

### <a name="create-a-role-assignment-for-a-user-at-a-resource-group-scope"></a>Een roltoewijzing maken voor een gebruiker in een bereik van een resource groep

Gebruik [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)om toegang te verlenen aan een gebruiker in een bereik van een resource groep.

```azurepowershell
New-AzRoleAssignment -SignInName <email_or_userprincipalname> -RoleDefinitionName <role_name> -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> New-AzRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Virtual Machine Contributor" -ResourceGroupName pharma-sales


RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/pr
                     oviders/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

### <a name="create-a-role-assignment-using-the-unique-role-id"></a>Een roltoewijzing maken met behulp van de unieke rol-ID

Er zijn een aantal keren dat een rolnaam kan worden gewijzigd, bijvoorbeeld:

- U gebruikt uw eigen aangepaste rol en u besluit de naam te wijzigen.
- U gebruikt een preview-functie met **(preview)** in de naam. Wanneer de rol wordt vrijgegeven, wordt de naam van de rol gewijzigd.

> [!IMPORTANT]
> Een preview-versie wordt zonder service level agreement gegeven en wordt niet aanbevolen voor productie werkbelastingen. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Zelfs als een rol een andere naam heeft gekregen, wordt de rol-ID niet gewijzigd. Als u scripts of automatisering gebruikt om roltoewijzingen te maken, is het een best practice om de unieke rol-ID te gebruiken in plaats van de rolnaam. Als de naam van een rol wordt gewijzigd, zijn uw scripts waarschijnlijker goed.

Als u een roltoewijzing wilt maken met behulp van de unieke rol-ID in plaats van de rolnaam, gebruikt u [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment).

```azurepowershell
New-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionId <role_id> -ResourceGroupName <resource_group_name>
```

In het volgende voor beeld wordt de rol [Inzender voor virtuele machines](built-in-roles.md#virtual-machine-contributor) toegewezen aan *alain@example.com* gebruiker op het *Pharma* van de resource groep. Als u de unieke rol-ID wilt ophalen, kunt u [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) gebruiken of [ingebouwde rollen voor Azure-resources](built-in-roles.md)weer geven.

```Example
PS C:\> New-AzRoleAssignment -ObjectId 44444444-4444-4444-4444-444444444444 -RoleDefinitionId 9980e02c-c2be-4d73-94e8-173b1dc7cf3c -Scope /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/providers/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

### <a name="create-a-role-assignment-for-a-group-at-a-resource-scope"></a>Een roltoewijzing maken voor een groep in een resource bereik

Gebruik [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)om toegang te verlenen aan een groep in een resource bereik.

```azurepowershell
New-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -ResourceName <resource_name> -ResourceType <resource_type> -ParentResource <parent resource> -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> Get-AzADGroup -SearchString "Pharma"

SecurityEnabled DisplayName         Id                                   Type
--------------- -----------         --                                   ----
           True Pharma Sales Admins aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa Group

PS C:\> New-AzRoleAssignment -ObjectId aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa -RoleDefinitionName "Virtual Machine Contributor" -ResourceName RobertVirtualNetwork -ResourceType Microsoft.Network/virtualNetworks -ResourceGroupName RobertVirtualNetworkResourceGroup

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/RobertVirtualNetwork/providers/Microsoft.Authorizat
                     ion/roleAssignments/bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/RobertVirtualNetwork
DisplayName        : Pharma Sales Admins
SignInName         :
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa
ObjectType         : Group
CanDelegate        : False
```

### <a name="create-a-role-assignment-for-an-application-at-a-subscription-scope"></a>Een roltoewijzing maken voor een toepassing op een abonnements bereik

Gebruik [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)om toegang te verlenen aan een toepassing op een abonnements bereik.

```azurepowershell
New-AzRoleAssignment -ObjectId <application_id> -RoleDefinitionName <role_name> -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> New-AzRoleAssignment -ObjectId 77777777-7777-7777-7777-777777777777 -RoleDefinitionName "Reader" -Scope /subscriptions/00000000-0000-0000-0000-000000000000

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/66666666-6666-6666-6666-666666666666
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
DisplayName        : MyApp1
SignInName         :
RoleDefinitionName : Reader
RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
ObjectId           : 77777777-7777-7777-7777-777777777777
ObjectType         : ServicePrincipal
CanDelegate        : False
```

### <a name="create-a-role-assignment-for-a-user-at-a-management-group-scope"></a>Een roltoewijzing maken voor een gebruiker in een bereik van een beheer groep

Gebruik [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)om toegang te verlenen aan een gebruiker op een beheer groeps bereik. Als u de beheer groep-ID wilt ophalen, kunt u deze vinden op de Blade **beheer groepen** in de Azure portal of u kunt [Get-AzManagementGroup](/powershell/module/az.resources/get-azmanagementgroup)gebruiken.

```azurepowershell
New-AzRoleAssignment -SignInName <email_or_userprincipalname> -RoleDefinitionName <role_name> -Scope /providers/Microsoft.Management/managementGroups/<group_id>
```

```Example
PS C:\> New-AzRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Billing Reader" -Scope /providers/Microsoft.Management/managementGroups/marketing-group

RoleAssignmentId   : /providers/Microsoft.Management/managementGroups/marketing-group/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
Scope              : /providers/Microsoft.Management/managementGroups/marketing-group
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Billing Reader
RoleDefinitionId   : fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

## <a name="remove-access"></a>Toegang intrekken

Als u de toegang wilt verwijderen, verwijdert u in RBAC een roltoewijzing met behulp van [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment).

In het volgende voor beeld wordt de toewijzing van de rol *Inzender voor virtuele machines* uit de *Alain \@example. com* -gebruiker in de resource groep *Pharma-Sales* verwijderd:

```Example
PS C:\> Remove-AzRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Virtual Machine Contributor" -ResourceGroupName pharma-sales
```

In het volgende voor beeld wordt de < role_name >-rol verwijderd uit < object_id > bij een abonnements bereik.

```azurepowershell
Remove-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -Scope /subscriptions/<subscription_id>
```

In het volgende voor beeld wordt de < role_name >-rol verwijderd uit < object_id-> in het bereik van de beheer groep.

```azurepowershell
Remove-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -Scope /providers/Microsoft.Management/managementGroups/<group_id>
```

Als u het volgende fout bericht wordt weer gegeven: ' de opgegeven informatie is niet toegewezen aan een roltoewijzing ', moet u ook de `-Scope`-of `-ResourceGroupName`-para meters opgeven. Zie [problemen met RBAC voor Azure-resources oplossen](troubleshooting.md#role-assignments-with-unknown-security-principal)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- [Zelf studie: een groep toegang verlenen tot Azure-resources met RBAC en Azure PowerShell](tutorial-role-assignments-group-powershell.md)
- [Zelf studie: een aangepaste rol maken voor Azure-resources met behulp van Azure PowerShell](tutorial-custom-role-powershell.md)
- [Resources beheren met Azure PowerShell](../azure-resource-manager/manage-resources-powershell.md)
