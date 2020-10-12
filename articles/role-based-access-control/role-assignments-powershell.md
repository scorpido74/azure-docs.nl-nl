---
title: Azure-roltoewijzingen toevoegen of verwijderen met Azure PowerShell-Azure RBAC
description: Meer informatie over het verlenen van toegang tot Azure-resources voor gebruikers, groepen, service-principals of beheerde identiteiten met behulp van Azure PowerShell en Azure op rollen gebaseerd toegangs beheer (Azure RBAC).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 9e225dba-9044-4b13-b573-2f30d77925a9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 9b0df4337a5e5faff3427222fb66caf8e02184a3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86146663"
---
# <a name="add-or-remove-azure-role-assignments-using-azure-powershell"></a>Azure-roltoewijzingen toevoegen of verwijderen met behulp van Azure PowerShell

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)] In dit artikel wordt beschreven hoe u rollen toewijst met behulp van Azure PowerShell.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

Om roltoewijzingen toe te voegen of te verwijderen, hebt u het volgende nodig:

- Machtigingen voor `Microsoft.Authorization/roleAssignments/write` en `Microsoft.Authorization/roleAssignments/delete`, zoals [Beheerder van gebruikerstoegang](built-in-roles.md#user-access-administrator) of [Eigenaar](built-in-roles.md#owner)
- [Power shell in azure Cloud shell](/azure/cloud-shell/overview) of [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="get-object-ids"></a>Object-Id's ophalen

Als u roltoewijzingen wilt toevoegen of verwijderen, moet u mogelijk de unieke ID van een object opgeven. De ID heeft de volgende indeling: `11111111-1111-1111-1111-111111111111` . U kunt de ID ophalen met behulp van de Azure Portal of Azure PowerShell.

### <a name="user"></a>Gebruiker

U kunt [Get-AzADUser](/powershell/module/az.resources/get-azaduser)gebruiken om de object-id voor een Azure AD-gebruiker op te halen.

```azurepowershell
Get-AzADUser -StartsWith <string_in_quotes>
(Get-AzADUser -DisplayName <name_in_quotes>).id
```

### <a name="group"></a>Groep

U kunt [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup)gebruiken om de object-id voor een Azure AD-groep op te halen.

```azurepowershell
Get-AzADGroup -SearchString <group_name_in_quotes>
(Get-AzADGroup -DisplayName <group_name_in_quotes>).id
```

### <a name="application"></a>Toepassing

U kunt [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal)gebruiken om de object-id op te halen voor een Azure AD-Service-Principal (identiteit die wordt gebruikt door een toepassing). Gebruik voor een service-principal de object-ID en **niet** de toepassings-id.

```azurepowershell
Get-AzADServicePrincipal -SearchString <service_name_in_quotes>
(Get-AzADServicePrincipal -DisplayName <service_name_in_quotes>).id
```

## <a name="add-a-role-assignment"></a>Een roltoewijzing toevoegen

In azure RBAC kunt u een roltoewijzing toevoegen om toegang te verlenen.

### <a name="user-at-a-resource-group-scope"></a>Gebruiker in een bereik van een resource groep

Als u een roltoewijzing wilt toevoegen voor een gebruiker in een bereik van een resource groep, gebruikt u [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment).

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

### <a name="using-the-unique-role-id"></a>De unieke rol-ID gebruiken

Er zijn een aantal keren dat een rolnaam kan worden gewijzigd, bijvoorbeeld:

- U gebruikt uw eigen aangepaste rol en u besluit de naam te wijzigen.
- U gebruikt een preview-functie met **(preview)** in de naam. Wanneer de rol wordt vrijgegeven, wordt de naam van de rol gewijzigd.

> [!IMPORTANT]
> Een preview-versie wordt zonder service level agreement gegeven en wordt niet aanbevolen voor productie werkbelastingen. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Zelfs als een rol een andere naam heeft gekregen, wordt de rol-ID niet gewijzigd. Als u scripts of automatisering gebruikt om roltoewijzingen te maken, is het een best practice om de unieke rol-ID te gebruiken in plaats van de rolnaam. Als de naam van een rol wordt gewijzigd, zijn uw scripts waarschijnlijker goed.

Als u een roltoewijzing wilt toevoegen met behulp van de unieke rol-ID in plaats van de rolnaam, gebruikt u [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment).

```azurepowershell
New-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionId <role_id> -Scope <resource_group_name/resource/management groups>
```

In het volgende voor beeld wordt de rol [Inzender voor virtuele machines](built-in-roles.md#virtual-machine-contributor) toegewezen aan *Alain \@ example.com* gebruiker op het *Pharma-Sales-* resource groeps bereik. Als u de unieke rol-ID wilt ophalen, kunt u [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) gebruiken of de [ingebouwde rollen van Azure](built-in-roles.md)bekijken.

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

### <a name="group-at-a-resource-scope"></a>Groeperen in een resource bereik

Als u een roltoewijzing wilt toevoegen voor een groep in een resource bereik, gebruikt u [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment). Zie [object-Id's ophalen](#get-object-ids)voor informatie over het ophalen van de object-id van de groep.

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

### <a name="application-at-a-subscription-scope"></a>Toepassing op een abonnements bereik

Als u een roltoewijzing wilt toevoegen voor een toepassing op een abonnements bereik, gebruikt u [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment). Zie [object-Id's ophalen](#get-object-ids)voor informatie over het ophalen van de object-id van de toepassing.

```azurepowershell
New-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -Scope /subscriptions/<subscription_id>
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

### <a name="user-at-a-management-group-scope"></a>Gebruiker op een beheer groeps bereik

Gebruik [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)om een roltoewijzing toe te voegen voor een gebruiker in een bereik van een beheer groep. Als u de beheer groep-ID wilt ophalen, kunt u deze vinden op de Blade **beheer groepen** in de Azure portal of u kunt [Get-AzManagementGroup](/powershell/module/az.resources/get-azmanagementgroup)gebruiken.

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

## <a name="remove-a-role-assignment"></a>Roltoewijzing verwijderen

In azure RBAC verwijdert u een roltoewijzing met behulp van [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment)om de toegang te verwijderen.

In het volgende voor beeld wordt de toewijzing van de rol *Inzender voor virtuele machines* uit de *Alain \@ example.com* -gebruiker voor de resource groep *Pharma-Sales* verwijderd:

```Example
PS C:\> Remove-AzRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Virtual Machine Contributor" -ResourceGroupName pharma-sales
```

In het volgende voor beeld wordt de <role_name> rol verwijderd uit <object_id> op een abonnements bereik.

```azurepowershell
Remove-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -Scope /subscriptions/<subscription_id>
```

In het volgende voor beeld wordt de <role_name> rol verwijderd uit <object_id> in het bereik van de beheer groep.

```azurepowershell
Remove-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -Scope /providers/Microsoft.Management/managementGroups/<group_id>
```

Als u het volgende fout bericht wordt weer gegeven: ' de opgegeven informatie is niet toegewezen aan een roltoewijzing ', zorg ervoor dat u ook `-Scope` de `-ResourceGroupName` para meters of opgeeft. Zie [problemen met Azure RBAC oplossen](troubleshooting.md#role-assignments-with-identity-not-found)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- [Toewijzingen van Azure-roltoewijzingen weer geven met Azure PowerShell](role-assignments-list-powershell.md)
- [Zelfstudie: een groep toegang verlenen tot Azure-resources met behulp van Azure PowerShell](tutorial-role-assignments-group-powershell.md)
- [Zelfstudie: Een aangepaste Azure-rol maken met Azure PowerShell](tutorial-custom-role-powershell.md)
- [Resources beheren met Azure PowerShell](../azure-resource-manager/management/manage-resources-powershell.md)
