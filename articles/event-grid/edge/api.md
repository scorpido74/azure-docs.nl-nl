---
title: REST API-Azure Event Grid IoT Edge | Microsoft Docs
description: REST API op Event Grid in IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: ee2b3a35b6f1817b89541a31d0bde4adf00ade2a
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992533"
---
# <a name="rest-api"></a>REST-API
In dit artikel worden de REST-Api's van Azure Event Grid op IoT Edge beschreven

## <a name="common-api-behavior"></a>Normaal API-gedrag

### <a name="base-url"></a>Basis-URL
Event Grid op IoT Edge heeft de volgende Api's die worden weer gegeven via HTTP (poort 5888) en HTTPS (poort 4438).

* Basis-URL voor HTTP: http://eventgridmodule:5888
* Basis-URL voor HTTPS: https://eventgridmodule:4438

### <a name="request-query-string"></a>Query reeks voor aanvraag
Voor alle API-aanvragen is de volgende query teken reeks parameter vereist:

```?api-version=2019-01-01-preview```

### <a name="request-content-type"></a>Type aanvraag inhoud
Alle API-aanvragen moeten een **inhouds type**hebben.

In het geval van **EventGridSchema** of **CustomSchema**kan de waarde van het inhouds type een van de volgende waarden hebben:

```Content-Type: application/json```

```Content-Type: application/json; charset=utf-8```

In het geval van **CloudEventSchemaV1_0** in de gestructureerde modus kan de waarde van het inhouds type een van de volgende waarden hebben:

```Content-Type: application/cloudevents+json```
    
```Content-Type: application/cloudevents+json; charset=utf-8```
    
```Content-Type: application/cloudevents-batch+json```
    
```Content-Type: application/cloudevents-batch+json; charset=utf-8```

