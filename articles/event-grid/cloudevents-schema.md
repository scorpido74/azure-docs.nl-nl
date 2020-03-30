---
title: Azure-gebeurtenisraster gebruiken met gebeurtenissen in het CloudEvents-schema
description: Beschrijft hoe u het CloudEvents-schema gebruiken voor gebeurtenissen in Azure Event Grid. De service ondersteunt evenementen bij de JSON-implementatie van Cloud Events.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: babanisa
ms.openlocfilehash: 0efccd2851885dad209d5548a76737c25777b891
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80372452"
---
# <a name="use-cloudevents-v10-schema-with-event-grid"></a>CloudEvents v1.0-schema gebruiken met gebeurtenisraster

Naast het [standaardgebeurtenisschema](event-schema.md)ondersteunt Azure Event Grid native gebeurtenissen in de [JSON-implementatie van CloudEvents v1.0-](https://github.com/cloudevents/spec/blob/v1.0/json-format.md) en [HTTP-protocolbinding](https://github.com/cloudevents/spec/blob/v1.0/http-protocol-binding.md). [CloudEvents](https://cloudevents.io/) is een [open specificatie](https://github.com/cloudevents/spec/blob/v1.0/spec.md) voor het beschrijven van gebeurtenisgegevens.

CloudEvents vereenvoudigt de interoperabiliteit door een algemeen gebeurtenisschema te bieden voor het publiceren en consumeren van cloudgebaseerde gebeurtenissen. Dit schema maakt uniforme tooling, standaardmanieren van routering & het afhandelen van gebeurtenissen en universele manieren om het externe gebeurtenisschema te deserialiseren. Met een gemeenschappelijk schema u werk gemakkelijker integreren op verschillende platforms.

CloudEvents wordt gebouwd door verschillende [medewerkers](https://github.com/cloudevents/spec/blob/master/community/contributors.md), waaronder Microsoft, via de [Cloud Native Computing Foundation](https://www.cncf.io/). Het is momenteel beschikbaar als versie 1.0.

In dit artikel wordt beschreven hoe u het cloudgebeurtenissenschema gebruiken met gebeurtenisraster.

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

## <a name="install-preview-feature"></a>Preview-functie installeren

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="cloudevent-schema"></a>CloudEvent-schema

Hier is een voorbeeld van een Azure Blob Storage-gebeurtenis in de CloudEvents-indeling:

``` JSON
{
    "specversion": "1.0",
    "type": "Microsoft.Storage.BlobCreated",  
    "source": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-account}",
    "id": "9aeb0fdf-c01e-0131-0922-9eb54906e209",
    "time": "2019-11-18T15:13:39.4589254Z",
    "subject": "blobServices/default/containers/{storage-container}/blobs/{new-file}",
    "dataschema": "#",
    "data": {
        "api": "PutBlockList",
        "clientRequestId": "4c5dd7fb-2c48-4a27-bb30-5361b5de920a",
        "requestId": "9aeb0fdf-c01e-0131-0922-9eb549000000",
        "eTag": "0x8D76C39E4407333",
        "contentType": "image/png",
        "contentLength": 30699,
        "blobType": "BlockBlob",
        "url": "https://gridtesting.blob.core.windows.net/testcontainer/{new-file}",
        "sequencer": "000000000000000000000000000099240000000000c41c18",
        "storageDiagnostics": {
            "batchId": "681fe319-3006-00a8-0022-9e7cde000000"
        }
    }
}
```

Een gedetailleerde beschrijving van de beschikbare velden, hun typen en definities in CloudEvents v1.0 is [hier beschikbaar.](https://github.com/cloudevents/spec/blob/v1.0/spec.md#required-attributes)

De koptekstwaarden voor gebeurtenissen die worden geleverd in het cloudgebeurtenissenschema en het schema gebeurtenisraster zijn hetzelfde, behalve voor `content-type`. Voor het cloudgebeurtenissenschema is `"content-type":"application/cloudevents+json; charset=utf-8"`die kopwaarde . Voor het schema Gebeurtenisraster `"content-type":"application/json; charset=utf-8"`is die kopwaarde .

## <a name="configure-event-grid-for-cloudevents"></a>Gebeurtenisraster configureren voor CloudEvents

U Gebeurtenisraster gebruiken voor zowel invoer als uitvoer van gebeurtenissen in het CloudEvents-schema. U CloudEvents gebruiken voor systeemgebeurtenissen, zoals Blob Storage-gebeurtenissen en IoT Hub-gebeurtenissen en aangepaste gebeurtenissen. Het kan ook transformeren die gebeurtenissen op de draad heen en weer.


| Invoerschema       | Uitvoerschema
|--------------------|---------------------
| CloudEvents-indeling | CloudEvents-indeling
| Gebeurtenisrasternotatie  | CloudEvents-indeling
| Gebeurtenisrasternotatie  | Gebeurtenisrasternotatie

Voor alle gebeurtenisschema's vereist gebeurtenisraster validatie bij het publiceren naar een gebeurtenisrasteronderwerp en bij het maken van een gebeurtenisabonnement. Zie [Beveiliging en verificatie van gebeurtenisraster](security-authentication.md)voor meer informatie.

### <a name="input-schema"></a>Invoerschema

U stelt het invoerschema in voor een aangepast onderwerp wanneer u het aangepaste onderwerp maakt.

Gebruik voor Azure CLI:

```azurecli-interactive
# If you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid topic create \
  --name <topic_name> \
  -l westcentralus \
  -g gridResourceGroup \
  --input-schema cloudeventschemav1_0
```

Gebruik voor PowerShell:

```azurepowershell-interactive
# If you have not already installed the module, do it now.
# This module is required for preview features.
Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery

New-AzureRmEventGridTopic `
  -ResourceGroupName gridResourceGroup `
  -Location westcentralus `
  -Name <topic_name> `
  -InputSchema CloudEventSchemaV1_0
```

### <a name="output-schema"></a>Uitvoerschema

U stelt het uitvoerschema in wanneer u het gebeurtenisabonnement maakt.

Gebruik voor Azure CLI:

```azurecli-interactive
topicID=$(az eventgrid topic show --name <topic-name> -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --name <event_subscription_name> \
  --source-resource-id $topicID \
  --endpoint <endpoint_URL> \
  --event-delivery-schema cloudeventschemav1_0
```

Gebruik voor PowerShell:
```azurepowershell-interactive
$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Name <topic-name>).Id

New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -DeliverySchema CloudEventSchemaV1_0
```

 Momenteel u geen trigger voor gebeurtenisrastergebruiken voor een Azure Functions-app wanneer de gebeurtenis wordt geleverd in het CloudEvents-schema. Gebruik een HTTP-trigger. Zie CloudEvents gebruiken [met Azure-functies](#azure-functions)voor voorbeelden van het implementeren van een HTTP-trigger die gebeurtenissen ontvangt in het CloudEvents-schema.

 ## <a name="endpoint-validation-with-cloudevents-v10"></a>Eindpuntvalidatie met CloudEvents v1.0

Als u al bekend bent met Gebeurtenisraster, bent u mogelijk op de hoogte van de handdruk van event grid voor het voorkomen van misbruik. CloudEvents v1.0 implementeert zijn eigen [misbruikbescherming semantiek](security-authentication.md#webhook-event-delivery) met behulp van de HTTP OPTIONS-methode. [Hier](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection) vindt u meer informatie. Wanneer u het CloudEvents-schema gebruikt voor uitvoer, gebruikt Gebeurtenisraster met de cloudevents v1.0-misbruikbeveiliging in plaats van het gebeurtenismechanisme voor gebeurtenisrastergebeurtenissen.

<a name="azure-functions"></a>

## <a name="use-with-azure-functions"></a>Gebruiken met Azure-functies

De [binding van het Azure Functions Event Grid](../azure-functions/functions-bindings-event-grid.md) biedt geen ondersteuning voor CloudEvents, dus http-geactiveerde functies worden gebruikt om CloudEvents-berichten te lezen. Wanneer u een HTTP-trigger gebruikt om CloudEvents te lezen, moet u code schrijven voor wat de trigger van eventgrid automatisch doet:

* Hiermee verzendt u een validatieantwoord op een [abonnementsvalidatieaanvraag](../event-grid/security-authentication.md#webhook-event-delivery).
* Roept de functie eenmaal per element van de gebeurtenisarray in de aanvraaginstantie.

Zie de [HTTP-triggerbindingsdocumentatie](../azure-functions/functions-bindings-http-webhook.md) voor informatie over de URL die moet worden gebruikt voor het lokaal inroepen van de functie of wanneer deze wordt uitgevoerd in Azure.

De volgende voorbeeldc#-code voor een HTTP-trigger simuleert gebeurtenisrastertriggergedrag.  Gebruik dit voorbeeld voor gebeurtenissen die worden geleverd in het CloudEvents-schema.

```csharp
[FunctionName("HttpTrigger")]
public static async Task<HttpResponseMessage> Run([HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", "options", Route = null)]HttpRequestMessage req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");
    if (req.Method == "OPTIONS")
    {
        // If the request is for subscription validation, send back the validation code
        
        var response = req.CreateResponse(HttpStatusCode.OK);
        response.Add("Webhook-Allowed-Origin", "eventgrid.azure.net");

        return response;
    }

    var requestmessage = await req.Content.ReadAsStringAsync();
    var message = JToken.Parse(requestmessage);

    // The request is not for subscription validation, so it's for an event.
    // CloudEvents schema delivers one event at a time.
    log.LogInformation($"Source: {message["source"]}");
    log.LogInformation($"Time: {message["eventTime"]}");
    log.LogInformation($"Event data: {message["data"].ToString()}");

    return req.CreateResponse(HttpStatusCode.OK);
}
```

De volgende voorbeeld JavaScript-code voor een HTTP-trigger simuleert gebeurtenisrastertriggergedrag. Gebruik dit voorbeeld voor gebeurtenissen die worden geleverd in het CloudEvents-schema.

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');
    
    if (req.method == "OPTIONS) {
        // If the request is for subscription validation, send back the validation code
        
        context.log('Validate request received');
        context.res = { status: 200, body: { "ValidationResponse": code } };
        context.res.headers.append('Webhook-Allowed-Origin', 'eventgrid.azure.net');
    }
    else
    {
        var message = req.body;
        
        // The request is not for subscription validation, so it's for an event.
        // CloudEvents schema delivers one event at a time.
        var event = JSON.parse(message);
        context.log('Source: ' + event.source);
        context.log('Time: ' + event.eventTime);
        context.log('Data: ' + JSON.stringify(event.data));
    }
 
    context.done();
};
```

## <a name="next-steps"></a>Volgende stappen

* Zie [Gebeurtenisnetbezorging controleren](monitor-event-delivery.md)voor informatie over het bewaken van gebeurtenisleveringen.
* We raden je aan om cloudevents te testen, te becommentariëpen en bij te [dragen aan](https://github.com/cloudevents/spec/blob/master/CONTRIBUTING.md) CloudEvents.
* Zie [Abonnement op gebeurtenisrastervoor](subscription-creation-schema.md)meer informatie over het maken van een Azure Event Grid-abonnement .
