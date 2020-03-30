---
title: Aangepaste rollen voor Azure-resources | Microsoft Documenten
description: Meer informatie over het maken van aangepaste rollen met rbac (role-based access control) voor fijnkorrelig toegangsbeheer van Azure-resources.
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
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9454962e210781559f2fdceb1c36f499c4ae8ff7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062171"
---
# <a name="custom-roles-for-azure-resources"></a>Aangepaste rollen voor Azure-resources

> [!IMPORTANT]
> Het toevoegen van `AssignableScopes` een beheergroep aan is momenteel in preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie.

Als de [ingebouwde rollen voor Azure-resources](built-in-roles.md) niet voldoen aan de specifieke behoeften van uw organisatie, u uw eigen aangepaste rollen maken. Net als ingebouwde rollen u aangepaste rollen toewijzen aan gebruikers, groepen en serviceprincipals bij beheergroep-, abonnements- en resourcegroepscopes.

Aangepaste rollen kunnen worden gedeeld tussen abonnementen die dezelfde Azure AD-map vertrouwen. Er is een limiet van **5.000** aangepaste rollen per map. (Voor Azure Germany en Azure China 21Vianet is de limiet 2.000 aangepaste rollen.) Aangepaste rollen kunnen worden gemaakt met behulp van de Azure-portal (Preview), Azure PowerShell, Azure CLI of de REST API.

## <a name="custom-role-example"></a>Voorbeeld van aangepaste rollen

