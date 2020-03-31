---
title: Functiedefinities in Azure RBAC weergeven met Azure-portal, Azure PowerShell, Azure CLI of REST API | Microsoft Documenten
description: Meer informatie over het weergeven van ingebouwde en aangepaste rollen in Azure RBAC met Azure-portal, Azure PowerShell, Azure CLI of REST API.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: aa888eedc81ceb3188f801e273c70722207bf512
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062989"
---
# <a name="list-role-definitions-in-azure-rbac"></a>Functiedefinities weergeven in Azure RBAC

Een roldefinitie is een verzameling machtigingen die kunnen worden uitgevoerd, zoals lezen, schrijven en verwijderen. Het wordt meestal gewoon een rol genoemd. [RBAC (Azure role-based access control)](overview.md) heeft meer dan 120 [ingebouwde rollen](built-in-roles.md) of u uw eigen aangepaste rollen maken. In dit artikel wordt beschreven hoe u de ingebouwde en aangepaste rollen weergeeft die u gebruiken om toegang te verlenen tot Azure-bronnen.

Zie [Beheerdersrolmachtigingen in Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md)voor de lijst met beheerdersrollen voor Azure Active Directory.

## <a name="azure-portal"></a>Azure Portal

### <a name="list-all-roles"></a>Alle rollen weergeven

Volg deze stappen om alle rollen in de Azure-portal weer te geven.

1. Klik in de Azure-portal op **Alle services** en selecteer vervolgens een bereik. U bijvoorbeeld **Beheergroepen,** **Abonnementen,** **Resourcegroepen**of een resource selecteren.

1. Klik op de specifieke resource.

1. Klik op **Toegangsbeheer (IAM)**.

1. Klik op het tabblad **Rollen** om een lijst met alle ingebouwde en aangepaste rollen weer te geven.

   U het aantal gebruikers en groepen zien dat aan elke rol is toegewezen op het huidige bereik.

   ![Lijst met rollen](./media/role-definitions-list/roles-list.png)

## <a name="azure-powershell"></a>Azure PowerShell

### <a name="list-all-roles"></a>Alle rollen weergeven

