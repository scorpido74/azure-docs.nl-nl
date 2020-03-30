---
author: ggailey777
ms.author: glenga
ms.date: 7/24/2019
ms.topic: include
ms.service: azure-functions
ms.openlocfilehash: 1928a8238cd73087e3c199675574dd1395f4d76d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68881332"
---
#### <a name="built-in-log-streaming"></a>Ingebouwde logstreaming

Gebruik `logstream` de optie om streaminglogboeken te ontvangen van een specifieke functie-app die in Azure wordt uitgevoerd, zoals in het volgende voorbeeld:

```bash
func azure functionapp logstream <FunctionAppName>
```

#### <a name="live-metrics-stream"></a>Live Metrics Stream

U ook de [Live Metrics Stream](../articles/azure-monitor/app/live-stream.md) voor uw functie-app in een nieuw browservenster bekijken door de `--browser` optie op te nemen, zoals in het volgende voorbeeld:

```bash
func azure functionapp logstream <FunctionAppName> --browser
```
