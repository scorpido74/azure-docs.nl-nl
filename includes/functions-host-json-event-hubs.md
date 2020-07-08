---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 2604a1608f21d7239db755027e15b8198fb3f9f2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "81791677"
---
### <a name="functions-2x-and-higher"></a>Functions 2.x en hoger

```json
{
    "version": "2.0",
    "extensions": {
        "eventHubs": {
            "batchCheckpointFrequency": 5,
            "eventProcessorOptions": {
                "maxBatchSize": 256,
                "prefetchCount": 512
            }
        }
    }
}  
```

|Eigenschap  |Standaard | Description |
|---------|---------|---------|
|maxBatchSize|10|Het maximum aantal gebeurtenissen dat per receive-lus is ontvangen.|
|prefetchCount|300|Het standaard aantal voor de vooraf ophalen dat wordt gebruikt door de onderliggende `EventProcessorHost` .|
|batchCheckpointFrequency|1|Het aantal gebeurtenis batches dat moet worden verwerkt voordat een EventHub-cursor controlepunt wordt gemaakt.|

> [!NOTE]
> Zie [host.jsop referentie voor Azure functions](../articles/azure-functions/functions-host-json.md)voor een referentie van host.jsin azure functions 2. x en hoger.

### <a name="functions-1x"></a>Functions 1.x

```json
{
    "eventHub": {
      "maxBatchSize": 64,
      "prefetchCount": 256,
      "batchCheckpointFrequency": 1
    }
}
```

|Eigenschap  |Standaard | Description |
|---------|---------|---------| 
|maxBatchSize|64|Het maximum aantal gebeurtenissen dat per receive-lus is ontvangen.|
|prefetchCount|N.v.t.|De standaard vooraf ophalen die wordt gebruikt door de onderliggende `EventProcessorHost` .| 
|batchCheckpointFrequency|1|Het aantal gebeurtenis batches dat moet worden verwerkt voordat een EventHub-cursor controlepunt wordt gemaakt.| 

> [!NOTE]
> Zie [host.jsbij verwijzing voor Azure functions 1. x](../articles/azure-functions/functions-host-json-v1.md)voor een referentie van host.jsin azure functions 1. x.

