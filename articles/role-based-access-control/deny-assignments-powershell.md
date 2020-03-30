---
title: Toewijzingen voor Azure-resources voor Azure-resources weergeven met Azure PowerShell
description: Meer informatie over het weergeven van de gebruikers, groepen, serviceprincipals en beheerde identiteiten die toegang hebben gekregen tot specifieke Azure-bronacties op bepaalde scopes met Azure PowerShell.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/12/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 5ba18b89bd37dbd55350321c503e37ab0590ab87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77137397"
---
# <a name="list-deny-assignments-for-azure-resources-using-azure-powershell"></a>Toewijzingen voor Azure-resources voor Azure-resources weergeven met Azure PowerShell

[Toewijzingen weigeren](deny-assignments.md) blokkeren gebruikers om specifieke Azure-bronacties uit te voeren, zelfs als een roltoewijzing hen toegang verleent. In dit artikel wordt beschreven hoe u weigeringstoewijzingen weergeven met Azure PowerShell.

> [!NOTE]
> Je niet direct je eigen weigeringsopdrachten maken. Zie Toewijzingen weigeren voor informatie over het maken van [weigeringsopdrachten.](deny-assignments.md)

## <a name="prerequisites"></a>Vereisten

Als u informatie wilt krijgen over een weigeringsopdracht, moet u het:

- `Microsoft.Authorization/denyAssignments/read`machtigingen, die is opgenomen in de meeste [ingebouwde rollen voor Azure-resources](built-in-roles.md)
- [PowerShell in Azure Cloud Shell](/azure/cloud-shell/overview) of Azure [PowerShell](/powershell/azure/install-az-ps)

## <a name="list-deny-assignments"></a>Lijst met geweigerde toewijzingen weergeven

### <a name="list-all-deny-assignments"></a>Alle weigeringsopdrachten weergeven

Als u alle weigeringstoewijzingen voor het huidige abonnement wilt aanbieden, gebruikt u [Get-AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment).

```azurepowershell
Get-AzDenyAssignment
```

```Example
PS C:\> Get-AzDenyAssignment

Id                      : 22222222-2222-2222-2222-222222222222
DenyAssignmentName      : Deny assignment '22222222-2222-2222-2222-222222222222' created by Blueprint Assignment
                          '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Description             : Created by Blueprint Assignment '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Actions                 : {*}
NotActions              : {*/read}
DataActions             : {}
NotDataActions          : {}
Scope                   : /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/TestingBPLocks
DoNotApplyToChildScopes : True
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
ExcludePrincipals       : {
                          ObjectType:   ServicePrincipal
                          }
IsSystemProtected       : True

Id                      : 33333333-3333-3333-3333-333333333333
DenyAssignmentName      : Deny assignment '33333333-3333-3333-3333-333333333333' created by Blueprint Assignment
                          '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Description             : Created by Blueprint Assignment '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Actions                 : {*}
NotActions              : {*/read}
DataActions             : {}
NotDataActions          : {}
Scope                   : /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/TestingBPLocks/providers/Microsoft.Storage/storageAccounts/storep6vkuxmu4m4pq
DoNotApplyToChildScopes : True
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
ExcludePrincipals       : {
                          DisplayName:  assignment-locked-storageaccount-TestingBPLocks
                          ObjectType:   ServicePrincipal
                          ObjectId:     2311a0b7-657a-4ca2-af6f-d1c33f6d2fff
                          }
IsSystemProtected       : True
```

### <a name="list-deny-assignments-at-a-resource-group-scope"></a>Toewijzingen weigeren aanbieden op een resourcegroepbereik

Als u alle weigeringstoewijzingen wilt weergeven in een resourcegroepbereik, gebruikt u [Get-AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment).

```azurepowershell
Get-AzDenyAssignment -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> Get-AzDenyAssignment -ResourceGroupName TestingBPLocks | FL DenyAssignmentName, Scope

DenyAssignmentName : Deny assignment '22222222-2222-2222-2222-222222222222' created by Blueprint Assignment
                     '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Scope              : /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/TestingBPLocks
Principals         : {
                     DisplayName:       All Principals
                     ObjectType:        SystemDefined
                     ObjectId:  00000000-0000-0000-0000-000000000000
                     }
```

### <a name="list-deny-assignments-at-a-subscription-scope"></a>Opdrachten weigeren aanbieden bij een abonnementsbereik

Als u alle weigeringstoewijzingen op een abonnementsbereik wilt aanbieden, gebruikt u [Get-AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment). Als u de abonnements-ID wilt krijgen, u deze vinden op het blade **Abonnementen** in de Azure-portal of u [Get-AzSubscription gebruiken.](/powershell/module/Az.Accounts/Get-AzSubscription)

```azurepowershell
Get-AzDenyAssignment -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> Get-AzDenyAssignment -Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="next-steps"></a>Volgende stappen

- [Inzicht in weigeringstoewijzingen voor Azure-resources](deny-assignments.md)
- [Toewijzingen voor Azure-resources voor Azure-resources weergeven met behulp van de Azure-portal](deny-assignments-portal.md)
- [Weigeren toewijzingen voor Azure-resources weergeven met behulp van de REST API](deny-assignments-rest.md)