---
title: Roltoewijzingen toevoegen of verwijderen met RBAC en Azure PowerShell
description: Meer informatie over het verlenen van toegang tot Azure-bronnen voor gebruikers, groepen, serviceprincipals of beheerde identiteiten met Behulp van RBAC (Azure PowerShell) en Azure PowerShell.
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
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 68a73f622dc69b70870ddc1db16edcf406b63800
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283210"
---
# <a name="add-or-remove-role-assignments-using-azure-rbac-and-azure-powershell"></a>Roltoewijzingen toevoegen of verwijderen met Azure RBAC en Azure PowerShell

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)]In dit artikel wordt beschreven hoe u rollen toewijzen met Azure PowerShell.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

Als u roltoewijzingen wilt toevoegen of verwijderen, moet u het als ander beschikken:

- `Microsoft.Authorization/roleAssignments/write`en `Microsoft.Authorization/roleAssignments/delete` machtigingen, zoals [beheerder van gebruikerstoegang](built-in-roles.md#user-access-administrator) of [eigenaar](built-in-roles.md#owner)
- [PowerShell in Azure Cloud Shell](/azure/cloud-shell/overview) of Azure [PowerShell](/powershell/azure/install-az-ps)

## <a name="get-object-ids"></a>Object-geïdentificeerde objecten oppakken

Als u roltoewijzingen wilt toevoegen of verwijderen, moet u mogelijk de unieke id van een object opgeven. De ID heeft `11111111-1111-1111-1111-111111111111`het formaat: . U de id krijgen via de Azure-portal of Azure PowerShell.

### <a name="user"></a>Gebruiker

Als u de object-id voor een Azure AD-gebruiker wilt krijgen, u [Get-AzADUser gebruiken.](/powershell/module/az.resources/get-azaduser)

```azurepowershell
Get-AzADUser -StartsWith <string_in_quotes>
(Get-AzADUser -DisplayName <name_in_quotes>).id
```

### <a name="group"></a>Groep

Als u de object-id voor een Azure AD-groep wilt krijgen, u [Get-AzADGroup gebruiken.](/powershell/module/az.resources/get-azadgroup)

```azurepowershell
Get-AzADGroup -SearchString <group_name_in_quotes>
(Get-AzADGroup -DisplayName <group_name_in_quotes>).id
```

### <a name="application"></a>Toepassing

Als u de object-id voor een Azure AD-serviceprincipal (identiteit die door een toepassing wordt gebruikt) wilt krijgen, u [Get-AzADServicePrincipal gebruiken.](/powershell/module/az.resources/get-azadserviceprincipal) Voor een serviceprincipal gebruikt u de object-id en **niet** de toepassings-id.

```azurepowershell
Get-AzADServicePrincipal -SearchString <service_name_in_quotes>
(Get-AzADServicePrincipal -DisplayName <service_name_in_quotes>).id
```

## <a name="add-a-role-assignment"></a>Een roltoewijzing toevoegen

In RBAC voegt u een roltoewijzing toe om toegang te verlenen om toegang te verlenen.

### <a name="user-at-a-resource-group-scope"></a>Gebruiker bij een resourcegroepbereik

Als u een roltoewijzing voor een gebruiker wilt toevoegen aan een resourcegroepbereik, gebruikt u [Nieuw-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment).

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

Er zijn een paar momenten waarop een rolnaam kan veranderen, bijvoorbeeld:

- U gebruikt uw eigen aangepaste rol en u besluit de naam te wijzigen.
- U gebruikt een voorbeeldrol met **(Preview)** in de naam. Wanneer de rol wordt vrijgegeven, wordt de rol hernoemd.

> [!IMPORTANT]
> Een preview-versie wordt geleverd zonder een servicelevelovereenkomst en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie.

Zelfs als een rol wordt hernoemd, wordt de rol-ID niet gewijzigd. Als u scripts of automatisering gebruikt om uw roltoewijzingen te maken, is het een goede gewoonte om de unieke rol-id te gebruiken in plaats van de rolnaam. Als een rol wordt hernoemd, werken uw scripts dus eerder.

Als u een roltoewijzing wilt toevoegen met de unieke rol-id in plaats van de rolnaam, gebruikt u [Nieuw-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment).

```azurepowershell
New-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionId <role_id> -ResourceGroupName <resource_group_name>
```

In het volgende voorbeeld wordt de rol [Van virtuele machinebijdrager](built-in-roles.md#virtual-machine-contributor) aan *\@alain example.com* gebruiker toegeschreven op het bereik van de *farma-sales* resourcegroep. Als u de unieke rol-id wilt krijgen, u [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) gebruiken of [ingebouwde rollen voor Azure-resources bekijken.](built-in-roles.md)

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

### <a name="group-at-a-resource-scope"></a>Groeperen op een resourcebereik

Als u een roltoewijzing voor een groep wilt toevoegen aan een resourcebereik, gebruikt u [Nieuw-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment). Zie [Object-id's opvragen voor](#get-object-ids)informatie over het krijgen van de object-id van de groep.

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

### <a name="application-at-a-subscription-scope"></a>Toepassing op een abonnementsbereik

Als u een roltoewijzing voor een toepassing op een abonnementsgebied wilt toevoegen, gebruikt u [Nieuw-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment). Zie [Object-id's opvragen voor](#get-object-ids)informatie over het krijgen van de object-id van de toepassing.

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

### <a name="user-at-a-management-group-scope"></a>Gebruiker bij een beheergroep

Als u een roltoewijzing voor een gebruiker wilt toevoegen aan een beheergroepbereik, gebruikt u [Nieuw-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment). Als u de beheergroep-id wilt krijgen, u deze vinden op het blade **van beheergroepen** in de Azure-portal of u [Get-AzManagementGroup gebruiken.](/powershell/module/az.resources/get-azmanagementgroup)

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

Als u in RBAC de toegang wilt verwijderen, verwijdert u een roltoewijzing met [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment).

In het volgende voorbeeld wordt de roltoewijzing *voor virtuele machineinzender* verwijderd van de *\@alain example.com-gebruiker* in de *farma-sales* resourcegroep:

```Example
PS C:\> Remove-AzRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Virtual Machine Contributor" -ResourceGroupName pharma-sales
```

In het volgende voorbeeld wordt de <role_name>-rol verwijderd uit <object_id> bij een abonnementsbereik.

```azurepowershell
Remove-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -Scope /subscriptions/<subscription_id>
```

In het volgende voorbeeld wordt de rol <role_name> verwijderd uit <object_id> bij het bereik van de beheergroep.

```azurepowershell
Remove-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -Scope /providers/Microsoft.Management/managementGroups/<group_id>
```

Als u de foutmelding krijgt: "De verstrekte informatie wordt niet toegewezen aan `-Scope` een `-ResourceGroupName` roltoewijzing", zorg ervoor dat u ook de of parameters opgeeft. Zie [Problemen met RBAC voor Azure-bronnen voor](troubleshooting.md#role-assignments-with-unknown-security-principal)meer informatie.

## <a name="next-steps"></a>Volgende stappen

- [Roltoewijzingen weergeven met Azure RBAC en Azure PowerShell](role-assignments-list-powershell.md)
- [Zelfstudie: Een groep toegang verlenen tot Azure-bronnen met RBAC en Azure PowerShell](tutorial-role-assignments-group-powershell.md)
- [Zelfstudie: Een aangepaste rol maken voor Azure-resources met Azure PowerShell](tutorial-custom-role-powershell.md)
- [Resources beheren met Azure PowerShell](../azure-resource-manager/management/manage-resources-powershell.md)