Het volgende laat zien hoe een aangepaste rol eruit ziet zoals weergegeven in JSON-indeling. Deze aangepaste rol kan worden gebruikt voor het monitoren en opnieuw opstarten van virtuele machines.

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
    "/providers/Microsoft.Management/managementGroups/{groupId1}"
  ]
}
```

Wanneer u een aangepaste rol maakt, wordt deze weergegeven in de Azure-portal met een oranje resourcepictogram.

![Pictogram Aangepaste rol](./media/custom-roles/roles-custom-role-icon.png)

## <a name="steps-to-create-a-custom-role"></a>Stappen om een aangepaste rol te maken

1. Bepalen hoe u de aangepaste rol wilt maken

    U aangepaste rollen maken met [Azure-portal](custom-roles-portal.md) (Preview), [Azure PowerShell,](custom-roles-powershell.md) [Azure CLI](custom-roles-cli.md)of de [REST-API.](custom-roles-rest.md)

1. Bepaal de machtigingen die u nodig hebt

    Wanneer u een aangepaste rol maakt, moet u de bewerkingen van de resourceprovider kennen die beschikbaar zijn om uw machtigingen te definiëren. Zie de [azure resource manager-providerbewerkingen](resource-provider-operations.md)voor het weergeven van de lijst met bewerkingen. U voegt de bewerkingen `NotActions` toe aan de of-eigenschappen `Actions` van de [roldefinitie.](role-definitions.md) Als u gegevensbewerkingen hebt, voegt `DataActions` `NotDataActions` u deze toe aan de of-eigenschappen.

1. De aangepaste rol maken

    Meestal begint u met een bestaande ingebouwde rol en wijzigt u deze vervolgens voor uw behoeften. Vervolgens gebruikt u de [nieuwe-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) of [az-roldefinitie opdrachten maken](/cli/azure/role/definition#az-role-definition-create) om de aangepaste rol te maken. Als u een aangepaste rol `Microsoft.Authorization/roleDefinitions/write` wilt maken, moet u de toestemming hebben voor iedereen, `AssignableScopes`zoals [eigenaar](built-in-roles.md#owner) of [gebruikerstoegangsbeheerder.](built-in-roles.md#user-access-administrator)

1. De aangepaste rol testen

    Zodra u uw aangepaste rol hebt, moet u deze testen om te controleren of deze werkt zoals u verwacht. Als u later aanpassingen moet aanbrengen, u de aangepaste rol bijwerken.

Zie [Zelfstudie: Een aangepaste rol maken met Azure PowerShell](tutorial-custom-role-powershell.md) of [Zelfstudie: Een aangepaste rol maken voor](tutorial-custom-role-cli.md)een stapsgewijze zelfstudie over het maken van een aangepaste rol.

## <a name="custom-role-properties"></a>Aangepaste functie-eigenschappen

Een aangepaste rol heeft de volgende eigenschappen.

| Eigenschap | Vereist | Type | Beschrijving |
| --- | --- | --- | --- |
| `Name` | Ja | Tekenreeks | De weergavenaam van de aangepaste rol. Hoewel een roldefinitie een beheergroep of bron op abonnementsniveau is, kan een roldefinitie worden gebruikt in meerdere abonnementen die dezelfde Azure AD-map delen. Deze weergavenaam moet uniek zijn in het bereik van de Azure AD-map. Kan letters, cijfers, spaties en speciale tekens bevatten. Het maximum aantal tekens is 128. |
| `Id` | Ja | Tekenreeks | De unieke ID van de aangepaste rol. Voor Azure PowerShell en Azure CLI wordt deze id automatisch gegenereerd wanneer u een nieuwe rol maakt. |
| `IsCustom` | Ja | Tekenreeks | Hiermee geeft u aan of dit een aangepaste rol is. Ingesteld `true` op aangepaste rollen. |
| `Description` | Ja | Tekenreeks | De beschrijving van de aangepaste rol. Kan letters, cijfers, spaties en speciale tekens bevatten. Het maximum aantal tekens is 1024. |
| `Actions` | Ja | Tekenreeks[] | Een array met tekenreeksen die de beheerbewerkingen aangeeft die de rol toestaat om te worden uitgevoerd. Zie [Acties voor](role-definitions.md#actions)meer informatie . |
| `NotActions` | Nee | Tekenreeks[] | Een array met tekenreeksen die de beheerbewerkingen `Actions`opgeeft die zijn uitgesloten van de toegestane . Zie [NotActions voor](role-definitions.md#notactions)meer informatie . |
| `DataActions` | Nee | Tekenreeks[] | Een array met tekenreeksen die de gegevensbewerkingen opgeeft die de rol toestaat om te worden uitgevoerd naar uw gegevens binnen dat object. Als u een aangepaste `DataActions`rol maakt met , kan die rol niet worden toegewezen aan het bereik van de beheergroep. Zie [DataActions](role-definitions.md#dataactions)voor meer informatie . |
| `NotDataActions` | Nee | Tekenreeks[] | Een array met tekenreeksen die de gegevensbewerkingen `DataActions`opgeeft die zijn uitgesloten van de toegestane . Zie [NotDataActions](role-definitions.md#notdataactions)voor meer informatie . |
| `AssignableScopes` | Ja | Tekenreeks[] | Een array met tekenreeksen die de scopes opgeeft die de aangepaste rol beschikbaar is voor toewijzing. U slechts één `AssignableScopes` beheergroep definiëren in een aangepaste rol. Het toevoegen van `AssignableScopes` een beheergroep aan is momenteel in preview. Zie [AssignableScopes voor](role-definitions.md#assignablescopes)meer informatie . |

## <a name="who-can-create-delete-update-or-view-a-custom-role"></a>Wie kan een aangepaste rol maken, verwijderen, bijwerken of weergeven

Net als ingebouwde rollen `AssignableScopes` geeft de eigenschap de scopes aan die de rol beschikbaar is voor toewijzing. De `AssignableScopes` eigenschap voor een aangepaste rol bepaalt ook wie de aangepaste rol kan maken, verwijderen, bijwerken of weergeven.

| Taak | Bewerking | Beschrijving |
| --- | --- | --- |
| Een aangepaste rol maken/verwijderen | `Microsoft.Authorization/ roleDefinitions/write` | Gebruikers die deze bewerking op `AssignableScopes` alle aangepaste rollen krijgen, kunnen aangepaste rollen maken (of verwijderen) voor gebruik in die scopes. Eigenaren [en](built-in-roles.md#owner) beheerders van [gebruikerstoegang](built-in-roles.md#user-access-administrator) van beheergroepen, abonnementen en brongroepen. |
| Een aangepaste rol bijwerken | `Microsoft.Authorization/ roleDefinitions/write` | Gebruikers die deze bewerking op `AssignableScopes` alle aangepaste rollen krijgen toegewezen, kunnen aangepaste rollen in die scopes bijwerken. Eigenaren [en](built-in-roles.md#owner) beheerders van [gebruikerstoegang](built-in-roles.md#user-access-administrator) van beheergroepen, abonnementen en brongroepen. |
| Een aangepaste rol weergeven | `Microsoft.Authorization/ roleDefinitions/read` | Gebruikers die deze bewerking op een bereik krijgen, kunnen de aangepaste rollen bekijken die beschikbaar zijn voor toewijzing op dat bereik. Met alle ingebouwde rollen kunnen aangepaste rollen beschikbaar zijn voor toewijzing. |

## <a name="custom-role-limits"></a>Aangepaste rollimieten

In de volgende lijst worden de limieten voor aangepaste rollen beschreven.

- Elke map kan maximaal **5000** aangepaste rollen hebben.
- Azure Germany en Azure China 21Vianet kan tot 2000 aangepaste rollen hebben voor elke map.
- U kunt `AssignableScopes` niet instellen`"/"`op het hoofdbereik ( ).
- U slechts één `AssignableScopes` beheergroep definiëren in een aangepaste rol. Het toevoegen van `AssignableScopes` een beheergroep aan is momenteel in preview.
- Aangepaste rollen `DataActions` met kan niet worden toegewezen aan het bereik van de beheergroep.
- Azure Resource Manager valideert het bestaan van de beheergroep niet in het toewijsbare bereik van de roldefinitie.

Zie [Uw resources ordenen met Azure-beheergroepen](../governance/management-groups/overview.md#custom-rbac-role-definition-and-assignment)voor meer informatie over aangepaste rollen en beheergroepen.

## <a name="next-steps"></a>Volgende stappen
- [Aangepaste Azure-rollen maken of bijwerken met de Azure-portal (Voorbeeld)](custom-roles-portal.md)
- [Functiedefinities voor Azure-resources begrijpen](role-definitions.md)
- [Problemen met RBAC voor Azure-resources oplossen](troubleshooting.md)
