---
title: Azure SQL Analytics-oplossing in Azure Monitor | Microsoft Documenten
description: Azure SQL Analytics-oplossing helpt u bij het beheren van uw Azure SQL-databases
ms.subservice: logs
ms.topic: conceptual
author: danimir
ms.author: danil
ms.date: 02/21/2020
ms.reviewer: carlrab
ms.openlocfilehash: 921a05c4dc6c1d5cfa663ac71b469573b8f1925b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275462"
---
# <a name="monitor-azure-sql-database-using-azure-sql-analytics-preview"></a>Azure SQL-database bewaken met Azure SQL Analytics (Preview)

![Azure SQL Analytics-symbool](./media/azure-sql/azure-sql-symbol.png)

Azure SQL Analytics is een geavanceerde cloudmonitoringoplossing voor het bewaken van de prestaties van al uw Azure SQL-databases op schaal en voor meerdere abonnementen in één weergave. Azure SQL Analytics verzamelt en visualiseert belangrijke prestatiestatistieken met ingebouwde informatie voor het oplossen van prestaties.

Met behulp van deze verzamelde statistieken u aangepaste controleregels en waarschuwingen maken. Azure SQL Analytics helpt u bij het identificeren van problemen op elke laag van uw toepassingsstack. Het maakt gebruik van Azure Diagnostic-statistieken samen met Azure Monitor-weergaven om gegevens over al uw Azure SQL-databases in één Log Analytics-werkruimte weer te geven. Azure Monitor helpt u gestructureerde en ongestructureerde gegevens te verzamelen, correleren en visualiseren.

Zie de ingesloten video voor een hands-on overzicht over het gebruik van Azure SQL Analytics-oplossing en voor typische gebruiksscenario's:

>[!VIDEO https://www.youtube.com/embed/j-NDkN4GIzg]
>

## <a name="connected-sources"></a>Verbonden bronnen

Azure SQL Analytics is een cloud-oplossing die alleen de bewaking van de ondersteuning van het streamen van diagnostische telemetrie voor al uw Azure SQL-databases ondersteunt. Omdat Azure SQL Analytics geen agents gebruikt om verbinding te maken met Azure Monitor, biedt het geen ondersteuning voor de bewaking van SQL Server die on-premises of in virtuele machines wordt gehost.

| Verbonden bron | Ondersteund | Beschrijving |
| --- | --- | --- |
| [Diagnostische instellingen](../platform/diagnostic-settings.md) | **Ja** | Azure-metrische en loggegevens worden rechtstreeks door Azure naar Azure Monitor-logboeken verzonden. |
| [Azure-opslagaccount](../platform/collect-azure-metrics-logs.md) | Nee | Azure Monitor leest de gegevens niet uit een opslagaccount. |
| [Windows-agents](../platform/agent-windows.md) | Nee | Directe Windows-agents worden niet gebruikt door Azure SQL Analytics. |
| [Linux-agents](../learn/quick-collect-linux-computer.md) | Nee | Directe Linux-agents worden niet gebruikt door Azure SQL Analytics. |
| [Beheergroep System Center Operations Manager](../platform/om-agents.md) | Nee | Een directe verbinding van de Operations Manager-agent met Azure Monitor wordt niet gebruikt door Azure SQL Analytics. |

## <a name="azure-sql-analytics-options"></a>Azure SQL Analytics-opties

In de onderstaande tabel worden ondersteunde opties beschreven voor twee versies van het Azure SQL Analytics-dashboard, een voor afzonderlijke en samengevoegde databases en elastische pools, en de andere voor beheerde exemplaren en instantiedatabases.

| Azure SQL Analytics, optie | Beschrijving | Ondersteuning voor een enkele en gepoolde database en elastische pools | Ondersteuning voor beheerde exemplaren en instanties database |
| --- | ------- | ----- | ----- |
| Resource op type | Perspectief dat alle bewaakte resources telt. | Ja | Ja |
| Inzichten | Biedt hiërarchische inzoomin intelligente inzichten in prestaties. | Ja | Ja |
| Fouten | Biedt hiërarchische inzoomin SQL-fouten die zijn opgetreden in de databases. | Ja | Ja |
| Time-outs | Biedt hiërarchische inzooming in SQL-time-outs die in de databases zijn uitgevoerd. | Ja | Nee |
| Blokkeringen | Biedt hiërarchische inzoomin SQL-blokkeringen die in de databases zijn gebeurd. | Ja | Nee |
| Database wacht | Biedt hiërarchische inzoomin SQL-wachtstatistieken op databaseniveau. Bevat samenvattingen van de totale wachttijd en de wachttijd per wachttype. |Ja | Nee |
| Queryduur | Biedt hiërarchische inzooming in de queryuitvoeringsstatistieken, zoals queryduur, CPU-gebruik, Gegevens IO-gebruik, Io-gebruik van logboek. | Ja | Ja |
| Querywachttijden | Biedt hiërarchische inzoomin de querywachtstatistieken per wachtcategorie. | Ja | Ja |

## <a name="configuration"></a>Configuratie

Gebruik het proces dat is beschreven in [Azure Monitor-oplossingen toevoegen vanuit de Galerie Oplossingen](../../azure-monitor/insights/solutions.md) om Azure SQL Analytics (Preview) toe te voegen aan uw Logboekanalysewerkruimte.

### <a name="configure-azure-sql-databases-to-stream-diagnostics-telemetry"></a>Azure SQL-databases configureren om diagnostische telemetrie te streamen

Nadat u azure SQL Analytics-oplossing in uw werkruimte hebt gemaakt, moet u elke resource **configureren** die u wilt controleren om de diagnostische telemetrie naar Azure SQL Analytics te streamen. Volg gedetailleerde instructies op deze pagina:

- Azure Diagnostics inschakelen voor uw Azure SQL-database om [diagnostische telemetrie te streamen naar Azure SQL Analytics.](../../sql-database/sql-database-metrics-diag-logging.md)

De bovenstaande pagina bevat ook instructies voor het inschakelen van ondersteuning voor het bewaken van meerdere Azure-abonnementen vanuit één Azure SQL Analytics-werkruimte als één deelvenster glas.

## <a name="using-azure-sql-analytics"></a>Azure SQL Analytics gebruiken

Wanneer u Azure SQL Analytics toevoegt aan uw werkruimte, wordt de tegel Azure SQL Analytics aan uw werkruimte toegevoegd en wordt deze weergegeven in Overzicht. Selecteer Koppeling Overzicht weergeven om de tegelinhoud te laden.

![Overzichtstegel Azure SQL Analytics](./media/azure-sql/azure-sql-sol-tile-01.png)

Eenmaal geladen, toont de tegel het aantal afzonderlijke en gepoolde databases, elastische pools, beheerde instanties en beheerde instantiedatabases waaruit Azure SQL Analytics diagnostische telemetrie ontvangt.

![Azure SQL Analytics-tegel](./media/azure-sql/azure-sql-sol-tile-02.png)

Azure SQL Analytics biedt twee afzonderlijke weergaven: één voor het bewaken van afzonderlijke databases en gepoolde databases en elastische pools, en de andere weergave voor het bewaken van beheerde exemplaren en instantiedatabases.

Als u het Azure SQL Analytics-bewakingsdashboard voor afzonderlijke en samengevoegde databases en elastische groepen wilt weergeven, klikt u op het bovenste gedeelte van de tegel. Als u het Azure SQL Analytics-bewakingsdashboard voor beheerde exemplaren en instantiedatabases wilt weergeven, klikt u op het onderste deel van de tegel.

### <a name="viewing-azure-sql-analytics-data"></a>Azure SQL Analytics-gegevens weergeven

Het dashboard bevat het overzicht van alle databases die worden gecontroleerd door verschillende perspectieven. Als u verschillende perspectieven wilt laten werken, moet u de juiste statistieken of logboeken op uw SQL-resources laten streamen naar de werkruimte Log Analytics.

Als sommige statistieken of logboeken niet worden gestreamd naar Azure Monitor, worden de tegels in Azure SQL Analytics niet gevuld met bewakingsgegevens.

### <a name="single-and-pooled-databases-and-elastic-pools-view"></a>Weergave afzonderlijke en samengevoegde databases en elastische pools

Zodra de Azure SQL Analytics-tegel voor de database is geselecteerd, wordt het bewakingsdashboard weergegeven.

![Overzicht van Azure SQL Analytics](./media/azure-sql/azure-sql-sol-overview.png)

Als u een van de tegels selecteert, wordt een inzoomrapport geopend in het specifieke perspectief. Zodra het perspectief is geselecteerd, wordt het inzoomenrapport geopend.

![Time-outs van Azure SQL Analytics](./media/azure-sql/azure-sql-sol-metrics.png)

Elk perspectief in deze weergave bevat samenvattingen op de niveaus van het abonnement, de server, de elastische pool en de database. Bovendien toont elk perspectief een perspectief dat specifiek is voor het verslag aan de rechterkant. Als u een abonnement, server, groep of database selecteert in de lijst, wordt de inzoom vervolg voortgezet.

### <a name="managed-instance-and-instances-databases-view"></a>Weergave beheerde instantie en instanties

Zodra de azure SQL Analytics-tegel voor de databases is geselecteerd, wordt het bewakingsdashboard weergegeven.

![Overzicht van Azure SQL Analytics](./media/azure-sql/azure-sql-sol-overview-mi.png)

Als u een van de tegels selecteert, wordt een inzoomrapport geopend in het specifieke perspectief. Zodra het perspectief is geselecteerd, wordt het inzoomenrapport geopend.

Als u de beheerde instantieweergave selecteert, worden details weergegeven over het beheerde instantiegebruik, de databases die het bevat en telemetrie op de query's die in de instantie worden uitgevoerd.

![Time-outs van Azure SQL Analytics](./media/azure-sql/azure-sql-sol-metrics-mi.png)

### <a name="intelligent-insights-report"></a>Intelligent Insights-rapport

Azure SQL Database [Intelligent Insights](../../sql-database/sql-database-intelligent-insights.md) laat u weten wat er gebeurt met de prestaties van alle Azure SQL-databases. Alle verzamelde Intelligent Insights kunnen worden gevisualiseerd en toegankelijk via het Insights-perspectief.

![Azure SQL Analytics Insights](./media/azure-sql/azure-sql-sol-insights.png)

### <a name="elastic-pools-and-database-reports"></a>Elastische pools en databaserapporten

Zowel elastische pools als databases hebben hun eigen specifieke rapporten die alle gegevens weergeven die in de opgegeven tijd voor de resource worden verzameld.

![Azure SQL Analytics-database](./media/azure-sql/azure-sql-sol-database.png)

![Azure SQL-elastische pool](./media/azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>Queryrapporten

Via de queryduur en querywachtperspectieven u de prestaties van elke query correleren via het queryrapport. In dit rapport worden de queryprestaties in verschillende databases vergeleken en u eenvoudig databases lokaliseren die de geselecteerde query goed uitvoeren ten opzichte van databases die traag zijn.

![Azure SQL Analytics-query's](./media/azure-sql/azure-sql-sol-queries.png)

## <a name="permissions"></a>Machtigingen

Als u Azure SQL Analytics wilt gebruiken, moeten gebruikers een minimale toestemming krijgen voor de Reader-rol in Azure. Met deze rol kunnen gebruikers de querytekst echter niet zien of automatische tuningacties uitvoeren. Meer tolerante rollen in Azure die het gebruik van Azure SQL Analytics in de ruimste mate mogelijk maken, zijn Eigenaar, Inzender, SQL DB-bijdrager of SQL Server-bijdrager. U ook overwegen een aangepaste rol in de portal te maken met specifieke machtigingen die alleen vereist zijn om Azure SQL Analytics te gebruiken en zonder toegang tot het beheren van andere bronnen.

### <a name="creating-a-custom-role-in-portal"></a>Een aangepaste rol in portal maken

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Als u erkent dat sommige organisaties strikte machtigingscontroles afdwingen in Azure, vindt u het volgende PowerShell-script waarmee een aangepaste rol 'SQL Analytics Monitoring Operator' in Azure-portal kan worden gemaakt met de minimale lees- en schrijfmachtigingen die nodig zijn om Azure SQL Analytics optimaal te gebruiken.

Vervang de {SubscriptionId}" in het onderstaande script door uw Azure-abonnements-id en voer het script uit dat is aangemeld als eigenaar of inzenderrol in Azure.

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

Zodra de nieuwe rol is gemaakt, wijst u deze rol toe aan elke gebruiker die u aangepaste machtigingen moet verlenen om Azure SQL Analytics te gebruiken.

## <a name="analyze-data-and-create-alerts"></a>Gegevens analyseren en waarschuwingen maken

Gegevensanalyse in Azure SQL Analytics is gebaseerd op [loganalytics-taal](../log-query/get-started-queries.md) voor uw aangepaste query's en rapportage. Zoek de beschrijving van de beschikbare gegevens die zijn verzameld uit databasebronnen voor aangepaste [query's in statistieken en logboeken die beschikbaar zijn.](../../sql-database/sql-database-metrics-diag-logging.md#metrics-and-logs-available)

Geautomatiseerde waarschuwingen in Azure SQL Analytics is gebaseerd op het schrijven van een Log Analytics-query die een waarschuwing activeert op basis van een voorwaarde die is voldaan. Hieronder vindt u enkele voorbeelden van Log Analytics-query's waarop waarschuwingen kunnen worden ingesteld in Azure SQL Analytics.

### <a name="creating-alerts-for-azure-sql-database"></a>Waarschuwingen maken voor Azure SQL Database

U eenvoudig [waarschuwingen maken](../platform/alerts-metric.md) met de gegevens afkomstig van Azure SQL Database-bronnen. Hier volgen enkele handige [logboekquery's](../log-query/log-query-overview.md) die u gebruiken met een logboekwaarschuwing:

#### <a name="high-cpu-on-azure-sql-database"></a>Hoge CPU op Azure SQL Database

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
> - Vooraf vereiste van het instellen van deze waarschuwing is dat bewaakte databases basisstatistieken streamen naar Azure SQL Analytics.
> - Vervang de waarde MetricName cpu_percent door dtu_consumption_percent om in plaats daarvan hoge DTU-resultaten te verkrijgen.

#### <a name="high-cpu-on-azure-sql-database-elastic-pools"></a>Hoge CPU op elastische pools van Azure SQL Database

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
> - Vooraf vereiste van het instellen van deze waarschuwing is dat bewaakte databases Basisstatistieken streamen naar Azure SQL Analytics.
> - Vervang de waarde MetricName cpu_percent door dtu_consumption_percent om in plaats daarvan hoge DTU-resultaten te verkrijgen.

#### <a name="azure-sql-database-storage-in-average-above-95-in-the-last-1-hr"></a>Azure SQL Database-opslag gemiddeld boven 95% in de laatste 1 uur

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
> - Vooraf vereiste van het instellen van deze waarschuwing is dat bewaakte databases basisstatistieken streamen naar Azure SQL Analytics.
> - Voor deze query moet een waarschuwingsregel worden ingesteld om een waarschuwing af te vuren wanneer er resultaten (> 0 resultaten) van de query bestaan, waarbij wordt aangenomen dat de voorwaarde in sommige databases bestaat. De uitvoer is een lijst met databasebronnen die zich boven de storage_threshold bevinden binnen de time_range gedefinieerd.
> - De uitvoer is een lijst met databasebronnen die zich boven de storage_threshold bevinden binnen de time_range gedefinieerd.

#### <a name="alert-on-intelligent-insights"></a>Alert op intelligente inzichten

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
> - Pre-vereiste van het instellen van deze waarschuwing is dat bewaakte databases SQLInsights-diagnostische logboeken streamen naar Azure SQL Analytics.
> - Voor deze query moet een waarschuwingsregel worden ingesteld om met dezelfde frequentie als alert_run_interval uit te voeren om dubbele resultaten te voorkomen. De regel moet worden ingesteld om de waarschuwing af te vuren wanneer er resultaten (> 0 resultaten) van de query zijn.
> - Pas de alert_run_interval aan om het tijdsbereik op te geven om te controleren of de voorwaarde is opgetreden in databases die zijn geconfigureerd om SQLInsights-logboek naar Azure SQL Analytics te streamen.
> - Pas de insights_string aan om de uitvoer van de hoofdoorzaakanalysetekst van Insights vast te leggen. Dit is dezelfde tekst die wordt weergegeven in de gebruikersinterface van Azure SQL Analytics die u gebruiken vanuit de bestaande inzichten. U de onderstaande query ook gebruiken om de tekst van alle inzichten die op uw abonnement zijn gegenereerd, te bekijken. Gebruik de uitvoer van de query om de afzonderlijke tekenreeksen te verzamelen voor het instellen van waarschuwingen op Insights.

```
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active"
| distinct rootCauseAnalysis_s
```

### <a name="creating-alerts-for-managed-instances"></a>Waarschuwingen maken voor beheerde instanties

#### <a name="managed-instance-storage-is-above-90"></a>Beheerde instantieopslag is boven de 90%

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
> - Vooraf vereiste van het instellen van deze waarschuwing is dat bewaakte de beheerde instantie heeft de streaming van ResourceUsageStats log ingeschakeld om Azure SQL Analytics.
> - Voor deze query moet een waarschuwingsregel worden ingesteld om een waarschuwing af te vuren wanneer er resultaten (> 0-resultaten) van de query bestaan, waarbij wordt aangenomen dat de voorwaarde bestaat voor de beheerde instantie. De uitvoer is opslagpercentage verbruik op de beheerde instantie.

#### <a name="managed-instance-cpu-average-consumption-is-above-95-in-the-last-1-hr"></a>Managed instance CPU gemiddelde verbruik is boven 95% in de laatste 1 uur

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
> - Vooraf vereiste van het instellen van deze waarschuwing is dat de bewaakte beheerde instantie de streaming van ResourceUsageStats-logboek heeft ingeschakeld voor Azure SQL Analytics.
> - Voor deze query moet een waarschuwingsregel worden ingesteld om een waarschuwing af te vuren wanneer er resultaten (> 0-resultaten) van de query bestaan, waarbij wordt aangenomen dat de voorwaarde bestaat voor de beheerde instantie. De uitvoer is het gemiddelde verbruik van cpu-gebruikspercentages in een bepaalde periode op de beheerde instantie.

### <a name="pricing"></a>Prijzen

Hoewel Azure SQL Analytics gratis te gebruiken is, is het verbruik van diagnostische telemetrie boven de gratis eenheden van gegevensopname die elke maand worden toegewezen, van toepassing op de prijzen van [Log Analytics.](https://azure.microsoft.com/pricing/details/monitor) De gratis eenheden van de verstrekte gegevensopname maken het mogelijk om elke maand verschillende databases gratis te monitoren. Actievere databases met zwaardere workloads nemen meer gegevens in dan inactieve databases. U uw gegevensopnameverbruik eenvoudig controleren in Azure SQL Analytics door OMS Workspace te selecteren in het navigatiemenu van Azure SQL Analytics en vervolgens Gebruiks- en geschatte kosten te selecteren.

## <a name="next-steps"></a>Volgende stappen

- Gebruik [logboekquery's](../log-query/log-query-overview.md) in Azure Monitor om gedetailleerde Azure SQL-gegevens weer te geven.
- [Maak uw eigen dashboards](../learn/tutorial-logs-dashboards.md) met Azure SQL-gegevens.
- [Maak waarschuwingen](../platform/alerts-overview.md) wanneer specifieke Azure SQL-gebeurtenissen optreden.
