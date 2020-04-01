---
title: Blob-opslag gebruiken als controlepuntopslag in Azure Stack Hub (voorbeeld)
description: In dit artikel wordt beschreven hoe u Blob Storage gebruiken als controlepuntopslag in gebeurtenishubs op Azure Stack Hub (voorbeeld).
services: event-hubs
documentationcenter: na
author: spelluru
ms.service: event-hubs
ms.topic: how-to
ms.date: 03/18/2020
ms.author: spelluru
ms.openlocfilehash: 1f0e4dea44007ef82cb4b700ff0be4a5579541d8
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398920"
---
# <a name="use-blob-storage-as-checkpoint-store---event-hubs-on-azure-stack-hub-preview"></a>Blob Storage gebruiken als checkpointstore - Gebeurtenishubs op Azure Stack Hub (voorbeeld)
Als u Azure Blob Storage gebruikt als controlepuntopslag in een omgeving die een andere versie van Storage Blob SDK ondersteunt dan de omgeving die doorgaans beschikbaar is op Azure, moet u code gebruiken om de API-versie van de opslagservice te wijzigen in de specifieke versie die door die omgeving wordt ondersteund. Als u bijvoorbeeld Gebeurtenishubs uitvoert [op een Azure Stack Hub-versie 2002,](https://docs.microsoft.com/azure-stack/user/event-hubs-overview)is versie 2017-11-09 de hoogst beschikbare versie voor de opslagservice. In dit geval moet u code gebruiken om de API-versie van de opslagservice te targeten op 2017-11-09. Zie deze voorbeelden op GitHub voor een voorbeeld over het targeten van een specifieke Storage API-versie: 

- [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample10_RunningWithDifferentStorageVersion.cs)
- [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorWithOlderStorageVersion.java). 
- [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/receiveEventsWithDownleveledStorage.js) of [TypeScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/receiveEventsWithDownleveledStorage.ts), 
- Python - [Synchroon](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob/samples/event_processor_blob_storage_example_with_storage_api_version.py), [Asynchroon](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/event_processor_blob_storage_example_with_storage_api_version.py)

> [!IMPORTANT]
> Gebeurtenishubs op Azure Stack Hub is momenteel in [preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) en is gratis. 

Als u gebeurtenishubs-ontvanger uitvoert die Blob Storage gebruikt als controlepuntopslag zonder zich te richten op de versie die Azure Stack Hub ondersteunt, ontvangt u het volgende foutbericht:

```
The value for one of the HTTP headers is not in the correct format
```


## <a name="sample-error-message-in-python"></a>Voorbeeldfoutbericht in Python
Voor Python wordt `azure.core.exceptions.HttpResponseError` een fout van `on_error(partition_context, error)` doorgegeven aan de fouthandler van `EventHubConsumerClient.receive()`. Maar de `receive()` methode geeft geen uitzondering. `print(error)`drukt de volgende uitzonderingsgegevens af:

```bash
The value for one of the HTTP headers is not in the correct format.

RequestId:f048aee8-a90c-08ba-4ce1-e69dba759297
Time:2020-03-17T22:04:13.3559296Z
ErrorCode:InvalidHeaderValue
Error:None
HeaderName:x-ms-version
HeaderValue:2019-07-07
```

De logger log twee waarschuwingen zoals de volgende:

```bash
WARNING:azure.eventhub.extensions.checkpointstoreblobaio._blobstoragecsaio: 
An exception occurred during list_ownership for namespace '<namespace-name>.eventhub.<region>.azurestack.corp.microsoft.com' eventhub 'python-eh-test' consumer group '$Default'. 

Exception is HttpResponseError('The value for one of the HTTP headers is not in the correct format.\nRequestId:f048aee8-a90c-08ba-4ce1-e69dba759297\nTime:2020-03-17T22:04:13.3559296Z\nErrorCode:InvalidHeaderValue\nError:None\nHeaderName:x-ms-version\nHeaderValue:2019-07-07')

WARNING:azure.eventhub.aio._eventprocessor.event_processor:EventProcessor instance '26d84102-45b2-48a9-b7f4-da8916f68214' of eventhub 'python-eh-test' consumer group '$Default'. An error occurred while load-balancing and claiming ownership. 

The exception is HttpResponseError('The value for one of the HTTP headers is not in the correct format.\nRequestId:f048aee8-a90c-08ba-4ce1-e69dba759297\nTime:2020-03-17T22:04:13.3559296Z\nErrorCode:InvalidHeaderValue\nError:None\nHeaderName:x-ms-version\nHeaderValue:2019-07-07'). Retrying after 71.45254944090853 seconds
```



## <a name="next-steps"></a>Volgende stappen

Zie het volgende artikel over partitionering en controlecontrole: [Verdelingsbelasting in meerdere exemplaren van uw toepassing balanceren](event-processor-balance-partition-load.md)
