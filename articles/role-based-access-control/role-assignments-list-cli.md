---
title: Azure-roltoewijzingen weer geven met Azure CLI-Azure RBAC
description: Meer informatie over hoe u kunt bepalen welke resources gebruikers, groepen, service-principals of beheerde identiteiten hebben toegang tot het gebruik van Azure CLI en op rollen gebaseerd toegangs beheer (Azure RBAC).
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
ms.date: 01/10/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: f4b635d6867c36b8b0f385320e3720bea41b54d1
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/03/2020
ms.locfileid: "82735739"
---
# <a name="list-azure-role-assignments-using-azure-cli"></a>Azure-roltoewijzingen weer geven met Azure CLI

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)]In dit artikel wordt beschreven hoe u roltoewijzingen kunt weer geven met behulp van Azure CLI.

> [!NOTE]
> Als uw organisatie uitbestede beheer functies heeft voor een service provider die gebruikmaakt van [Azure delegated resource management](../lighthouse/concepts/azure-delegated-resource-management.md), worden roltoewijzingen die door die service provider worden toegestaan, hier niet weer gegeven.

## <a name="prerequisites"></a>Vereisten

- [Bash in azure Cloud shell](/azure/cloud-shell/overview) of [Azure cli](/cli/azure)

## <a name="list-role-assignments-for-a-user"></a>Roltoewijzingen voor een gebruiker weergeven

Als u de roltoewijzingen voor een specifieke gebruiker wilt weer geven, gebruikt u de [lijst AZ Role Assignment](/cli/azure/role/assignment#az-role-assignment-list):

```azurecli-interactive
az role assignment list --assignee <assignee>
```

Standaard worden alleen roltoewijzingen voor het huidige abonnement weer gegeven. Als u roltoewijzingen voor het huidige abonnement en hieronder wilt weer geven, `--all` voegt u de para meter toe. Als u overgenomen roltoewijzingen wilt weer geven, `--include-inherited` voegt u de para meter toe.

In het volgende voor beeld worden de roltoewijzingen weer gegeven die rechtstreeks aan *de\@patlong contoso.com* -gebruiker zijn toegewezen:

```azurecli-interactive
az role assignment list --all --assignee patlong@contoso.com --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
```

## <a name="list-role-assignments-for-a-resource-group"></a>Roltoewijzingen voor een resourcegroep opvragen

Als u de roltoewijzingen wilt weer geven die voor komen in een bereik van een resource groep, gebruikt u de [lijst AZ Role Assignment](/cli/azure/role/assignment#az-role-assignment-list):

```azurecli-interactive
az role assignment list --resource-group <resource_group>
```

In het volgende voor beeld worden de roltoewijzingen voor de resource groep *Pharma-Sales* weer gegeven:

```azurecli-interactive
az role assignment list --resource-group pharma-sales --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}

...
```

## <a name="list-role-assignments-for-a-subscription"></a>Roltoewijzingen weergeven voor een abonnement

Gebruik [AZ Role Assignment List](/cli/azure/role/assignment#az-role-assignment-list)om alle roltoewijzingen in een abonnements bereik weer te geven. Als u de abonnements-ID wilt ophalen, kunt u deze vinden op de Blade **abonnementen** in de Azure portal of kunt u [AZ account list](/cli/azure/account#az-account-list)gebruiken.

```azurecli-interactive
az role assignment list --subscription <subscription_name_or_id>
```

Voorbeeld:

```azurecli-interactive
az role assignment list --subscription 00000000-0000-0000-0000-000000000000 --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

## <a name="list-role-assignments-for-a-management-group"></a>Roltoewijzingen voor een beheer groep weer geven

Gebruik [AZ Role Assignment List](/cli/azure/role/assignment#az-role-assignment-list)om alle roltoewijzingen in een beheer groeps bereik weer te geven. Als u de beheer groep-ID wilt ophalen, kunt u deze vinden op de Blade **beheer groepen** in de Azure portal of u kunt [AZ Account Management-Group List](/cli/azure/account/management-group#az-account-management-group-list)gebruiken.

```azurecli-interactive
az role assignment list --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

Voorbeeld:

```azurecli-interactive
az role assignment list --scope /providers/Microsoft.Management/managementGroups/marketing-group --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

## <a name="list-role-assignments-for-a-managed-identity"></a>Roltoewijzingen voor een beheerde identiteit weer geven

1. Haal de object-ID op van de door het systeem toegewezen of door de gebruiker toegewezen beheerde identiteit.

    Als u de object-ID van een door de gebruiker toegewezen beheerde identiteit wilt ophalen, kunt u [AZ AD SP List](/cli/azure/ad/sp#az-ad-sp-list) of [AZ ID List](/cli/azure/identity#az-identity-list)gebruiken.

    ```azurecli-interactive
    az ad sp list --display-name "<name>" --query [].objectId --output tsv
    ```

    Als u de object-ID van een door het systeem toegewezen beheerde identiteit wilt ophalen, kunt u [AZ AD SP List](/cli/azure/ad/sp#az-ad-sp-list)gebruiken.

    ```azurecli-interactive
    az ad sp list --display-name "<vmname>" --query [].objectId --output tsv
    ```

1. Als u de roltoewijzingen wilt weer geven, gebruikt u de [lijst AZ Role Assignment](/cli/azure/role/assignment#az-role-assignment-list).

    Standaard worden alleen roltoewijzingen voor het huidige abonnement weer gegeven. Als u roltoewijzingen voor het huidige abonnement en hieronder wilt weer geven, `--all` voegt u de para meter toe. Als u overgenomen roltoewijzingen wilt weer geven, `--include-inherited` voegt u de para meter toe.

    ```azurecli-interactive
    az role assignment list --assignee <objectid>
    ```

## <a name="next-steps"></a>Volgende stappen

- [Azure-roltoewijzingen toevoegen of verwijderen met behulp van Azure CLI](role-assignments-cli.md)
