---
title: Roltoewijzingen toevoegen of verwijderen met behulp van Azure RBAC en Azure CLI
description: Meer informatie over het verlenen van toegang tot Azure-resources voor gebruikers, groepen, service-principals of beheerde identiteiten met behulp van Azure op rollen gebaseerd toegangs beheer (RBAC) en Azure CLI.
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79245666"
---
# <a name="add-or-remove-role-assignments-using-azure-rbac-and-azure-cli"></a>Roltoewijzingen toevoegen of verwijderen met behulp van Azure RBAC en Azure CLI

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)] in dit artikel wordt beschreven hoe u rollen toewijst met behulp van Azure CLI.

## <a name="prerequisites"></a>Vereisten

Om roltoewijzingen toe te voegen of te verwijderen, hebt u het volgende nodig:

- `Microsoft.Authorization/roleAssignments/write`-en `Microsoft.Authorization/roleAssignments/delete` machtigingen, zoals beheerder of [eigenaar](built-in-roles.md#owner) van [gebruikers toegang](built-in-roles.md#user-access-administrator)
- [Bash in azure Cloud shell](/azure/cloud-shell/overview) of [Azure cli](/cli/azure)

## <a name="get-object-ids"></a>Object-Id's ophalen

Als u roltoewijzingen wilt toevoegen of verwijderen, moet u mogelijk de unieke ID van een object opgeven. De ID heeft de volgende indeling: `11111111-1111-1111-1111-111111111111`. U kunt de ID ophalen met behulp van de Azure Portal of Azure CLI.

### <a name="user"></a>Gebruiker

Als u de object-ID voor een Azure AD-gebruiker wilt ophalen, kunt u [AZ AD User show](/cli/azure/ad/user#az-ad-user-show)gebruiken.

```azurecli
az ad user show --id "{email}" --query objectId --output tsv
```

### <a name="group"></a>Groep

Als u de object-ID voor een Azure AD-groep wilt ophalen, kunt u [AZ Ad Group show](/cli/azure/ad/group#az-ad-group-show) of [AZ Ad Group List](/cli/azure/ad/group#az-ad-group-list)gebruiken.

```azurecli
az ad group show --group "{name}" --query objectId --output tsv
```

### <a name="application"></a>Toepassing

Als u de object-ID wilt ophalen voor een Azure AD-Service-Principal (identiteit die wordt gebruikt door een toepassing), kunt u [AZ AD SP List](/cli/azure/ad/sp#az-ad-sp-list)gebruiken. Gebruik voor een service-principal de object-ID en **niet** de toepassings-id.

```azurecli
az ad sp list --display-name "{name}" --query [].objectId --output tsv
```

## <a name="add-a-role-assignment"></a>Een roltoewijzing toevoegen

Als u in RBAC toegang wilt verlenen, voegt u een roltoewijzing toe.

### <a name="user-at-a-resource-group-scope"></a>Gebruiker in een bereik van een resource groep

Als u een roltoewijzing wilt toevoegen voor een gebruiker in een bereik van een resource groep, gebruikt u [AZ Role Assignment Create](/cli/azure/role/assignment#az-role-assignment-create).

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --resource-group <resource_group>
```

In het volgende voor beeld wordt de rol van *Inzender voor virtuele machines* toegewezen aan *patlong\@contoso.com* -gebruiker op het *Pharma-Sales-* resource groeps bereik:

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="using-the-unique-role-id"></a>De unieke rol-ID gebruiken

Er zijn een aantal keren dat een rolnaam kan worden gewijzigd, bijvoorbeeld:

- U gebruikt uw eigen aangepaste rol en u besluit de naam te wijzigen.
- U gebruikt een preview-functie met **(preview)** in de naam. Wanneer de rol wordt vrijgegeven, wordt de naam van de rol gewijzigd.

> [!IMPORTANT]
> Een preview-versie wordt zonder service level agreement gegeven en wordt niet aanbevolen voor productie werkbelastingen. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Zelfs als een rol een andere naam heeft gekregen, wordt de rol-ID niet gewijzigd. Als u scripts of automatisering gebruikt om roltoewijzingen te maken, is het een best practice om de unieke rol-ID te gebruiken in plaats van de rolnaam. Als de naam van een rol wordt gewijzigd, zijn uw scripts waarschijnlijker goed.

Als u een roltoewijzing wilt toevoegen met behulp van de unieke rol-ID in plaats van de rolnaam, gebruikt u [AZ Role Assignment Create](/cli/azure/role/assignment#az-role-assignment-create).

```azurecli
az role assignment create --role <role_id> --assignee <assignee> --resource-group <resource_group>
```

In het volgende voor beeld wordt de rol [Inzender voor virtuele machines](built-in-roles.md#virtual-machine-contributor) toegewezen aan de *patlong\@contoso.com* -gebruiker op het *Pharma-Sales-* resource groeps bereik. Als u de unieke rol-ID wilt ophalen, kunt u de [lijst met AZ Role definition](/cli/azure/role/definition#az-role-definition-list) gebruiken of [ingebouwde rollen voor Azure-resources](built-in-roles.md)weer geven.

```azurecli
az role assignment create --role 9980e02c-c2be-4d73-94e8-173b1dc7cf3c --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="group-at-a-subscription-scope"></a>Groeperen op een abonnements bereik

Gebruik [AZ Role Assignment Create](/cli/azure/role/assignment#az-role-assignment-create)om een roltoewijzing voor een groep toe te voegen. Zie [object-Id's ophalen](#get-object-ids)voor informatie over het ophalen van de object-id van de groep.

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

In het volgende voor beeld wordt de rol *lezer* toegewezen aan de *team groep Anne Mack* met id 22222222-2222-2222-2222-222222222222 bij een abonnements bereik.

```azurecli
az role assignment create --role Reader --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000
```

### <a name="group-at-a-resource-scope"></a>Groeperen in een resource bereik

Gebruik [AZ Role Assignment Create](/cli/azure/role/assignment#az-role-assignment-create)om een roltoewijzing voor een groep toe te voegen. Zie [object-Id's ophalen](#get-object-ids)voor informatie over het ophalen van de object-id van de groep.

In het volgende voor beeld wordt de rol van *Inzender voor virtuele machines* toegewezen aan de *team groep Anne Mack* met id 22222222-2222-2222-2222-222222222222 in een resource bereik voor een virtueel netwerk met de naam *Pharma-Sales-project-Network*.

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/pharma-sales/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
```

### <a name="application-at-a-resource-group-scope"></a>Toepassing in een bereik van een resource groep

Gebruik [AZ Role Assignment Create](/cli/azure/role/assignment#az-role-assignment-create)om een roltoewijzing voor een toepassing toe te voegen. Zie [object-Id's ophalen](#get-object-ids)voor informatie over het ophalen van de object-id van de toepassing.

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group>
```

In het volgende voor beeld wordt de rol van *Inzender voor virtuele machines* toegewezen aan een toepassing met object-id 44444444-4444-4444-4444-444444444444 in het bereik *Pharma-Sales* .

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 44444444-4444-4444-4444-444444444444 --resource-group pharma-sales
```

### <a name="user-at-a-subscription-scope"></a>Gebruiker bij een abonnements bereik

Als u een roltoewijzing wilt toevoegen voor een gebruiker op een abonnements bereik, gebruikt u [AZ Role Assignment Create](/cli/azure/role/assignment#az-role-assignment-create). Als u de abonnements-ID wilt ophalen, kunt u deze vinden op de Blade **abonnementen** in de Azure portal of kunt u [AZ account list](/cli/azure/account#az-account-list)gebruiken.

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --subscription <subscription_name_or_id>
```

In het volgende voor beeld wordt de rol van *lezer* toegewezen aan de *annm\@example.com* -gebruiker bij een abonnements bereik.

```azurecli
az role assignment create --role "Reader" --assignee annm@example.com --subscription 00000000-0000-0000-0000-000000000000
```

### <a name="user-at-a-management-group-scope"></a>Gebruiker op een beheer groeps bereik

Als u een roltoewijzing wilt toevoegen voor een gebruiker op een beheer groeps bereik, gebruikt u [AZ Role Assignment Create](/cli/azure/role/assignment#az-role-assignment-create). Als u de beheer groep-ID wilt ophalen, kunt u deze vinden op de Blade **beheer groepen** in de Azure portal of u kunt [AZ Account Management-Group List](/cli/azure/account/management-group#az-account-management-group-list)gebruiken.

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

In het volgende voor beeld wordt de rol *facturerings lezer* toegewezen aan de *Alain\@example.com* -gebruiker in een bereik van een beheer groep.

```azurecli
az role assignment create --role "Billing Reader" --assignee alain@example.com --scope /providers/Microsoft.Management/managementGroups/marketing-group
```

### <a name="new-service-principal"></a>Nieuwe Service-Principal

Als u een nieuwe Service-Principal maakt en een rol onmiddellijk probeert toe te wijzen aan die Service-Principal, kan die roltoewijzing in sommige gevallen mislukken. Als u bijvoorbeeld een script gebruikt voor het maken van een nieuwe beheerde identiteit en vervolgens probeert een rol toe te wijzen aan die Service-Principal, kan de roltoewijzing mislukken. De oorzaak van deze fout is waarschijnlijk een replicatie vertraging. De service-principal wordt gemaakt in één regio. de roltoewijzing kan echter plaatsvinden in een andere regio waarvoor de Service-Principal nog niet is gerepliceerd. Als u dit scenario wilt aanpakken, geeft u het Principal-type op bij het maken van de roltoewijzing.

Als u een roltoewijzing wilt toevoegen, gebruikt u [AZ Role Assignment Create](/cli/azure/role/assignment#az-role-assignment-create), geeft u een waarde op voor `--assignee-object-id`en stelt u vervolgens `--assignee-principal-type` in op `ServicePrincipal`.

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --assignee-principal-type <assignee_principal_type> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

In het volgende voor beeld wordt de rol van *Inzender voor virtuele machines* toegewezen aan de door *MSI-test* beheerde identiteit in het *Pharma* van de resource groep:

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 33333333-3333-3333-3333-333333333333 --assignee-principal-type ServicePrincipal --resource-group pharma-sales
```

## <a name="remove-a-role-assignment"></a>Roltoewijzing verwijderen

Als u de toegang wilt verwijderen in RBAC, verwijdert u een roltoewijzing met behulp van [AZ Role Assignment delete](/cli/azure/role/assignment#az-role-assignment-delete):

```azurecli
az role assignment delete --assignee <assignee> --role <role_name_or_id> --resource-group <resource_group>
```

In het volgende voor beeld wordt de toewijzing van de rol *Inzender voor virtuele machines* uit de *patlong\@contoso.com* -gebruiker voor de resource groep *Pharma-Sales* verwijderd:

```azurecli
az role assignment delete --assignee patlong@contoso.com --role "Virtual Machine Contributor" --resource-group pharma-sales
```

In het volgende voor beeld wordt de rol *lezer* verwijderd uit de *team groep Anne Mack* met id 22222222-2222-2222-2222-222222222222 bij een abonnements bereik. Zie [object-Id's ophalen](#get-object-ids)voor informatie over het ophalen van de object-id van de groep.

```azurecli
az role assignment delete --assignee 22222222-2222-2222-2222-222222222222 --role "Reader" --subscription 00000000-0000-0000-0000-000000000000
```

In het volgende voor beeld wordt de rol *facturerings lezer* verwijderd uit de *Alain\@example.com* -gebruiker in het bereik van de beheer groep. Als u de ID van de beheer groep wilt ophalen, kunt u [AZ Account Management-Group List](/cli/azure/account/management-group#az-account-management-group-list)gebruiken.

```azurecli
az role assignment delete --assignee alain@example.com --role "Billing Reader" --scope /providers/Microsoft.Management/managementGroups/marketing-group
```

## <a name="next-steps"></a>Volgende stappen

- [Roltoewijzingen weer geven met behulp van Azure RBAC en Azure CLI](role-assignments-list-cli.md)
- [Azure CLI gebruiken voor het beheren van Azure-resources en-resource groepen](../azure-resource-manager/cli-azure-resource-manager.md)
