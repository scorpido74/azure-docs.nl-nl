---
title: Gebeurtenissen ontvangen van Azure Event Grid naar een HTTP-eindpunt
description: Beschrijft hoe u een HTTP-eindpunt valideren en gebeurtenissen vervolgens ontvangen en deserialiseren vanuit Azure Event Grid
services: event-grid
author: banisadr
manager: darosa
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/01/2019
ms.author: babanisa
ms.openlocfilehash: cb38fd17c0c1bfbe3e5957d8f432f0a43b285c93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60803811"
---
# <a name="receive-events-to-an-http-endpoint"></a>Gebeurtenissen op een HTTP-eindpunt ontvangen

In dit artikel wordt beschreven hoe [u een HTTP-eindpunt valideren](security-authentication.md#webhook-event-delivery) om gebeurtenissen van een gebeurtenisabonnement te ontvangen en vervolgens gebeurtenissen te ontvangen en te deserialiseren. In dit artikel wordt een Azure-functie gebruikt voor demonstratiedoeleinden, maar dezelfde concepten zijn van toepassing, ongeacht waar de toepassing wordt gehost.

> [!NOTE]
> Het wordt **ten zeerste** aanbevolen dat u een [Gebeurtenisrastertrigger](../azure-functions/functions-bindings-event-grid.md) gebruikt bij het activeren van een Azure-functie met gebeurtenisraster. Het gebruik van een generieke WebHook trigger hier is demonstratief.

## <a name="prerequisites"></a>Vereisten

U hebt een functie-app nodig met een HTTP-geactiveerde functie.

## <a name="add-dependencies"></a>Afhankelijkheden toevoegen

Als u zich ontwikkelt in .NET, voegt u `Microsoft.Azure.EventGrid` een afhankelijkheid [toe](../azure-functions/functions-reference-csharp.md#referencing-custom-assemblies) aan uw functie voor het [Nuget-pakket.](https://www.nuget.org/packages/Microsoft.Azure.EventGrid) De voorbeelden in dit artikel vereisen versie 1.4.0 of hoger.

SDK's voor andere talen zijn beschikbaar via de [referentie Voor Het publiceren van SDKs.](./sdk-overview.md#data-plane-sdks) Deze pakketten hebben de modellen voor `EventGridEvent` `StorageBlobCreatedEventData`native `EventHubCaptureFileCreatedEventData`event types zoals , en .

Klik op de koppeling Bestanden weergeven in uw Azure-functie (het meeste deelvenster in de Azure-functiesportal) en maak een bestand genaamd project.json. Voeg de volgende `project.json` inhoud toe aan het bestand en sla deze op:

 ```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "Microsoft.Azure.EventGrid": "2.0.0"
      }
    }
   }
}
```

![NuGet-pakket toegevoegd](./media/receive-events/add-dependencies.png)

## <a name="endpoint-validation"></a>Validatie van eindpunten

Het eerste wat je wilt `Microsoft.EventGrid.SubscriptionValidationEvent` doen is omgaan met gebeurtenissen. Elke keer dat iemand zich abonneert op een gebeurtenis, stuurt `validationCode` Event Grid een validatiegebeurtenis naar het eindpunt met een in de gegevenspayload. Het eindpunt is vereist om dit terug te echoën in de reactie-instantie om te [bewijzen dat het eindpunt geldig is en eigendom is van u.](security-authentication.md#webhook-event-delivery) Als u een [gebeurtenisrastertrigger](../azure-functions/functions-bindings-event-grid.md) gebruikt in plaats van een WebHook-geactiveerde functie, wordt de validatie van eindpunten voor u uitgevoerd. Als u een API-service van derden gebruikt (zoals [Zapier](https://zapier.com) of [IFTTT),](https://ifttt.com/)u de validatiecode mogelijk niet programmatisch herhalen. Voor deze services u het abonnement handmatig valideren met behulp van een validatie-URL die wordt verzonden in de gebeurtenis voor abonnementsvalidatie. Kopieer die URL `validationUrl` in de eigenschap en stuur een GET-aanvraag via een REST-client of uw webbrowser.

In C#, `DeserializeEventGridEvents()` deserialiseert de functie de gebeurtenissen van het gebeurtenisraster. De gebeurtenisgegevens worden gedeserialiseert naar het juiste type, zoals StorageBlobCreatedEventData. Gebruik `Microsoft.Azure.EventGrid.EventTypes` de klasse om ondersteunde gebeurtenistypen en namen te krijgen.

Als u de validatiecode programmatisch wilt herhalen, gebruikt u de volgende code. U gerelateerde voorbeelden vinden bij [event grid consumer voorbeeld](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/tree/master/EventGridConsumer).

```csharp
using System.Net;
using Newtonsoft.Json;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.EventGrid;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"C# HTTP trigger function begun");
    string response = string.Empty;

    string requestContent = await req.Content.ReadAsStringAsync();
    log.Info($"Received events: {requestContent}");

    EventGridSubscriber eventGridSubscriber = new EventGridSubscriber();

    EventGridEvent[] eventGridEvents = eventGridSubscriber.DeserializeEventGridEvents(requestContent);

    foreach (EventGridEvent eventGridEvent in eventGridEvents)
    {
        if (eventGridEvent.Data is SubscriptionValidationEventData)
        {
            var eventData = (SubscriptionValidationEventData)eventGridEvent.Data;
            log.Info($"Got SubscriptionValidation event data, validation code: {eventData.ValidationCode}, topic: {eventGridEvent.Topic}");
            // Do any additional validation (as required) and then return back the below response

            var responseData = new SubscriptionValidationResponse()
            {
                ValidationResponse = eventData.ValidationCode
            };

            return req.CreateResponse(HttpStatusCode.OK, responseData);
        }
    }

    return req.CreateResponse(HttpStatusCode.OK, response);
}
```

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function begun');
    var validationEventType = "Microsoft.EventGrid.SubscriptionValidationEvent";

    for (var events in req.body) {
        var body = req.body[events];
        // Deserialize the event data into the appropriate type based on event type
        if (body.data && body.eventType == validationEventType) {
            context.log("Got SubscriptionValidation event data, validation code: " + body.data.validationCode + " topic: " + body.topic);

            // Do any additional validation (as required) and then return back the below response
            var code = body.data.validationCode;
            context.res = { status: 200, body: { "ValidationResponse": code } };
        }
    }
    context.done();
};
```

### <a name="test-validation-response"></a>Reactie van testvalidatie

Test de functie validatierespons door de voorbeeldgebeurtenis in het testveld voor de functie te plakken:

```json
[{
  "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "subject": "",
  "data": {
    "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
  },
  "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
  "eventTime": "2018-01-25T22:12:19.4556811Z",
  "metadataVersion": "1",
  "dataVersion": "1"
}]
```

Wanneer u op Uitvoeren klikt, moet de `{"ValidationResponse":"512d38b6-c7b8-40c8-89fe-f46f9e9622b6"}` uitvoer 200 OK en in de hoofdtekst zijn:

![validatierespons](./media/receive-events/validation-response.png)

## <a name="handle-blob-storage-events"></a>Blob-opslaggebeurtenissen verwerken

Laten we nu de functie `Microsoft.Storage.BlobCreated`uitbreiden om te verwerken:

```cs
using System.Net;
using Newtonsoft.Json;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.EventGrid;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"C# HTTP trigger function begun");
    string response = string.Empty;

    string requestContent = await req.Content.ReadAsStringAsync();
    log.Info($"Received events: {requestContent}");

    EventGridSubscriber eventGridSubscriber = new EventGridSubscriber();

    EventGridEvent[] eventGridEvents = eventGridSubscriber.DeserializeEventGridEvents(requestContent);

    foreach (EventGridEvent eventGridEvent in eventGridEvents)
    {
        if (eventGridEvent.Data is SubscriptionValidationEventData)
        {
            var eventData = (SubscriptionValidationEventData)eventGridEvent.Data;
            log.Info($"Got SubscriptionValidation event data, validation code: {eventData.ValidationCode}, topic: {eventGridEvent.Topic}");
            // Do any additional validation (as required) and then return back the below response

            var responseData = new SubscriptionValidationResponse()
            {
                ValidationResponse = eventData.ValidationCode
            };

            return req.CreateResponse(HttpStatusCode.OK, responseData);
        }
        else if (eventGridEvent.Data is StorageBlobCreatedEventData)
        {
            var eventData = (StorageBlobCreatedEventData)eventGridEvent.Data;
            log.Info($"Got BlobCreated event data, blob URI {eventData.Url}");
        }
    }

    return req.CreateResponse(HttpStatusCode.OK, response);
}
```

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function begun');
    var validationEventType = "Microsoft.EventGrid.SubscriptionValidationEvent";
    var storageBlobCreatedEvent = "Microsoft.Storage.BlobCreated";

    for (var events in req.body) {
        var body = req.body[events];
        // Deserialize the event data into the appropriate type based on event type  
        if (body.data && body.eventType == validationEventType) {
            context.log("Got SubscriptionValidation event data, validation code: " + body.data.validationCode + " topic: " + body.topic);

            // Do any additional validation (as required) and then return back the below response
            var code = body.data.validationCode;
            context.res = { status: 200, body: { "ValidationResponse": code } };
        }

        else if (body.data && body.eventType == storageBlobCreatedEvent) {
            var blobCreatedEventData = body.data;
            context.log("Relaying received blob created event payload:" + JSON.stringify(blobCreatedEventData));
        }
    }
    context.done();
};

```

