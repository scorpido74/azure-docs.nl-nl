---
title: Toegang tot Azure-resources beheren met RBAC en Azure CLI | Microsoft Docs
description: Meer informatie over het beheren van de toegang tot Azure-resources voor gebruikers, groepen en toepassingen met behulp van op rollen gebaseerd toegangs beheer (RBAC) en Azure CLI. U vindt hier instructies voor het weergeven van toegang, het verlenen van toegang en het intrekken van toegang.
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
ms.date: 11/21/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 795a97f84bebf6c0e7c1692e82df2f7ce11e0bbd
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74384102"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-azure-cli"></a>Toegang tot Azure-resources beheren met RBAC en Azure CLI

[Op rollen gebaseerd toegangsbeheer (RBAC)](overview.md) is de manier waarop u de toegang tot Azure-resources beheert. In dit artikel wordt beschreven hoe u de toegang beheert voor gebruikers, groepen en toepassingen met RBAC en Azure CLI.

## <a name="prerequisites"></a>Vereisten

Als u de toegang wilt beheren, hebt u een van de volgende opties nodig:

* [Bash in Azure Cloud Shell](/azure/cloud-shell/overview)
* [Azure CLI](/cli/azure)

## <a name="list-roles"></a>Rollen opvragen

Als u alle beschik bare roldefinities wilt weer geven, gebruikt u de [lijst AZ Role definition](/cli/azure/role/definition#az-role-definition-list):

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

In het volgende voor beeld ziet u alle ingebouwde roldefinities:

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

## <a name="list-a-role-definition"></a>Een roldefinitie weer geven

Gebruik [AZ Role definition List](/cli/azure/role/definition#az-role-definition-list)om een functie definitie weer te geven:

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

### <a name="list-actions-of-a-role"></a>Acties van een rol weer geven

*In het* volgende voor beeld worden alleen de *acties* en de verslechtering van de rol *Inzender* weer gegeven:

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

In het volgende voor beeld worden alleen de acties van de rol *Inzender voor virtuele machines* vermeld:

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

## <a name="list-access"></a>Toegang opvragen

In RBAC kunt u de roltoewijzingen weer geven om toegang weer te geven.

### <a name="list-role-assignments-for-a-user"></a>Roltoewijzingen voor een gebruiker weergeven

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

### <a name="list-role-assignments-at-a-resource-group-scope"></a>Roltoewijzingen weer geven in een bereik van een resource groep

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

### <a name="list-role-assignments-at-a-subscription-scope"></a>Roltoewijzingen in een abonnements bereik weer geven

Gebruik [AZ Role Assignment List](/cli/azure/role/assignment#az-role-assignment-list)om alle roltoewijzingen in een abonnements bereik weer te geven. Als u de abonnements-ID wilt ophalen, kunt u deze vinden op de Blade **abonnementen** in de Azure portal of kunt u [AZ account list](/cli/azure/account#az-account-list)gebruiken.

```azurecli
az role assignment list --subscription <subscription_name_or_id>
```

```Example
az role assignment list --subscription 00000000-0000-0000-0000-000000000000 --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

### <a name="list-role-assignments-at-a-management-group-scope"></a>Roltoewijzingen in een beheer groeps bereik weer geven

Gebruik [AZ Role Assignment List](/cli/azure/role/assignment#az-role-assignment-list)om alle roltoewijzingen in een beheer groeps bereik weer te geven. Als u de beheer groep-ID wilt ophalen, kunt u deze vinden op de Blade **beheer groepen** in de Azure portal of u kunt [AZ Account Management-Group List](/cli/azure/ext/managementgroups/account/management-group#ext-managementgroups-az-account-management-group-list)gebruiken.

```azurecli
az role assignment list --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

```Example
az role assignment list --scope /providers/Microsoft.Management/managementGroups/marketing-group --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

## <a name="get-object-ids"></a>Object-Id's ophalen

Als u roltoewijzingen wilt weer geven, toevoegen of verwijderen, moet u mogelijk de unieke ID van een object opgeven. De ID heeft de volgende indeling: `11111111-1111-1111-1111-111111111111`. U kunt de ID ophalen met behulp van de Azure Portal of Azure CLI.

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

## <a name="grant-access"></a>Toegang verlenen

In RBAC verleent u toegang door een roltoewijzing te maken.

### <a name="create-a-role-assignment-for-a-user-at-a-resource-group-scope"></a>Een roltoewijzing maken voor een gebruiker in een bereik van een resource groep

Als u toegang wilt verlenen aan een gebruiker in een bereik van een resource groep, gebruikt u [AZ Role Assignment Create](/cli/azure/role/assignment#az-role-assignment-create).

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --resource-group <resource_group>
```

In het volgende voor beeld wordt de rol van *Inzender voor virtuele machines* toegewezen aan *patlong\@contoso.com* -gebruiker op het *Pharma-Sales-* resource groeps bereik:

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="create-a-role-assignment-using-the-unique-role-id"></a>Een roltoewijzing maken met behulp van de unieke rol-ID

Er zijn een aantal keren dat een rolnaam kan worden gewijzigd, bijvoorbeeld:

- U gebruikt uw eigen aangepaste rol en u besluit de naam te wijzigen.
- U gebruikt een preview-functie met **(preview)** in de naam. Wanneer de rol wordt vrijgegeven, wordt de naam van de rol gewijzigd.

> [!IMPORTANT]
> Een preview-versie wordt zonder service level agreement gegeven en wordt niet aanbevolen voor productie werkbelastingen. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Zelfs als een rol een andere naam heeft gekregen, wordt de rol-ID niet gewijzigd. Als u scripts of automatisering gebruikt om roltoewijzingen te maken, is het een best practice om de unieke rol-ID te gebruiken in plaats van de rolnaam. Als de naam van een rol wordt gewijzigd, zijn uw scripts waarschijnlijker goed.

Als u een roltoewijzing wilt maken met behulp van de unieke rol-ID in plaats van de rolnaam, gebruikt u [AZ Role Assignment Create](/cli/azure/role/assignment#az-role-assignment-create).

```azurecli
az role assignment create --role <role_id> --assignee <assignee> --resource-group <resource_group>
```

In het volgende voor beeld wordt de rol [Inzender voor virtuele machines](built-in-roles.md#virtual-machine-contributor) toegewezen aan de *patlong\@contoso.com* -gebruiker op het *Pharma-Sales-* resource groeps bereik. Als u de unieke rol-ID wilt ophalen, kunt u de [lijst met AZ Role definition](/cli/azure/role/definition#az-role-definition-list) gebruiken of [ingebouwde rollen voor Azure-resources](built-in-roles.md)weer geven.

```azurecli
az role assignment create --role 9980e02c-c2be-4d73-94e8-173b1dc7cf3c --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="create-a-role-assignment-for-a-group"></a>Een roltoewijzing voor een groep maken

Gebruik [AZ Role Assignment Create](/cli/azure/role/assignment#az-role-assignment-create)om toegang te verlenen aan een groep. Zie [object-Id's ophalen](#get-object-ids)voor informatie over het ophalen van de object-id van de groep.

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

In het volgende voor beeld wordt de rol *lezer* toegewezen aan de *team groep Anne Mack* met id 22222222-2222-2222-2222-222222222222 bij een abonnements bereik.

```azurecli
az role assignment create --role Reader --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000
```

In het volgende voor beeld wordt de rol van *Inzender voor virtuele machines* toegewezen aan de *team groep Anne Mack* met id 22222222-2222-2222-2222-222222222222 in een resource bereik voor een virtueel netwerk met de naam *Pharma-Sales-project-Network*.

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/pharma-sales/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
```

### <a name="create-a-role-assignment-for-an-application-at-a-resource-group-scope"></a>Een roltoewijzing maken voor een toepassing in een bereik van een resource groep

Gebruik [AZ Role Assignment Create](/cli/azure/role/assignment#az-role-assignment-create)om toegang te verlenen aan een toepassing. Zie [object-Id's ophalen](#get-object-ids)voor informatie over het ophalen van de object-id van de toepassing.

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group>
```

In het volgende voor beeld wordt de rol van *Inzender voor virtuele machines* toegewezen aan een toepassing met object-id 44444444-4444-4444-4444-444444444444 in het bereik *Pharma-Sales* .

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 44444444-4444-4444-4444-444444444444 --resource-group pharma-sales
```

### <a name="create-a-role-assignment-for-a-user-at-a-subscription-scope"></a>Een roltoewijzing maken voor een gebruiker op een abonnements bereik

Als u toegang wilt verlenen aan een gebruiker op een abonnements bereik, gebruikt u [AZ Role Assignment Create](/cli/azure/role/assignment#az-role-assignment-create). Als u de abonnements-ID wilt ophalen, kunt u deze vinden op de Blade **abonnementen** in de Azure portal of kunt u [AZ account list](/cli/azure/account#az-account-list)gebruiken.

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --subscription <subscription_name_or_id>
```

In het volgende voor beeld wordt de rol van *lezer* toegewezen aan de *annm\@example.com* -gebruiker bij een abonnements bereik.

```azurecli
az role assignment create --role "Reader" --assignee annm@example.com --subscription 00000000-0000-0000-0000-000000000000
```

### <a name="create-a-role-assignment-for-a-user-at-a-management-group-scope"></a>Een roltoewijzing maken voor een gebruiker in een bereik van een beheer groep

Als u toegang wilt verlenen aan een gebruiker op een beheer groeps bereik, gebruikt u [AZ Role Assignment Create](/cli/azure/role/assignment#az-role-assignment-create). Als u de beheer groep-ID wilt ophalen, kunt u deze vinden op de Blade **beheer groepen** in de Azure portal of u kunt [AZ Account Management-Group List](/cli/azure/ext/managementgroups/account/management-group#ext-managementgroups-az-account-management-group-list)gebruiken.

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

In het volgende voor beeld wordt de rol *facturerings lezer* toegewezen aan de *Alain\@example.com* -gebruiker in een bereik van een beheer groep.

```azurecli
az role assignment create --role "Billing Reader" --assignee alain@example.com --scope /providers/Microsoft.Management/managementGroups/marketing-group
```

### <a name="create-a-role-assignment-for-a-new-service-principal"></a>Een roltoewijzing maken voor een nieuwe Service-Principal

Als u een nieuwe Service-Principal maakt en een rol onmiddellijk probeert toe te wijzen aan die Service-Principal, kan die roltoewijzing in sommige gevallen mislukken. Als u bijvoorbeeld een script gebruikt voor het maken van een nieuwe beheerde identiteit en vervolgens probeert een rol toe te wijzen aan die Service-Principal, kan de roltoewijzing mislukken. De oorzaak van deze fout is waarschijnlijk een replicatie vertraging. De service-principal wordt gemaakt in één regio. de roltoewijzing kan echter plaatsvinden in een andere regio waarvoor de Service-Principal nog niet is gerepliceerd. Als u dit scenario wilt aanpakken, geeft u het Principal-type op bij het maken van de roltoewijzing.

Als u een roltoewijzing wilt maken, gebruikt u [AZ Role Assignment Create](/cli/azure/role/assignment#az-role-assignment-create), geeft u een waarde op voor `--assignee-object-id`en stelt u vervolgens `--assignee-principal-type` in op `ServicePrincipal`.

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --assignee-principal-type <assignee_principal_type> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

In het volgende voor beeld wordt de rol van *Inzender voor virtuele machines* toegewezen aan de door *MSI-test* beheerde identiteit in het *Pharma* van de resource groep:

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 33333333-3333-3333-3333-333333333333 --assignee-principal-type ServicePrincipal --resource-group pharma-sales
```

## <a name="remove-access"></a>Toegang intrekken

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

In het volgende voor beeld wordt de rol *facturerings lezer* verwijderd uit de *Alain\@example.com* -gebruiker in het bereik van de beheer groep. Als u de ID van de beheer groep wilt ophalen, kunt u [AZ Account Management-Group List](/cli/azure/ext/managementgroups/account/management-group#ext-managementgroups-az-account-management-group-list)gebruiken.

```azurecli
az role assignment delete --assignee alain@example.com --role "Billing Reader" --scope /providers/Microsoft.Management/managementGroups/marketing-group
```

## <a name="next-steps"></a>Volgende stappen

- [Zelf studie: een aangepaste rol maken voor Azure-resources met behulp van Azure CLI](tutorial-custom-role-cli.md)
- [Azure CLI gebruiken voor het beheren van Azure-resources en-resource groepen](../azure-resource-manager/cli-azure-resource-manager.md)
