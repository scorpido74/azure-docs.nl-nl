---
title: Aangepaste rollen voor Azure-resources maken of bijwerken met de REST-API
description: Meer informatie over het aanbieden, maken, bijwerken of verwijderen van aangepaste rollen met RBAC (Role-based access control) voor Azure-resources met behulp van de REST API.
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
editor: ''
ms.assetid: 1f90228a-7aac-4ea7-ad82-b57d222ab128
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: fda0400310f46da64322654c42af75521746d679
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062191"
---
# <a name="create-or-update-custom-roles-for-azure-resources-using-the-rest-api"></a>Aangepaste rollen voor Azure-resources maken of bijwerken met behulp van de REST-API

> [!IMPORTANT]
> Het toevoegen van `AssignableScopes` een beheergroep aan is momenteel in preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie.

Als de [ingebouwde rollen voor Azure-resources](built-in-roles.md) niet voldoen aan de specifieke behoeften van uw organisatie, u uw eigen aangepaste rollen maken. In dit artikel wordt beschreven hoe aangepaste rollen worden weergegeven, gemaakt, bijgewerkt of verwijderd met behulp van de REST-API.

## <a name="list-custom-roles"></a>Aangepaste rollen opvragen

Als u alle aangepaste rollen in een map wilt weergeven, gebruikt u de [functiedefinities - Rest-API weergeven.](/rest/api/authorization/roledefinitions/list)

1. Begin met de volgende aanvraag:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. Vervang *{filter}* door het roltype.

    > [!div class="mx-tableFixed"]
    > | Filteren | Beschrijving |
    > | --- | --- |
    > | `$filter=type+eq+'CustomRole'` | Filteren op basis van het type CustomRole |

## <a name="list-custom-roles-at-a-scope"></a>Aangepaste rollen aanbieden op een bereik

Als u aangepaste rollen op een bereik wilt weergeven, gebruikt u de [functiedefinities - REST-API weergeven.](/rest/api/authorization/roledefinitions/list)

1. Begin met de volgende aanvraag:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. Vervang *{scope}* binnen de URI door het bereik waarvoor u de rollen wilt weergeven.

    > [!div class="mx-tableFixed"]
    > | Bereik | Type |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Abonnement |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Resourcegroep |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}/providers/Microsoft.Web/sites/{site1}` | Resource |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Beheergroep |

1. Vervang *{filter}* door het roltype.

    > [!div class="mx-tableFixed"]
    > | Filteren | Beschrijving |
    > | --- | --- |
    > | `$filter=type+eq+'CustomRole'` | Filteren op basis van het type CustomRole |

## <a name="list-a-custom-role-definition-by-name"></a>Een aangepaste roldefinitie op naam weergeven

Als u informatie wilt krijgen over een aangepaste rol op basis van de weergavenaam, gebruikt u de [roldefinities - REST-API opvragen.](/rest/api/authorization/roledefinitions/get)

1. Begin met de volgende aanvraag:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. Vervang *{scope}* binnen de URI door het bereik waarvoor u de rollen wilt weergeven.

    > [!div class="mx-tableFixed"]
    > | Bereik | Type |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Abonnement |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Resourcegroep |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}/providers/Microsoft.Web/sites/{site1}` | Resource |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Beheergroep |

1. Vervang *{filter}* door de weergavenaam voor de rol.

    > [!div class="mx-tableFixed"]
    > | Filteren | Beschrijving |
    > | --- | --- |
    > | `$filter=roleName+eq+'{roleDisplayName}'` | Gebruik de door de URL gecodeerde vorm van de exacte weergavenaam van de rol. Bijvoorbeeld,`$filter=roleName+eq+'Virtual%20Machine%20Contributor'` |

## <a name="list-a-custom-role-definition-by-id"></a>Een aangepaste roldefinitie op id weergeven

Als u informatie wilt krijgen over een aangepaste rol door de unieke id, gebruikt u de [roldefinities - Rest-API opvragen.](/rest/api/authorization/roledefinitions/get)

1. Gebruik de [functiedefinities - Lijst](/rest/api/authorization/roledefinitions/list) REST-API om de GUID-id voor de rol te krijgen.

