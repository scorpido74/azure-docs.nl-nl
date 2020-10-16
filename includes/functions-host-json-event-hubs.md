---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 2604a1608f21d7239db755027e15b8198fb3f9f2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
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

|Eigenschap  |Standaard | Beschrijving |
|---------|---------|---------|
|maxBatchSize|10|Het maximale gebeurtenisaantal dat per ontvangstlus wordt ontvangen.|
|prefetchCount|300|Het standaardaantal voor vooraf ophalen dat wordt gebruikt door de onderliggende `EventProcessorHost`.|
|batchCheckpointFrequency|1|Het aantal gebeurtenisbatches dat moet worden verwerkt voordat een controlepunt voor een EventHub-cursor wordt gemaakt.|

> [!NOTE]
> Zie [Naslaginformatie over host.json voor Azure Functions](../articles/azure-functions/functions-host-json.md) voor naslaginformatie over host.json in Azure Functions 2.x en hoger.

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

|Eigenschap  |Standaard | Beschrijving |
|---------|---------|---------| 
|maxBatchSize|64|Het maximale gebeurtenisaantal dat per ontvangstlus wordt ontvangen.|
|prefetchCount|n.v.t.|Het standaardaantal voor vooraf ophalen dat wordt gebruikt door de onderliggende `EventProcessorHost`.| 
|batchCheckpointFrequency|1|Het aantal gebeurtenisbatches dat moet worden verwerkt voordat een controlepunt voor een EventHub-cursor wordt gemaakt.| 

> [!NOTE]
> Zie [Naslaginformatie over host.json voor Azure Functions 1.x](../articles/azure-functions/functions-host-json-v1.md) voor naslaginformatie over host.json voor Azure Functions 1.x.

