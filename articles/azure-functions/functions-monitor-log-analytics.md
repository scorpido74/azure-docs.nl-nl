---
title: Azure Functions controleren met Azure Monitor-logboeken
description: Meer informatie over het gebruik van Azure Monitor-logboeken met Azure Functions voor het bewaken van functie-uitvoeringen.
author: ahmedelnably
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: aelnably
ms.openlocfilehash: f4af646569edc8a9274af752e7e4f2a36585ae4d
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75769095"
---
# <a name="monitoring-azure-functions-with-azure-monitor-logs"></a>Azure Functions controleren met Azure Monitor-logboeken

Azure Functions biedt een integratie met [Azure monitor-logboeken](../azure-monitor/platform/data-platform-logs.md) om functies te bewaken. Dit artikel laat u zien hoe u Azure Functions kunt configureren voor het verzenden van door het systeem gegenereerde en door de gebruiker gegenereerde logboeken naar Azure Monitor-Logboeken.

Met Azure Monitor Logboeken kunt u logboeken van verschillende resources in dezelfde werk ruimte consolideren, waar het kan worden geanalyseerd met [query's](../azure-monitor/log-query/log-query-overview.md) om verzamelde gegevens snel op te halen, samen te voegen en te analyseren.  U kunt query's maken en testen met behulp van [log Analytics](../azure-monitor/log-query/portals.md) in de Azure Portal en vervolgens de gegevens rechtstreeks analyseren met behulp van deze hulpprogram ma's of query's opslaan voor gebruik met [Visualisaties](../azure-monitor/visualizations.md) of [waarschuwings regels](../azure-monitor/platform/alerts-overview.md).

Azure Monitor gebruikt een versie van de [Kusto-query taal](/azure/kusto/query/) die wordt gebruikt door Azure Data Explorer die geschikt is voor eenvoudige logboek query's, maar bevat ook geavanceerde functies zoals aggregaties, samen voegingen en slimme analyses. U kunt de query taal snel leren kennen met [meerdere lessen](../azure-monitor/log-query/get-started-queries.md).

> [!NOTE]
> Integratie met Azure Monitor Logboeken is momenteel beschikbaar als open bare Preview voor functie-apps die worden uitgevoerd op Windows-verbruiks-, Premium-en speciale hosting abonnementen.

## <a name="setting-up"></a>Instellen

Selecteer **Diagnostische instellingen** in het gedeelte **bewaking** en klik vervolgens op **Diagnostische instelling toevoegen**.

![Een diagnostische instelling toevoegen](media/functions-monitor-log-analytics/diagnostic-settings-add.png)

Kies op de pagina **Diagnostische instellingen** de optie **verzenden naar log Analytics**en selecteer vervolgens uw log Analytics-werk ruimte. Onder **logboek** kiest u **FunctionAppLogs**. deze tabel bevat de gewenste Logboeken.

![Een diagnostische instelling toevoegen](media/functions-monitor-log-analytics/choose-table.png)

## <a name="user-generated-logs"></a>Door de gebruiker gegenereerde logboeken

Als u aangepaste logboeken wilt genereren, kunt u de specifieke registratie-instructie gebruiken, afhankelijk van uw taal. Hier volgen enkele voor beelden van code fragmenten:


# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
log.LogInformation("My app logs here.");
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
context.getLogger().info("My app logs here.");
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
context.log('My app logs here.');
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

```powershell
Write-Host "My app logs here."
```

# <a name="pythontabpython"></a>[Python](#tab/python)

```python
logging.info('My app logs here.')
```

---

## <a name="querying-the-logs"></a>Query's uitvoeren op de logboeken

Als u de gegenereerde logboeken wilt doorzoeken, gaat u naar de Log Analytics-werk ruimte die u hebt geconfigureerd om de functie Logboeken te verzenden naar en klikt u op **Logboeken**.

![Query venster in de werk ruimte van LA](media/functions-monitor-log-analytics/querying.png)

Azure Functions worden alle logboeken naar de tabel **FunctionAppLogs** geschreven. Dit zijn enkele voor beelden van query's.

### <a name="all-logs"></a>Alle logboeken

```

FunctionAppLogs
| order by TimeGenerated desc

```

### <a name="a-specific-function-logs"></a>Een specifieke functie Logboeken

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

- Bekijk het [Azure functions overzicht](functions-overview.md)
- Meer informatie over [Azure monitor-logboeken](../azure-monitor/platform/data-platform-logs.md)
- Meer informatie over de [query taal](../azure-monitor/log-query/get-started-queries.md).