In het geval van **CloudEventSchemaV1_0** in binaire modus raadpleegt u de [documentatie](https://github.com/cloudevents/spec/blob/master/http-protocol-binding.md) voor meer informatie.

### <a name="error-response"></a>Fout bericht
Alle Api's retour neren een fout met de volgende Payload:

```json
{
    "error":
    {
        "code": "<HTTP STATUS CODE>",
        "details": 
        {
            "code": "<Detailed Error Code>",
            "message": "..."
        }
    }
}
```

## <a name="manage-topics"></a>Onderwerpen beheren

### <a name="put-topic-create--update"></a>Onderwerp plaatsen (maken/bijwerken)

**Aanvraag**: ``` PUT /topics/<topic_name>?api-version=2019-01-01-preview ```

**Payload**:

```json
    {
        "name": "<topic_name>", // optional, inferred from URL. If specified must match URL topic_name
        "properties":
        {
            "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0" // optional
        }
    }
```

**Antwoord**: http 200

**Payload**:

```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
    "name": "<topic_name>",
    "type": "Microsoft.EventGrid/topics",
    "properties":
    {
        "endpoint": "<get_request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
        "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0" // populated with EventGridSchema if not explicitly specified in PUT request
    }
}
```

### <a name="get-topic"></a>Onderwerp ophalen

**Aanvraag**: ``` GET /topics/<topic_name>?api-version=2019-01-01-preview ```

**Antwoord**: http 200

**Payload**:
```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
    "name": "<topic_name>",
    "type": "Microsoft.EventGrid/topics",
    "properties":
    {
        "endpoint": "<request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
        "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0"
    }
}
```

### <a name="get-all-topics"></a>Alle onderwerpen ophalen

**Aanvraag**: ``` GET /topics?api-version=2019-01-01-preview ```

**Antwoord**: http 200

**Payload**:
```json
[
    {
        "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
        "name": "<topic_name>",
        "type": "Microsoft.EventGrid/topics",
        "properties":
        {
            "endpoint": "<request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
            "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0"
        }
    },
    {
        "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
        "name": "<topic_name>",
        "type": "Microsoft.EventGrid/topics",
        "properties":
        {
            "endpoint": "<request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
            "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0"
        }
    }
]
```

### <a name="delete-topic"></a>Onderwerp verwijderen

**Aanvraag**: ``` DELETE /topics/<topic_name>?api-version=2019-01-01-preview ```

**Antwoord**: http 200, lege nettolading

## <a name="manage-event-subscriptions"></a>Gebeurtenis abonnementen beheren
Voor beelden in deze sectie gebruiken `EndpointType=Webhook;`. De JSON-voor beelden voor `EndpointType=EdgeHub / EndpointType=EventGrid` bevinden zich in de volgende sectie. 

### <a name="put-event-subscription-create--update"></a>Gebeurtenis abonnement plaatsen (maken/bijwerken)

**Aanvraag**: ``` PUT /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**Payload**:
```json
{
    "name": "<subscription_name>", // optional, inferred from URL. If specified must match URL subscription_name
    "properties":
    {
        "topicName": "<topic_name>", // optional, inferred from URL. If specified must match URL topic_name
        "eventDeliverySchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0", // optional
        "retryPolicy":  //optional
        {
            "eventExpiryInMinutes": 120,
            "maxDeliveryAttempts": 50
        },
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "maxEventsPerBatch": 10, // optional
                "preferredBatchSizeInKilobytes": 1033 // optional
            }
        },
        "filter": // optional
        {
            "subjectBeginsWith": "...",
            "subjectEndsWith": "...",
            "isSubjectCaseSensitive": true|false,
            "includedEventTypes": ["...", "..."],
            "advancedFilters":
            [
                {
                    "OperatorType": "BoolEquals",
                    "Key": "...",
                    "Value": "..."
                },
                {
                    "OperatorType": "NumberLessThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberLessThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "NumberNotIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "StringIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringNotIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringBeginsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringEndsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringContains",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                }
            ]
        }
    }
}
```

**Antwoord**: http 200

**Payload**:

```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>/eventSubscriptions/<subscription_name>",
    "name": "<subscription_name>",
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "properties":
    {
        "topicName": "<topic_name>",
        "eventDeliverySchema": "EventGridSchema | CustomEventSchema  | CloudEventSchemaV1_0", // populated with EventGridSchema if not explicitly specified in PUT request
        "retryPolicy":  // only populated if specified in the PUT request
        {
            "eventExpiryInMinutes": 120,
            "maxDeliveryAttempts": 50
        },
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "maxEventsPerBatch": 10, // optional
                "preferredBatchSizeInKilobytes": 1033 // optional
            }
        },
        "filter": // only populated if specified in the PUT request 
        {
            "subjectBeginsWith": "...",
            "subjectEndsWith": "...",
            "isSubjectCaseSensitive": true|false,
            "includedEventTypes": ["...", "..."],
            "advancedFilters":
            [
                {
                    "OperatorType": "BoolEquals",
                    "Key": "...",
                    "Value": "..."
                },
                {
                    "OperatorType": "NumberLessThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberLessThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "NumberNotIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "StringIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringNotIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringBeginsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringEndsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringContains",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                }
            ]
        }
    }
}
```


### <a name="get-event-subscription"></a>Gebeurtenis abonnement ophalen

**Aanvraag**: ``` GET /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**Antwoord**: http 200

**Payload**:
```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>/eventSubscriptions/<subscription_name>",
    "name": "<subscription_name>",
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "properties":
    {
        "topicName": "<topic_name>",
        "eventDeliverySchema": "EventGridSchema | CustomEventSchema  | CloudEventSchemaV1_0", // populated with EventGridSchema if not explicitly specified in PUT request
        "retryPolicy":  // only populated if specified in the PUT request
        {
            "eventExpiryInMinutes": 120,
            "maxDeliveryAttempts": 50
        },
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "maxEventsPerBatch": 10, // optional
                "preferredBatchSizeInKilobytes": 1033 // optional
            }
        },
        "filter": // only populated if specified in the PUT request 
        {
            "subjectBeginsWith": "...",
            "subjectEndsWith": "...",
            "isSubjectCaseSensitive": true|false,
            "includedEventTypes": ["...", "..."],
            "advancedFilters":
            [
                {
                    "OperatorType": "BoolEquals",
                    "Key": "...",
                    "Value": "..."
                },
                {
                    "OperatorType": "NumberLessThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberLessThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "NumberNotIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "StringIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringNotIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringBeginsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringEndsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringContains",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                }
            ]
        }
    }
}
```

