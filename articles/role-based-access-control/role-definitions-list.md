---
title: Roldefinities weer geven in azure RBAC met behulp van Azure Portal, Azure PowerShell of Azure CLI | Microsoft Docs
description: Meer informatie over het vermelden van ingebouwde en aangepaste rollen in azure RBAC met behulp van Azure Portal, Azure PowerShell of Azure CLI.
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
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 839393d7535de530a27752f77e311c87c75825d9
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74710345"
---
# <a name="list-role-definitions-in-azure-rbac"></a>Roldefinities in azure RBAC weer geven

Een roldefinitie is een verzameling machtigingen die kan worden uitgevoerd, zoals lezen, schrijven en verwijderen. Normaal gesp roken wordt gewoon een rol genoemd. [Op rollen gebaseerd toegangs beheer (RBAC) van Azure](overview.md) heeft meer dan 120 [ingebouwde rollen](built-in-roles.md) of u kunt uw eigen aangepaste rollen maken. In dit artikel wordt beschreven hoe u de ingebouwde en aangepaste rollen vermeldt die u kunt gebruiken om toegang te verlenen tot Azure-resources.

Zie [beheerders rollen in azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md)voor een overzicht van de beheerders rollen voor Azure Active Directory.

## <a name="azure-portal"></a>Azure Portal

### <a name="list-all-roles"></a>Alle rollen weer geven

Volg deze stappen om alle rollen in de Azure Portal weer te geven.

1. Klik in de Azure Portal op **alle services** en selecteer vervolgens een bereik. U kunt bijvoorbeeld **beheer groepen**, **abonnementen**, **resource groepen**of een resource selecteren.

1. Klik op de specifieke resource.

1. Klik op **Toegangsbeheer (IAM)** .

1. Klik op het tabblad **rollen** om een lijst met alle ingebouwde en aangepaste rollen weer te geven.

   U kunt het aantal gebruikers en groepen zien dat is toegewezen aan elke rol in het huidige bereik.

   ![Lijst met rollen](./media/role-definitions-list/roles-list.png)

## <a name="azure-powershell"></a>Azure PowerShell

### <a name="list-all-roles"></a>Alle rollen weer geven

Als u alle rollen in Azure PowerShell wilt weer geven, gebruikt u [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

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

### <a name="list-a-role-definition"></a>Een roldefinitie weer geven

Gebruik [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition)om de details van een specifieke rol weer te geven.

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

### <a name="list-a-role-definition-in-json-format"></a>Een roldefinitie in JSON-indeling weer geven

Gebruik [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition)om een rol in JSON-indeling weer te geven.

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

### <a name="list-permissions-of-a-role-definition"></a>Machtigingen van een roldefinitie weer geven

Gebruik [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition)om de machtigingen voor een specifieke rol weer te geven.

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

## <a name="azure-cli"></a>Azure CLI

### <a name="list-all-roles"></a>Alle rollen weer geven

Als u alle rollen in azure CLI wilt weer geven, gebruikt u de [lijst AZ Role definition](/cli/azure/role/definition#az-role-definition-list).

```azurecli
az role definition list
```

In het volgende voor beeld worden de naam en beschrijving van alle beschik bare roldefinities weer gegeven:

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

In het volgende voor beeld worden alle ingebouwde rollen weer gegeven.

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

### <a name="list-a-role-definition"></a>Een roldefinitie weer geven

Gebruik [AZ Role definition List](/cli/azure/role/definition#az-role-definition-list)om details van een rol weer te geven.

```azurecli
az role definition list --name <role_name>
```

In het volgende voor beeld wordt de roldefinitie van de rol *Inzender* weer gegeven:

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

### <a name="list-permissions-of-a-role-definition"></a>Machtigingen van een roldefinitie weer geven

*In het* volgende voor beeld worden alleen de *acties* en de verslechtering van de rol *Inzender* vermeld.

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

In het volgende voor beeld worden alleen de acties van de rol *Inzender voor virtuele machines* vermeld.

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

## <a name="next-steps"></a>Volgende stappen

- [Ingebouwde rollen voor Azure-resources](built-in-roles.md)
- [Aangepaste rollen voor Azure-resources](custom-roles.md)
- [Roltoewijzingen weer geven met behulp van Azure RBAC en de Azure Portal](role-assignments-list-portal.md)
- [Roltoewijzingen toevoegen of verwijderen met behulp van Azure RBAC en de Azure Portal](role-assignments-portal.md)
