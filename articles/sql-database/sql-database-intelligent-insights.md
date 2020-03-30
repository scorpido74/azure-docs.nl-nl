---
title: Monitor de prestaties van de database met Intelligent Insights
description: Azure SQL Database Intelligent Insights maakt gebruik van ingebouwde intelligentie om het gebruik van gegevens continu te monitoren door middel van kunstmatige intelligentie en storende gebeurtenissen te detecteren die slechte prestaties veroorzaken.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 03/10/2020
ms.openlocfilehash: d7b9ada17871dc7882209b7a8a449a8edcd61a94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79214079"
---
# <a name="intelligent-insights-using-ai-to-monitor-and-troubleshoot-database-performance-preview"></a>Intelligente inzichten met Behulp van AI om de prestaties van de database te controleren en problemen op te lossen (Voorbeeld)

Azure SQL Database Intelligent Insights laat u weten wat er gebeurt met uw databaseprestaties.

Intelligent Insights maakt gebruik van ingebouwde intelligentie om het gebruik van gegevens continu te monitoren door middel van kunstmatige intelligentie en storende gebeurtenissen te detecteren die slechte prestaties veroorzaken. Eenmaal gedetecteerd, wordt een gedetailleerde analyse uitgevoerd die een Intelligent Insights-bronlogboek (SQLInsights) genereert met een intelligente beoordeling van het probleem. Deze beoordeling bestaat uit een hoofdoorzaakanalyse van het probleem van de databaseprestaties en, waar mogelijk, aanbevelingen voor prestatieverbeteringen.

## <a name="what-can-intelligent-insights-do-for-you"></a>Wat kan Intelligent Insights voor u doen

Intelligent Insights is een unieke mogelijkheid van azure ingebouwde intelligentie die de volgende waarde biedt:

- Proactieve controle
- Inzichten op maat
- Vroegtijdige detectie van verslechtering van de databaseprestaties
- Hoofdoorzaakanalyse van gedetecteerde problemen
- Aanbevelingen voor prestatieverbetering
- Capaciteit uitschalen op honderdduizenden databases
- Positieve impact op de resources van DevOps en de totale eigendomskosten

## <a name="how-does-intelligent-insights-work"></a>Hoe werkt Intelligent Insights

Intelligent Insights analyseert de databaseprestaties door de databaseworkload van het laatste uur te vergelijken met de afgelopen zevendaagse basislijnworkload. Databasewerkbelasting bestaat uit query's die worden bepaald als de belangrijkste voor de databaseprestaties, zoals de meest herhaalde en grootste query's. Omdat elke database uniek is op basis van de structuur, gegevens, het gebruik en de toepassing, is elke workloadbasislijn die wordt gegenereerd specifiek en uniek voor die werkbelasting. Intelligent Insights, onafhankelijk van de basislijn van de werkbelasting, bewaakt ook absolute operationele drempels en detecteert problemen met te hoge wachttijden, kritieke uitzonderingen en problemen met queryparameters die van invloed kunnen zijn op de prestaties.

Nadat een prestatiedegradatieprobleem wordt gedetecteerd uit meerdere waargenomen statistieken met behulp van kunstmatige intelligentie, wordt analyse uitgevoerd. Een diagnoselogboek wordt gegenereerd met een intelligent inzicht in wat er met uw database gebeurt. Intelligent Insights maakt het eenvoudig om het probleem van de databaseprestaties bij te houden vanaf de eerste weergave tot de resolutie. Elk gedetecteerd probleem wordt gedurende de levenscyclus bijgehouden, van de eerste probleemdetectie en verificatie van de prestatieverbetering tot de voltooiing ervan.

![Workflow voor databaseprestatieanalyse](./media/sql-database-intelligent-insights/intelligent-insights-concept.png)

