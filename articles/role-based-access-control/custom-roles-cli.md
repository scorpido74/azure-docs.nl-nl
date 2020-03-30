---
title: Aangepaste rollen voor Azure-resources maken of bijwerken met Azure CLI | Microsoft Documenten
description: Meer informatie over het aanbieden, maken, bijwerken of verwijderen van aangepaste rollen met RBAC (Role-based access control) voor Azure-resources met Azure CLI.
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
ms.openlocfilehash: 44676f7b92c2bcd30612295840054ab2f0c0cf12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062218"
---
# <a name="create-or-update-custom-roles-for-azure-resources-using-azure-cli"></a>Aangepaste rollen voor Azure-resources maken of bijwerken met Azure CLI

> [!IMPORTANT]
> Het toevoegen van `AssignableScopes` een beheergroep aan is momenteel in preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie.

Als de [ingebouwde rollen voor Azure-resources](built-in-roles.md) niet voldoen aan de specifieke behoeften van uw organisatie, u uw eigen aangepaste rollen maken. In dit artikel wordt beschreven hoe aangepaste rollen met Azure CLI kunnen worden weergegeven, gemaakt, bijgewerkt of verwijderd.

Zie [Zelfstudie: Een aangepaste rol maken voor Azure-resources met Azure CLI](tutorial-custom-role-cli.md)voor een stapsgewijze zelfstudie over het maken van een aangepaste rol.

## <a name="prerequisites"></a>Vereisten

Als u aangepaste rollen wilt maken, hebt u het belangrijkste nodig:

- Machtigingen voor het maken van aangepaste rollen, zoals [Eigenaar](built-in-roles.md#owner) of [Administrator voor gebruikerstoegang](built-in-roles.md#user-access-administrator)
- [Azure Cloud Shell](../cloud-shell/overview.md) of [Azure CLI](/cli/azure/install-azure-cli)

## <a name="list-custom-roles"></a>Aangepaste rollen opvragen

Als u aangepaste rollen wilt weergeven die beschikbaar zijn voor toewijzing, gebruikt u [de lijst met az-rollendefinitie](/cli/azure/role/definition#az-role-definition-list). In de volgende voorbeelden worden alle aangepaste rollen in het huidige abonnement weergegeven.

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

## <a name="list-a-custom-role-definition"></a>Een aangepaste roldefinitie weergeven

Als u een aangepaste roldefinitie wilt weergeven, gebruikt u [de lijst met az-rollendefinitie](/cli/azure/role/definition#az-role-definition-list). Dit is dezelfde opdracht die u zou gebruiken voor een ingebouwde rol.

```azurecli
az role definition list --name <role_name>
```

In het volgende voorbeeld wordt de roldefinitie *van Virtual Machine Operator* weergegeven:

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

In het volgende voorbeeld worden alleen de acties van de rol *Virtual Machine Operator* weergegeven:

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

Als u een aangepaste rol wilt maken, gebruikt u [de definitie van AZ-rollen maken](/cli/azure/role/definition#az-role-definition-create). De roldefinitie kan een JSON-beschrijving zijn of een pad naar een bestand met een JSON-beschrijving.

```azurecli
az role definition create --role-definition <role_definition>
```

In het volgende voorbeeld wordt een aangepaste rol gemaakt met de naam *Virtual Machine Operator*. Met deze aangepaste rol wordt toegang toegewezen tot alle leesbewerkingen van *Microsoft.Compute,* *Microsoft.Storage*en *Microsoft.Network-bronproviders* en wordt toegang toegewezen voor het starten, opnieuw opstarten en bewaken van virtuele machines. Deze aangepaste rol kan worden gebruikt in twee abonnementen. In dit voorbeeld wordt een JSON-bestand als invoer gebruikt.

vmoperator.json

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

Als u een aangepaste rol wilt bijwerken, gebruikt u eerst [de lijst met az-rollendefinitie](/cli/azure/role/definition#az-role-definition-list) om de roldefinitie op te halen. Ten tweede, breng de gewenste wijzigingen in de roldefinitie. Tot slot u de [functiedefinitie-update van AZ](/cli/azure/role/definition#az-role-definition-update) gebruiken om de bijgewerkte roldefinitie op te slaan.

```azurecli
az role definition update --role-definition <role_definition>
```

In het volgende voorbeeld wordt de bewerking `Actions` Microsoft.Insights/diagnosticSettings/aan de aangepaste rol Van `AssignableScopes` *Microsoft.Insights/diagnosticSettings/toegevoegd* aan en wordt een beheergroep toegevoegd aan de aangepaste rol *van Virtual Machine Operator.* Het toevoegen van `AssignableScopes` een beheergroep aan is momenteel in preview.

vmoperator.json

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

Als u een aangepaste rol wilt verwijderen, gebruikt u [de definitie van az-rollen verwijderen](/cli/azure/role/definition#az-role-definition-delete). Als u de rol wilt opgeven die u wilt verwijderen, gebruikt u de rolnaam of de rol-id. Als u de rol-ID wilt bepalen, gebruikt u [de lijst met az-rollendefinitie](/cli/azure/role/definition#az-role-definition-list).

```azurecli
az role definition delete --name <role_name or role_id>
```

In het volgende voorbeeld wordt de aangepaste rol *van de virtuele machineoperator* verwijderd.

```azurecli
az role definition delete --name "Virtual Machine Operator"
```

## <a name="next-steps"></a>Volgende stappen

- [Zelfstudie: Een aangepaste rol maken voor Azure-resources met Azure CLI](tutorial-custom-role-cli.md)
- [Aangepaste rollen voor Azure-resources](custom-roles.md)
- [Azure Resource Manager-resourceproviderbewerkingen](resource-provider-operations.md)