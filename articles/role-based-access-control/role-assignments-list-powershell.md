---
title: Roltoewijzingen weergeven met Azure RBAC en Azure PowerShell
description: Meer informatie over het bepalen van welke resources gebruikers, groepen, serviceprincipals of beheerde identiteiten toegang hebben tot het gebruik van RBAC (Azure Role-based Access Control) en Azure PowerShell.
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
ms.date: 01/10/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 0ec3153e5b1bfbe04a079d1cfc44e8e8709784d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75931157"
---
# <a name="list-role-assignments-using-azure-rbac-and-azure-powershell"></a>Roltoewijzingen weergeven met Azure RBAC en Azure PowerShell

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)]In dit artikel wordt beschreven hoe u roltoewijzingen weergeven met Azure PowerShell.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

> [!NOTE]
> Als uw organisatie beheerfuncties heeft uitbesteed aan een serviceprovider die [azure-gedelegeerd resourcebeheer](../lighthouse/concepts/azure-delegated-resource-management.md)gebruikt, worden roltoewijzingen die zijn geautoriseerd door die serviceprovider, hier niet weergegeven.

## <a name="prerequisites"></a>Vereisten

- [PowerShell in Azure Cloud Shell](/azure/cloud-shell/overview) of Azure [PowerShell](/powershell/azure/install-az-ps)

## <a name="list-role-assignments-for-the-current-subscription"></a>Roltoewijzingen voor het huidige abonnement aanbieden

De eenvoudigste manier om een lijst te krijgen van alle roltoewijzingen in het huidige abonnement (inclusief overgenomen roltoewijzingen van root- en beheergroepen) is door [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) zonder parameters te gebruiken.

```azurepowershell
Get-AzRoleAssignment
```

```Example
PS C:\> Get-AzRoleAssignment

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
DisplayName        : Alain
SignInName         : alain@example.com
RoleDefinitionName : Storage Blob Data Reader
RoleDefinitionId   : 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/providers/Microsoft.Authorization/roleAssignments/33333333-3333-3333-3333-333333333333
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Marketing
SignInName         :
RoleDefinitionName : Contributor
RoleDefinitionId   : b24988ac-6180-42a0-ab88-20f7382dd24c
ObjectId           : 22222222-2222-2222-2222-222222222222
ObjectType         : Group
CanDelegate        : False

...
```

## <a name="list-role-assignments-for-a-subscription"></a>Roltoewijzingen weergeven voor een abonnement

Als u alle roltoewijzingen op een abonnementsgebied wilt weergeven, gebruikt u [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment). Als u de abonnements-ID wilt krijgen, u deze vinden op het blade **Abonnementen** in de Azure-portal of u [Get-AzSubscription gebruiken.](/powershell/module/Az.Accounts/Get-AzSubscription)

```azurepowershell
Get-AzRoleAssignment -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> Get-AzRoleAssignment -Scope /subscriptions/00000000-0000-0000-0000-000000000000
```

## <a name="list-role-assignments-for-a-user"></a>Roltoewijzingen voor een gebruiker weergeven

Als u alle rollen wilt weergeven die aan een bepaalde gebruiker zijn toegewezen, gebruikt u [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment).

```azurepowershell
Get-AzRoleAssignment -SignInName <email_or_userprincipalname>
```

```Example
PS C:\> Get-AzRoleAssignment -SignInName isabella@example.com | FL DisplayName, RoleDefinitionName, Scope

DisplayName        : Isabella Simonsen
RoleDefinitionName : BizTalk Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
```

Als u alle rollen wilt weergeven die aan een bepaalde gebruiker zijn toegewezen en de rollen die zijn toegewezen aan de groepen waartoe de gebruiker behoort, gebruikt u [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment).

```azurepowershell
Get-AzRoleAssignment -SignInName <email_or_userprincipalname> -ExpandPrincipalGroups
```

```Example
Get-AzRoleAssignment -SignInName isabella@example.com -ExpandPrincipalGroups | FL DisplayName, RoleDefinitionName, Scope
```

## <a name="list-role-assignments-for-a-resource-group"></a>Roltoewijzingen voor een resourcegroep opvragen

Als u alle roltoewijzingen wilt weergeven in een resourcegroepbereik, gebruikt u [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment).

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

## <a name="list-role-assignments-for-a-management-group"></a>Roltoewijzingen voor een beheergroep opsommen

Als u alle roltoewijzingen wilt weergeven in een beheergroepbereik, gebruikt u [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment). Als u de beheergroep-id wilt krijgen, u deze vinden op het blade **van beheergroepen** in de Azure-portal of u [Get-AzManagementGroup gebruiken.](/powershell/module/az.resources/get-azmanagementgroup)

```azurepowershell
Get-AzRoleAssignment -Scope /providers/Microsoft.Management/managementGroups/<group_id>
```

```Example
PS C:\> Get-AzRoleAssignment -Scope /providers/Microsoft.Management/managementGroups/marketing-group
```

## <a name="list-role-assignments-for-classic-service-administrator-and-co-administrators"></a>Roltoewijzingen voor klassieke servicebeheerders en medebeheerders weergeven

Als u roltoewijzingen wilt weergeven voor de klassieke abonnementsbeheerder en medebeheerders, gebruikt u [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment).

```azurepowershell
Get-AzRoleAssignment -IncludeClassicAdministrators
```

## <a name="list-role-assignments-for-a-managed-identity"></a>Roltoewijzingen voor een beheerde identiteit opsommen

1. De object-id van de door het systeem toegewezen of door de gebruiker toegewezen beheerde identiteit opte halen. 

    Als u de object-id van een door de gebruiker toegewezen beheerde identiteit wilt krijgen, u [Get-AzADServicePrincipal gebruiken.](/powershell/module/az.resources/get-azadserviceprincipal)

    ```azurepowershell
    Get-AzADServicePrincipal -DisplayNameBeginsWith "<name> or <vmname>"
    ```

1. Als u de roltoewijzingen wilt weergeven, gebruikt u [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment).

    ```azurepowershell
    Get-AzRoleAssignment -ObjectId <objectid>
    ```

## <a name="next-steps"></a>Volgende stappen

- [Roltoewijzingen toevoegen of verwijderen met Azure RBAC en Azure PowerShell](role-assignments-powershell.md)
