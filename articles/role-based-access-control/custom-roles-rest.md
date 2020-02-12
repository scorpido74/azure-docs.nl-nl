---
title: Aangepaste rollen voor Azure-resources maken of bijwerken met de REST API
description: Meer informatie over het weer geven, maken, bijwerken of verwijderen van aangepaste rollen met op rollen gebaseerd toegangs beheer (RBAC) voor Azure-resources met behulp van de REST API.
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
ms.date: 04/18/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 145bc45e1b7faeddc23cf5f0662337e15ab51c29
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/11/2020
ms.locfileid: "77137365"
---
# <a name="create-or-update-custom-roles-for-azure-resources-using-the-rest-api"></a>Aangepaste rollen maken of bijwerken voor Azure-resources met behulp van de REST API

Als de [ingebouwde rollen voor Azure-resources](built-in-roles.md) niet voldoen aan de specifieke behoeften van uw organisatie, kunt u uw eigen aangepaste rollen maken. In dit artikel wordt beschreven hoe u aangepaste rollen oplijsteert, maakt, bijwerkt of verwijdert met behulp van de REST API.

## <a name="list-custom-roles"></a>Aangepaste rollen opvragen

Als u alle aangepaste rollen in een directory wilt weer geven, gebruikt u de [roldefinities-lijst](/rest/api/authorization/roledefinitions/list) rest API.

1. Begin met de volgende aanvraag:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. Vervang *{filter}* door het type rol.

    | Filteren | Beschrijving |
    | --- | --- |
    | `$filter=type%20eq%20'CustomRole'` | Filteren op basis van het type CustomRole |

## <a name="list-custom-roles-at-a-scope"></a>Aangepaste rollen in een bereik weer geven

Als u aangepaste rollen in een bereik wilt weer geven, gebruikt u de [roldefinities-lijst](/rest/api/authorization/roledefinitions/list) rest API.

1. Begin met de volgende aanvraag:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. Vervang *{Scope}* in de URI door het bereik waarvoor u de rollen wilt weer geven.

    | Bereik | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Abonnement |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Resourcegroep |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Resource |

1. Vervang *{filter}* door het type rol.

    | Filteren | Beschrijving |
    | --- | --- |
    | `$filter=type%20eq%20'CustomRole'` | Filteren op basis van het type CustomRole |

## <a name="list-a-custom-role-definition-by-name"></a>Een aangepaste roldefinitie op naam vermelden

Als u informatie wilt weer geven over een aangepaste rol met behulp van de weergave naam, gebruikt u de [roldefinities-ophalen](/rest/api/authorization/roledefinitions/get) rest API.

1. Begin met de volgende aanvraag:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. Vervang *{Scope}* in de URI door het bereik waarvoor u de rollen wilt weer geven.

    | Bereik | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Abonnement |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Resourcegroep |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Resource |

1. Vervang *{filter}* door de weergave naam voor de rol.

    | Filteren | Beschrijving |
    | --- | --- |
    | `$filter=roleName%20eq%20'{roleDisplayName}'` | Gebruik het formulier URL-code ring van de exacte weergave naam van de rol. Bijvoorbeeld `$filter=roleName%20eq%20'Virtual%20Machine%20Contributor'` |

## <a name="list-a-custom-role-definition-by-id"></a>Een aangepaste roldefinitie op basis van een ID weer geven

Als u informatie wilt ophalen over een aangepaste rol met de unieke id, gebruikt u de [roldefinities-get](/rest/api/authorization/roledefinitions/get) rest API.

1. Gebruik de [roldefinities-lijst](/rest/api/authorization/roledefinitions/list) rest API om de GUID-id voor de rol op te halen.

1. Begin met de volgende aanvraag:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. Vervang *{Scope}* in de URI door het bereik waarvoor u de rollen wilt weer geven.

    | Bereik | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Abonnement |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Resourcegroep |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Resource |

1. Vervang *{roledefinitionid hebben}* door de GUID-id van de roldefinitie.

## <a name="create-a-custom-role"></a>Een aangepaste rol maken

