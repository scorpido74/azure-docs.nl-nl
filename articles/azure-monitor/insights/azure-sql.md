---
title: Azure SQL-analyse oplossing in Azure Monitor | Microsoft Docs
description: Azure SQL Analytics-oplossing helpt u bij het beheren van uw Azure SQL-databases
ms.subservice: logs
ms.topic: conceptual
author: danimir
ms.author: danil
ms.date: 02/21/2020
ms.reviewer: carlrab
ms.openlocfilehash: 921a05c4dc6c1d5cfa663ac71b469573b8f1925b
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77667037"
---
# <a name="monitor-azure-sql-database-using-azure-sql-analytics-preview"></a>Azure SQL Database controleren met Azure SQL Analytics (Preview)

![Azure SQL Analytics-symbool](./media/azure-sql/azure-sql-symbol.png)

Azure SQL-analyse is een geavanceerde Cloud bewakings oplossing voor het bewaken van de prestaties van alle Azure SQL-data bases op schaal en op meerdere abonnementen in één weer gave. Azure SQL-analyse verzamelt en visualiseert metrische prestatie gegevens met ingebouwde intelligentie voor het oplossen van prestaties.

Door deze verzamelde metrische gegevens te gebruiken, kunt u aangepaste bewakings regels en waarschuwingen maken. Azure SQL-analyse helpt u bij het identificeren van problemen op elke laag van uw toepassings stack. Het maakt gebruik van Azure Diagnostic-metrische gegevens samen met Azure Monitor-weer gaven voor het presen teren van informatie over al uw Azure SQL-data bases in één Log Analytics-werk ruimte. Met Azure Monitor kunt u gestructureerde en ongestructureerde gegevens verzamelen, correleren en visualiseren.

Zie de ingesloten video voor een praktische overzicht over het gebruik van Azure SQL Analytics-oplossing en voor typische gebruiksscenario's:

