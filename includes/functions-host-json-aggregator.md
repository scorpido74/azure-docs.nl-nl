---
title: bestand opnemen
description: bestand opnemen
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/19/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: c63cee1c451918569e9b7aa2e76209e8069d86ac
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/18/2020
ms.locfileid: "92167646"
---
Hiermee geeft u op hoeveel functieaanroepen worden geaggregeerd wanneer u [meetwaarden voor Application Insights berekent](../articles/azure-functions/configure-monitoring.md#configure-the-aggregator). 

```json
{
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    }
}
```

|Eigenschap |Standaard  | Beschrijving |
|---------|---------|---------| 
|batchSize|1000|Maximaal aantal aanvragen om samen te voegen.| 
|flushTimeout|00:00:30|Maximale tijdsperiode om samen te voegen.| 

Functieaanroepen worden geaggregeerd wanneer de eerste van de twee limieten is bereikt.