Als u een aangepaste rol wilt maken, gebruikt u de [roldefinities-rest API maken of bijwerken](/rest/api/authorization/roledefinitions/createorupdate) . Als u deze API wilt aanroepen, moet u zijn aangemeld met een gebruiker waaraan een rol is toegewezen die de machtiging `Microsoft.Authorization/roleDefinitions/write` heeft voor alle `assignableScopes`. Van de ingebouwde rollen, alleen de beheerder van de [eigenaar](built-in-roles.md#owner) en de [gebruikers toegang](built-in-roles.md#user-access-administrator) bevatten deze machtiging.

1. Bekijk de lijst met beschik bare bewerkingen voor de [resource provider](resource-provider-operations.md) voor het maken van de machtigingen voor uw aangepaste rol.

1. Gebruik een GUID-hulp programma voor het genereren van een unieke id die wordt gebruikt voor de id van de aangepaste rol. De id heeft de volgende indeling: `00000000-0000-0000-0000-000000000000`

1. Beginnen met de volgende aanvraag en hoofd tekst:

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
          "/subscriptions/{subscriptionId}"
        ]
      }
    }
    ```

1. Vervang *{Scope}* in de URI door de eerste `assignableScopes` van de aangepaste rol.

    | Bereik | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Abonnement |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Resourcegroep |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Resource |

1. Vervang *{roledefinitionid hebben}* door de GUID-id van de aangepaste rol.

1. Vervang in de hoofd tekst van de aanvraag in de eigenschap `assignableScopes` *{roledefinitionid hebben}* door de GUID-id.

1. Vervang *{subscriptionId}* door de id van uw abonnement.

1. Voeg in de eigenschap `actions` de bewerkingen toe die door de functie kunnen worden uitgevoerd.

1. Voeg in de eigenschap `notActions` de bewerkingen toe die zijn uitgesloten van de toegestane `actions`.

1. Geef in de eigenschappen `roleName` en `description` een unieke rolnaam en een beschrijving op. Zie voor meer informatie over de eigenschappen [aangepaste rollen](custom-roles.md).

    Hieronder ziet u een voor beeld van een aanvraag tekst:

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
          "/subscriptions/00000000-0000-0000-0000-000000000000"
        ]
      }
    }
    ```

## <a name="update-a-custom-role"></a>Een aangepaste rol bijwerken

Als u een aangepaste rol wilt bijwerken, gebruikt u de [roldefinities-rest API maken of bijwerken](/rest/api/authorization/roledefinitions/createorupdate) . Als u deze API wilt aanroepen, moet u zijn aangemeld met een gebruiker waaraan een rol is toegewezen die de machtiging `Microsoft.Authorization/roleDefinitions/write` heeft voor alle `assignableScopes`. Van de ingebouwde rollen, alleen de beheerder van de [eigenaar](built-in-roles.md#owner) en de [gebruikers toegang](built-in-roles.md#user-access-administrator) bevatten deze machtiging.

1. De roldefinities gebruiken [-lijst](/rest/api/authorization/roledefinitions/list) -of [roldefinities-rest API ophalen](/rest/api/authorization/roledefinitions/get) om informatie over de aangepaste rol op te halen. Zie de sectie [met aangepaste rollen](#list-custom-roles) van de eerdere lijst voor meer informatie.

1. Begin met de volgende aanvraag:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. Vervang *{Scope}* in de URI door de eerste `assignableScopes` van de aangepaste rol.

    | Bereik | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Abonnement |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Resourcegroep |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Resource |

1. Vervang *{roledefinitionid hebben}* door de GUID-id van de aangepaste rol.

1. Maak op basis van de informatie over de aangepaste rol een aanvraag tekst met de volgende indeling:

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
          "/subscriptions/{subscriptionId}"
        ]
      }
    }
    ```

1. Werk de hoofd tekst van de aanvraag bij met de wijzigingen die u wilt aanbrengen in de aangepaste rol.

    Hieronder ziet u een voor beeld van een aanvraag tekst met een nieuwe actie voor Diagnostische instellingen toegevoegd:

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
          "/subscriptions/00000000-0000-0000-0000-000000000000"
        ]
      }
    }
    ```

## <a name="delete-a-custom-role"></a>Een aangepaste rol verwijderen

Als u een aangepaste rol wilt verwijderen, gebruikt u de [roldefinities-rest API verwijderen](/rest/api/authorization/roledefinitions/delete) . Als u deze API wilt aanroepen, moet u zijn aangemeld met een gebruiker waaraan een rol is toegewezen die de machtiging `Microsoft.Authorization/roleDefinitions/delete` heeft voor alle `assignableScopes`. Van de ingebouwde rollen, alleen de beheerder van de [eigenaar](built-in-roles.md#owner) en de [gebruikers toegang](built-in-roles.md#user-access-administrator) bevatten deze machtiging.

1. Gebruik de roldefinities [-lijst](/rest/api/authorization/roledefinitions/list) -of [roldefinities-Get](/rest/api/authorization/roledefinitions/get) rest API om de GUID-id van de aangepaste rol op te halen. Zie de sectie [met aangepaste rollen](#list-custom-roles) van de eerdere lijst voor meer informatie.

1. Begin met de volgende aanvraag:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. Vervang *{Scope}* in de URI door het bereik dat u wilt verwijderen van de aangepaste rol.

    | Bereik | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Abonnement |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Resourcegroep |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Resource |

1. Vervang *{roledefinitionid hebben}* door de GUID-id van de aangepaste rol.

## <a name="next-steps"></a>Volgende stappen

- [Aangepaste rollen voor Azure-resources](custom-roles.md)
- [Toegang tot Azure-resources beheren met RBAC en de REST API](role-assignments-rest.md)
- [Azure REST API-naslaginformatie](/rest/api/azure/)
