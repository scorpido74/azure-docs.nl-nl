---
title: Roltoewijzingen weer geven met behulp van Azure RBAC en Azure CLI
description: Meer informatie over hoe u kunt bepalen welke resources gebruikers, groepen, service-principals of beheerde identiteiten hebben toegang tot het gebruik van op rollen gebaseerd toegangs beheer (RBAC) en Azure CLI.
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
ms.openlocfilehash: 1a58d2170ec1107222f0e37e432063af23743e42
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74710436"
---
# <a name="list-role-assignments-using-azure-rbac-and-azure-cli"></a>Roltoewijzingen weer geven met behulp van Azure RBAC en Azure CLI

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)] in dit artikel wordt beschreven hoe u roltoewijzingen kunt weer geven met behulp van Azure CLI.

## <a name="prerequisites"></a>Vereisten

- [Bash in azure Cloud shell](/azure/cloud-shell/overview) of [Azure cli](/cli/azure)

## <a name="list-role-assignments-for-a-user"></a>Roltoewijzingen voor een gebruiker weergeven

Als u de roltoewijzingen voor een specifieke gebruiker wilt weer geven, gebruikt u de [lijst AZ Role Assignment](/cli/azure/role/assignment#az-role-assignment-list):

```azurecli
az role assignment list --assignee <assignee>
```

Standaard worden alleen directe toewijzingen weer gegeven die zijn gericht op een abonnement. Als u toewijzingen wilt weer geven die zijn afgestemd op resource of groep, gebruikt u `--all` en kunt u overgenomen toewijzingen weer geven met behulp van `--include-inherited`.

In het volgende voor beeld ziet u de roltoewijzingen die rechtstreeks zijn toegewezen aan de *patlong-\@contoso.com* -gebruiker:

```azurecli
az role assignment list --all --assignee patlong@contoso.com --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```Output
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

```azurecli
az role assignment list --resource-group <resource_group>
```

In het volgende voor beeld worden de roltoewijzingen voor de resource groep *Pharma-Sales* weer gegeven:

```azurecli
az role assignment list --resource-group pharma-sales --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```Output
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

```azurecli
az role assignment list --subscription <subscription_name_or_id>
```

```Example
az role assignment list --subscription 00000000-0000-0000-0000-000000000000 --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

## <a name="list-role-assignments-for-a-management-group"></a>Roltoewijzingen voor een beheer groep weer geven

Gebruik [AZ Role Assignment List](/cli/azure/role/assignment#az-role-assignment-list)om alle roltoewijzingen in een beheer groeps bereik weer te geven. Als u de beheer groep-ID wilt ophalen, kunt u deze vinden op de Blade **beheer groepen** in de Azure portal of u kunt [AZ Account Management-Group List](/cli/azure/ext/managementgroups/account/management-group#ext-managementgroups-az-account-management-group-list)gebruiken.

```azurecli
az role assignment list --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

```Example
az role assignment list --scope /providers/Microsoft.Management/managementGroups/marketing-group --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

## <a name="next-steps"></a>Volgende stappen

- [Roltoewijzingen toevoegen of verwijderen met behulp van Azure RBAC en Azure CLI](role-assignments-cli.md)