### <a name="test-blob-created-event-handling"></a>Gebeurtenisafhandeling van Blob gemaakt testen

Test de nieuwe functionaliteit van de functie door een [Blob-opslaggebeurtenis](./event-schema-blob-storage.md#example-event) in het testveld te plaatsen en uit te voeren:

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
  "subject": "/blobServices/default/containers/testcontainer/blobs/testfile.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "0x8D4BCC2E4835CD0",
    "contentType": "text/plain",
    "contentLength": 524288,
    "blobType": "BlockBlob",
    "url": "https://example.blob.core.windows.net/testcontainer/testfile.txt",
    "sequencer": "00000000000004420000000000028963",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

U ziet de URL-uitvoer van blobs in het functielogboek:

![Uitvoerlogboek](./media/receive-events/blob-event-response.png)

U ook testen door een Klodder-opslagaccount of GPv2-opslagaccount (General Purpose V2) te maken, [een abonnement op toevoegen en gebeurtenissen in](../storage/blobs/storage-blob-event-quickstart.md)te stellen en het eindpunt in te stellen op de functie-URL:

![Functie-URL](./media/receive-events/function-url.png)

## <a name="handle-custom-events"></a>Aangepaste gebeurtenissen afhandelen

Tot slot, laat de functie nogmaals uitbreiden, zodat het ook aangepaste gebeurtenissen kan verwerken. 

In C#ondersteunt de SDK het toewijzen van een gebeurtenistypenaam aan het gebeurtenisgegevenstype. Gebruik `AddOrUpdateCustomEventMapping()` de functie om de aangepaste gebeurtenis in kaart te brengen.

Voeg een cheque `Contoso.Items.ItemReceived`voor uw evenement toe. Uw uiteindelijke code moet eruit zien als:

```cs
using System.Net;
using Newtonsoft.Json;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.EventGrid;

class ContosoItemReceivedEventData
{
    [JsonProperty(PropertyName = "itemSku")]
    public string ItemSku { get; set; }
}

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"C# HTTP trigger function begun");
    string response = string.Empty;

    string requestContent = await req.Content.ReadAsStringAsync();
    log.Info($"Received events: {requestContent}");

    EventGridSubscriber eventGridSubscriber = new EventGridSubscriber();
    eventGridSubscriber.AddOrUpdateCustomEventMapping("Contoso.Items.ItemReceived", typeof(ContosoItemReceivedEventData));
    EventGridEvent[] eventGridEvents = eventGridSubscriber.DeserializeEventGridEvents(requestContent);

    foreach (EventGridEvent eventGridEvent in eventGridEvents)
    {
        if (eventGridEvent.Data is SubscriptionValidationEventData)
        {
            var eventData = (SubscriptionValidationEventData)eventGridEvent.Data;
            log.Info($"Got SubscriptionValidation event data, validation code: {eventData.ValidationCode}, topic: {eventGridEvent.Topic}");
            // Do any additional validation (as required) and then return back the below response

            var responseData = new SubscriptionValidationResponse()
            {
                ValidationResponse = eventData.ValidationCode
            };

            return req.CreateResponse(HttpStatusCode.OK, responseData);
        }
        else if (eventGridEvent.Data is StorageBlobCreatedEventData)
        {
            var eventData = (StorageBlobCreatedEventData)eventGridEvent.Data;
            log.Info($"Got BlobCreated event data, blob URI {eventData.Url}");
        }
        else if (eventGridEvent.Data is ContosoItemReceivedEventData)
        {
            var eventData = (ContosoItemReceivedEventData)eventGridEvent.Data;
            log.Info($"Got ContosoItemReceived event data, item SKU {eventData.ItemSku}");
        }
    }

    return req.CreateResponse(HttpStatusCode.OK, response);
}
```

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function begun');
    var validationEventType = "Microsoft.EventGrid.SubscriptionValidationEvent";
    var storageBlobCreatedEvent = "Microsoft.Storage.BlobCreated";
    var customEventType = "Contoso.Items.ItemReceived";

    for (var events in req.body) {
        var body = req.body[events];
        // Deserialize the event data into the appropriate type based on event type
        if (body.data && body.eventType == validationEventType) {
            context.log("Got SubscriptionValidation event data, validation code: " + body.data.validationCode + " topic: " + body.topic);

            // Do any additional validation (as required) and then return back the below response
            var code = body.data.validationCode;
            context.res = { status: 200, body: { "ValidationResponse": code } };
        }

        else if (body.data && body.eventType == storageBlobCreatedEvent) {
            var blobCreatedEventData = body.data;
            context.log("Relaying received blob created event payload:" + JSON.stringify(blobCreatedEventData));
        }

        else if (body.data && body.eventType == customEventType) {
            var payload = body.data;
            context.log("Relaying received custom payload:" + JSON.stringify(payload));
        }
    }
    context.done();
};
```

### <a name="test-custom-event-handling"></a>Aangepaste gebeurtenisafhandeling testen

Test ten slotte of uw functie nu uw aangepaste gebeurtenistype kan verwerken:

```json
[{
    "subject": "Contoso/foo/bar/items",
    "eventType": "Contoso.Items.ItemReceived",
    "eventTime": "2017-08-16T01:57:26.005121Z",
    "id": "602a88ef-0001-00e6-1233-1646070610ea",
    "data": { 
            "itemSku": "Standard"
            },
    "dataVersion": "",
    "metadataVersion": "1"
}]
```

U deze functionaliteit ook live testen door [een aangepaste gebeurtenis met CURL vanuit de Portal](./custom-event-quickstart-portal.md) te verzenden of door een aangepast onderwerp te [posten](./post-to-custom-topic.md) met behulp van een service of toepassing die kan posten naar een eindpunt zoals [Postman.](https://www.getpostman.com/) Maak een aangepast onderwerp en een gebeurtenisabonnement met het eindpunt als de functie-URL.

## <a name="next-steps"></a>Volgende stappen

* Ontdek de [Azure Event Grid Management en Publiceer SDKs](./sdk-overview.md)
* Meer informatie over het [plaatsen van een aangepast onderwerp](./post-to-custom-topic.md)
* Probeer een van de diepgaande gebeurtenisrasters en functies, zoals [het formaat van afbeeldingen wijzigen die zijn geüpload naar blob-opslag](resize-images-on-storage-blob-upload-event.md)
