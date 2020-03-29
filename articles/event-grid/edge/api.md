---
title: REST API - IoT Edge van Azure Event Grid | Microsoft Documenten
description: REST API op Gebeurtenisraster op IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 19f86b1d8233e05844201e1095c1f79324955cd7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841826"
---
# <a name="rest-api"></a>REST API
In dit artikel worden de REST API's van Azure Event Grid op IoT Edge beschreven

## <a name="common-api-behavior"></a>Algemeen API-gedrag

### <a name="base-url"></a>Basis-URL
Event Grid on IoT Edge heeft de volgende API's die worden blootgesteld via HTTP (poort 5888) en HTTPS (poort 4438).

* Url baseren voor HTTP:http://eventgridmodule:5888
* URL baseren voor HTTPS:https://eventgridmodule:4438

### <a name="request-query-string"></a>Querytekenreeks aanvragen
Voor alle API-aanvragen is de volgende parameter querytekenreeks vereist:

```?api-version=2019-01-01-preview```

### <a name="request-content-type"></a>Inhoudstype aanvragen
Alle API-aanvragen moeten een **inhoudstype**hebben.

In het geval van **EventGridSchema** of **CustomSchema**kan de waarde van Content-Type een van de volgende waarden zijn:

```Content-Type: application/json```

```Content-Type: application/json; charset=utf-8```

In het geval van **CloudEventSchemaV1_0** in gestructureerde modus, kan de waarde van Content-Type een van de volgende waarden zijn:

```Content-Type: application/cloudevents+json```
    
```Content-Type: application/cloudevents+json; charset=utf-8```
    
```Content-Type: application/cloudevents-batch+json```
    
```Content-Type: application/cloudevents-batch+json; charset=utf-8```

