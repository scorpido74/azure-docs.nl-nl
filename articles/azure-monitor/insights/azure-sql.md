---
title: Azure SQL-analyse oplossing in Azure Monitor | Microsoft Docs
description: Azure SQL Analytics-oplossing helpt u bij het beheren van uw Azure SQL-databases
services: log-analytics
ms.service: log-analytics
ms.custom: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: carlrab
manager: craigg
ms.date: 12/17/2018
ms.openlocfilehash: 60538145652f3539768e6deb591352a1765488fd
ms.sourcegitcommit: 71db032bd5680c9287a7867b923bf6471ba8f6be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/16/2019
ms.locfileid: "71019008"
---
# <a name="monitor-azure-sql-database-using-azure-sql-analytics-preview"></a>Azure SQL Database controleren met Azure SQL Analytics (Preview)

![Azure SQL Analytics-symbool](./media/azure-sql/azure-sql-symbol.png)

Azure SQL-analyse is een geavanceerde Cloud bewakings oplossing voor het bewaken van de prestaties van Azure SQL-data bases, elastische groepen en beheerde exemplaren op schaal en op meerdere abonnementen via één glas venster. Er worden verzameld en worden gevisualiseerd met belangrijke metrische gegevens voor Azure SQL Database-prestaties met ingebouwde intelligentie voor het oplossen van prestaties.

Metrische gegevens die u hebt verzameld met de oplossing gebruikt, kunt u aangepaste regels voor bewaking en waarschuwingen kunt maken. De oplossing helpt u bij het identificeren van problemen in elke laag van uw toepassingsstack. Het maakt gebruik van Azure Diagnostic-metrische gegevens samen met Azure Monitor weergaven voor het presen teren van informatie over al uw Azure SQL-data bases, elastische Pools en data bases in beheerde exemplaren in één Log Analytics-werk ruimte. Met Azure Monitor kunt u gestructureerde en ongestructureerde gegevens verzamelen, correleren en visualiseren.

Zie de ingesloten video voor een praktische overzicht over het gebruik van Azure SQL Analytics-oplossing en voor typische gebruiksscenario's:

>[!VIDEO https://www.youtube.com/embed/j-NDkN4GIzg]
>

## <a name="connected-sources"></a>Verbonden bronnen

Azure SQL Analytics is een cloud alleen bewaking ondersteunende streaming-oplossing van diagnostische gegevens telemetrie voor Azure SQL-databases: één, gegroepeerde en beheerde exemplaar-databases. Omdat de oplossing geen agents gebruikt om verbinding te maken met Azure Monitor, biedt de oplossing geen ondersteuning voor de bewaking van SQL Server die on-premises worden gehost of op virtuele machines. Zie de onderstaande compatibiliteits tabel.

| Verbonden bron | Ondersteund | Description |
| --- | --- | --- |
| [Azure Diagnostics](../platform/collect-azure-metrics-logs.md) | **Ja** | Azure metrische en logboek gegevens worden rechtstreeks door Azure naar Azure Monitor logboeken verzonden. |
| [Azure Storage-account](../platform/collect-azure-metrics-logs.md) | Nee | Azure Monitor leest de gegevens van een opslag account niet. |
| [Windows-agents](../platform/agent-windows.md) | Nee | Direct Windows-agents niet worden gebruikt door de oplossing. |
| [Linux-agents](../learn/quick-collect-linux-computer.md) | Nee | Directe Linux-agents niet worden gebruikt door de oplossing. |
| [System Center Operations Manager-beheergroep](../platform/om-agents.md) | Nee | Een directe verbinding van de Operations Manager agent naar Azure Monitor wordt niet gebruikt door de oplossing. |

## <a name="configuration"></a>Configuratie
Gebruik het proces dat wordt beschreven in [Azure monitor oplossingen toevoegen van de Oplossingengalerie](../../azure-monitor/insights/solutions.md) om de oplossing Azure SQL-analyse (preview) toe te voegen aan uw log Analytics-werk ruimte.

### <a name="configure-azure-sql-databases-elastic-pools-and-managed-instances-to-stream-diagnostics-telemetry"></a>Azure SQL-Databases, elastische pools en beheerde instanties tot stream diagnostische gegevens telemetrie configureren

Nadat u Azure SQL Analytics-oplossing in uw werkruimte hebt gemaakt, moet u **elk configureren** resources die u wenst te bewaken om te streamen van de diagnostische gegevens telemetrie aan de oplossing. Gedetailleerde instructies op deze pagina:

- Azure Diagnostics inschakelt voor uw Azure SQL-database om [streamen van diagnostische gegevens telemetrie naar Azure SQL Analytics](../../sql-database/sql-database-metrics-diag-logging.md).

De bovenstaande pagina bevat ook instructies over het inschakelen van ondersteuning voor het bewaken van meerdere Azure-abonnementen van één Azure SQL Analytics-werkruimte als een enkel glazen.

## <a name="using-the-solution"></a>De oplossing gebruiken

Wanneer u de oplossing aan uw werkruimte toevoegt, de tegel Azure SQL Analytics wordt toegevoegd aan uw werkruimte en deze wordt weergegeven in het overzicht. Selecteer overzichts koppeling weer geven om de inhoud van de tegel te laden.

![Tegel Azure SQL-analyse overzicht](./media/azure-sql/azure-sql-sol-tile-01.png)

Zodra de tegel is geladen, wordt het aantal Azure SQL-data bases, elastische Pools, beheerde exemplaren en data bases weer gegeven in beheerde exemplaren waarvan de oplossing diagnostische telemetrie ontvangt.

![Azure SQL Analytics-tegel](./media/azure-sql/azure-sql-sol-tile-02.png)

De oplossing biedt twee afzonderlijke weergaven: een voor het bewaken van Azure SQL-Databases en elastische pools en de andere weergave voor het bewaken van Managed Instance en databases in beheerde instanties.

Voor het dashboard van Azure SQL Analytics-controle voor Azure SQL-Databases en elastische pools weergeven, klikt u op het bovenste gedeelte van de tegel. Als u bewaking dashboard voor Managed Instance en databases in het beheerde exemplaar van Azure SQL Analytics, klik op het onderste gedeelte van de tegel.

### <a name="viewing-azure-sql-analytics-data"></a>Azure SQL Analytics-gegevens weergeven

Het dashboard bevat een overzicht van alle databases die via verschillende perspectieven worden bewaakt. Als u verschillende perspectieven wilt gebruiken, moet u de juiste metrische gegevens of Logboeken op uw SQL-resources inschakelen om te streamen naar Log Analytics werk ruimte.

Houd er rekening mee dat als bepaalde metrische gegevens of logboeken niet worden gestreamd naar Azure Monitor, de tegels in de oplossing niet zijn gevuld met bewakings informatie.

### <a name="azure-sql-database-and-elastic-pool-view"></a>Azure SQL-databases en elastische Pools weergeven

Nadat de tegel Azure SQL Analytics voor de database is geselecteerd, wordt het controleprogramma dashboard wordt weergegeven.

![Overzicht van Azure SQL Analytics](./media/azure-sql/azure-sql-sol-overview.png)

Het selecteren van de tegels, wordt een rapport zoomen geopend in de specifieke perspectief. Nadat het perspectief is geselecteerd, wordt het rapport zoomen geopend.

![Azure SQL Analytics time-outs](./media/azure-sql/azure-sql-sol-metrics.png)

Elk perspectief in deze weergave biedt samenvattingen op abonnement, server, elastische pool en databaseniveau. Bovendien ziet elk perspectief u een perspectief specifiek zijn voor het rapport aan de rechterkant. Abonnement, server, groep of database selecteren in de lijst, blijft de inzoomen.

### <a name="managed-instance-and-databases-in-managed-instance-view"></a>Voor het beheerde exemplaar en de databases in het beheerde exemplaar weergeven

Nadat de tegel Azure SQL Analytics voor de databases is geselecteerd, wordt het controleprogramma dashboard wordt weergegeven.

![Overzicht van Azure SQL Analytics](./media/azure-sql/azure-sql-sol-overview-mi.png)

Het selecteren van de tegels, wordt een rapport zoomen geopend in de specifieke perspectief. Nadat het perspectief is geselecteerd, wordt het rapport zoomen geopend.

Selectie van beheerd exemplaar van een weergave worden details weergegeven in de Managed Instance-gebruik, de databases die deze bevat en de telemetrie over de query's uitgevoerd op het exemplaar.

![Azure SQL Analytics time-outs](./media/azure-sql/azure-sql-sol-metrics-mi.png)

### <a name="perspectives"></a>Perspectieven

De onderstaande tabel geeft een overzicht van perspectieven voor twee versies van het dashboard, één voor Azure SQL-databases en elastische pools en de andere is voor de Managed Instance wordt ondersteund.

| Perspectief | Description | Ondersteuning voor SQL-databases en elastische pools | Ondersteuning voor Instance beheerd |
| --- | ------- | ----- | ----- |
| Resource per type | Perspectief dat alle resources die worden bewaakt telt. | Ja | Ja |
| Inzichten | Hiërarchische inzoomen biedt naar intelligente inzichten in prestaties. | Ja | Ja |
| Fouten | Biedt hiërarchische inzoomen in SQL-fouten die hebben plaatsgevonden voor de databases. | Ja | Ja |
| Time-outs | Biedt hiërarchische inzoomen in SQL-outs die hebben plaatsgevonden voor de databases. | Ja | Nee |
| Blokkeringen | Biedt hiërarchische inzoomen in SQL-blockings die hebben plaatsgevonden voor de databases. | Ja | Nee |
| Wachten op database | Biedt hiërarchische inzoomen in SQL wacht statistieken op het databaseniveau van de. Bevat overzichten van de totale wachttijd en de wachttijd per type wachten. |Ja | Ja |
| Queryduur | Biedt hiërarchische inzoomen in de uitvoering van querystatistieken, zoals queryduur van de, CPU-gebruik, gegevens-IO en logboek-IO-gebruik. | Ja | Ja |
| Wachten op query | Biedt hiërarchische inzoomen in de wacht querystatistieken op categorie wachten. | Ja | Ja |

### <a name="intelligent-insights-report"></a>Intelligent Insights-rapport

Azure SQL Database [Intelligent Insights](../../sql-database/sql-database-intelligent-insights.md) kunt u weten wat er gebeurt met de prestaties van alle Azure SQL-databases. Alle Intelligent Insights verzamelde kan worden gevisualiseerd en toegankelijk is via de Insights-perspectief.

![Azure SQL Analytics-inzichten](./media/azure-sql/azure-sql-sol-insights.png)

### <a name="elastic-pool-and-database-reports"></a>Elastische pool en Database-rapporten

Zowel elastische pools en SQL-Databases hebben hun eigen specifieke rapporten waarin alle gegevens die worden verzameld voor de resource in de opgegeven tijd.

![Azure SQL Analytics-Database](./media/azure-sql/azure-sql-sol-database.png)

![Azure SQL elastic-pool](./media/azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>Query-rapporten

Via de duur van de Query en de query wacht perspectieven, kunt u de prestaties van elke query door de queryrapport correleren. Dit rapport worden de prestaties van query's voor verschillende databases met elkaar vergeleken en kunt u gemakkelijk de databases die de geselecteerde query ook ten opzichte van die langzaam worden uitgevoerd met deze functie.

![Azure SQL Analytics-query 's](./media/azure-sql/azure-sql-sol-queries.png)

## <a name="permissions"></a>Machtigingen

Gebruikers moeten een minimale machtigingen van de rol van lezer in Azure worden verleend voor het gebruik van Azure SQL Analytics. Deze rol echter niet toestaan dat gebruikers de querytekst wilt zien, of een functie automatisch afstemmen van acties uitvoeren. Ruimere rollen in Azure die met behulp van de oplossing aan de eisen voor zover is toegestaan zijn eigenaar, Inzender, ' SQL DB Contributor ' of Inzender voor SQL Server. U kunt ook rekening houden met het maken van een aangepaste rol in de portal met specifieke machtigingen vereist alleen voor het gebruik van Azure SQL Analytics, en geen toegang tot andere resources beheren.

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

Analyse van gegevens in Azure SQL Analytics is gebaseerd op [Log Analytics-taal](../log-query/get-started-queries.md) voor uw aangepaste uitvoeren van query's en rapportage. Zoeken naar beschrijving van de beschikbare gegevens die worden verzameld van de databaseresource voor aangepaste query's [metrische gegevens en logboeken beschikbaar](../../sql-database/sql-database-metrics-diag-logging.md#metrics-and-logs-available).

Geautomatiseerde waarschuwingen in de oplossing is gebaseerd op een Log Analytics-query die een alarm van een voorwaarde wordt voldaan aan het schrijven. Vindt u hieronder verschillende voorbeelden gegeven van de Log Analytics-query's bij welke waarschuwingen kan worden ingesteld in de oplossing.

### <a name="creating-alerts-for-azure-sql-database"></a>Het maken van waarschuwingen voor Azure SQL Database

U kunt eenvoudig [waarschuwingen maken](../platform/alerts-metric.md) met de gegevens die afkomstig zijn van Azure SQL Database-resources. Hier volgen enkele nuttige [query's bijgehouden](../log-query/log-query-overview.md) die u kunt gebruiken met een waarschuwing:

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
> - De vereiste voor het instellen van deze waarschuwing is dat bewaakte data bases basis gegevens streamen naar de oplossing.
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
> - De vereiste voor het instellen van deze waarschuwing is dat bewaakte data bases basis gegevens streamen naar de oplossing.
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
> - De vereiste voor het instellen van deze waarschuwing is dat bewaakte data bases basis gegevens streamen naar de oplossing.
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
> - Vooraf vereiste van het instellen van deze waarschuwing is die diagnoselogboek voor gecontroleerde databases stream SQLInsights aan de oplossing.
> - Deze query vereist een waarschuwingsregel om te worden ingesteld om uit te voeren met dezelfde frequentie als alert_run_interval om te voorkomen dat dubbele resultaten. De regel moet worden ingesteld om te worden geactiveerd uit de waarschuwing als er resultaten (> 0 resultaten) van de query.
> - De alert_run_interval om op te geven van het tijdsbereik om te controleren als de voorwaarde is opgetreden op de databases die zijn geconfigureerd in stream SQLInsights logboek op de oplossing aanpassen.
> - De insights_string om vast te leggen van de uitvoer van de inzichten hoofdmap oorzaak analysis tekst aanpassen. Dit is dezelfde tekst weergegeven in de gebruikersinterface van de oplossing die u uit de bestaande inzichten gebruiken kunt. U kunt ook de onderstaande query gebruiken om te zien van de tekst van alle inzichten die zijn gegenereerd voor uw abonnement. De uitvoer van de query gebruiken om de afzonderlijke tekenreeksen voor het instellen van waarschuwingen voor inzichten.

```
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active"
| distinct rootCauseAnalysis_s
```

### <a name="creating-alerts-for-managed-instance"></a>Waarschuwingen maken voor beheerd exemplaar

#### <a name="managed-instance-storage-is-above-90"></a>Managed Instance-opslag meer dan 90 is %

```
let storage_percentage_threshold = 90;
AzureDiagnostics
| where Category =="ResourceUsageStats"
| summarize (TimeGenerated, calculated_storage_percentage) = arg_max(TimeGenerated, todouble(storage_space_used_mb_s) *100 / todouble (reserved_storage_mb_s))
   by ResourceId
| where calculated_storage_percentage > storage_percentage_threshold
```

> [!NOTE]
> - Vooraf vereiste van het instellen van deze waarschuwing is dat bewaakte beheerd exemplaar voor het streamen van ResourceUsageStats logboek is ingeschakeld op de oplossing.
> - Deze query vereist een waarschuwingsregel om te worden ingesteld voor het uitschakelen van een waarschuwing wordt geactiveerd wanneer er resultaten (> 0 resultaten) van de query, die aangeeft of de voorwaarde op het beheerde exemplaar bestaat. De uitvoer is percentage het opslagverbruik van het beheerde exemplaar.

#### <a name="managed-instance-cpu-average-consumption-is-above-95-in-the-last-1-hr"></a>Beheerde exemplaar gemiddelde processorgebruik hoger is dan 95% in de afgelopen 1 uur

```
let cpu_percentage_threshold = 95;
let time_threshold = ago(1h);
AzureDiagnostics
| where Category == "ResourceUsageStats" and TimeGenerated > time_threshold
| summarize avg_cpu = max(todouble(avg_cpu_percent_s)) by ResourceId
| where avg_cpu > cpu_percentage_threshold
```

> [!NOTE]
> - Vooraf vereiste van het instellen van deze waarschuwing is dat bewaakte beheerd exemplaar voor het streamen van ResourceUsageStats logboek is ingeschakeld op de oplossing.
> - Deze query vereist een waarschuwingsregel om te worden ingesteld voor het uitschakelen van een waarschuwing wordt geactiveerd wanneer er resultaten (> 0 resultaten) van de query, die aangeeft of de voorwaarde op het beheerde exemplaar bestaat. De uitvoer is de gemiddelde CPU-gebruik percentage verbruik in gedefinieerde periode op het beheerde exemplaar.

### <a name="pricing"></a>Prijzen

De oplossing is gratis te gebruiken, gebruik van diagnostische gegevens telemetrie boven de gratis eenheden van gegevensopname die elke maand toegewezen van toepassing is, Zie [Log Analytics-prijzen](https://azure.microsoft.com/pricing/details/monitor). Gratis eenheden van de gegevensopname opgegeven gratis bewaking inschakelen van meerdere databases per maand. Houd er rekening mee dat meer actieve databases met een zwaardere werkbelasting meer gegevens ten opzichte van niet-actieve databases opnemen. U kunt uw gegevensverbruik voor opname in de oplossing eenvoudig bewaken met OMS-werkruimte in het navigatiemenu van Azure SQL Analytics selecteren en vervolgens de optie gebruik en geschatte kosten.

## <a name="next-steps"></a>Volgende stappen

- Gebruik [logboek query's](../log-query/log-query-overview.md) in azure monitor om gedetailleerde Azure SQL-gegevens weer te geven.
- [Maak uw eigen dashboards](../learn/tutorial-logs-dashboards.md) met Azure SQL-gegevens.
- [Waarschuwingen maken](../platform/alerts-overview.md) wanneer specifieke Azure SQL-gebeurtenissen plaatsvinden.
