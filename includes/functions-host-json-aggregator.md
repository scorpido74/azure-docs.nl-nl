---
title: bestand opnemen
description: bestand opnemen
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/19/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 9c51ce726545d1c64d69c86c36fc69ea43c3b882
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76279503"
---
Hiermee geeft u op hoeveel functie aanroepen worden geaggregeerd bij het [berekenen van metrische gegevens voor Application Insights](../articles/azure-functions/functions-monitoring.md#configure-the-aggregator). 

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
|batchSize|1000|Maximum aantal aanvragen om samen te voegen.| 
|flushTimeout|00:00:30|Maximale tijds periode voor aggregatie.| 

Functie aanroepen worden geaggregeerd wanneer de eerste van de twee limieten is bereikt.