In het geval van **CloudEventSchemaV1_0** in binaire modus, verwijzen naar [documentatie](https://github.com/cloudevents/spec/blob/master/http-protocol-binding.md) voor details.

### <a name="error-response"></a>Foutreactie
Alle API's geven een fout met de volgende payload:

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

### <a name="put-topic-create--update"></a>Put topic (maken / bijwerken)

**Verzoek**:``` PUT /topics/<topic_name>?api-version=2019-01-01-preview ```

**Laadvermogen**:

```json
    {
        "name": "<topic_name>", // optional, inferred from URL. If specified must match URL topic_name
        "properties":
        {
            "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0" // optional
        }
    }
```

**Reactie**: HTTP 200

**Laadvermogen**:

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

### <a name="get-topic"></a>Onderwerp bekijken

**Verzoek**:``` GET /topics/<topic_name>?api-version=2019-01-01-preview ```

**Reactie**: HTTP 200

**Laadvermogen**:
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

### <a name="get-all-topics"></a>Alle onderwerpen oppakken

**Verzoek**:``` GET /topics?api-version=2019-01-01-preview ```

**Reactie**: HTTP 200

**Laadvermogen**:
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

**Verzoek**:``` DELETE /topics/<topic_name>?api-version=2019-01-01-preview ```

**Reactie**: HTTP 200, leeg laadvermogen

## <a name="manage-event-subscriptions"></a>Gebeurtenisabonnementen beheren
Monsters in deze `EndpointType=Webhook;`sectie gebruiken . De json `EndpointType=EdgeHub / EndpointType=EventGrid` monsters voor zijn in de volgende sectie. 

### <a name="put-event-subscription-create--update"></a>Abonnement op evenementen plaatsen (maken / bijwerken)

**Verzoek**:``` PUT /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**Laadvermogen**:
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
        "persistencePolicy": "true",
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

**Reactie**: HTTP 200

**Laadvermogen**:

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


### <a name="get-event-subscription"></a>Abonnement op een evenement

**Verzoek**:``` GET /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**Reactie**: HTTP 200

**Laadvermogen**:
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

### <a name="get-event-subscriptions"></a>Abonnementen op evenementen

**Verzoek**:``` GET /topics/<topic_name>/eventSubscriptions?api-version=2019-01-01-preview ```

**Reactie**: HTTP 200

**Laadvermogen**:
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

### <a name="delete-event-subscription"></a>Gebeurtenisabonnement verwijderen

**Verzoek**:``` DELETE /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**Reactie**: HTTP 200, geen payload


## <a name="publish-events-api"></a>API voor gebeurtenissen publiceren

### <a name="send-batch-of-events-in-event-grid-schema"></a>Batch gebeurtenissen verzenden (in het schema van gebeurtenisraster)

**Verzoek**:``` POST /topics/<topic_name>/events?api-version=2019-01-01-preview ```

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

**Reactie**: HTTP 200, leeg laadvermogen


**Beschrijvingen van het payloadveld**
- ```Id```is verplicht. Het kan elke tekenreekswaarde zijn die wordt ingevuld door de beller. Event Grid doet geen dubbele detectie of dwingt geen semantiek af op dit veld.
- ```Topic```is optioneel, maar als opgegeven moet overeenkomen met de topic_name van de aanvraag-URL
- ```Subject```is verplicht, kan elke tekenreekswaarde zijn
- ```EventType```is verplicht, kan elke tekenreekswaarde zijn
- ```EventTime```is verplicht, het is niet gevalideerd, maar moet een goede DateTime.
- ```DataVersion```is verplicht
- ```MetadataVersion```is optioneel, indien gespecificeerd moet het een tekenreeks zijn met de waarde```"1"```
- ```Data```is optioneel en kan elk JSON-token zijn (getal, tekenreeks, booleaan, array, object)

### <a name="send-batch-of-events-in-custom-schema"></a>Batch gebeurtenissen verzenden (volgens het aangepaste schema)

**Verzoek**:``` POST /topics/<topic_name>/events?api-version=2019-01-01-preview ```

```json
[
    {
        ...
    }
]
```

**Reactie**: HTTP 200, leeg laadvermogen


**Payload-beperkingen**
- Moet een scala aan gebeurtenissen.
- Elke array-item moet een JSON-object zijn.
- Geen andere beperkingen (andere dan payload grootte).

## <a name="examples"></a>Voorbeelden

### <a name="set-up-topic-with-eventgrid-schema"></a>Onderwerp instellen met EventGrid-schema
Hiermee stelt u een onderwerp in om te vereisen dat gebeurtenissen worden gepubliceerd in **eventgridschema.**

```json
    {
        "name": "myeventgridtopic",
        "properties":
        {
            "inputSchema": "EventGridSchema"
        }
    }
```

### <a name="set-up-topic-with-custom-schema"></a>Onderwerp instellen met aangepast schema
Hiermee stelt u een onderwerp in `customschema`om te vereisen dat gebeurtenissen worden gepubliceerd in .

```json
    {
        "name": "mycustomschematopic",
        "properties":
        {
            "inputSchema": "CustomSchema"
        }
    }
```

### <a name="set-up-topic-with-cloud-event-schema"></a>Onderwerp instellen met cloudgebeurtenisschema
Hiermee stelt u een onderwerp in `cloudeventschema`om te vereisen dat gebeurtenissen worden gepubliceerd in .

```json
    {
        "name": "mycloudeventschematopic",
        "properties":
        {
            "inputSchema": "CloudEventSchemaV1_0"
        }
    }
```

### <a name="set-up-webhook-as-destination-events-to-be-delivered-in-eventgridschema"></a>WebHook instellen als bestemming, gebeurtenissen die moeten worden geleverd in eventgridschema
Gebruik dit doeltype om gebeurtenissen te verzenden naar een andere module (die een HTTP-eindpunt host) of naar een HTTP-adresseerbaar eindpunt op het netwerk/internet.

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

Beperkingen op `endpointUrl` het kenmerk:
- Het moet niet-null zijn.
- Het moet een absolute URL zijn.
- Als outbound__webhook__httpsOnly is ingesteld op true in de EventGridModule-instellingen, moet dit alleen HTTPS zijn.
- Als outbound__webhook__httpsOnly ingesteld op false, kan het HTTP of HTTPS zijn.

Beperkingen op `eventDeliverySchema` de eigenschap:
- Het moet overeenkomen met het invoerschema van het abonnementsonderwerp.
- Het kan null zijn. Het standaard is het invoerschema van het onderwerp.

### <a name="set-up-iot-edge-as-destination"></a>IoT Edge instellen als bestemming

Gebruik deze bestemming om gebeurtenissen naar IoT Edge Hub te verzenden en te worden onderworpen aan het subsysteem routering/filtering/forwarding van Edge Hub.

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

### <a name="set-up-event-grid-cloud-as-destination"></a>Gebeurtenisrasterwolk instellen als bestemming

Gebruik deze bestemming om gebeurtenissen naar gebeurtenisraster in de cloud (Azure) te verzenden. U moet eerst een gebruikersonderwerp instellen in de cloud waarnaar gebeurtenissen moeten worden verzonden, voordat u een gebeurtenisabonnement maakt.

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

EindpuntURL:
- Het moet niet-null zijn.
- Het moet een absolute URL zijn.
- Het `/api/events` pad moet worden gedefinieerd in het URL-pad van de aanvraag.
- Het moet `api-version=2018-01-01` in de querytekenreeks staan.
- Als outbound__eventgrid__httpsOnly is ingesteld op true in de EventGridModule-instellingen (standaard waar), moet dit alleen HTTPS zijn.
- Als outbound__eventgrid__httpsOnly is ingesteld op false, kan dit HTTP of HTTPS zijn.
- Als outbound__eventgrid__allowInvalidHostnames is ingesteld op false (standaard false), moet deze zich richten op een van de volgende eindpunten:
   - `eventgrid.azure.net`
   - `eventgrid.azure.us`
   - `eventgrid.azure.cn`

SasKey:
- Moet niet-null zijn.

TopicName:
- Als het Subscription.EventDeliverySchema is ingesteld op EventGridSchema, wordt de waarde van dit veld in het onderwerpveld van elke gebeurtenis geplaatst voordat deze wordt doorgestuurd naar Gebeurtenisraster in de cloud.
- Als het Subscription.EventDeliverySchema is ingesteld op CustomEventSchema, wordt deze eigenschap genegeerd en wordt de aangepaste gebeurtenispayload precies doorgestuurd zoals deze is ontvangen.

## <a name="set-up-event-hubs-as-a-destination"></a>Gebeurtenishubs instellen als bestemming

Als u wilt publiceren naar `endpointType` `eventHub` een gebeurtenishub, stelt u het in en geeft u het:

* connectionString: verbindingstekenreeks voor de specifieke gebeurtenishub die u target die wordt gegenereerd via een beleid voor gedeelde toegang.

    >[!NOTE]
    > De verbindingstekenreeks moet entiteitsspecifiek zijn. Het gebruik van een verbindingstekenreeks voor naamruimte werkt niet. U een entiteitsspecifieke verbindingstekenreeks genereren door te navigeren naar de specifieke gebeurtenishub die u wilt publiceren in de Azure Portal en te klikken op **Beleid voor gedeelde toegang** om een nieuwe entiteitsspecifieke connecection-tekenreeks te genereren.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventHub",
              "properties": {
                "connectionString": "<your-event-hub-connection-string>"
              }
            }
          }
        }
    ```

## <a name="set-up-service-bus-queues-as-a-destination"></a>Wachtrijen voor servicebussen instellen als bestemming

Als u wilt publiceren in `endpointType` een `serviceBusQueue` servicebuswachtrij, stelt u het instellen en op bieden:

* connectionString: verbindingstekenreeks voor de specifieke servicebuswachtrij die u target die wordt gegenereerd via een beleid voor gedeelde toegang.

    >[!NOTE]
    > De verbindingstekenreeks moet entiteitsspecifiek zijn. Het gebruik van een verbindingstekenreeks voor naamruimte werkt niet. Genereer een entiteitsspecifieke verbindingstekenreeks door te navigeren naar de specifieke servicebuswachtrij die u wilt publiceren in de Azure Portal en te klikken op **Beleid voor gedeelde toegang** om een nieuwe entiteitsspecifieke connecection-tekenreeks te genereren.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusQueue",
              "properties": {
                "connectionString": "<your-service-bus-queue-connection-string>"
              }
            }
          }
        }
    ```

## <a name="set-up-service-bus-topics-as-a-destination"></a>Onderwerpen voor servicebussen instellen als bestemming

Als u wilt publiceren naar `endpointType` een `serviceBusTopic` servicebusonderwerp, stelt u het aan en de informatie in:

* connectionString: verbindingstekenreeks voor het specifieke servicebusonderwerp dat u target, gegenereerd via een beleid voor gedeelde toegang.

    >[!NOTE]
    > De verbindingstekenreeks moet entiteitsspecifiek zijn. Het gebruik van een verbindingstekenreeks voor naamruimte werkt niet. Genereer een entiteitsspecifieke verbindingstekenreeks door te navigeren naar het specifieke servicebusonderwerp dat u wilt publiceren in de Azure Portal en te klikken op **Beleid voor gedeelde toegang** om een nieuwe entiteitsspecifieke connecection-tekenreeks te genereren.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusTopic",
              "properties": {
                "connectionString": "<your-service-bus-topic-connection-string>"
              }
            }
          }
        }
    ```

## <a name="set-up-storage-queues-as-a-destination"></a>Opslagwachtrijen instellen als bestemming

Als u wilt publiceren in `endpointType` `storageQueue` een opslagwachtrij, stelt u het in en geeft u het:

* queueName: naam van de opslagwachtrij die u publiceert.
* verbindingTekenreeks: verbindingstekenreeks voor het opslagaccount waarin de opslagwachtrij zich bevindt.

    >[!NOTE]
    > De verbindingstekenreeks die wordt gebruikt voor opslagwachtrijen is niet entiteitsspecifiek. In plaats daarvan moet het alleen de verbindingstekenreeks voor het opslagaccount.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "storageQueue",
              "properties": {
                "queueName": "<your-storage-queue-name>",
                "connectionString": "<your-storage-account-connection-string>"
              }
            }
          }
        }
    ```