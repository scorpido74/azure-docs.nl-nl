---
title: Azure Event Grid-abonnementsschema
description: In dit artikel worden de eigenschappen beschreven om u te abonneren op een gebeurtenis met Azure Event Grid. Abonnementschema gebeurtenisraster.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: reference
ms.date: 01/23/2020
ms.author: babanisa
ms.openlocfilehash: 4bb04d22b762f31a02515549b698030a5267e4cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720755"
---
# <a name="event-grid-subscription-schema"></a>Abonnementprogramma gebeurtenisraster

Als u een abonnement op eventgrid wilt maken, stuurt u een verzoek naar de abonnementsbewerking Gebeurtenis maken. Gebruik de volgende indeling:

```HTTP
PUT /subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/{resource-provider}/{resource-type}/{resource-name}/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

Als u bijvoorbeeld een gebeurtenisabonnement wilt `examplestorage` maken voor een `examplegroup`opslagaccount met de naam resourcegroep met de naam , gebruikt u de volgende indeling:

```HTTP
PUT /subscriptions/{subscription-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageaccounts/examplestorage/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

De naam van het gebeurtenisabonnement moet 3-64 tekens lang zijn en mag alleen a-z, A-Z, 0-9 en '-' bevatten. Het artikel beschrijft de eigenschappen en het schema voor de hoofdtekst van de aanvraag.
 
## <a name="event-subscription-properties"></a>Eigenschappen van gebeurtenisabonnement

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| doel | object | Het object dat het eindpunt definieert. |
| filter | object | Een optioneel veld voor het filteren van de typen gebeurtenissen. |

### <a name="destination-object"></a>doelobject

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| eindpuntType | tekenreeks | Het type eindpunt voor het abonnement (webhook/HTTP, Event Hub of wachtrij). | 
| endpointUrl | tekenreeks | De bestemmings-URL voor gebeurtenissen in dit gebeurtenisabonnement. | 

### <a name="filter-object"></a>filterobject

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| includedEventTypes | matrix | Overeenkomen wanneer het gebeurtenistype in het gebeurtenisbericht exact overeenkomt met een van deze gebeurtenisnamen. Er wordt een fout gegenereerd wanneer de naam van de gebeurtenis niet overeenkomt met de geregistreerde gebeurtenisnamen voor de gebeurtenisbron. Standaard komt overeen met alle gebeurtenistypen. |
| onderwerpBeginsMet | tekenreeks | Een voorvoegsel-matchfilter naar het onderwerpveld in het gebeurtenisbericht. De standaard- of lege tekenreeks komt overeen met alle tekenreeksen. | 
| subjectEndsMet | tekenreeks | Een achtervoegsel-matchfilter naar het onderwerpveld in het gebeurtenisbericht. De standaard- of lege tekenreeks komt overeen met alle tekenreeksen. |
| isSubjectCaseSensitive | tekenreeks | Hiermee regelt u casegevoelige matching voor filters. |


## <a name="example-subscription-schema"></a>Voorbeeldabonnementsschema

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

* Zie Wat is Event Grid voor een inleiding tot Event [Grid?](overview.md)