Als u alle rollen in Azure PowerShell wilt weergeven, gebruikt u [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

```azurepowershell
Get-AzRoleDefinition | FT Name, Description
```

```Example
AcrImageSigner                                    acr image signer
AcrQuarantineReader                               acr quarantine data reader
AcrQuarantineWriter                               acr quarantine data writer
API Management Service Contributor                Can manage service and the APIs
API Management Service Operator Role              Can manage service but not the APIs
API Management Service Reader Role                Read-only access to service and APIs
Application Insights Component Contributor        Can manage Application Insights components
Application Insights Snapshot Debugger            Gives user permission to use Application Insights Snapshot Debugge...
Automation Job Operator                           Create and Manage Jobs using Automation Runbooks.
Automation Operator                               Automation Operators are able to start, stop, suspend, and resume ...
...
```

### <a name="list-a-role-definition"></a>Een roldefinitie weergeven

Als u de details van een specifieke rol wilt weergeven, gebruikt u [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

```azurepowershell
Get-AzRoleDefinition <role_name>
```

```Example
PS C:\> Get-AzRoleDefinition "Contributor"

Name             : Contributor
Id               : b24988ac-6180-42a0-ab88-20f7382dd24c
IsCustom         : False
Description      : Lets you manage everything except access to resources.
Actions          : {*}
NotActions       : {Microsoft.Authorization/*/Delete, Microsoft.Authorization/*/Write,
                   Microsoft.Authorization/elevateAccess/Action}
DataActions      : {}
NotDataActions   : {}
AssignableScopes : {/}
```

### <a name="list-a-role-definition-in-json-format"></a>Een roldefinitie in JSON-indeling weergeven

Als u een rol wilt aanbieden in JSON-indeling, gebruikt u [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

```azurepowershell
Get-AzRoleDefinition <role_name> | ConvertTo-Json
```

```Example
PS C:\> Get-AzRoleDefinition "Contributor" | ConvertTo-Json

{
  "Name": "Contributor",
  "Id": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "IsCustom": false,
  "Description": "Lets you manage everything except access to resources.",
  "Actions": [
    "*"
  ],
  "NotActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action",
    "Microsoft.Blueprint/blueprintAssignments/write",
    "Microsoft.Blueprint/blueprintAssignments/delete"
  ],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

### <a name="list-permissions-of-a-role-definition"></a>Machtigingen voor een roldefinitie weergeven

Als u de machtigingen voor een specifieke rol wilt weergeven, gebruikt u [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

```azurepowershell
Get-AzRoleDefinition <role_name> | FL Actions, NotActions
```

```Example
PS C:\> Get-AzRoleDefinition "Contributor" | FL Actions, NotActions

Actions    : {*}
NotActions : {Microsoft.Authorization/*/Delete, Microsoft.Authorization/*/Write,
             Microsoft.Authorization/elevateAccess/Action,
             Microsoft.Blueprint/blueprintAssignments/write...}
```

```azurepowershell
(Get-AzRoleDefinition <role_name>).Actions
```

```Example
PS C:\> (Get-AzRoleDefinition "Virtual Machine Contributor").Actions

Microsoft.Authorization/*/read
Microsoft.Compute/availabilitySets/*
Microsoft.Compute/locations/*
Microsoft.Compute/virtualMachines/*
Microsoft.Compute/virtualMachineScaleSets/*
Microsoft.DevTestLab/schedules/*
Microsoft.Insights/alertRules/*
Microsoft.Network/applicationGateways/backendAddressPools/join/action
Microsoft.Network/loadBalancers/backendAddressPools/join/action
...
```

## <a name="azure-cli"></a>Azure-CLI

### <a name="list-all-roles"></a>Alle rollen weergeven

Als u alle rollen in Azure CLI wilt weergeven, gebruikt u [de lijst met az-rollendefinitie](/cli/azure/role/definition#az-role-definition-list).

```azurecli
az role definition list
```

In het volgende voorbeeld worden de naam en beschrijving van alle beschikbare roldefinities weergegeven:

```azurecli
az role definition list --output json | jq '.[] | {"roleName":.roleName, "description":.description}'
```

```Output
{
  "roleName": "API Management Service Contributor",
  "description": "Can manage service and the APIs"
}
{
  "roleName": "API Management Service Operator Role",
  "description": "Can manage service but not the APIs"
}
{
  "roleName": "API Management Service Reader Role",
  "description": "Read-only access to service and APIs"
}

...
```

In het volgende voorbeeld worden alle ingebouwde rollen weergegeven.

```azurecli
az role definition list --custom-role-only false --output json | jq '.[] | {"roleName":.roleName, "description":.description, "roleType":.roleType}'
```

```Output
{
  "roleName": "API Management Service Contributor",
  "description": "Can manage service and the APIs",
  "roleType": "BuiltInRole"
}
{
  "roleName": "API Management Service Operator Role",
  "description": "Can manage service but not the APIs",
  "roleType": "BuiltInRole"
}
{
  "roleName": "API Management Service Reader Role",
  "description": "Read-only access to service and APIs",
  "roleType": "BuiltInRole"
}

...
```

### <a name="list-a-role-definition"></a>Een roldefinitie weergeven

Als u details van een rol wilt vermelden, gebruikt u [de lijst met az-rollendefinitie](/cli/azure/role/definition#az-role-definition-list).

```azurecli
az role definition list --name <role_name>
```

In het volgende voorbeeld wordt de *roldefinitie van inzender* weergegeven:

```azurecli
az role definition list --name "Contributor"
```

```Output
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you manage everything except access to resources.",
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
    "permissions": [
      {
        "actions": [
          "*"
        ],
        "additionalProperties": {},
        "dataActions": [],
        "notActions": [
          "Microsoft.Authorization/*/Delete",
          "Microsoft.Authorization/*/Write",
          "Microsoft.Authorization/elevateAccess/Action"
        ],
        "notDataActions": []
      }
    ],
    "roleName": "Contributor",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

### <a name="list-permissions-of-a-role-definition"></a>Machtigingen voor een roldefinitie weergeven

In het volgende voorbeeld worden alleen de *acties* en *nietActies* van de rol *bijdragers* weergegeven.

