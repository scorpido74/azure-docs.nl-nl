---
title: Aangepaste rollen voor Azure-resources | Microsoft Docs
description: Meer informatie over het maken van aangepaste rollen met op rollen gebaseerd toegangs beheer (RBAC) voor een nauw keurig toegangs beheer van Azure-resources.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: e4206ea9-52c3-47ee-af29-f6eef7566fa5
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/07/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 64161451c0c8b1af7666fcd104d337856e5803c7
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72821446"
---
# <a name="custom-roles-for-azure-resources"></a>Aangepaste rollen voor Azure-resources

Als de [ingebouwde rollen voor Azure-resources](built-in-roles.md) niet voldoen aan de specifieke behoeften van uw organisatie, kunt u uw eigen aangepaste rollen maken. Net als bij ingebouwde rollen kunt u aangepaste rollen toewijzen aan gebruikers, groepen en service-principals bij het abonnement, de resource groep en het bereik van de resource.

Aangepaste rollen kunnen worden gedeeld tussen abonnementen die dezelfde Azure AD-Directory vertrouwen. Er geldt een limiet van **5.000** aangepaste rollen per map. (Voor gespecialiseerde Clouds, zoals Azure Government, Azure Duitsland en Azure China 21Vianet, is de limiet 2.000 aangepaste rollen.) Aangepaste rollen kunnen worden gemaakt met behulp van Azure PowerShell, Azure CLI of de REST API.

## <a name="custom-role-example"></a>Voor beeld van aangepaste rol

Hieronder ziet u hoe een aangepaste rol eruitziet zoals wordt weer gegeven in JSON-indeling. Deze aangepaste rol kan worden gebruikt voor het bewaken en opnieuw starten van virtuele machines.

```json
{
  "Name": "Virtual Machine Operator",
  "Id": "88888888-8888-8888-8888-888888888888",
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
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/subscriptions/{subscriptionId1}",
    "/subscriptions/{subscriptionId2}",
    "/subscriptions/{subscriptionId3}"
  ]
}
```

Wanneer u een aangepaste rol maakt, wordt deze weer gegeven in de Azure Portal met een oranje resource pictogram.

![Pictogram aangepaste rol](./media/custom-roles/roles-custom-role-icon.png)

## <a name="steps-to-create-a-custom-role"></a>Stappen voor het maken van een aangepaste rol

1. Bepalen hoe u de aangepaste rol wilt maken

    U kunt aangepaste rollen maken met behulp van [Azure PowerShell](custom-roles-powershell.md), [Azure CLI](custom-roles-cli.md)of de [rest API](custom-roles-rest.md).

1. De benodigde machtigingen bepalen

    Wanneer u een aangepaste rol maakt, moet u weten welke bewerkingen van de resource provider beschikbaar zijn voor het definiëren van uw machtigingen. Als u de lijst met bewerkingen wilt weer geven, raadpleegt u de bewerkingen van de [resource provider van Azure Resource Manager](resource-provider-operations.md). U voegt de bewerkingen toe aan de `Actions`-of `NotActions` eigenschappen van de [functie definitie](role-definitions.md). Als u gegevens bewerkingen hebt, voegt u deze toe aan de eigenschappen `DataActions` of `NotDataActions`.

