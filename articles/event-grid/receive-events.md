---
title: Gebeurtenissen ontvangen van Azure Event Grid naar een HTTP-eind punt
description: Hierin wordt beschreven hoe u een HTTP-eind punt valideert en vervolgens gebeurtenissen van Azure Event Grid ontvangt en deserialiseren
services: event-grid
author: banisadr
manager: darosa
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/01/2019
ms.author: babanisa
ms.openlocfilehash: cb38fd17c0c1bfbe3e5957d8f432f0a43b285c93
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/27/2020
ms.locfileid: "60803811"
---
# <a name="receive-events-to-an-http-endpoint"></a>Gebeurtenissen op een HTTP-eindpunt ontvangen

In dit artikel wordt beschreven hoe u [een HTTP-eind punt kunt valideren](security-authentication.md#webhook-event-delivery) om gebeurtenissen van een gebeurtenis abonnement te ontvangen en vervolgens gebeurtenissen te ontvangen en te deserialiseren. In dit artikel wordt een Azure-functie gebruikt voor demonstratie doeleinden, maar dezelfde concepten zijn echter van toepassing, ongeacht waar de toepassing wordt gehost.

> [!NOTE]
> Het wordt **ten zeerste** aanbevolen om een [Event grid trigger](../azure-functions/functions-bindings-event-grid.md) te gebruiken bij het activeren van een Azure-functie met Event grid. Het gebruik van een generieke webhook-trigger is hier aangetoond.

## <a name="prerequisites"></a>Vereisten

U hebt een functie-app met een door HTTP geactiveerde functie nodig.

## <a name="add-dependencies"></a>Afhankelijkheden toevoegen

Als u in .net ontwikkelt, [voegt u een afhankelijkheid](../azure-functions/functions-reference-csharp.md#referencing-custom-assemblies) toe aan uw functie `Microsoft.Azure.EventGrid` voor het [Nuget-pakket](https://www.nuget.org/packages/Microsoft.Azure.EventGrid). Voor de voor beelden in dit artikel is versie 1.4.0 of hoger vereist.

Sdk's voor andere talen zijn beschikbaar via de naslag informatie over het [publiceren van sdk's](./sdk-overview.md#data-plane-sdks) . Deze pakketten hebben de modellen voor systeem eigen gebeurtenis typen zoals `EventGridEvent`, `StorageBlobCreatedEventData`en `EventHubCaptureFileCreatedEventData`.

Klik op de koppeling ' bestanden weer geven ' in uw Azure function (rechter deel venster van de portal van Azure functions) en maak een bestand met de naam project. json. Voeg de volgende inhoud toe aan `project.json` het bestand en sla het op:

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

## <a name="endpoint-validation"></a>Eindpunt validatie

Het eerste wat u wilt doen, is het `Microsoft.EventGrid.SubscriptionValidationEvent` verwerken van gebeurtenissen. Telkens wanneer iemand zich abonneert op een gebeurtenis, stuurt Event Grid een validatie gebeurtenis naar het eind punt `validationCode` met een in de gegevens lading. Het eind punt is vereist om dit terug te echo's in de antwoord tekst, om aan te [tonen dat het eind punt geldig is en het eigendom van u is](security-authentication.md#webhook-event-delivery). Als u een [Event grid trigger](../azure-functions/functions-bindings-event-grid.md) gebruikt in plaats van een door een webhook geactiveerde functie, wordt de eindpunt validatie voor u afgehandeld. Als u een API-service van derden gebruikt (zoals [Zapier](https://zapier.com) of [IFTTT](https://ifttt.com/)), is het mogelijk dat u de validatie code niet programmatisch kunt echo. Voor die services kunt u het abonnement hand matig valideren met behulp van een validatie-URL die wordt verzonden in de validatie gebeurtenis van het abonnement. Kopieer de URL in de `validationUrl` eigenschap en verzend een GET-aanvraag via een rest-client of uw webbrowser.

In C# deserialiseren de `DeserializeEventGridEvents()` functie de Event grid-gebeurtenissen. Het deserialiseren van de gebeurtenis gegevens in het juiste type, zoals StorageBlobCreatedEventData. Gebruik de `Microsoft.Azure.EventGrid.EventTypes` -klasse om ondersteunde gebeurtenis typen en-namen op te halen.

Gebruik de volgende code om de validatie code programmatisch te echo's. U kunt verwante voor beelden vinden op [Event grid voor beeld](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/tree/master/EventGridConsumer)van de consument.

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

### <a name="test-validation-response"></a>Validatie antwoord testen

Test de validatie antwoord functie door de voorbeeld gebeurtenis te plakken in het veld test voor de functie:

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

Wanneer u op uitvoeren klikt, moet de uitvoer 200 OK en `{"ValidationResponse":"512d38b6-c7b8-40c8-89fe-f46f9e9622b6"}` in de hoofd tekst zijn:

![validatie antwoord](./media/receive-events/validation-response.png)

## <a name="handle-blob-storage-events"></a>Blob Storage-gebeurtenissen afhandelen

Nu gaan we de functie uitbreiden om te verwerken `Microsoft.Storage.BlobCreated`:

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

### <a name="test-blob-created-event-handling"></a>Door BLOB gemaakte gebeurtenis verwerking testen

Test de nieuwe functionaliteit van de functie door een [Blob Storage-gebeurtenis](./event-schema-blob-storage.md#example-event) in het veld test te plaatsen en uit te voeren:

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

U ziet de uitvoer van de BLOB-URL in het functie logboek:

![Uitvoer logboek](./media/receive-events/blob-event-response.png)

U kunt ook testen door een Blob Storage-account of Algemeen v2-opslag account (GPv2) te maken, een [gebeurtenis abonnement toe te voegen en](../storage/blobs/storage-blob-event-quickstart.md)het eind punt in te stellen op de functie-URL:

![Functie-URL](./media/receive-events/function-url.png)

## <a name="handle-custom-events"></a>Aangepaste gebeurtenissen verwerken

Tot slot kunt u de functie nog één keer uitbreiden zodat deze ook aangepaste gebeurtenissen kan verwerken. 

In C# ondersteunt de SDK de toewijzing van een gebeurtenis type naam aan het gegevens type gebeurtenis. Gebruik de `AddOrUpdateCustomEventMapping()` functie om de aangepaste gebeurtenis toe te wijzen.

Voeg een controle toe voor uw `Contoso.Items.ItemReceived`gebeurtenis. De uiteindelijke code moet er als volgt uitzien:

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

### <a name="test-custom-event-handling"></a>Verwerking van aangepaste gebeurtenis testen

Controleer ten slotte of uw functie uw aangepaste gebeurtenis type nu kan verwerken:

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

U kunt deze functionaliteit ook testen door [een aangepaste gebeurtenis te verzenden met een krul vanuit de portal](./custom-event-quickstart-portal.md) of door [te boeken naar een aangepast onderwerp](./post-to-custom-topic.md) met behulp van een service of toepassing die kan worden gepost naar een eind punt, zoals een [postman](https://www.getpostman.com/). Maak een aangepast onderwerp en een gebeurtenis abonnement met het eind punt dat is ingesteld als de functie-URL.

## <a name="next-steps"></a>Volgende stappen

* Verken het [Azure Event grid-beheer en publiceer sdk's](./sdk-overview.md)
* Meer informatie over het [plaatsen van berichten naar een aangepast onderwerp](./post-to-custom-topic.md)
* Probeer een van de diep gaande Event Grid en functions zelf studies, zoals het wijzigen van de grootte van afbeeldingen die zijn [geüpload naar de Blob-opslag](resize-images-on-storage-blob-upload-event.md)
