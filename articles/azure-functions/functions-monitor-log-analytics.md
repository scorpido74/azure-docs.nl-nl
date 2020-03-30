---
title: Azure-functies bewaken met Azure-monitorlogboeken
description: Meer informatie over het gebruik van Azure Monitor Logs met Azure Functions om functie-uitvoeringen te controleren.
author: craigshoemaker
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: cshoe
ms.openlocfilehash: 13c72a1cf8a0dd4a1124e51b9ceee04ae04bf261
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77649871"
---
# <a name="monitoring-azure-functions-with-azure-monitor-logs"></a>Azure-functies bewaken met Azure-monitorlogboeken

Azure Functions biedt een integratie met [Azure Monitor Logs](../azure-monitor/platform/data-platform-logs.md) om functies te controleren. In dit artikel ziet u hoe u Azure-functies configureert om door het systeem gegenereerde en door gebruikers gegenereerde logboeken naar Azure Monitor-logboeken te verzenden.

Azure Monitor Logs biedt u de mogelijkheid om logboeken van verschillende bronnen in dezelfde werkruimte te consolideren, waar deze kunnen worden geanalyseerd met [query's](../azure-monitor/log-query/log-query-overview.md) om snel verzamelde gegevens op te halen, te consolideren en te analyseren.  U query's maken en testen met Behulp van [Log Analytics](../azure-monitor/log-query/portals.md) in de Azure-portal en vervolgens de gegevens direct analyseren met behulp van deze hulpprogramma's of query's opslaan voor gebruik met [visualisaties](../azure-monitor/visualizations.md) of [waarschuwingsregels.](../azure-monitor/platform/alerts-overview.md)

Azure Monitor gebruikt een versie van de [Kusto-querytaal](/azure/kusto/query/) die wordt gebruikt door Azure Data Explorer en die geschikt is voor eenvoudige logboekquery's, maar ook geavanceerde functionaliteit bevat, zoals aggregaties, joins en slimme analyses. U de querytaal snel leren met [meerdere lessen.](../azure-monitor/log-query/get-started-queries.md)

> [!NOTE]
> Integratie met Azure Monitor Logs is momenteel in openbare preview voor functie-apps die worden uitgevoerd op Windows-beheer, Premium en Dedicated-hostingplannen.

## <a name="setting-up"></a>Instellen

Selecteer **diagnostische** **instellingen** in de sectie Controle en klik op **Diagnostische instelling toevoegen**.

![Een diagnostische instelling toevoegen](media/functions-monitor-log-analytics/diagnostic-settings-add.png)

Kies **op** de pagina Diagnostische instellingen de optie **Verzenden naar Logboekanalyse**en selecteer vervolgens de werkruimte Log Analytics. Onder **log** kies **FunctionAppLogs**, deze tabel bevat de gewenste logs.

![Een diagnostische instelling toevoegen](media/functions-monitor-log-analytics/choose-table.png)

## <a name="user-generated-logs"></a>Door gebruikers gegenereerde logboeken

Als u aangepaste logboeken wilt genereren, u de specifieke logboekeninstructie gebruiken, afhankelijk van uw taal, hier volgen voorbeeldcodefragmenten:


# <a name="c"></a>[C #](#tab/csharp)

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

# <a name="powershell"></a>[Powershell](#tab/powershell)

```powershell
Write-Host "My app logs here."
```

# <a name="python"></a>[Python](#tab/python)

```python
logging.info('My app logs here.')
```

---

## <a name="querying-the-logs"></a>De logboeken opvragen

Als u de gegenereerde logboeken wilt opvragen, gaat u naar de werkruimte Log Analytics die u hebt geconfigureerd om de functielogboeken naar te verzenden en klikt u op **Logboeken**.

![Queryvenster in LA-werkruimte](media/functions-monitor-log-analytics/querying.png)

Azure Functions schrijft alle logboeken naar de tabel **FunctionAppLogs,** hier zijn enkele voorbeeldquery's.

### <a name="all-logs"></a>Alle logboeken

```

FunctionAppLogs
| order by TimeGenerated desc

```

### <a name="a-specific-function-logs"></a>Een specifieke functielogboeken

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

- Het [overzicht azure-functies bekijken](functions-overview.md)
- Meer informatie over [Azure Monitor-logboeken](../azure-monitor/platform/data-platform-logs.md)
- Meer informatie over de [querytaal](../azure-monitor/log-query/get-started-queries.md).