1. De aangepaste rol maken

    Normaal gesp roken begint u met een bestaande ingebouwde rol en wijzigt u deze voor uw behoeften. Vervolgens gebruikt u de [nieuwe AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) of [AZ Role definition maken](/cli/azure/role/definition#az-role-definition-create) om de aangepaste rol te maken. Als u een aangepaste rol wilt maken, moet u de machtiging `Microsoft.Authorization/roleDefinitions/write` hebben voor alle `AssignableScopes`, zoals de [eigenaar](built-in-roles.md#owner) of de beheerder van de [gebruikers toegang](built-in-roles.md#user-access-administrator).

1. De aangepaste rol testen

    Wanneer u uw aangepaste rol hebt, moet u deze testen om te controleren of deze werkt zoals verwacht. Als u later aanpassingen wilt aanbrengen, kunt u de aangepaste rol bijwerken.

Voor een stapsgewijze zelf studie over het maken van een aangepaste rol, Zie [zelf studie: een aangepaste rol maken met behulp van Azure PowerShell](tutorial-custom-role-powershell.md) of [zelf studie: een aangepaste rol maken met behulp van Azure cli](tutorial-custom-role-cli.md).

## <a name="custom-role-properties"></a>Eigenschappen van aangepaste rol

Een aangepaste rol heeft de volgende eigenschappen.

| Eigenschap | Verplicht | Type | Beschrijving |
| --- | --- | --- | --- |
| `Name` | Ja | Tekenreeks | De weergave naam van de aangepaste rol. Hoewel een functie definitie een resource op abonnements niveau is, kan een roldefinitie worden gebruikt in meerdere abonnementen die dezelfde Azure AD-directory delen. Deze weergave naam moet uniek zijn binnen het bereik van de Azure AD-adres lijst. Kan letters, cijfers, spaties en speciale tekens bevatten. Het maximum aantal tekens is 128. |
| `Id` | Ja | Tekenreeks | De unieke ID van de aangepaste rol. Voor Azure PowerShell en Azure CLI wordt deze ID automatisch gegenereerd wanneer u een nieuwe rol maakt. |
| `IsCustom` | Ja | Tekenreeks | Hiermee wordt aangegeven of dit een aangepaste rol is. Ingesteld op `true` voor aangepaste rollen. |
| `Description` | Ja | Tekenreeks | De beschrijving van de aangepaste rol. Kan letters, cijfers, spaties en speciale tekens bevatten. Het maximum aantal tekens is 1024. |
| `Actions` | Ja | Teken reeks [] | Een matrix met teken reeksen die de beheer bewerkingen specificeert die de rol kan uitvoeren. Zie [acties](role-definitions.md#actions)voor meer informatie. |
| `NotActions` | Nee | Teken reeks [] | Een matrix met teken reeksen die de beheer bewerkingen specificeert die zijn uitgesloten van de toegestane `Actions`. Zie voor meer informatie [intact](role-definitions.md#notactions). |
| `DataActions` | Nee | Teken reeks [] | Een matrix met teken reeksen waarmee de gegevens bewerkingen worden opgegeven die door de functie kunnen worden uitgevoerd op uw gegevens in dat object. Zie [DataActions](role-definitions.md#dataactions)voor meer informatie. |
| `NotDataActions` | Nee | Teken reeks [] | Een matrix met teken reeksen die de gegevens bewerkingen specificeert die worden uitgesloten van de toegestane `DataActions`. Zie [NotDataActions](role-definitions.md#notdataactions)voor meer informatie. |
| `AssignableScopes` | Ja | Teken reeks [] | Een matrix met teken reeksen die de bereiken aangeeft waarvoor de aangepaste rol beschikbaar is voor toewijzing. Voor aangepaste rollen kunt u momenteel geen `AssignableScopes` instellen op het hoofd bereik (`"/"`) of een bereik van een beheer groep. Zie [AssignableScopes](role-definitions.md#assignablescopes) en [Organiseer uw resources met Azure-beheer groepen](../governance/management-groups/overview.md#custom-rbac-role-definition-and-assignment)voor meer informatie. |

## <a name="who-can-create-delete-update-or-view-a-custom-role"></a>Wie kan een aangepaste rol maken, verwijderen, bijwerken of weer geven

Net als bij ingebouwde rollen specificeert de eigenschap `AssignableScopes` de bereiken die de rol beschikbaar is voor toewijzing. De eigenschap `AssignableScopes` voor een aangepaste rol bepaalt ook wie de aangepaste rol kan maken, verwijderen, bijwerken of weer geven.

| Taak | Bewerking | Beschrijving |
| --- | --- | --- |
| Een aangepaste rol maken/verwijderen | `Microsoft.Authorization/ roleDefinitions/write` | Gebruikers aan wie deze bewerking is verleend op alle `AssignableScopes` van de aangepaste rol kunnen aangepaste rollen maken (of verwijderen) voor gebruik in deze bereiken. Bijvoorbeeld [eigen aren](built-in-roles.md#owner) en [beheerders van gebruikers toegang tot](built-in-roles.md#user-access-administrator) abonnementen, resource groepen en resources. |
| Een aangepaste rol bijwerken | `Microsoft.Authorization/ roleDefinitions/write` | Gebruikers aan wie deze bewerking is verleend op alle `AssignableScopes` van de aangepaste rol, kunnen aangepaste rollen in deze bereiken bijwerken. Bijvoorbeeld [eigen aren](built-in-roles.md#owner) en [beheerders van gebruikers toegang tot](built-in-roles.md#user-access-administrator) abonnementen, resource groepen en resources. |
| Een aangepaste rol weer geven | `Microsoft.Authorization/ roleDefinitions/read` | Gebruikers aan wie deze bewerking in een bereik is verleend, kunnen de aangepaste rollen bekijken die beschikbaar zijn voor toewijzing in dat bereik. Bij alle ingebouwde rollen kunnen aangepaste rollen beschikbaar zijn voor toewijzing. |

## <a name="next-steps"></a>Volgende stappen
- [Aangepaste rollen maken voor Azure-resources met Azure PowerShell](custom-roles-powershell.md)
- [Aangepaste rollen maken voor Azure-resources met Azure CLI](custom-roles-cli.md)
- [Functie definities voor Azure-resources begrijpen](role-definitions.md)
- [Problemen met RBAC voor Azure-resources oplossen](troubleshooting.md)
