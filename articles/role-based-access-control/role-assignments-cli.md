---
title: Roltoewijzingen toevoegen of verwijderen met Azure RBAC en Azure CLI
description: Meer informatie over het verlenen van toegang tot Azure-bronnen voor gebruikers, groepen, serviceprincipals of beheerde identiteiten met Behulp van RBAC (Azure ROLE- en Azure CLI).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: b32df50715d5e7276861e0696df1bd6ceb3f684e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245666"
---
# <a name="add-or-remove-role-assignments-using-azure-rbac-and-azure-cli"></a>Roltoewijzingen toevoegen of verwijderen met Azure RBAC en Azure CLI

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)]In dit artikel wordt beschreven hoe u rollen toewijzen met Azure CLI.

## <a name="prerequisites"></a>Vereisten

Als u roltoewijzingen wilt toevoegen of verwijderen, moet u het als ander beschikken:

- `Microsoft.Authorization/roleAssignments/write`en `Microsoft.Authorization/roleAssignments/delete` machtigingen, zoals [beheerder van gebruikerstoegang](built-in-roles.md#user-access-administrator) of [eigenaar](built-in-roles.md#owner)
- [Bash in Azure Cloud Shell](/azure/cloud-shell/overview) of Azure [CLI](/cli/azure)

## <a name="get-object-ids"></a>Object-geïdentificeerde objecten oppakken

Als u roltoewijzingen wilt toevoegen of verwijderen, moet u mogelijk de unieke id van een object opgeven. De ID heeft `11111111-1111-1111-1111-111111111111`het formaat: . U de id krijgen via de Azure-portal of Azure CLI.

### <a name="user"></a>Gebruiker

Als u de object-id voor een Azure AD-gebruiker wilt krijgen, u [de az-advertentiegebruikersweergeven gebruiken.](/cli/azure/ad/user#az-ad-user-show)

```azurecli
az ad user show --id "{email}" --query objectId --output tsv
```

### <a name="group"></a>Groep

Als u de object-id voor een Azure AD-groep wilt krijgen, u [de lijst met az-advertentiegroepen](/cli/azure/ad/group#az-ad-group-show) of [de az-advertentiegroep gebruiken.](/cli/azure/ad/group#az-ad-group-list)

```azurecli
az ad group show --group "{name}" --query objectId --output tsv
```

### <a name="application"></a>Toepassing

Als u de object-id voor een Azure AD-serviceprincipal (identiteit die door een toepassing wordt gebruikt) wilt krijgen, u [de lijst van AZ-ad sp gebruiken.](/cli/azure/ad/sp#az-ad-sp-list) Voor een serviceprincipal gebruikt u de object-id en **niet** de toepassings-id.

```azurecli
az ad sp list --display-name "{name}" --query [].objectId --output tsv
```

## <a name="add-a-role-assignment"></a>Een roltoewijzing toevoegen

In RBAC voegt u een roltoewijzing toe om toegang te verlenen om toegang te verlenen.

### <a name="user-at-a-resource-group-scope"></a>Gebruiker bij een resourcegroepbereik

Als u een roltoewijzing voor een gebruiker wilt toevoegen aan een resourcegroepbereik, gebruikt u [az-roltoewijzing maken](/cli/azure/role/assignment#az-role-assignment-create).

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --resource-group <resource_group>
```

In het volgende voorbeeld wordt de rol *Virtuele machinebijdrager toewijsd* op *de\@contoso.com* gebruiker van de *farma-salesresourcegroep:*

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="using-the-unique-role-id"></a>De unieke rol-ID gebruiken

Er zijn een paar momenten waarop een rolnaam kan veranderen, bijvoorbeeld:

- U gebruikt uw eigen aangepaste rol en u besluit de naam te wijzigen.
- U gebruikt een voorbeeldrol met **(Preview)** in de naam. Wanneer de rol wordt vrijgegeven, wordt de rol hernoemd.

> [!IMPORTANT]
> Een preview-versie wordt geleverd zonder een servicelevelovereenkomst en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie.

Zelfs als een rol wordt hernoemd, wordt de rol-ID niet gewijzigd. Als u scripts of automatisering gebruikt om uw roltoewijzingen te maken, is het een goede gewoonte om de unieke rol-id te gebruiken in plaats van de rolnaam. Als een rol wordt hernoemd, werken uw scripts dus eerder.

Als u een roltoewijzing wilt toevoegen met de unieke rol-id in plaats van de rolnaam, gebruikt u [az-roltoewijzing maken](/cli/azure/role/assignment#az-role-assignment-create).

```azurecli
az role assignment create --role <role_id> --assignee <assignee> --resource-group <resource_group>
```

In het volgende voorbeeld wordt de rol [virtuele machinebijdrager toewijsd](built-in-roles.md#virtual-machine-contributor) aan de *contoso.com\@* gebruiker van de *farma-salesbron.* Als u de unieke rol-id wilt krijgen, u [de lijst met az-rollendefinitie](/cli/azure/role/definition#az-role-definition-list) gebruiken of [ingebouwde rollen voor Azure-resources bekijken.](built-in-roles.md)

```azurecli
az role assignment create --role 9980e02c-c2be-4d73-94e8-173b1dc7cf3c --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="group-at-a-subscription-scope"></a>Groeperen op een abonnementsbereik

Als u een roltoewijzing voor een groep wilt toevoegen, gebruikt u [az-roltoewijzing maken](/cli/azure/role/assignment#az-role-assignment-create). Zie [Object-id's opvragen voor](#get-object-ids)informatie over het krijgen van de object-id van de groep.

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

In het volgende voorbeeld wordt de *rol Reader* toewijst aan de Ann *Mack Team-groep* met ID 22222222-2222-2222-2222222222222 222222 22 22 222 22222 bij een abonnementsscope.

```azurecli
az role assignment create --role Reader --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000
```

### <a name="group-at-a-resource-scope"></a>Groeperen op een resourcebereik

Als u een roltoewijzing voor een groep wilt toevoegen, gebruikt u [az-roltoewijzing maken](/cli/azure/role/assignment#az-role-assignment-create). Zie [Object-id's opvragen voor](#get-object-ids)informatie over het krijgen van de object-id van de groep.

In het volgende voorbeeld wordt de rol *van virtuele machinebijdrager* toete wijs aan de *Ann Mack Team-groep* met ID 22222222-2222-2222-2222222222 222222 22 2222222 bij een resourcebereik voor een virtueel netwerk genaamd *pharma-sales-project-network*.

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/pharma-sales/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
```

### <a name="application-at-a-resource-group-scope"></a>Toepassing op een bereik van een resourcegroep

Als u een roltoewijzing voor een toepassing wilt toevoegen, gebruikt u [az-roltoewijzing maken](/cli/azure/role/assignment#az-role-assignment-create). Zie [Object-id's opvragen voor](#get-object-ids)informatie over het krijgen van de object-id van de toepassing.

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group>
```

In het volgende voorbeeld wordt de rol *virtuele machineinzender toegevoegd* aan een toepassing met object-ID 44444444-4444-4444-444444444444444 444444. *pharma-sales*

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 44444444-4444-4444-4444-444444444444 --resource-group pharma-sales
```

### <a name="user-at-a-subscription-scope"></a>Gebruiker bij een abonnementsbereik

Als u een roltoewijzing wilt toevoegen voor een gebruiker op een abonnementsbereik, gebruikt u [az-roltoewijzing maken](/cli/azure/role/assignment#az-role-assignment-create). Als u de abonnements-ID wilt krijgen, u deze vinden op het **blade Abonnementen** in de Azure-portal of u de lijst [met AZ-account](/cli/azure/account#az-account-list)gebruiken.

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --subscription <subscription_name_or_id>
```

In het volgende voorbeeld wordt de *rol Reader* toewijst aan de *\@anm-example.com* gebruiker bij een abonnementsbereik.

```azurecli
az role assignment create --role "Reader" --assignee annm@example.com --subscription 00000000-0000-0000-0000-000000000000
```

### <a name="user-at-a-management-group-scope"></a>Gebruiker bij een beheergroep

Als u een roltoewijzing voor een gebruiker wilt toevoegen in een beheergroepbereik, gebruikt u [az-roltoewijzing maken](/cli/azure/role/assignment#az-role-assignment-create). Als u de beheergroep-id wilt krijgen, u deze vinden op het blade **van beheergroepen** in de Azure-portal of u [de lijst met AZ-accountbeheergroepen](/cli/azure/account/management-group#az-account-management-group-list)gebruiken.

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

In het volgende voorbeeld wordt de rol *Billing Reader* toegeschreven aan de gebruiker van *Alain\@example.com* bij een beheergroep.

```azurecli
az role assignment create --role "Billing Reader" --assignee alain@example.com --scope /providers/Microsoft.Management/managementGroups/marketing-group
```

### <a name="new-service-principal"></a>Nieuwe serviceprincipal

Als u een nieuwe serviceprincipal maakt en onmiddellijk probeert een rol toe te wijzen aan die serviceprincipal, kan die roltoewijzing in sommige gevallen mislukken. Als u bijvoorbeeld een script gebruikt om een nieuwe beheerde identiteit te maken en vervolgens probeert een rol toe te wijzen aan die serviceprincipal, kan de roltoewijzing mislukken. De reden voor deze fout is waarschijnlijk een replicatievertraging. De serviceprincipal wordt in één regio gemaakt; De roltoewijzing kan echter plaatsvinden in een andere regio die de serviceprincipal nog niet heeft gerepliceerd. Als u dit scenario wilt aanpakken, moet u het hoofdtype opgeven bij het maken van de roltoewijzing.

Als u een roltoewijzing wilt toevoegen, gebruikt `--assignee-object-id`u [az-roltoewijzing maken,](/cli/azure/role/assignment#az-role-assignment-create)geeft u een waarde op voor , en stelt u deze in op `--assignee-principal-type` `ServicePrincipal`.

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --assignee-principal-type <assignee_principal_type> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

In het volgende voorbeeld wordt de rol *virtuele machinebijdrager toewijsd* op de *door MSI-test* beheerde identiteit op het bereik van de *farma-sales* resourcegroep:

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 33333333-3333-3333-3333-333333333333 --assignee-principal-type ServicePrincipal --resource-group pharma-sales
```

## <a name="remove-a-role-assignment"></a>Roltoewijzing verwijderen

Als u in RBAC de toegang wilt verwijderen, verwijdert u een roltoewijzing door [az-roltoewijzing te verwijderen:](/cli/azure/role/assignment#az-role-assignment-delete)

```azurecli
az role assignment delete --assignee <assignee> --role <role_name_or_id> --resource-group <resource_group>
```

In het volgende voorbeeld wordt de roltoewijzing *voor virtuele machineinzender* verwijderd van de *\@contoso.com-gebruiker* in de brongroep *farma-verkoop:*

```azurecli
az role assignment delete --assignee patlong@contoso.com --role "Virtual Machine Contributor" --resource-group pharma-sales
```

In het volgende voorbeeld wordt de *rol Reader* verwijderd uit de Ann *Mack Team-groep* met ID 22222222-2222-2222-2222222222222 2222222 bij een abonnementsbereik. Zie [Object-id's opvragen voor](#get-object-ids)informatie over het krijgen van de object-id van de groep.

```azurecli
az role assignment delete --assignee 22222222-2222-2222-2222-222222222222 --role "Reader" --subscription 00000000-0000-0000-0000-000000000000
```

In het volgende voorbeeld wordt de rol *Billing Reader* verwijderd uit de gebruiker van *Alain\@example.com* bij de beheergroep. Om de ID van de beheergroep te krijgen, u [de az-accountmanagementgroeplijst](/cli/azure/account/management-group#az-account-management-group-list)gebruiken.

```azurecli
az role assignment delete --assignee alain@example.com --role "Billing Reader" --scope /providers/Microsoft.Management/managementGroups/marketing-group
```

## <a name="next-steps"></a>Volgende stappen

- [Roltoewijzingen weergeven met Azure RBAC en Azure CLI](role-assignments-list-cli.md)
- [De Azure CLI gebruiken om Azure-resources en -bronnengroepen te beheren](../azure-resource-manager/cli-azure-resource-manager.md)