### <a name="get-event-subscriptions"></a>Gebeurtenis abonnementen ophalen

**Aanvraag**: ``` GET /topics/<topic_name>/eventSubscriptions?api-version=2019-01-01-preview ```

**Antwoord**: http 200

**Payload**:
```json
[
    {
        // same event-subscription json as that returned from Get-EventSubscription above
    },
    {
    },
    ...
]
```

### <a name="delete-event-subscription"></a>Gebeurtenis abonnement verwijderen

**Aanvraag**: ``` DELETE /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**Antwoord**: http 200, geen nettolading


## <a name="publish-events-api"></a>API voor publiceren van gebeurtenissen

### <a name="send-batch-of-events-in-event-grid-schema"></a>Batch gebeurtenissen verzenden (in Event Grid schema)

**Aanvraag**: ``` POST /topics/<topic_name>/events?api-version=2019-01-01-preview ```

```json
[
    {
        "id": "<user-defined-event-id>",
        "topic": "<topic_name>",
        "subject": "",
        "eventType": "",
        "eventTime": ""
        "dataVersion": "",
        "metadataVersion": "1",
        "data": 
            ...
    }
]
```

**Antwoord**: http 200, lege nettolading


**Beschrijvingen van lading veld**
- ```Id``` is verplicht. Dit kan een wille keurige teken reeks waarde zijn die wordt ingevuld door de aanroeper. Event Grid voert geen duplicaten detectie uit of dwingt geen semantiek af van dit veld.
- ```Topic``` is optioneel, maar indien opgegeven moet overeenkomen met de topic_name van de aanvraag-URL
- ```Subject``` is verplicht, kan een wille keurige teken reeks waarde zijn
- ```EventType``` is verplicht, kan een wille keurige teken reeks waarde zijn
- ```EventTime``` is verplicht, wordt niet gevalideerd, maar moet een juiste datum/tijd zijn.
- ```DataVersion``` is verplicht
- ```MetadataVersion``` is optioneel, indien opgegeven, moet een teken reeks zijn met de waarde ```"1"```
- ```Data``` is optioneel en kan elk JSON-token (getal, teken reeks, Booleaanse waarde, matrix, object) zijn

### <a name="send-batch-of-events-in-custom-schema"></a>Batch gebeurtenissen verzenden (in aangepast schema)

**Aanvraag**: ``` POST /topics/<topic_name>/events?api-version=2019-01-01-preview ```

```json
[
    {
        ...
    }
]
```

**Antwoord**: http 200, lege nettolading


**Beperkingen voor nettolading**
- MOET een matrix met gebeurtenissen zijn.
- Elke matrix vermelding moet een JSON-object zijn.
- Geen andere beperkingen (met uitzonde ring van Payload-grootte).

## <a name="examples"></a>Voorbeelden

### <a name="set-up-topic-with-eventgrid-schema"></a>Onderwerp instellen met EventGrid-schema
Hiermee stelt u een onderwerp in voor het vereisen van gebeurtenissen die moeten worden gepubliceerd in **eventgridschema**.

```json
    {
        "name": "myeventgridtopic",
        "properties":
        {
            "inputSchema": "EventGridSchema"
        }
    }
```

### <a name="set-up-topic-with-custom-schema"></a>Onderwerp met aangepast schema instellen
Hiermee stelt u een onderwerp in waarmee gebeurtenissen moeten worden gepubliceerd in `customschema`.

```json
    {
        "name": "mycustomschematopic",
        "properties":
        {
            "inputSchema": "CustomSchema"
        }
    }
