---
title: Logboeken configureren en lezen met Azure-functies trigger voor Cosmos DB
description: Meer informatie over het blootstellen van de logboeken aan de azure-netwerkpijplijn voor logboeken wanneer u azure-functies gebruikt voor Cosmos DB
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: maquaran
ms.openlocfilehash: 5ff747b225f8984bcaafd80015e85a9f014bdb50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75441829"
---
# <a name="how-to-configure-and-read-the-logs-when-using-azure-functions-trigger-for-cosmos-db"></a>De logboeken configureren en lezen wanneer u azure-functies gebruikt voor Cosmos DB

In dit artikel wordt beschreven hoe u de azure-functieomgeving configureren om de trigger voor Azure-functies voor Cosmos DB-logboeken naar uw geconfigureerde [bewakingsoplossing](../azure-functions/functions-monitoring.md)te verzenden.

## <a name="included-logs"></a>Opgenomen logboeken

De trigger voor Azure-functies voor Cosmos DB gebruikt de [Change Feed Processor Library](./change-feed-processor.md) intern en de bibliotheek genereert een set statuslogboeken die kunnen worden gebruikt om interne bewerkingen te controleren voor [probleemoplossingsdoeleinden.](./troubleshoot-changefeed-functions.md)

De statuslogboeken beschrijven hoe de Azure-functies zich gedragen voor Cosmos DB wanneer ze proberen te worden uitgevoerd tijdens taakverdelingsscenario's of initialisatie.

## <a name="enabling-logging"></a>Logboekregistratie inschakelen

Als u logboekregistratie wilt inschakelen wanneer u `host.json` de trigger voor Azure Functions voor Cosmos DB gebruikt, zoekt u het bestand in uw Azure Functions-project of Azure Functions App en [configureert u het vereiste logboekregistratieniveau.](../azure-functions/functions-monitoring.md#log-configuration-in-hostjson) U moet de sporen `Host.Triggers.CosmosDB` inschakelen voor zoals weergegeven in het volgende voorbeeld:

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

Nadat de Azure-functie is geïmplementeerd met de bijgewerkte configuratie, ziet u de trigger voor Azure-functies voor Cosmos DB-logboeken als onderdeel van uw traces. U de logboeken in uw geconfigureerde logboekregistratieprovider bekijken onder de *categorie* `Host.Triggers.CosmosDB`.

## <a name="query-the-logs"></a>De logboeken opvragen

Voer de volgende query uit om de logboeken op te vragen die zijn gegenereerd door de Trigger voor Azure Functions voor Cosmos DB in [Azure Application Insights' Analytics:](../azure-monitor/app/analytics.md)

```sql
traces
| where customDimensions.Category == "Host.Triggers.CosmosDB"
```

## <a name="next-steps"></a>Volgende stappen

* [Bewaking inschakelen](../azure-functions/functions-monitoring.md) in uw Azure Functions-toepassingen.
* Meer informatie over het [diagnosticeren en oplossen van veelvoorkomende problemen](./troubleshoot-changefeed-functions.md) bij het gebruik van de trigger voor Azure-functies voor Cosmos DB.