---
author: mrbullwinkle
ms.service: application-insights
ms.topic: include
ms.date: 11/09/2018
ms.author: mbullwin
ms.openlocfilehash: 91141e314bf276d9138157c8a9d85d5262ac5907
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67175793"
---
Verzameling van aangepaste metingen. Gebruik deze verzameling om de meting met de naam te melden die is gekoppeld aan het telemetrieitem. Typische use cases zijn:
- de grootte van de payload van Afhankelijkheidtelemetrie
- het aantal wachtrijitems dat is verwerkt door Telemetrie aanvragen
- tijd die de klant heeft genomen om de stap in wizardstap voltooiing seintelemetrie te voltooien.

U [aangepaste metingen opvragen](https://analytics.applicationinsights.io/demo?q=H4sIAAAAAAAAA2WLOw6DMAyGd07hZoLeoRPqyMaGGAL8aiPhGCV2kKoeHsHK%2Bj1myyr8LoiaqfrT%2FkUCzRft4LMl8OUeL3LuLLIx%2BxR%2BIF8%2BtcoiNq2o78vgWuFthQaJ1AeGGxt6UlBwKxa1qQ6EpLhAfQAAAA%3D%3D&timespan=PT24H) in Application Analytics:

```
customEvents
| where customMeasurements != ""
| summarize avg(todouble(customMeasurements["Completion Time"]) * itemCount)
```

 > [!NOTE]
 > Aangepaste metingen zijn gekoppeld aan het telemetrie-item waartoe ze behoren. Ze zijn onderworpen aan bemonstering met het telemetrie-item dat deze metingen bevat. Als u een meting wilt bijhouden die een waarde heeft die onafhankelijk is van andere telemetrietypen, gebruikt u [metrische telemetrie](../articles/azure-monitor/app/api-custom-events-metrics.md).

Maximale sleutellengte: 150