```

### <a name="set-up-topic-with-cloud-event-schema"></a>Onderwerp instellen met Cloud-gebeurtenis schema
Hiermee stelt u een onderwerp in waarmee gebeurtenissen moeten worden gepubliceerd in `cloudeventschema`.

```json
    {
        "name": "mycloudeventschematopic",
        "properties":
        {
            "inputSchema": "CloudEventSchemaV1_0"
        }
    }
```

### <a name="set-up-webhook-as-destination-events-to-be-delivered-in-eventgridschema"></a>Webhook als doel instellen, gebeurtenissen die moeten worden geleverd in eventgridschema
Gebruik dit doel type om gebeurtenissen te verzenden naar een andere module (die als host fungeert voor een HTTP-eind punt) of naar een HTTP-eind punt op het netwerk/Internet.

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "eventDeliverySchema": "eventgridschema",
            }
        }
    }
}
```

Beperkingen voor het kenmerk `endpointUrl`:
- De waarde mag niet null zijn.
- Dit moet een absolute URL zijn.
- Als outbound__webhook__httpsOnly is ingesteld op True in de EventGridModule-instellingen, moet dit alleen HTTPS zijn.
- Als outbound__webhook__httpsOnly is ingesteld op False, kan dit HTTP of HTTPS zijn.

Beperkingen voor de eigenschap `eventDeliverySchema`:
- Het moet overeenkomen met het invoer schema van het geabonneerde onderwerp.
- Dit kan null zijn. De standaard instelling is het invoer schema van het onderwerp.

### <a name="set-up-iot-edge-as-destination"></a>IoT Edge instellen als bestemming

Gebruik deze bestemming om gebeurtenissen te verzenden naar IoT Edge hub en te worden blootgesteld aan het subsysteem Routing/filtering/forwarding van Edge hub.

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "EdgeHub",
            "properties":
            {
                "outputName": "<eventgridmodule_output_port_name>"
            }
        }
    }
}
```

### <a name="set-up-event-grid-cloud-as-destination"></a>Event Grid Cloud instellen als bestemming

Gebruik deze bestemming voor het verzenden van gebeurtenissen naar Event Grid in de Cloud (Azure). Voordat u een gebeurtenis abonnement op de rand maakt, moet u eerst een gebruikers onderwerp instellen in de Cloud waarnaar gebeurtenissen moeten worden verzonden.

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "EventGrid",
            "properties":
            {
                "endpointUrl": "<eventgrid_user_topic_url>",
                "sasKey": "<user_topic_sas_key>",
                "topicName": "<new value to populate in forwarded EventGridEvent.Topic>" // if not specified, the Topic field on every event gets nulled out before being sent to Azure Event Grid
            }
        }
    }
}
```

EndpointUrl
- De waarde mag niet null zijn.
- Dit moet een absolute URL zijn.
- Het pad `/api/events` moet worden gedefinieerd in het pad van de aanvraag-URL.
- Deze moet `api-version=2018-01-01` hebben in de query reeks.
- Als outbound__eventgrid__httpsOnly is ingesteld op True in de EventGridModule-instellingen (standaard instelling waar), moet dit alleen HTTPS zijn.
- Als outbound__eventgrid__httpsOnly is ingesteld op False, kan dit HTTP of HTTPS zijn.
- Als outbound__eventgrid__allowInvalidHostnames is ingesteld op False (standaard instelling ONWAAR), moet dit een van de volgende eind punten zijn:
   - `eventgrid.azure.net`
   - `eventgrid.azure.us`
   - `eventgrid.azure.cn`

SasKey:
- Mag niet null zijn.

Onderwerpnaam:
- Als het abonnement. EventDeliverySchema is ingesteld op EventGridSchema, wordt de waarde uit dit veld in het veld onderwerp van elke gebeurtenis geplaatst voordat deze wordt doorgestuurd naar Event Grid in de Cloud.
- Als het abonnement. EventDeliverySchema is ingesteld op CustomEventSchema, wordt deze eigenschap genegeerd en wordt de nettolading van de aangepaste gebeurtenis exact doorgestuurd zoals deze is ontvangen.
