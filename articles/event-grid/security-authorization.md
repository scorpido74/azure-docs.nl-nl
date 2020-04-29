---
title: Beveiliging en verificatie Azure Event Grid
description: Beschrijving van Azure Event Grid en de concepten ervan.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: babanisa
ms.openlocfilehash: 03bc2f9de6f50f08c9f62f86a3d1791a067cecd0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78899280"
---
# <a name="authorizing-access-to-event-grid-resources"></a>Toegang tot Event Grid-resources autoriseren
Met Azure Event Grid kunt u het toegangs niveau dat aan verschillende gebruikers wordt gegeven, beheren om verschillende beheer bewerkingen uit te voeren, zoals abonnementen op lijst gebeurtenissen, nieuwe maken en sleutels genereren. Event Grid maakt gebruik van het op rollen gebaseerd toegangs beheer (RBAC) van Azure.

## <a name="operation-types"></a>Bewerkings typen

Event Grid ondersteunt de volgende acties:

* Micro soft. EventGrid/*/Read
* Micro soft. EventGrid/*/write
* Micro soft. EventGrid/*/Delete
* Micro soft. EventGrid/eventSubscriptions/getFullUrl/Action
* Micro soft. EventGrid/topics/Listkeys ophalen/Action
* Micro soft. EventGrid/topics/regenerateKey/Action

Met de laatste drie bewerkingen wordt mogelijk geheime informatie geretourneerd, waardoor de normale Lees bewerkingen worden gefilterd. Het is raadzaam de toegang tot deze bewerkingen te beperken. 

## <a name="built-in-roles"></a>Ingebouwde rollen

Event Grid biedt twee ingebouwde rollen voor het beheren van gebeurtenis abonnementen. Ze zijn belang rijk bij het implementeren van [gebeurtenis domeinen](event-domains.md) omdat ze gebruikers de machtigingen geven die ze nodig hebben om zich te abonneren op onderwerpen in uw gebeurtenis domein. Deze rollen zijn gericht op gebeurtenis abonnementen en verlenen geen toegang voor acties zoals het maken van onderwerpen.

U kunt [deze rollen toewijzen aan een gebruiker of groep](../role-based-access-control/quickstart-assign-role-user-portal.md).

**EventGrid EventSubscription Inzender**: Event grid-abonnements bewerkingen beheren

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

**EventGrid EventSubscription-lezer**: Event grid-abonnementen lezen

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

Als u machtigingen wilt opgeven die afwijken van de ingebouwde rollen, kunt u aangepaste rollen maken.

Hier volgen enkele voor beelden van Event Grid roldefinities waarmee gebruikers verschillende acties kunnen uitvoeren. Deze aangepaste rollen verschillen van de ingebouwde rollen omdat ze bredere toegang verlenen dan alleen gebeurtenis abonnementen.

**EventGridReadOnlyRole. json**: alleen alleen-lezen bewerkingen toestaan.

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

**EventGridNoDeleteListKeysRole. json**: beperkte post acties toestaan, maar geen verwijderings acties toestaan.

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

**EventGridContributorRole. json**: alle gebeurtenis raster acties toestaan.

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

U kunt aangepaste rollen maken met [Power shell](../role-based-access-control/custom-roles-powershell.md), [Azure cli](../role-based-access-control/custom-roles-cli.md)en [rest](../role-based-access-control/custom-roles-rest.md).



### <a name="encryption-at-rest"></a>Versleuteling 'at rest'

Alle gebeurtenissen of gegevens die door de Event Grid-Service naar schijf worden geschreven, worden versleuteld met een door micro soft beheerde sleutel, zodat deze op rest versleuteld is. Bovendien is de maximale tijds duur dat gebeurtenissen of gegevens bewaard 24 uur in acht te komen in de [Event grid beleid voor opnieuw proberen](delivery-and-retry.md). Event Grid verwijdert automatisch alle gebeurtenissen of gegevens na 24 uur, of de TTL van de gebeurtenis, afhankelijk van wat kleiner is.

## <a name="next-steps"></a>Volgende stappen

* Zie [About Event grid](overview.md) voor een inleiding tot Event grid.