De statistieken die worden gebruikt om problemen met de prestaties van de database te meten en op te sporen, zijn gebaseerd op queryduur, time-outaanvragen, buitensporige wachttijden en foutieve aanvragen. Zie [Detectiestatistieken voor](#detection-metrics)meer informatie over statistieken .

Geïdentificeerde SQL Database prestatiedegradaties worden geregistreerd in het SQLInsights-logboek met intelligente vermeldingen die bestaan uit de volgende eigenschappen:

| Eigenschap | Details |
| :------------------- | ------------------- |
| Database-informatie | Metagegevens over een database waarop een inzicht is gedetecteerd, zoals een resource URI. |
| Waargenomen tijdsbereik | Begin- en eindtijd voor de periode van het gedetecteerde inzicht. |
| Beïnvloede statistieken | Statistieken die ervoor zorgden dat een inzicht werd gegenereerd: <ul><li>Duur van de query verhogen [seconden].</li><li>Overmatig wachten [seconden].</li><li>Getimede aanvragen [percentage].</li><li>Fout-out aanvragen [percentage].</li></ul>|
| Impactwaarde | Waarde van een gemeten statistiek. |
| Beïnvloede query's en foutcodes | Queryhash of foutcode. Deze kunnen worden gebruikt om gemakkelijk te correleren met de getroffen query's. Statistieken die bestaan uit een toename van de queryduur, wachttijd, time-outtellingen of foutcodes worden verstrekt. |
| Detecties | Detectie geïdentificeerd in de database tijdens een gebeurtenis. Er zijn 15 detectiepatronen. Zie [Problemen met de prestaties van de database oplossen met Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md)voor meer informatie. |
| Hoofdoorzaakanalyse | Hoofdoorzaakanalyse van het probleem dat in een door de mens leesbare indeling is geïdentificeerd. Sommige inzichten kunnen waar mogelijk een aanbeveling voor prestatieverbetering bevatten. |
|||

Zie deze video voor een hands-on overzicht over het gebruik van Intelligente Inzichten met Azure SQL Analytics en voor typische gebruiksscenario's:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Get-Intelligent-Insights-for-Improving-Azure-SQL-Database-Performance/player]
>

Intelligent Insights schittert in het ontdekken en oplossen van SQL Database prestatieproblemen. Zie Problemen met [azure SQL Database oplossen met Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md)om intelligente inzichten te gebruiken om problemen met de prestaties van de database op te lossen.

## <a name="intelligent-insights-options"></a>Intelligente insights-opties

Intelligente insights-opties die beschikbaar zijn in Azure SQL Database zijn:

| Intelligent Insights- optie | Ondersteuning voor één database en gepoolde database | Ondersteuning voor instantiedatabase |
| :----------------------------- | ----- | ----- |
| **Intelligent Insights configureren** - Intelligent Insights-analyse configureren voor uw databases. | Ja | Ja |
| **Inzichten streamen naar Azure SQL Analytics** : Insights streamen naar Azure SQL Analytics-bewakingsoplossing voor Azure SQL Database. | Ja | Ja |
| **Stream inzichten naar Event Hub** - Stream inzichten naar Event Hubs voor verdere aangepaste integraties. | Ja | Ja |
| **Stream inzichten naar Azure Storage** - Stream inzichten naar Azure Storage voor verdere analyse en langetermijnarchivering. | Ja | Ja |

## <a name="configure-the-export-of-the-intelligent-insights-log"></a>De export van het logboek Intelligent Insights configureren

Output van de Intelligent Insights kan worden gestreamd naar een van de verschillende bestemmingen voor analyse:

- Uitvoer die naar een Log Analytics-werkruimte wordt gestreamd, kan worden gebruikt met [Azure SQL Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql) om inzichten weer te geven via de gebruikersinterface van de Azure-portal. Dit is de geïntegreerde Azure-oplossing en de meest typische manier om inzichten te bekijken.
- Uitvoer gestreamd naar Azure Event Hubs kan worden gebruikt voor het ontwikkelen van aangepaste monitoring- en waarschuwingsscenario's
- Uitvoer gestreamd naar Azure Storage kan worden gebruikt voor aangepaste applicatie-ontwikkeling, zoals aangepaste rapportage, lange termijn data archivering enzovoort.

Integratie van Azure SQL Analytics, Azure Event Hub, Azure Storage of producten van derden voor verbruik wordt uitgevoerd door eerst Intelligent Insights-logboekregistratie (het logboek 'SQLInsights' in het diagnostische instellingenblad van een database in te schakelen) en vervolgens het configureren van Intelligente Inzichten loggegevens die naar een van deze bestemmingen worden gestreamd.

Zie [Azure SQL Database metrics en diagnostics logging](sql-database-metrics-diag-logging.md)voor meer informatie over het inschakelen van Intelligent Insights-logboekregistratie en het configureren van metrische en resourceloggegevens die naar een verbruikend product moeten worden gestreamd.

### <a name="set-up-with-azure-sql-analytics"></a>Instellen met Azure SQL Analytics

Azure SQL Analytics-oplossing biedt grafische gebruikersinterface- en rapportagemogelijkheden voor databaseprestaties, met behulp van de gegevens van intelligent insights-bronlogboeken.

Azure SQL Analytics toevoegen aan uw Azure-portaldashboard vanaf de marktplaats en om een werkruimte te maken, [raadpleegt u Azure SQL Analytics configureren](../azure-monitor/insights/azure-sql.md#configuration)

Zie [Azure SQL Database-statistieken en diagnostische logboekregistratie](sql-database-metrics-diag-logging.md)als u Intelligente Inzichten-logboekgegevens wilt gebruiken die moeten worden gestreamd naar azure SQL Analytics-werkruimte die u in de vorige stap hebt gemaakt.

In het volgende voorbeeld wordt een Intelligent Insights weergegeven die wordt weergegeven via Azure SQL Analytics:

![Intelligent Insights-rapport](./media/sql-database-intelligent-insights/intelligent-insights-azure-sql-analytics.png)

### <a name="set-up-with-event-hubs"></a>Instellen met gebeurtenishubs

Als u Intelligente inzichten wilt gebruiken met gebeurtenishubs, configureert u intelligente inzichtenlogboekgegevens die naar gebeurtenishubs worden gestreamd, raadpleegt [u Azure SQL Database-statistieken en diagnostische logboeken voor diagnostische gegevens](sql-database-metrics-diag-logging.md) en en Stream [Azure-diagnostische logboeken naar gebeurtenishubs](../azure-monitor/platform/resource-logs-stream-event-hubs.md).

Als u gebeurtenishubs wilt gebruiken om aangepaste bewaking en waarschuwingen in te stellen, raadpleegt u [Wat te doen met statistieken en diagnostische logboeken in gebeurtenishubs](sql-database-metrics-diag-logging.md#what-to-do-with-metrics-and-resource-logs-in-event-hubs).

### <a name="set-up-with-azure-storage"></a>Instellen met Azure Storage

Als u Intelligente inzichten met opslag wilt gebruiken, configureert u intelligente inzichtenlogboekgegevens die naar opslag moeten worden gestreamd, [raadpleegt u Azure SQL Database-statistieken en diagnostische logboekregistratie](sql-database-metrics-diag-logging.md) en [Stream naar Azure Storage.](sql-database-metrics-diag-logging.md#stream-into-azure-storage)

### <a name="custom-integrations-of-intelligent-insights-log"></a>Aangepaste integraties van Intelligent Insights log

Zie Het logboek van [de intelligent insights-databaseprestatiediagnose gebruiken](sql-database-intelligent-insights-use-diagnostics-log.md)om Intelligente Inzichten te gebruiken met hulpprogramma's van derden of voor aangepaste waarschuwingen en monitoring.

## <a name="detection-metrics"></a>Detectiestatistieken

Statistieken die worden gebruikt voor detectiemodellen die Intelligente inzichten genereren, zijn gebaseerd op monitoring:

- Queryduur
- Time-outaanvragen
- Overmatige wachttijd
- Fout-out aanvragen

Queryduur en time-outaanvragen worden gebruikt als primaire modellen bij het detecteren van problemen met de prestaties van de databasewerkbelasting. Ze worden gebruikt omdat ze direct meten wat er gebeurt met de werkbelasting. Om alle mogelijke gevallen van prestatieverslechtering van de werkbelasting te detecteren, worden overmatige wachttijd en foute aanvragen gebruikt als aanvullende modellen om problemen aan te geven die van invloed zijn op de prestaties van de werkbelasting.

Het systeem houdt automatisch rekening met wijzigingen in de werkbelasting en wijzigingen in het aantal queryaanvragen dat in de database wordt gedaan om de normale en niet-gewone databaseprestatiedrempels dynamisch te bepalen.

Alle statistieken worden samen in verschillende relaties bekeken via een wetenschappelijk afgeleid gegevensmodel dat elk gedetecteerd prestatieprobleem categoriseert. Informatie die wordt verstrekt door middel van een intelligent inzicht omvat:

- Details van het prestatieprobleem gedetecteerd.
- Een hoofdoorzaakanalyse van het probleem gedetecteerd.
- Aanbevelingen om waar mogelijk de prestaties van de bewaakte SQL-database te verbeteren.

## <a name="query-duration"></a>Queryduur

Het degradatiemodel voor queryduur analyseert afzonderlijke query's en detecteert de toename van de tijd die nodig is om een query samen te stellen en uit te voeren in vergelijking met de prestatiebasislijn.

Als sql database-ingebouwde intelligentie een aanzienlijke toename van querycompilering of queryuitvoeringstijd detecteert die de prestaties van de werkbelasting beïnvloedt, worden deze query's gemarkeerd als problemen met de verslechtering van de prestatiedeprestaties van query's.

Met het logboek Intelligent Insights-diagnose wordt de queryhash van de query afgebroken in prestaties uitgevoerd. De queryhash geeft aan of de prestatiedegradatie verband hield met querycompileren of de uitvoeringstijd verhogen, waardoor de duur van de query is toegenomen.

## <a name="timeout-requests"></a>Time-outaanvragen

Het time-out-aanvraagdegradatiemodel analyseert afzonderlijke query's en detecteert elke toename van time-outs op query-uitvoeringsniveau en de algehele time-outs van de aanvraag op databaseniveau in vergelijking met de prestatiebasislijnperiode.

Sommige query's kunnen een time-out maken voordat ze de uitvoeringsfase bereiken. Door middel van afgebroken werknemers vs. gemaakte verzoeken meet sql database ingebouwde intelligentie alle query's die de database bereikten of ze de uitvoeringsfase hebben bereikt of niet.

Nadat het aantal time-outs voor uitgevoerde query's of het aantal afgebroken aanvraagmedewerkers de door het systeem beheerde drempel overschrijdt, wordt een diagnostisch logboek gevuld met intelligente inzichten.

De gegenereerde inzichten bevatten het aantal time-outaanvragen en het aantal time-outquery's. Indicatie van de prestatiedegradatie is gerelateerd aan time-out toename in de uitvoeringsfase, of het totale databaseniveau wordt verstrekt. Wanneer de toename van time-outs als belangrijk wordt beschouwd voor de databaseprestaties, worden deze query's gemarkeerd als time-out prestatiebeperkingsproblemen.

## <a name="excessive-wait-times"></a>Te hoge wachttijden

Het overmatige wachttijdmodel controleert afzonderlijke databasequery's. Het detecteert ongewoon hoge query wachtstatistieken die het systeem beheerde absolute drempels overschreden. De volgende query-overmatige wait-time metrics worden waargenomen met behulp van de nieuwe SQL Server-functie, Query Store Wait Stats (sys.query_store_wait_stats):

- Het bereiken van resourcelimieten
- Het bereiken van elastische poolresourcelimieten
- Buitensporig aantal werknemers- of sessiethreads
- Overmatige databasevergrendeling
- Geheugendruk
- Andere wachtstatistieken

Het bereiken van resourcelimieten of elastische limietvoor de groep resource geeft aan dat het verbruik van beschikbare resources op een abonnement of in de elastische groep absolute drempels overschreed. Deze statistieken geven prestatiedegradatie van de werkbelasting aan. Een buitensporig aantal werknemers- of sessiethreads geeft een voorwaarde aan waarin het aantal gestarte werknemersthreads of -sessies absolute drempelwaarden heeft overschreden. Deze statistieken geven prestatiedegradatie van de werkbelasting aan.

Overmatige databasevergrendeling duidt op een voorwaarde waarbij het aantal vergrendelingen in een database absolute drempelwaarden heeft overschreden. Deze stat duidt op een prestatiedegradatie van de werkbelasting. Geheugendruk is een aandoening waarbij het aantal threads dat geheugensubsidies aanvraagt een absolute drempel overschreed. Deze stat duidt op een prestatiedegradatie van de werkbelasting.

Andere detectie van wachtstatistieken geeft een voorwaarde aan waarin diverse statistieken gemeten via de Query Store Wachtstatistieken een absolute drempel overschreden. Deze statistieken geven prestatiedegradatie van de werkbelasting aan.

Nadat overmatige wachttijden zijn gedetecteerd, afhankelijk van de beschikbare gegevens, worden de resultaten van het Intelligent Insights-diagnoselogboek uitgevoerd met de in prestaties gedegradeerde en beïnvloede query's, details van de statistieken die ervoor zorgen dat query's in uitvoering wachten en gemeten wachttijd.

## <a name="errored-requests"></a>Foute aanvragen

Het foutilerende aanvragen-degradatiemodel controleert afzonderlijke query's en detecteert een toename van het aantal query's dat is uitgeschakeld in vergelijking met de basislijnperiode. Dit model controleert ook kritieke uitzonderingen die absolute drempelwaarden overschreden die worden beheerd door sql-database ingebouwde intelligentie. Het systeem houdt automatisch rekening met het aantal queryaanvragen dat aan de database wordt gedaan en houdt rekening met eventuele werkbelastingwijzigingen in de bewaakte periode.

Wanneer de gemeten toename van het aantal foute aanvragen ten opzichte van het totale aantal aanvragen als significant wordt beschouwd voor de prestaties van de werkbelasting, worden getroffen query's gemarkeerd als foutieaanvragen prestatiebeperkingsproblemen.

Met het logboek Intelligent Insights wordt het aantal foutgeparkeerde aanvragen uitgevoerd. Het geeft aan of de prestatiedegradatie verband hield met een toename van foutige aanvragen of met het overschrijden van een gecontroleerde kritieke uitzonderingsdrempel en de gemeten tijd van de prestatiedegradatie.

Als een van de gecontroleerde kritieke uitzonderingen de absolute drempelwaarden overschrijdt die door het systeem worden beheerd, wordt een intelligent inzicht gegenereerd met kritieke uitzonderingsdetails.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [bewaken van SQL Database met SQL Analytics.](../azure-monitor/insights/azure-sql.md)
- Meer informatie over het [oplossen van sql-database-prestatieproblemen met Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md).
