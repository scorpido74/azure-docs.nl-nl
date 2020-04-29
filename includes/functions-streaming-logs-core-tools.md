---
author: ggailey777
ms.author: glenga
ms.date: 7/24/2019
ms.topic: include
ms.service: azure-functions
ms.openlocfilehash: 1928a8238cd73087e3c199675574dd1395f4d76d
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/27/2020
ms.locfileid: "68881332"
---
#### <a name="built-in-log-streaming"></a>Ingebouwde logboek streaming

Gebruik de `logstream` optie om streaming-logboeken te ontvangen van een specifieke functie-app die wordt uitgevoerd in azure, zoals in het volgende voor beeld:

```bash
func azure functionapp logstream <FunctionAppName>
```

#### <a name="live-metrics-stream"></a>Live Metrics Stream

U kunt ook de [Live Metrics stream](../articles/azure-monitor/app/live-stream.md) voor uw functie-app in een nieuw browser venster weer geven door `--browser` de optie op te nemen, zoals in het volgende voor beeld:

```bash
func azure functionapp logstream <FunctionAppName> --browser
```
