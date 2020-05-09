---
title: Aangepaste Azure-rollen maken of bijwerken met Azure CLI-Azure RBAC
description: Meer informatie over het weer geven, maken, bijwerken of verwijderen van aangepaste Azure-rollen met Azure CLI en Azure op rollen gebaseerd toegangs beheer (Azure RBAC).
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
ms.date: 03/18/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: cac0116cf7a068e63cb54698f7273b8c063ff854
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/03/2020
ms.locfileid: "82734838"
---
# <a name="create-or-update-azure-custom-roles-using-azure-cli"></a>Aangepaste Azure-rollen maken of bijwerken met Azure CLI

> [!IMPORTANT]
> Een beheer groep toevoegen aan `AssignableScopes` is momenteel beschikbaar als preview-versie.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie voor meer informatie [aanvullende gebruiks voorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Als de [ingebouwde rollen van Azure](built-in-roles.md) niet voldoen aan de specifieke behoeften van uw organisatie, kunt u uw eigen aangepaste rollen maken. In dit artikel wordt beschreven hoe u aangepaste rollen oplijstt, maakt, bijwerkt of verwijdert met behulp van Azure CLI.

Zie [zelf studie: een aangepaste Azure-rol maken met behulp van Azure cli](tutorial-custom-role-cli.md)voor een stapsgewijze zelf studie over het maken van een aangepaste rol.

## <a name="prerequisites"></a>Vereisten

Als u aangepaste rollen wilt maken, hebt u het volgende nodig:

- Machtigingen voor het maken van aangepaste rollen, zoals [Eigenaar](built-in-roles.md#owner) of [Administrator voor gebruikerstoegang](built-in-roles.md#user-access-administrator)
- [Azure Cloud Shell](../cloud-shell/overview.md) of [Azure CLI](/cli/azure/install-azure-cli)

## <a name="list-custom-roles"></a>Aangepaste rollen opvragen

Gebruik [AZ Role definition List](/cli/azure/role/definition#az-role-definition-list)om aangepaste rollen weer te geven die beschikbaar zijn voor toewijzing. De volgende voor beelden geven een lijst van alle aangepaste rollen in het huidige abonnement.

```azurecli
az role definition list --custom-role-only true --output json | jq '.[] | {"roleName":.roleName, "roleType":.roleType}'
```

```azurecli
az role definition list --output json | jq '.[] | if .roleType == "CustomRole" then {"roleName":.roleName, "roleType":.roleType} else empty end'
```

```Output
{
  "roleName": "My Management Contributor",
  "type": "CustomRole"
}
{
  "roleName": "My Service Reader Role",
  "type": "CustomRole"
}
{
  "roleName": "Virtual Machine Operator",
  "type": "CustomRole"
}

...
```

## <a name="list-a-custom-role-definition"></a>Een aangepaste roldefinitie weer geven

Als u een aangepaste roldefinitie wilt weer geven, gebruikt u de [lijst AZ Role definition](/cli/azure/role/definition#az-role-definition-list). Dit is dezelfde opdracht die u zou gebruiken voor een ingebouwde rol.

```azurecli
az role definition list --name <role_name>
```

In het volgende voor beeld wordt de roldefinitie van de *virtuele-machine operator* weer gegeven:

```azurecli
az role definition list --name "Virtual Machine Operator"
```

```Output
[
  {
    "assignableScopes": [
      "/subscriptions/11111111-1111-1111-1111-111111111111"
    ],
    "description": "Can monitor and restart virtual machines.",
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/00000000-0000-0000-0000-000000000000",
    "name": "00000000-0000-0000-0000-000000000000",
    "permissions": [
      {
        "actions": [
          "Microsoft.Storage/*/read",
          "Microsoft.Network/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action",
          "Microsoft.Authorization/*/read",
          "Microsoft.ResourceHealth/availabilityStatuses/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Insights/diagnosticSettings/*",
          "Microsoft.Support/*"
        ],
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "roleName": "Virtual Machine Operator",
    "roleType": "CustomRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

In het volgende voor beeld worden alleen de acties van de rol *virtuele-machine operator* vermeld:

```azurecli
az role definition list --name "Virtual Machine Operator" --output json | jq '.[] | .permissions[0].actions'
```

```Output
[
  "Microsoft.Storage/*/read",
  "Microsoft.Network/*/read",
  "Microsoft.Compute/*/read",
  "Microsoft.Compute/virtualMachines/start/action",
  "Microsoft.Compute/virtualMachines/restart/action",
  "Microsoft.Authorization/*/read",
  "Microsoft.ResourceHealth/availabilityStatuses/read",
  "Microsoft.Resources/subscriptions/resourceGroups/read",
  "Microsoft.Insights/alertRules/*",
  "Microsoft.Insights/diagnosticSettings/*",
  "Microsoft.Support/*"
]
```

## <a name="create-a-custom-role"></a>Een aangepaste rol maken

Gebruik [AZ Role definition Create](/cli/azure/role/definition#az-role-definition-create)om een aangepaste rol te maken. De roldefinitie kan een JSON-beschrijving of een pad naar een bestand met een JSON-beschrijving zijn.

```azurecli
az role definition create --role-definition <role_definition>
```

In het volgende voor beeld wordt een aangepaste rol met de naam *virtuele-machine operator*gemaakt. Met deze aangepaste rol krijgt u toegang tot alle Lees bewerkingen van *micro soft. Compute*-, *micro soft. Storage*-en *micro soft. Network* resource providers en wordt er toegang toegewezen om virtuele machines te starten, opnieuw op te starten en te bewaken. Deze aangepaste rol kan in twee abonnementen worden gebruikt. In dit voor beeld wordt een JSON-bestand gebruikt als invoer.

vmoperator. json

```json
{
  "Name": "Virtual Machine Operator",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.ResourceHealth/availabilityStatuses/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/11111111-1111-1111-1111-111111111111",
    "/subscriptions/33333333-3333-3333-3333-333333333333"
  ]
}
```

```azurecli
az role definition create --role-definition ~/roles/vmoperator.json
```

## <a name="update-a-custom-role"></a>Een aangepaste rol bijwerken

Als u een aangepaste rol wilt bijwerken, gebruikt u eerst de [lijst AZ Role definition](/cli/azure/role/definition#az-role-definition-list) om de roldefinitie op te halen. Breng vervolgens de gewenste wijzigingen aan in de functie definitie. Gebruik tenslotte de [Update AZ Role definition](/cli/azure/role/definition#az-role-definition-update) om de bijgewerkte roldefinitie op te slaan.

```azurecli
az role definition update --role-definition <role_definition>
```

In het volgende voor beeld wordt de *micro soft. Insights-diagnosticSettings/-* bewerking toegevoegd aan `Actions` en `AssignableScopes` wordt een beheer groep toegevoegd aan voor de aangepaste rol van de operator voor de *virtuele machine* . Een beheer groep toevoegen aan `AssignableScopes` is momenteel beschikbaar als preview-versie.

vmoperator. json

```json
{
  "Name": "Virtual Machine Operator",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.ResourceHealth/availabilityStatuses/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/11111111-1111-1111-1111-111111111111",
    "/subscriptions/33333333-3333-3333-3333-333333333333",
    "/providers/Microsoft.Management/managementGroups/marketing-group"
  ]
}
```

```azurecli
az role definition update --role-definition ~/roles/vmoperator.json
```

## <a name="delete-a-custom-role"></a>Een aangepaste rol verwijderen

Gebruik [AZ Role definition delete](/cli/azure/role/definition#az-role-definition-delete)om een aangepaste functie te verwijderen. Als u de te verwijderen rol wilt opgeven, gebruikt u de rolnaam of de rol-ID. Gebruik [AZ Role definition List](/cli/azure/role/definition#az-role-definition-list)om de rol-id te bepalen.

```azurecli
az role definition delete --name <role_name or role_id>
```

In het volgende voor beeld wordt de aangepaste rol van de *operator virtuele machine* verwijderd.

```azurecli
az role definition delete --name "Virtual Machine Operator"
```

## <a name="next-steps"></a>Volgende stappen

- [Zelf studie: een aangepaste Azure-rol maken met behulp van Azure CLI](tutorial-custom-role-cli.md)
- [Aangepaste Azure-rollen](custom-roles.md)
- [Bewerkingen voor de resource provider Azure Resource Manager](resource-provider-operations.md)