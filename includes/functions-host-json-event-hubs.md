---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 2604a1608f21d7239db755027e15b8198fb3f9f2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81791677"
---
### <a name="functions-2x-and-higher"></a>Functies 2. x en hoger

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
|maxBatchSize|10|Het maximum aantal gebeurtenissen dat per receive-lus is ontvangen.|
|prefetchCount|300|Het standaard aantal voor de vooraf ophalen dat wordt gebruikt `EventProcessorHost`door de onderliggende.|
|batchCheckpointFrequency|1|Het aantal gebeurtenis batches dat moet worden verwerkt voordat een EventHub-cursor controlepunt wordt gemaakt.|

> [!NOTE]
> Zie voor een verwijzing naar de host. json in Azure Functions 2. x en hoger de [verwijzing host. json voor Azure functions](../articles/azure-functions/functions-host-json.md).

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
|maxBatchSize|64|Het maximum aantal gebeurtenissen dat per receive-lus is ontvangen.|
|prefetchCount|N.v.t.|De standaard vooraf ophalen die wordt gebruikt door de onderliggende `EventProcessorHost`.| 
|batchCheckpointFrequency|1|Het aantal gebeurtenis batches dat moet worden verwerkt voordat een EventHub-cursor controlepunt wordt gemaakt.| 

> [!NOTE]
> Zie [host. json Reference voor Azure functions 1. x](../articles/azure-functions/functions-host-json-v1.md)voor een verwijzing naar de host. json in azure functions 1. x.