>[!VIDEO https://www.youtube.com/embed/j-NDkN4GIzg]
>

## <a name="connected-sources"></a>Verbonden bronnen

Azure SQL-analyse is een bewakings oplossing die alleen in de Cloud wordt ondersteund voor het streamen van de telemetrie van diagnostische gegevens voor alle Azure SQL-data bases Omdat Azure SQL-analyse geen agents gebruikt om verbinding te maken met Azure Monitor, biedt het geen ondersteuning voor de bewaking van SQL Server die on-premises of in virtuele machines worden gehost.

| Verbonden bron | Ondersteund | Beschrijving |
| --- | --- | --- |
| [Diagnostische instellingen](../platform/diagnostic-settings.md) | **Ja** | Azure metrische en logboek gegevens worden rechtstreeks door Azure naar Azure Monitor logboeken verzonden. |
| [Azure Storage-account](../platform/collect-azure-metrics-logs.md) | Nee | Azure Monitor leest de gegevens van een opslag account niet. |
| [Windows-agents](../platform/agent-windows.md) | Nee | Directe Windows-agents worden niet gebruikt door Azure SQL-analyse. |
| [Linux-agents](../learn/quick-collect-linux-computer.md) | Nee | Direct Linux-agents worden niet gebruikt door Azure SQL-analyse. |
| [System Center Operations Manager beheer groep](../platform/om-agents.md) | Nee | Een directe verbinding van de Operations Manager agent naar Azure Monitor wordt niet gebruikt door Azure SQL-analyse. |

## <a name="azure-sql-analytics-options"></a>Azure SQL-analyse opties

De onderstaande tabel bevat een overzicht van de ondersteunde opties voor twee versies van het dash board Azure SQL-analyse, één voor afzonderlijke en gepoolde data bases en elastische Pools, en de andere voor beheerde instanties en exemplaar databases.

| Azure SQL-analyse optie | Beschrijving | Ondersteuning voor enkelvoudige en gepoolde data bases en elastische Pools | Ondersteuning voor beheerde exemplaren en instanties database |
| --- | ------- | ----- | ----- |
| Resource per type | Perspectief dat alle resources die worden bewaakt telt. | Ja | Ja |
| Insights | Hiërarchische inzoomen biedt naar intelligente inzichten in prestaties. | Ja | Ja |
| Fouten | Biedt hiërarchische inzoomen in SQL-fouten die hebben plaatsgevonden voor de databases. | Ja | Ja |
| Time-outs | Biedt hiërarchische inzoomen in SQL-outs die hebben plaatsgevonden voor de databases. | Ja | Nee |
| Blokkeringen | Biedt hiërarchische inzoomen in SQL-blockings die hebben plaatsgevonden voor de databases. | Ja | Nee |
| Wachten op database | Biedt hiërarchische inzoomen in SQL wacht statistieken op het databaseniveau van de. Bevat overzichten van de totale wachttijd en de wachttijd per type wachten. |Ja | Nee |
| Queryduur | Biedt hiërarchische inzoomen in de uitvoering van querystatistieken, zoals queryduur van de, CPU-gebruik, gegevens-IO en logboek-IO-gebruik. | Ja | Ja |
| Wachten op query | Biedt hiërarchische inzoomen in de wacht querystatistieken op categorie wachten. | Ja | Ja |

## <a name="configuration"></a>Configuratie

Gebruik het proces beschreven in [Azure monitor oplossingen toevoegen van de Oplossingengalerie](../../azure-monitor/insights/solutions.md) om Azure SQL-analyse (preview) toe te voegen aan uw log Analytics-werk ruimte.

### <a name="configure-azure-sql-databases-to-stream-diagnostics-telemetry"></a>Azure SQL-data bases configureren voor het streamen van de telemetrie diagnostische gegevens

Zodra u Azure SQL-analyse oplossing hebt gemaakt in uw werk ruimte, moet u **elke resource configureren** die u wilt bewaken om de diagnostische telemetrie naar Azure SQL-analyse te streamen. Gedetailleerde instructies op deze pagina:

- Schakel Azure Diagnostics voor uw Azure SQL database in om de [telemetrie diagnostische gegevens naar Azure SQL-analyse te streamen](../../sql-database/sql-database-metrics-diag-logging.md).

De bovenstaande pagina bevat ook instructies over het inschakelen van ondersteuning voor het bewaken van meerdere Azure-abonnementen van één Azure SQL Analytics-werkruimte als een enkel glazen.

## <a name="using-azure-sql-analytics"></a>Azure SQL-analyse gebruiken

Wanneer u Azure SQL-analyse toevoegt aan uw werk ruimte, wordt de Azure SQL-analyse tegel toegevoegd aan uw werk ruimte en wordt deze weer gegeven in overzicht. Selecteer overzichts koppeling weer geven om de inhoud van de tegel te laden.

![Tegel Azure SQL-analyse overzicht](./media/azure-sql/azure-sql-sol-tile-01.png)

Zodra de tegel is geladen, wordt het aantal afzonderlijke en gepoolde data bases, elastische groepen, beheerde exemplaren en beheerde exemplaar databases waarvan Azure SQL-analyse diagnostische gegevens over de telemetrie ontvangt, weer gegeven.

![Azure SQL Analytics-tegel](./media/azure-sql/azure-sql-sol-tile-02.png)

Azure SQL-analyse biedt twee afzonderlijke weer gaven: één voor het bewaken van afzonderlijke data bases en gepoolde data bases en elastische Pools, en de andere weer gave voor het bewaken van beheerde instanties en exemplaar databases.

Klik op het bovenste gedeelte van de tegel om Azure SQL-analyse bewakings dashboard weer te geven voor enkelvoudige en gepoolde data bases en elastische Pools. Klik op het onderste deel van de tegel om Azure SQL-analyse bewakings dashboard te bekijken voor beheerde instanties en exemplaar databases.

### <a name="viewing-azure-sql-analytics-data"></a>Azure SQL Analytics-gegevens weergeven

Het dashboard bevat een overzicht van alle databases die via verschillende perspectieven worden bewaakt. Als u verschillende perspectieven wilt gebruiken, moet u de juiste metrische gegevens of Logboeken op uw SQL-resources inschakelen om te streamen naar Log Analytics werk ruimte.

Als sommige metrische gegevens of logboeken niet naar Azure Monitor worden gestreamd, worden de tegels in Azure SQL-analyse niet gevuld met controle-informatie.

### <a name="single-and-pooled-databases-and-elastic-pools-view"></a>Enkelvoudige en gepoolde data bases en elastische Pools weer geven

Nadat de tegel Azure SQL Analytics voor de database is geselecteerd, wordt het controleprogramma dashboard wordt weergegeven.

![Overzicht van Azure SQL Analytics](./media/azure-sql/azure-sql-sol-overview.png)

Het selecteren van de tegels, wordt een rapport zoomen geopend in de specifieke perspectief. Nadat het perspectief is geselecteerd, wordt het rapport zoomen geopend.

![Azure SQL Analytics time-outs](./media/azure-sql/azure-sql-sol-metrics.png)

Elk perspectief in deze weer gave bevat samen vattingen van het abonnement, de server, de elastische pool en de database niveaus. Bovendien ziet elk perspectief u een perspectief specifiek zijn voor het rapport aan de rechterkant. Abonnement, server, groep of database selecteren in de lijst, blijft de inzoomen.

### <a name="managed-instance-and-instances-databases-view"></a>Weer gave beheerde instantie en instanties-data bases

Nadat de tegel Azure SQL Analytics voor de databases is geselecteerd, wordt het controleprogramma dashboard wordt weergegeven.

![Overzicht van Azure SQL Analytics](./media/azure-sql/azure-sql-sol-overview-mi.png)

Het selecteren van de tegels, wordt een rapport zoomen geopend in de specifieke perspectief. Nadat het perspectief is geselecteerd, wordt het rapport zoomen geopend.

Als u de weer gave van een beheerd exemplaar selecteert, worden details weer gegeven over het gebruik van het beheerde exemplaar, de data bases die het bevat en telemetrie voor de query's die in het exemplaar worden uitgevoerd.

![Azure SQL Analytics time-outs](./media/azure-sql/azure-sql-sol-metrics-mi.png)

### <a name="intelligent-insights-report"></a>Intelligent Insights-rapport

Azure SQL Database [intelligent Insights](../../sql-database/sql-database-intelligent-insights.md) kunt u zien wat er gebeurt met de prestaties van alle Azure SQL-data bases. Alle Intelligent Insights verzamelde kan worden gevisualiseerd en toegankelijk is via de Insights-perspectief.

![Azure SQL Analytics-inzichten](./media/azure-sql/azure-sql-sol-insights.png)

### <a name="elastic-pools-and-database-reports"></a>Elastische Pools en database rapporten

Zowel elastische Pools als data bases hebben hun eigen specifieke rapporten waarin alle gegevens worden weer gegeven die voor de resource zijn verzameld in de opgegeven tijd.

![Azure SQL Analytics-Database](./media/azure-sql/azure-sql-sol-database.png)

![Azure SQL elastic-pool](./media/azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>Query-rapporten

Met de duur van de query en de query wordt gewacht op perspectieven, kunt u de prestaties van een query door middel van het query rapport correleren. Dit rapport worden de prestaties van query's voor verschillende databases met elkaar vergeleken en kunt u gemakkelijk de databases die de geselecteerde query ook ten opzichte van die langzaam worden uitgevoerd met deze functie.

![Azure SQL Analytics-query 's](./media/azure-sql/azure-sql-sol-queries.png)

## <a name="permissions"></a>Machtigingen

Gebruikers moeten een minimale machtigingen van de rol van lezer in Azure worden verleend voor het gebruik van Azure SQL Analytics. Deze rol echter niet toestaan dat gebruikers de querytekst wilt zien, of een functie automatisch afstemmen van acties uitvoeren. Meer strikte rollen in azure die het gebruik van Azure SQL-analyse in de meeste mate toestaan, zijn eigenaar, bijdrager, SQL DB-bijdrager of SQL Server Inzender. U kunt ook rekening houden met het maken van een aangepaste rol in de portal met specifieke machtigingen vereist alleen voor het gebruik van Azure SQL Analytics, en geen toegang tot andere resources beheren.

### <a name="creating-a-custom-role-in-portal"></a>Het maken van een aangepaste rol in de portal

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

De volgende PowerShell-script voor het inschakelen van het maken van een aangepaste rol 'SQL Analytics Toepassingsbewaking-Operator' in Azure portal met de minimale machtigingen lezen en schrijven vereist om te erkennen dat sommige organisaties strikte machtiging besturingselementen in Azure afdwingen, zoeken Gebruik Azure SQL Analytics aan de eisen voor zover is.

Vervangen van de '{SubscriptionId}' in het onderstaande script met de ID van uw Azure-abonnement, en voer het script dat is aangemeld als een eigenaar of de rol Inzender in Azure.

   ```powershell
    Connect-AzAccount
    Select-AzSubscription {SubscriptionId}
    $role = Get-AzRoleDefinition -Name Reader
    $role.Name = "SQL Analytics Monitoring Operator"
    $role.Description = "Lets you monitor database performance with Azure SQL Analytics as a reader. Does not allow change of resources."
    $role.IsCustom = $true
    $role.Actions.Add("Microsoft.SQL/servers/databases/read");
    $role.Actions.Add("Microsoft.SQL/servers/databases/topQueries/queryText/*");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/read");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/write");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/recommendedActions/read");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/recommendedActions/write");
    $role.Actions.Add("Microsoft.Sql/servers/databases/automaticTuning/read");
    $role.Actions.Add("Microsoft.Sql/servers/databases/automaticTuning/write");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/read");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/write");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/recommendedActions/read");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/recommendedActions/write");
    $role.Actions.Add("Microsoft.Resources/deployments/write");
    $role.AssignableScopes = "/subscriptions/{SubscriptionId}"
    New-AzRoleDefinition $role
   ```

Zodra de nieuwe rol is gemaakt, moet u deze rol toewijzen aan elke gebruiker die u nodig hebt om aangepaste machtigingen voor het gebruik van Azure SQL Analytics te verlenen.

## <a name="analyze-data-and-create-alerts"></a>Gegevens analyseren en waarschuwingen te maken

Gegevens analyse in Azure SQL-analyse is gebaseerd op [log Analytics taal](../log-query/get-started-queries.md) voor uw aangepaste query en rapportage. Hier vindt u een beschrijving van de beschik bare gegevens die zijn verzameld uit de database resource voor aangepaste query's in [metrische gegevens en logboeken](../../sql-database/sql-database-metrics-diag-logging.md#metrics-and-logs-available).

Automatische waarschuwingen in Azure SQL-analyse is gebaseerd op het schrijven van een Log Analytics query die een waarschuwing activeert wanneer aan een voor waarde wordt voldaan. Zoek hieronder een aantal voor beelden over Log Analytics query's waarop waarschuwingen kunnen worden ingesteld in Azure SQL-analyse.

### <a name="creating-alerts-for-azure-sql-database"></a>Het maken van waarschuwingen voor Azure SQL Database

U kunt eenvoudig [waarschuwingen maken](../platform/alerts-metric.md) met de gegevens die afkomstig zijn uit Azure SQL database resources. Hier volgen enkele nuttige [logboek query's](../log-query/log-query-overview.md) die u kunt gebruiken met een logboek waarschuwing:

#### <a name="high-cpu-on-azure-sql-database"></a>Hoge CPU-capaciteit op Azure SQL Database

```
AzureMetrics
| where ResourceProvider=="MICROSOFT.SQL"
| where ResourceId contains "/DATABASES/"
| where MetricName=="cpu_percent"
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

> [!NOTE]
>
> - De vereiste voor het instellen van deze waarschuwing is dat bewaakte data bases basis gegevens streamen naar Azure SQL-analyse.
> - Vervang de waarde MetricName cpu_percent door dtu_consumption_percent hoog DTU om resultaten te verkrijgen in plaats daarvan.

#### <a name="high-cpu-on-azure-sql-database-elastic-pools"></a>Hoge CPU-capaciteit op Azure SQL Database elastische pools

```
AzureMetrics
| where ResourceProvider=="MICROSOFT.SQL"
| where ResourceId contains "/ELASTICPOOLS/"
| where MetricName=="cpu_percent"
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

> [!NOTE]
>
> - De vereiste voor het instellen van deze waarschuwing is dat bewaakte data bases basis gegevens streamen naar Azure SQL-analyse.
> - Vervang de waarde MetricName cpu_percent door dtu_consumption_percent hoog DTU om resultaten te verkrijgen in plaats daarvan.

#### <a name="azure-sql-database-storage-in-average-above-95-in-the-last-1-hr"></a>Azure SQL Database-opslag in gemiddelde meer dan 95% in de afgelopen 1 uur

```
let time_range = 1h;
let storage_threshold = 95;
AzureMetrics
| where ResourceId contains "/DATABASES/"
| where MetricName == "storage_percent"
| summarize max_storage = max(Average) by ResourceId, bin(TimeGenerated, time_range)
| where max_storage > storage_threshold
| distinct ResourceId
```

> [!NOTE]
>
> - De vereiste voor het instellen van deze waarschuwing is dat bewaakte data bases basis gegevens streamen naar Azure SQL-analyse.
> - Deze query vereist een waarschuwingsregel om te worden ingesteld voor het starten een waarschuwing als er resultaten (> 0 resultaten) van de query, die aangeeft of de voorwaarde op sommige databases bestaat. De uitvoer is een lijst van databaseresources die zich boven de storage_threshold binnen de time_range gedefinieerd.
> - De uitvoer is een lijst van databaseresources die zich boven de storage_threshold binnen de time_range gedefinieerd.

#### <a name="alert-on-intelligent-insights"></a>Waarschuwing bij Intelligent insights

```
let alert_run_interval = 1h;
let insights_string = "hitting its CPU limits";
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active"
| where TimeGenerated > ago(alert_run_interval)
| where rootCauseAnalysis_s contains insights_string
| distinct ResourceId
```

> [!NOTE]
>
> - De vereiste voor het instellen van deze waarschuwing is dat bewaakte data bases SQLInsights Diagnostische logboeken naar Azure SQL-analyse.
> - Deze query vereist een waarschuwingsregel om te worden ingesteld om uit te voeren met dezelfde frequentie als alert_run_interval om te voorkomen dat dubbele resultaten. De regel moet worden ingesteld om te worden geactiveerd uit de waarschuwing als er resultaten (> 0 resultaten) van de query.
> - Pas de alert_run_interval aan om het tijds bereik op te geven om te controleren of de voor waarde is opgetreden op data bases die zijn geconfigureerd voor het streamen van SQLInsights-logboek naar Azure SQL-analyse.
> - De insights_string om vast te leggen van de uitvoer van de inzichten hoofdmap oorzaak analysis tekst aanpassen. Dit is dezelfde tekst die wordt weer gegeven in de gebruikers interface van Azure SQL-analyse die u vanuit de bestaande inzichten kunt gebruiken. U kunt ook de onderstaande query gebruiken om te zien van de tekst van alle inzichten die zijn gegenereerd voor uw abonnement. De uitvoer van de query gebruiken om de afzonderlijke tekenreeksen voor het instellen van waarschuwingen voor inzichten.

```
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active"
| distinct rootCauseAnalysis_s
```

### <a name="creating-alerts-for-managed-instances"></a>Waarschuwingen voor beheerde instanties maken

#### <a name="managed-instance-storage-is-above-90"></a>Beheerde exemplaar opslag is meer dan 90%

```
let storage_percentage_threshold = 90;
AzureDiagnostics
| where Category =="ResourceUsageStats"
| summarize (TimeGenerated, calculated_storage_percentage) = arg_max(TimeGenerated, todouble(storage_space_used_mb_s) *100 / todouble (reserved_storage_mb_s))
   by ResourceId
| where calculated_storage_percentage > storage_percentage_threshold
```

> [!NOTE]
>
> - De eerste vereiste voor het instellen van deze waarschuwing is dat de beheerde instantie het streamen van ResourceUsageStats-logboek heeft ingeschakeld voor Azure SQL-analyse.
> - Deze query vereist dat er een waarschuwings regel wordt ingesteld om een waarschuwing te starten wanneer er resultaten zijn (> 0 resultaten) van de query, waarbij wordt opgegeven dat de voor waarde bestaat op het beheerde exemplaar. De uitvoer is opslag percentage van het beheerde exemplaar.

#### <a name="managed-instance-cpu-average-consumption-is-above-95-in-the-last-1-hr"></a>Het gemiddelde CPU-verbruik van het beheerde exemplaar is hoger dan 95% in de afgelopen uur

```
let cpu_percentage_threshold = 95;
let time_threshold = ago(1h);
AzureDiagnostics
| where Category == "ResourceUsageStats" and TimeGenerated > time_threshold
| summarize avg_cpu = max(todouble(avg_cpu_percent_s)) by ResourceId
| where avg_cpu > cpu_percentage_threshold
```

> [!NOTE]
>
> - De vereisten voor het instellen van deze waarschuwing zijn dat het bewaakte beheerde exemplaar het streamen van ResourceUsageStats-logboek heeft ingeschakeld voor Azure SQL-analyse.
> - Deze query vereist dat er een waarschuwings regel wordt ingesteld om een waarschuwing te starten wanneer er resultaten zijn (> 0 resultaten) van de query, waarbij wordt opgegeven dat de voor waarde bestaat op het beheerde exemplaar. De uitvoer is gemiddeld CPU-gebruiks percentage verbruik in gedefinieerde periode op het beheerde exemplaar.

### <a name="pricing"></a>Prijzen

Hoewel Azure SQL-analyse gratis is voor gebruik, kunt u het verbruik van de telemetrie van diagnostische gegevens boven de gratis eenheden van de gegevensopname die elke maand van toepassing is, bekijken [log Analytics prijzen](https://azure.microsoft.com/pricing/details/monitor). Gratis eenheden van de gegevensopname opgegeven gratis bewaking inschakelen van meerdere databases per maand. Meer actieve data bases met zwaarere workloads nemen meer gegevens op in plaats van inactieve data bases. U kunt uw gegevensopname verbruik eenvoudig bewaken in Azure SQL-analyse door OMS-werk ruimte te selecteren in het navigatie menu van Azure SQL-analyse en vervolgens gebruik en geschatte kosten te selecteren.

## <a name="next-steps"></a>Volgende stappen

- Gebruik [logboek query's](../log-query/log-query-overview.md) in azure monitor om gedetailleerde Azure SQL-gegevens weer te geven.
- [Maak uw eigen Dash boards](../learn/tutorial-logs-dashboards.md) waarin Azure SQL-gegevens worden weer gegeven.
- [Maak waarschuwingen](../platform/alerts-overview.md) wanneer er specifieke Azure SQL-gebeurtenissen optreden.
