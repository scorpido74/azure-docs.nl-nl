---
title: Azure Event Grid-abonnementsschema
description: In dit artikel worden de eigenschappen beschreven voor het abonneren op een gebeurtenis met Azure Event Grid. Schema van Event Grid abonnement.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: reference
ms.date: 01/23/2020
ms.author: babanisa
ms.openlocfilehash: 4bb04d22b762f31a02515549b698030a5267e4cd
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720755"
---
# <a name="event-grid-subscription-schema"></a>Event Grid-abonnementsschema

Voor het maken van een Event Grid-abonnement, kunt u een aanvraag verzendt naar de abonnement-bewerking gebeurtenis maken. Gebruik de volgende indeling:

```HTTP
PUT /subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/{resource-provider}/{resource-type}/{resource-name}/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

Bijvoorbeeld, om een gebeurtenisabonnement voor een opslagaccount te maken met de naam `examplestorage` in een resourcegroep met de naam `examplegroup`, gebruik de volgende indeling:

```HTTP
PUT /subscriptions/{subscription-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageaccounts/examplestorage/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

Naam van het abonnement van de gebeurtenis moet 3 tot 64 tekens lang zijn en mag alleen a-z, A-Z, 0-9 en '-'. Het artikel beschrijft de eigenschappen en het schema voor de hoofdtekst van de aanvraag.
 
## <a name="event-subscription-properties"></a>Eigenschappen van gebeurtenis-abonnement

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| destination | object | Het object dat het eindpunt definieert. |
| filter | object | Een optioneel veld voor het filteren van de typen gebeurtenissen. |

### <a name="destination-object"></a>doelobject

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| endpointType | string | Het type van het eindpunt voor het abonnement (webhook/HTTP, Event Hub of wachtrij). | 
| endpointUrl | string | De doel-URL voor gebeurtenissen in dit gebeurtenisabonnement. | 

### <a name="filter-object"></a>Filter-object

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| includedEventTypes | matrix | Overeenkomst wanneer het gebeurtenistype bericht in de gebeurtenis is een exacte overeenkomst voor een van de namen van deze gebeurtenis. Wordt een fout gegenereerd als de naam van gebeurtenis komt niet overeen met de namen van de geregistreerde gebeurtenis type voor de gebeurtenisbron. Standaard komt overeen met alle gebeurtenistypen. |
| subjectBeginsWith | string | Een voorvoegselovereenkomst filteren op het onderwerpveld in de gebeurtenis bericht. De standaard- of lege tekenreeks komt overeen met alle. | 
| subjectEndsWith | string | Een achtervoegsel-match filteren op het onderwerpveld in de gebeurtenis bericht. De standaard- of lege tekenreeks komt overeen met alle. |
| isSubjectCaseSensitive | string | Hoofdlettergevoelige overeenkomende filters voor besturingselementen. |


## <a name="example-subscription-schema"></a>Voorbeeld abonnementsschema

```json
{
  "properties": {
    "destination": {
      "endpointType": "webhook",
      "properties": {
          "endpointUrl": "https://example.azurewebsites.net/api/HttpTriggerCSharp1?code=VXbGWce53l48Mt8wuotr0GPmyJ/nDT4hgdFj9DpBiRt38qqnnm5OFg=="
      }
    },
    "filter": {
      "includedEventTypes": [ "Microsoft.Storage.BlobCreated", "Microsoft.Storage.BlobDeleted" ],
      "subjectBeginsWith": "/blobServices/default/containers/mycontainer/log",
      "subjectEndsWith": ".jpg",
      "isSubjectCaseSensitive ": "true"
    }
  }
}
```

## <a name="next-steps"></a>Volgende stappen

* Zie voor een inleiding tot Event Grid, [wat is Event Grid?](overview.md)
