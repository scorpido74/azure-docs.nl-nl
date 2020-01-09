---
title: Logboeken met Azure Functions trigger voor Cosmos DB configureren en lezen
description: Meer informatie over hoe u de logboeken kunt weer geven in de pipeline voor Azure Functions Logboeken bij het gebruik van Azure Functions trigger voor Cosmos DB
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: maquaran
ms.openlocfilehash: 5ff747b225f8984bcaafd80015e85a9f014bdb50
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75441829"
---
# <a name="how-to-configure-and-read-the-logs-when-using-azure-functions-trigger-for-cosmos-db"></a>De logboeken configureren en lezen bij het gebruik van Azure Functions trigger voor Cosmos DB

In dit artikel wordt beschreven hoe u uw Azure Functions omgeving kunt configureren om de Azure Functions trigger voor Cosmos DB logboeken te verzenden naar uw geconfigureerde [bewakings oplossing](../azure-functions/functions-monitoring.md).

## <a name="included-logs"></a>Opgenomen logboeken

De Azure Functions trigger voor Cosmos DB gebruikt de [bibliotheek voor de wijzigings doorvoer processor](./change-feed-processor.md) intern en de bibliotheek genereert een set status logboeken die kunnen worden gebruikt voor het bewaken van interne bewerkingen voor het [oplossen van problemen](./troubleshoot-changefeed-functions.md).

De status logboeken beschrijven hoe de Azure Functions trigger voor Cosmos DB zich gedraagt bij het uitvoeren van bewerkingen tijdens taak verdeling of-initialisatie.

## <a name="enabling-logging"></a>Logboek registratie inschakelen

Als u logboek registratie wilt inschakelen wanneer u Azure Functions trigger voor Cosmos DB gebruikt, zoekt u het `host.json` bestand in uw Azure Functions project of Azure Functions app en [configureert u het niveau van de vereiste logboek registratie](../azure-functions/functions-monitoring.md#log-configuration-in-hostjson). U moet de traceringen voor `Host.Triggers.CosmosDB` inschakelen, zoals wordt weer gegeven in het volgende voor beeld:

```js
{
  "version": "2.0",
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "Host.Triggers.CosmosDB": "Trace"
    }
  }
}
```

Nadat de Azure function is geïmplementeerd met de bijgewerkte configuratie, ziet u de Azure Functions trigger voor Cosmos DB-Logboeken als onderdeel van uw traceringen. U kunt de logboeken in uw geconfigureerde logboek registratie provider bekijken onder de *categorie* `Host.Triggers.CosmosDB`.

## <a name="query-the-logs"></a>Query's uitvoeren op de logboeken

Voer de volgende query uit om een query uit te voeren op de logboeken die zijn gegenereerd door de Azure Functions trigger voor Cosmos DB in [Azure-toepassing Insights-analyse](../azure-monitor/app/analytics.md):

```sql
traces
| where customDimensions.Category == "Host.Triggers.CosmosDB"
```

## <a name="next-steps"></a>Volgende stappen

* [Schakel bewaking](../azure-functions/functions-monitoring.md) in uw Azure functions-toepassingen in.
* Meer informatie over het [vaststellen en oplossen van veelvoorkomende problemen](./troubleshoot-changefeed-functions.md) bij het gebruik van de Azure functions trigger voor Cosmos db.