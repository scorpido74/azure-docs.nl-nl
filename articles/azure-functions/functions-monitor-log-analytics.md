---
title: Azure Functions controleren met Azure Monitor-logboeken
description: Meer informatie over het gebruik van Azure Monitor-logboeken met Azure Functions voor het bewaken van functie-uitvoeringen.
author: craigshoemaker
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: cshoe
ms.custom: tracking-python
ms.openlocfilehash: a84dda648ccc1dc73956aeb5d8364c4c3f31c9f6
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87055275"
---
# <a name="monitoring-azure-functions-with-azure-monitor-logs"></a>Azure Functions controleren met Azure Monitor-logboeken

Azure Functions biedt een integratie met [Azure monitor-logboeken](../azure-monitor/platform/data-platform-logs.md) om functies te bewaken. Dit artikel laat u zien hoe u Azure Functions kunt configureren voor het verzenden van door het systeem gegenereerde en door de gebruiker gegenereerde logboeken naar Azure Monitor-Logboeken.

Met Azure Monitor Logboeken kunt u logboeken van verschillende resources in dezelfde werk ruimte consolideren, waar het kan worden geanalyseerd met [query's](../azure-monitor/log-query/log-query-overview.md) om verzamelde gegevens snel op te halen, samen te voegen en te analyseren.  U kunt query's maken en testen door [Log Analytics](../azure-monitor/log-query/log-query-overview.md) in Azure Portal te gebruiken. Vervolgens kunt u de gegevens onmiddellijk analyseren met deze hulpprogramma's, maar u kunt de query's ook opslaan om deze te gebruiken met [visualisaties](../azure-monitor/visualizations.md) of [waarschuwingsregels](../azure-monitor/platform/alerts-overview.md).

Azure Monitor gebruikt een versie van de [Kusto-querytaal](/azure/kusto/query/) (KQL) die door Azure Data Explorer wordt gebruikt en die geschikt is voor eenvoudige logboekquery's maar die ook geavanceerde functies bevat, zoals aggregaties, joins en slimme analyse. Via [diverse lessen](../azure-monitor/log-query/get-started-queries.md) kunt u de querytaal snel leren.

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


# <a name="c"></a>[C#](#tab/csharp)

```csharp
log.LogInformation("My app logs here.");
```

# <a name="java"></a>[Java](#tab/java)

```java
context.getLogger().info("My app logs here.");
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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
- Meer informatie over de [querytaal](../azure-monitor/log-query/get-started-queries.md).
