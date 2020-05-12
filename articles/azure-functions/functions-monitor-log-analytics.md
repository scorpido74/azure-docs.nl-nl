---
title: Azure Functions controleren met Azure Monitor-logboeken
description: Meer informatie over het gebruik van Azure Monitor-logboeken met Azure Functions voor het bewaken van functie-uitvoeringen.
author: craigshoemaker
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: cshoe
ms.openlocfilehash: 4b21912de95ccba1d97d187922bfada4d9dc2c56
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/12/2020
ms.locfileid: "83121621"
---
# <a name="monitoring-azure-functions-with-azure-monitor-logs"></a>Azure Functions controleren met Azure Monitor-logboeken

Azure Functions biedt een integratie met [Azure monitor-logboeken](../azure-monitor/platform/data-platform-logs.md) om functies te bewaken. Dit artikel laat u zien hoe u Azure Functions kunt configureren voor het verzenden van door het systeem gegenereerde en door de gebruiker gegenereerde logboeken naar Azure Monitor-Logboeken.

Met Azure Monitor Logboeken kunt u logboeken van verschillende resources in dezelfde werk ruimte consolideren, waar het kan worden geanalyseerd met [query's](../azure-monitor/log-query/log-query-overview.md) om verzamelde gegevens snel op te halen, samen te voegen en te analyseren.  U kunt query's maken en testen met behulp van [log Analytics](../azure-monitor/log-query/portals.md) in de Azure Portal en vervolgens de gegevens rechtstreeks analyseren met behulp van deze hulpprogram ma's of query's opslaan voor gebruik met [Visualisaties](../azure-monitor/visualizations.md) of [waarschuwings regels](../azure-monitor/platform/alerts-overview.md).

Azure Monitor gebruikt een versie van de [Kusto-query taal](/azure/kusto/query/) die wordt gebruikt door Azure Data Explorer die geschikt is voor eenvoudige logboek query's, maar bevat ook geavanceerde functies zoals aggregaties, samen voegingen en slimme analyses. U kunt de query taal snel leren kennen met [meerdere lessen](../azure-monitor/log-query/get-started-queries.md).

> [!NOTE]
> Integratie met Azure Monitor Logboeken is momenteel beschikbaar als open bare Preview voor functie-apps die worden uitgevoerd op Windows-verbruiks-, Premium-en speciale hosting abonnementen.

## <a name="setting-up"></a>Instellen

1. Selecteer in de sectie **bewaking** van de functie-app [Azure portal](https://portal.azure.com)in het Azure Portal **Diagnostische instellingen**en selecteer vervolgens **Diagnostische instelling toevoegen**.

   :::image type="content" source="media/functions-monitor-log-analytics/diagnostic-settings-add.png" alt-text="Diagnostische instellingen selecteren":::

1. Kies op de pagina **Diagnostische instellingen** onder **categorie Details** en **logboek**de optie **FunctionAppLogs**.

   De tabel **FunctionAppLogs** bevat de gewenste Logboeken.

1. Klik onder **Details van bestemming**op **verzenden naar log Analytics**en selecteer vervolgens uw **log Analytics werk ruimte**. 

1. Voer een naam in voor de **Diagnostische instellingen**en selecteer vervolgens **Opslaan**.

   :::image type="content" source="media/functions-monitor-log-analytics/choose-table.png" alt-text="Een diagnostische instelling toevoegen":::

## <a name="user-generated-logs"></a>Door de gebruiker gegenereerde logboeken

Als u aangepaste logboeken wilt genereren, gebruikt u de registratie-instructie die specifiek is voor uw taal. Hier volgen enkele voor beelden van code fragmenten:


# <a name="c"></a>[G #](#tab/csharp)

```csharp
log.LogInformation("My app logs here.");
```

# <a name="java"></a>[Java](#tab/java)

```java
context.getLogger().info("My app logs here.");
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
context.log('My app logs here.');
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
Write-Host "My app logs here."
```

# <a name="python"></a>[Python](#tab/python)

```python
logging.info('My app logs here.')
```

---

## <a name="querying-the-logs"></a>Query's uitvoeren op de logboeken

Query's uitvoeren op de gegenereerde logboeken:
 
1. Selecteer **Diagnostische instellingen**uit de functie-app. 

1. Selecteer in de lijst **Diagnostische instellingen** de log Analytics werk ruimte die u hebt geconfigureerd voor het verzenden van de functie Logboeken naar. 

1. Selecteer op de pagina **log Analytics werk ruimte** **Logboeken**.

   Azure Functions schrijft alle logboeken naar de tabel **FunctionAppLogs** onder **LogManagement**. 

   :::image type="content" source="media/functions-monitor-log-analytics/querying.png" alt-text="Query venster in Log Analytics werk ruimte":::

Hier volgen enkele voor beelden van query's:

### <a name="all-logs"></a>Alle logboeken

```

FunctionAppLogs
| order by TimeGenerated desc

```

### <a name="specific-function-logs"></a>Specifieke functie Logboeken

```

FunctionAppLogs
| where FunctionName == "<Function name>" 

```

### <a name="exceptions"></a>Uitzonderingen

```

FunctionAppLogs
| where ExceptionDetails != ""  
| order by TimeGenerated asc

```

## <a name="next-steps"></a>Volgende stappen

- Bekijk het [Azure functions overzicht](functions-overview.md).
- Meer informatie over [Azure monitor-logboeken](../azure-monitor/platform/data-platform-logs.md).
- Meer informatie over de [query taal](../azure-monitor/log-query/get-started-queries.md).