```azurecli
az role definition list --name "Contributor" --output json | jq '.[] | {"actions":.permissions[0].actions, "notActions":.permissions[0].notActions}'
```

```Output
{
  "actions": [
    "*"
  ],
  "notActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action"
  ]
}
```

In het volgende voorbeeld worden alleen de acties van de rol *Virtuele machinebijdrager weergegeven.*

```azurecli
az role definition list --name "Virtual Machine Contributor" --output json | jq '.[] | .permissions[0].actions'
```

```Output
[
  "Microsoft.Authorization/*/read",
  "Microsoft.Compute/availabilitySets/*",
  "Microsoft.Compute/locations/*",
  "Microsoft.Compute/virtualMachines/*",
  "Microsoft.Compute/virtualMachineScaleSets/*",
  "Microsoft.Insights/alertRules/*",
  "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
  "Microsoft.Network/loadBalancers/backendAddressPools/join/action",

  ...

  "Microsoft.Storage/storageAccounts/listKeys/action",
  "Microsoft.Storage/storageAccounts/read"
]
```

## <a name="rest-api"></a>REST API

### <a name="list-role-definitions"></a>Lijst met roldefinities weergeven

Als u functiedefinities wilt weergeven, gebruikt u de [API Voor functiedefinities - Rest-API](/rest/api/authorization/roledefinitions/list) weergeven. Als u uw resultaten wilt verfijnen, geeft u een bereik en een optioneel filter op.

1. Begin met de volgende aanvraag:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?$filter={$filter}&api-version=2015-07-01
    ```

1. Vervang *{scope}* binnen de URI door het bereik waarvoor u de roldefinities wilt weergeven.

    > [!div class="mx-tableFixed"]
    > | Bereik | Type |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Beheergroep |
    > | `subscriptions/{subscriptionId1}` | Abonnement |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resourcegroep |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Resource |

    In het vorige voorbeeld is microsoft.web een resourceprovider die verwijst naar een app-service-exemplaar. U ook andere resourceproviders gebruiken en het bereik opgeven. Zie [Azure Resource providers en -typen en](../azure-resource-manager/management/resource-providers-and-types.md) ondersteunde Azure Resource [Manager-resourceproviderbewerkingen](resource-provider-operations.md)voor meer informatie.  
     
1. Vervang *{filter}* door de voorwaarde die u wilt toepassen om de lijst met roldefinitie te filteren.

    > [!div class="mx-tableFixed"]
    > | Filteren | Beschrijving |
    > | --- | --- |
    > | `$filter=atScopeAndBelow()` | Hiermee worden roldefinities voor het opgegeven bereik en eventuele subscopen weergegeven. |
    > | `$filter=type+eq+'{type}'` | Hiermee worden roldefinities van het opgegeven type weergegeven. Type rol kan `CustomRole` `BuiltInRole`zijn of . |

### <a name="list-a-role-definition"></a>Een roldefinitie weergeven

Als u de details van een specifieke rol wilt weergeven, gebruikt u de [functiedefinities - Definities of](/rest/api/authorization/roledefinitions/get) [Roldefinities - Get By Id](/rest/api/authorization/roledefinitions/getbyid) REST API.

1. Begin met de volgende aanvraag:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

    Voor een roldefinitie op directoryniveau u dit verzoek gebruiken:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. Vervang *{scope}* binnen de URI door het bereik waarvoor u de roldefinitie wilt weergeven.

    > [!div class="mx-tableFixed"]
    > | Bereik | Type |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Beheergroep |
    > | `subscriptions/{subscriptionId1}` | Abonnement |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resourcegroep |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Resource |
     
1. Vervang *{roleDefinitionId}* door de functiedefinitie-id.

## <a name="next-steps"></a>Volgende stappen

- [Ingebouwde rollen voor Azure-resources](built-in-roles.md)
- [Aangepaste rollen voor Azure-resources](custom-roles.md)
- [Roltoewijzingen weergeven met Azure RBAC en de Azure-portal](role-assignments-list-portal.md)
- [Roltoewijzingen toevoegen of verwijderen met Azure RBAC en de Azure-portal](role-assignments-portal.md)
