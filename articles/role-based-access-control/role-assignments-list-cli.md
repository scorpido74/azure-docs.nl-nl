---
title: Roltoewijzingen weergeven met Azure RBAC en Azure CLI
description: Meer informatie over het bepalen van welke resources gebruikers, groepen, serviceprincipals of beheerde identiteiten toegang hebben tot het gebruik van RBAC (Azure ROLE-based Access Control) en Azure CLI.
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
ms.openlocfilehash: 5716e7bb89d017866bd1575256e2d119bb7acbe5
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385058"
---
# <a name="list-role-assignments-using-azure-rbac-and-azure-cli"></a>Roltoewijzingen weergeven met Azure RBAC en Azure CLI

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)]In dit artikel wordt beschreven hoe u roltoewijzingen weergeven met Azure CLI.

> [!NOTE]
> Als uw organisatie beheerfuncties heeft uitbesteed aan een serviceprovider die [azure-gedelegeerd resourcebeheer](../lighthouse/concepts/azure-delegated-resource-management.md)gebruikt, worden roltoewijzingen die zijn geautoriseerd door die serviceprovider, hier niet weergegeven.

## <a name="prerequisites"></a>Vereisten

- [Bash in Azure Cloud Shell](/azure/cloud-shell/overview) of Azure [CLI](/cli/azure)

## <a name="list-role-assignments-for-a-user"></a>Roltoewijzingen voor een gebruiker weergeven

Als u de roltoewijzingen voor een specifieke gebruiker wilt vermelden, gebruikt u [de lijst met az-rollentoewijzing:](/cli/azure/role/assignment#az-role-assignment-list)

```azurecli-interactive
az role assignment list --assignee <assignee>
```

Standaard worden alleen roltoewijzingen voor het huidige abonnement weergegeven. Als u roltoewijzingen voor het huidige `--all` abonnement en hieronder wilt weergeven, voegt u de parameter toe. Als u overgenomen roltoewijzingen `--include-inherited` wilt weergeven, voegt u de parameter toe.

In het volgende voorbeeld worden de roltoewijzingen weergegeven die rechtstreeks aan de *contoso.com\@* gebruiker zijn toegewezen:

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

Als u de roltoewijzingen wilt weergeven die in een bereik van een resourcegroep bestaan, gebruikt u [de lijst met az-rollentoewijzing:](/cli/azure/role/assignment#az-role-assignment-list)

```azurecli-interactive
az role assignment list --resource-group <resource_group>
```

In het volgende voorbeeld worden de roltoewijzingen voor de *farma-sales* resourcegroep weergegeven:

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

Als u alle roltoewijzingen op een abonnementsgebied wilt vermelden, gebruikt u [de lijst met az-rollentoewijzing](/cli/azure/role/assignment#az-role-assignment-list). Als u de abonnements-ID wilt krijgen, u deze vinden op het **blade Abonnementen** in de Azure-portal of u de lijst [met AZ-account](/cli/azure/account#az-account-list)gebruiken.

```azurecli-interactive
az role assignment list --subscription <subscription_name_or_id>
```

Voorbeeld:

```azurecli-interactive
az role assignment list --subscription 00000000-0000-0000-0000-000000000000 --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

## <a name="list-role-assignments-for-a-management-group"></a>Roltoewijzingen voor een beheergroep opsommen

Als u alle roltoewijzingen wilt vermelden in een beheergroepbereik, gebruikt u [de lijst met az-rollentoewijzing](/cli/azure/role/assignment#az-role-assignment-list). Als u de beheergroep-id wilt krijgen, u deze vinden op het blade **van beheergroepen** in de Azure-portal of u [de lijst met AZ-accountbeheergroepen](/cli/azure/account/management-group#az-account-management-group-list)gebruiken.

```azurecli-interactive
az role assignment list --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

Voorbeeld:

```azurecli-interactive
az role assignment list --scope /providers/Microsoft.Management/managementGroups/marketing-group --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

## <a name="list-role-assignments-for-a-managed-identity"></a>Roltoewijzingen voor een beheerde identiteit opsommen

1. Haal de object-id van de door het systeem toegewezen of door de gebruiker toegewezen beheerde identiteit op.

    Als u de object-id van een door de gebruiker toegewezen beheerde identiteit wilt krijgen, u [de AZ-lijst of de](/cli/azure/ad/sp#az-ad-sp-list) [AZ-identiteitslijst](/cli/azure/identity#az-identity-list)gebruiken.

    ```azurecli-interactive
    az ad sp list --display-name "<name>" --query [].objectId --output tsv
    ```

    Als u de object-id van een door het systeem toegewezen beheerde identiteit wilt krijgen, u [de az-ad sp-lijst](/cli/azure/ad/sp#az-ad-sp-list)gebruiken.

    ```azurecli-interactive
    az ad sp list --display-name "<vmname>" --query [].objectId --output tsv
    ```

1. Als u de roltoewijzingen wilt vermelden, gebruikt u [de lijst met az-rollentoewijzing](/cli/azure/role/assignment#az-role-assignment-list).

    Standaard worden alleen roltoewijzingen voor het huidige abonnement weergegeven. Als u roltoewijzingen voor het huidige `--all` abonnement en hieronder wilt weergeven, voegt u de parameter toe. Als u overgenomen roltoewijzingen `--include-inherited` wilt weergeven, voegt u de parameter toe.

    ```azurecli-interactive
    az role assignment list --assignee <objectid>
    ```

## <a name="next-steps"></a>Volgende stappen

- [Roltoewijzingen toevoegen of verwijderen met Azure RBAC en Azure CLI](role-assignments-cli.md)
