---
title: Gebeurtenis plaatsen in het aangepaste Azure Event Grid-onderwerp
description: In dit artikel wordt beschreven hoe u een gebeurtenis plaatsen in een aangepast onderwerp. Het toont de indeling van de post- en gebeurtenisgegevens.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 0afad249f71a36bf7552da499e985b68d48ee7a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721552"
---
# <a name="post-to-custom-topic-for-azure-event-grid"></a>Berichten plaatsen in aangepast onderwerp voor Azure Event Grid

In dit artikel wordt beschreven hoe u een gebeurtenis plaatsen in een aangepast onderwerp. Het toont de indeling van de post- en gebeurtenisgegevens. De [SLA (Service Level Agreement)](https://azure.microsoft.com/support/legal/sla/event-grid/v1_0/) is alleen van toepassing op berichten die overeenkomen met de verwachte indeling.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="endpoint"></a>Eindpunt

Gebruik de URI-indeling bij het verzenden van `https://<topic-endpoint>?api-version=2018-01-01`de HTTP-post naar een aangepast onderwerp: .

Een geldige URI is `https://exampletopic.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01`bijvoorbeeld: .

Gebruik het als tabblad Voor een aangepast onderwerp met Azure CLI:

```azurecli-interactive
az eventgrid topic show --name <topic-name> -g <topic-resource-group> --query "endpoint"
```

Gebruik het voorbeeld voor een aangepast onderwerp met Azure PowerShell om het eindpunt voor een aangepast onderwerp met Azure PowerShell te gebruiken:

```powershell
(Get-AzEventGridTopic -ResourceGroupName <topic-resource-group> -Name <topic-name>).Endpoint
```

## <a name="header"></a>Header

Voeg in het verzoek een `aeg-sas-key` kopwaarde met de naam toe die een sleutel voor verificatie bevat.

Een geldige kopwaarde is `aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==`bijvoorbeeld .

Gebruik het belangrijkste voor een aangepast onderwerp met Azure CLI:

```azurecli
az eventgrid topic key list --name <topic-name> -g <topic-resource-group> --query "key1"
```

Gebruik het belangrijkste voor een aangepast onderwerp met PowerShell:

```powershell
(Get-AzEventGridTopicKey -ResourceGroupName <topic-resource-group> -Name <topic-name>).Key1
```

## <a name="event-data"></a>Gebeurtenisgegevens

Voor aangepaste onderwerpen bevatten de gegevens op het hoogste niveau dezelfde velden als standaardgebeurtenissen die door resources zijn gedefinieerd. Een van deze eigenschappen is een eigenschap met gegevens die eigenschappen bevat die uniek zijn voor het aangepaste onderwerp. Als gebeurtenis-uitgever bepaalt u de eigenschappen voor dat gegevensobject. Gebruik het volgende schema:

```json
[
  {
    "id": string,    
    "eventType": string,
    "subject": string,
    "eventTime": string-in-date-time-format,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string
  }
]
```

Zie [Azure Event Grid-gebeurtenisschema](event-schema.md)voor een beschrijving van deze eigenschappen . Wanneer u gebeurtenissen plaatst in een onderwerp van het gebeurtenisraster, kan de array een totale grootte van maximaal 1 MB hebben. Elke gebeurtenis in de array is beperkt tot 64 KB (Algemene beschikbaarheid) of 1 MB (voorbeeld).

> [!NOTE]
> Een gebeurtenis van grootte tot 64 KB wordt gedekt door de ALGEMENE Beschikbaarheid (GA) Service Level Agreement (SLA). De ondersteuning voor een gebeurtenis van maximaal 1 MB is momenteel in preview. Gebeurtenissen van meer dan 64 KB worden in stappen van 64 KB in rekening gebracht. 

Een geldig gebeurtenisgegevensschema is bijvoorbeeld:

```json
[{
  "id": "1807",
  "eventType": "recordInserted",
  "subject": "myapp/vehicles/motorcycles",
  "eventTime": "2017-08-10T21:03:07+00:00",
  "data": {
    "make": "Ducati",
    "model": "Monster"
  },
  "dataVersion": "1.0"
}]
```

## <a name="response"></a>Antwoord

Nadat je naar het eindpunt van het onderwerp bent geplaatst, ontvang je een reactie. Het antwoord is een standaard HTTP-antwoordcode. Enkele veelvoorkomende reacties zijn:

|Resultaat  |Antwoord  |
|---------|---------|
|Geslaagd  | 200 OK  |
|Gebeurtenisgegevens hebben een onjuiste indeling | 400 Slecht verzoek |
|Ongeldige toegangssleutel | 401 Ongeautoriseerd |
|Onjuist eindpunt | 404 Niet gevonden |
|Array of gebeurtenis overschrijdt groottelimieten | 413 Payload te groot |

Voor fouten heeft de berichttekst de volgende indeling:

```json
{
    "error": {
        "code": "<HTTP status code>",
        "message": "<description>",
        "details": [{
            "code": "<HTTP status code>",
            "message": "<description>"
    }]
  }
}
```

## <a name="next-steps"></a>Volgende stappen

* Zie [Gebeurtenisnetbezorging controleren](monitor-event-delivery.md)voor informatie over het bewaken van gebeurtenisleveringen.
* Zie [Beveiliging en verificatie van gebeurtenisraster](security-authentication.md)voor meer informatie over de verificatiesleutel.
* Zie [Abonnement op gebeurtenisrastervoor](subscription-creation-schema.md)meer informatie over het maken van een Azure Event Grid-abonnement .
