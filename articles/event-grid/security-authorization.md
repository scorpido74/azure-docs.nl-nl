---
title: Beveiliging en verificatie van Azure Event Grid
description: Beschrijving van Azure Event Grid en de concepten ervan.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: babanisa
ms.openlocfilehash: 03bc2f9de6f50f08c9f62f86a3d1791a067cecd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78899280"
---
# <a name="authorizing-access-to-event-grid-resources"></a>Toegang tot bronnen voor gebeurtenisrasters toestaan
Met Azure Event Grid u het toegangsniveau beheren dat aan verschillende gebruikers wordt gegeven om verschillende beheerbewerkingen uit te voeren, zoals gebeurtenisabonnementen op de lijst, nieuwe abonnementen te maken en sleutels te genereren. Event Grid maakt gebruik van Azure's role-based access control (RBAC).

## <a name="operation-types"></a>Bewerkingstypen

Event Grid ondersteunt de volgende acties:

* Microsoft.EventGrid/*/gelezen
* Microsoft.EventGrid/*/write
* Microsoft.EventGrid/*/delete
* Microsoft.EventGrid/eventAbonnementen/getFullUrl/actie
* Microsoft.EventGrid/topics/listKeys/action
* Microsoft.EventGrid/topics/regenerateKey/action

De laatste drie bewerkingen leveren mogelijk geheime informatie op, die wordt gefilterd uit normale leesbewerkingen. Het wordt aanbevolen om de toegang tot deze bewerkingen te beperken. 

## <a name="built-in-roles"></a>Ingebouwde rollen

Event Grid biedt twee ingebouwde rollen voor het beheren van gebeurtenisabonnementen. Ze zijn belangrijk bij het implementeren van [gebeurtenisdomeinen](event-domains.md) omdat ze gebruikers de machtigingen geven die ze nodig hebben om zich te abonneren op onderwerpen in uw gebeurtenisdomein. Deze rollen zijn gericht op gebeurtenisabonnementen en verlenen geen toegang voor acties zoals het maken van onderwerpen.

U [deze rollen toewijzen aan een gebruiker of groep.](../role-based-access-control/quickstart-assign-role-user-portal.md)

**EventGrid EventSubscription Contributor**: event grid-abonnementsbewerkingen beheren

```json
[
  {
    "Description": "Lets you manage EventGrid event subscription operations.",
    "IsBuiltIn": true,
    "Id": "428e0ff05e574d9ca2212c70d0e0a443",
    "Name": "EventGrid EventSubscription Contributor",
    "IsServiceRole": false,
    "Permissions": [
      {
        "Actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.EventGrid/eventSubscriptions/*",
          "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Resources/deployments/*",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Support/*"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": [],
        "Condition": null
      }
    ],
    "Scopes": [
      "/"
    ]
  }
]
```

**EventGrid EventSubscription Reader**: lees Event Grid abonnementen

```json
[
  {
    "Description": "Lets you read EventGrid event subscriptions.",
    "IsBuiltIn": true,
    "Id": "2414bbcf64974faf8c65045460748405",
    "Name": "EventGrid EventSubscription Reader",
    "IsServiceRole": false,
    "Permissions": [
      {
        "Actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.EventGrid/eventSubscriptions/read",
          "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": []
       }
    ],
    "Scopes": [
      "/"
    ]
  }
]
```

## <a name="custom-roles"></a>Aangepaste rollen

Als u machtigingen moet opgeven die anders zijn dan de ingebouwde rollen, u aangepaste rollen maken.

Hieronder volgen voorbeelddefinities van gebeurtenisrasterrollen waarmee gebruikers verschillende acties kunnen uitvoeren. Deze aangepaste rollen verschillen van de ingebouwde rollen omdat ze bredere toegang bieden dan alleen gebeurtenisabonnementen.

**EventGridReadOnlyRole.json**: Alleen-lezen bewerkingen toestaan.

```json
{
  "Name": "Event grid read only role",
  "Id": "7C0B6B59-A278-4B62-BA19-411B70753856",
  "IsCustom": true,
  "Description": "Event grid read only role",
  "Actions": [
    "Microsoft.EventGrid/*/read"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<Subscription Id>"
  ]
}
```

**EventGridNoDeleteListKeysRole.json**: Sta acties voor beperkte berichten toe, maar verontgelaten verwijderingsacties.

```json
{
  "Name": "Event grid No Delete Listkeys role",
  "Id": "B9170838-5F9D-4103-A1DE-60496F7C9174",
  "IsCustom": true,
  "Description": "Event grid No Delete Listkeys role",
  "Actions": [
    "Microsoft.EventGrid/*/write",
    "Microsoft.EventGrid/eventSubscriptions/getFullUrl/action"
    "Microsoft.EventGrid/topics/listkeys/action",
    "Microsoft.EventGrid/topics/regenerateKey/action"
  ],
  "NotActions": [
    "Microsoft.EventGrid/*/delete"
  ],
  "AssignableScopes": [
    "/subscriptions/<Subscription id>"
  ]
}
```

**EventGridContributorRole.json**: Hiermee kunnen alle gebeurtenisrasteracties worden uitgevoerd.

```json
{
  "Name": "Event grid contributor role",
  "Id": "4BA6FB33-2955-491B-A74F-53C9126C9514",
  "IsCustom": true,
  "Description": "Event grid contributor role",
  "Actions": [
    "Microsoft.EventGrid/*/write",
    "Microsoft.EventGrid/*/delete",
    "Microsoft.EventGrid/topics/listkeys/action",
    "Microsoft.EventGrid/topics/regenerateKey/action",
    "Microsoft.EventGrid/eventSubscriptions/getFullUrl/action"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/<Subscription id>"
  ]
}
```

U aangepaste rollen maken met [PowerShell,](../role-based-access-control/custom-roles-powershell.md) [Azure CLI](../role-based-access-control/custom-roles-cli.md)en [REST.](../role-based-access-control/custom-roles-rest.md)



### <a name="encryption-at-rest"></a>Versleuteling 'at rest'

Alle gebeurtenissen of gegevens die door de Event Grid-service naar de schijf zijn geschreven, worden versleuteld door een door Microsoft beheerde sleutel die ervoor zorgt dat deze in rust wordt versleuteld. Bovendien is de maximale periode die gebeurtenissen of gegevens bewaren 24 uur in overeenstemming met het [beleid voor opnieuw proberen van het gebeurtenisraster](delivery-and-retry.md). Event Grid verwijdert automatisch alle gebeurtenissen of gegevens na 24 uur, of de gebeurtenis time-to-live, wat minder is.

## <a name="next-steps"></a>Volgende stappen

* Zie [Over gebeurtenisraster](overview.md) voor een inleiding tot gebeurtenisraster