1. Begin met de volgende aanvraag:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. Vervang *{scope}* binnen de URI door het bereik waarvoor u de rollen wilt weergeven.

    > [!div class="mx-tableFixed"]
    > | Bereik | Type |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Abonnement |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Resourcegroep |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}/providers/Microsoft.Web/sites/{site1}` | Resource |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Beheergroep |

1. Vervang *{roleDefinitionId}* door de GUID-id van de roldefinitie.

## <a name="create-a-custom-role"></a>Een aangepaste rol maken

Als u een aangepaste rol wilt maken, gebruikt u de [functiedefinities - REST-API maken of bijwerken.](/rest/api/authorization/roledefinitions/createorupdate) Als u deze API wilt aanroepen, moet u zijn aangemeld `Microsoft.Authorization/roleDefinitions/write` bij een `assignableScopes`gebruiker waaraan een rol is toegewezen die de toestemming heeft voor alle . Van de ingebouwde rollen bevatten alleen [eigenaar-](built-in-roles.md#owner) en [gebruikerstoegangsbeheerder](built-in-roles.md#user-access-administrator) deze machtiging.

1. Bekijk de lijst met bewerkingen van [resourceprovideren](resource-provider-operations.md) die beschikbaar zijn om de machtigingen voor uw aangepaste rol te maken.

1. Gebruik een GUID-tool om een unieke id te genereren die wordt gebruikt voor de aangepaste rol-id. De id heeft de indeling:`00000000-0000-0000-0000-000000000000`

1. Begin met de volgende aanvraag en instantie:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

    ```json
    {
      "name": "{roleDefinitionId}",
      "properties": {
        "roleName": "",
        "description": "",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
    
            ],
            "notActions": [
    
            ]
          }
        ],
        "assignableScopes": [
          "/subscriptions/{subscriptionId1}",
          "/subscriptions/{subscriptionId2}",
          "/subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}",
          "/subscriptions/{subscriptionId2}/resourceGroups/{resourceGroup2}",
          "/providers/Microsoft.Management/managementGroups/{groupId1}"
        ]
      }
    }
    ```

1. Vervang *{scope}* binnen de URI `assignableScopes` door de eerste van de aangepaste rol.

    > [!div class="mx-tableFixed"]
    > | Bereik | Type |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Abonnement |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Resourcegroep |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Beheergroep |

1. Vervang *{roleDefinitionId}* door de GUID-id van de aangepaste rol.

1. Vervang *{roleDefinitionId}* binnen de aanvraaginstantie door de GUID-id.

1. Als `assignableScopes` dit een abonnement of resourcegroep is, vervangt u de *{subscriptionId}* of *{resourceGroup}* exemplaren door uw id's.

1. Als `assignableScopes` het een beheergroep is, vervangt u de instantie *{groupId}* door uw beheergroep-id. Het toevoegen van `assignableScopes` een beheergroep aan is momenteel in preview.

1. Voeg `actions` in de eigenschap de bewerkingen toe die de rol toestaat om te worden uitgevoerd.

1. Voeg `notActions` in de eigenschap de bewerkingen toe `actions`die zijn uitgesloten van de toegestane .

1. Geef `roleName` in `description` de eigenschappen en eigenschappen een unieke rolnaam en een beschrijving op. Zie [Aangepaste rollen voor](custom-roles.md)meer informatie over de eigenschappen.

    Het volgende toont een voorbeeld van een aanvraaginstantie:

    ```json
    {
      "name": "88888888-8888-8888-8888-888888888888",
      "properties": {
        "roleName": "Virtual Machine Operator",
        "description": "Can monitor and restart virtual machines.",
        "type": "CustomRole",
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
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "assignableScopes": [
          "/subscriptions/00000000-0000-0000-0000-000000000000",
          "/providers/Microsoft.Management/managementGroups/marketing-group"
        ]
      }
    }
    ```

## <a name="update-a-custom-role"></a>Een aangepaste rol bijwerken

Als u een aangepaste rol wilt bijwerken, gebruikt u de [functiedefinities - REST-API maken of bijwerken.](/rest/api/authorization/roledefinitions/createorupdate) Als u deze API wilt aanroepen, moet u zijn aangemeld `Microsoft.Authorization/roleDefinitions/write` bij een `assignableScopes`gebruiker waaraan een rol is toegewezen die de toestemming heeft voor alle . Van de ingebouwde rollen bevatten alleen [eigenaar-](built-in-roles.md#owner) en [gebruikerstoegangsbeheerder](built-in-roles.md#user-access-administrator) deze machtiging.

1. Gebruik de [functiedefinities - Lijst-](/rest/api/authorization/roledefinitions/list) of [roldefinities - Download](/rest/api/authorization/roledefinitions/get) DE REST-API om informatie te krijgen over de aangepaste rol. Zie de sectie Aangepaste [rollen lijst](#list-custom-roles) voor meer informatie.

1. Begin met de volgende aanvraag:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. Vervang *{scope}* binnen de URI `assignableScopes` door de eerste van de aangepaste rol.

    > [!div class="mx-tableFixed"]
    > | Bereik | Type |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Abonnement |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Resourcegroep |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Beheergroep |

1. Vervang *{roleDefinitionId}* door de GUID-id van de aangepaste rol.

1. Maak op basis van de informatie over de aangepaste rol een aanvraaginstantie met de volgende indeling:

    ```json
    {
      "name": "{roleDefinitionId}",
      "properties": {
        "roleName": "",
        "description": "",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
    
            ],
            "notActions": [
    
            ]
          }
        ],
        "assignableScopes": [
          "/subscriptions/{subscriptionId1}",
          "/subscriptions/{subscriptionId2}",
          "/subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}",
          "/subscriptions/{subscriptionId2}/resourceGroups/{resourceGroup2}",
          "/providers/Microsoft.Management/managementGroups/{groupId1}"
        ]
      }
    }
    ```

1. Werk de aanvraaginstantie bij met de wijzigingen die u wilt aanbrengen in de aangepaste rol.

    Het volgende toont een voorbeeld van een aanvraaginstantie met een nieuwe actie voor diagnostische instellingen:

    ```json
    {
      "name": "88888888-8888-8888-8888-888888888888",
      "properties": {
        "roleName": "Virtual Machine Operator",
        "description": "Can monitor and restart virtual machines.",
        "type": "CustomRole",
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
            "notActions": []
          }
        ],
        "assignableScopes": [
          "/subscriptions/00000000-0000-0000-0000-000000000000",
          "/providers/Microsoft.Management/managementGroups/marketing-group"
        ]
      }
    }
    ```

## <a name="delete-a-custom-role"></a>Een aangepaste rol verwijderen

Als u een aangepaste rol wilt verwijderen, gebruikt u de [API Voor roldefinities verwijderen](/rest/api/authorization/roledefinitions/delete) . Als u deze API wilt aanroepen, moet u zijn aangemeld `Microsoft.Authorization/roleDefinitions/delete` bij een `assignableScopes`gebruiker waaraan een rol is toegewezen die de toestemming heeft voor alle . Van de ingebouwde rollen bevatten alleen [eigenaar-](built-in-roles.md#owner) en [gebruikerstoegangsbeheerder](built-in-roles.md#user-access-administrator) deze machtiging.

1. Gebruik de [functiedefinities - Lijst-](/rest/api/authorization/roledefinitions/list) of [roldefinities - Download](/rest/api/authorization/roledefinitions/get) DE API voor REST om de GUID-id van de aangepaste rol te krijgen. Zie de sectie Aangepaste [rollen lijst](#list-custom-roles) voor meer informatie.

1. Begin met de volgende aanvraag:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. Vervang *{scope}* binnen de URI door het bereik dat u de aangepaste rol wilt verwijderen.

    > [!div class="mx-tableFixed"]
    > | Bereik | Type |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Abonnement |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Resourcegroep |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Beheergroep |

1. Vervang *{roleDefinitionId}* door de GUID-id van de aangepaste rol.

## <a name="next-steps"></a>Volgende stappen

- [Aangepaste rollen voor Azure-resources](custom-roles.md)
- [Toegang tot Azure-bronnen beheren met RBAC en de REST API](role-assignments-rest.md)
- [Azure REST API-verwijzing](/rest/api/azure/)
