---
title: Prestaties van de data base bewaken met Intelligent Insights
description: Azure SQL Database Intelligent Insights maakt gebruik van ingebouwde intelligentie om continu database gebruik te bewaken door middel van kunst matige intelligentie en detecteert storende gebeurtenissen die de prestaties nadelig beïnvloeden.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 12/19/2018
ms.openlocfilehash: d32ce79d0c8b26e0e78e2bae0f0335315c1a3a40
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77587325"
---
# <a name="intelligent-insights-using-ai-to-monitor-and-troubleshoot-database-performance-preview"></a>Intelligent Insights met AI gebruiken om database prestaties te controleren en op te lossen (preview-versie)

Azure SQL Database Intelligent Insights kunt u zien wat er gebeurt met de prestaties van uw SQL Database en beheerde exemplaar database.

Intelligent Insights maakt gebruik van ingebouwde intelligentie om continu database gebruik te bewaken door middel van kunst matige intelligentie en detectie van storende gebeurtenissen die de prestaties nadelig beïnvloeden. Eenmaal gedetecteerd, wordt een gedetailleerde analyse uitgevoerd waarmee een diagnose logboek wordt gegenereerd met een intelligente evaluatie van het probleem. Deze evaluatie bestaat uit een analyse van de hoofd oorzaak van het prestatie probleem van de data base en, waar mogelijk, aanbevelingen voor betere prestaties.

## <a name="what-can-intelligent-insights-do-for-you"></a>Wat kan er Intelligent Insights voor u doen?

Intelligent Insights is een unieke mogelijkheid van ingebouwde intelligentie van Azure die de volgende waarde biedt:

- Proactieve controle
- Aangepaste prestatie inzichten
- Vroegtijdige detectie van de prestaties van de data base verlagen
- Hoofd oorzaak analyse van gedetecteerde problemen
- Aanbevelingen voor prestatie verbetering
- Mogelijkheden voor uitschalen op honderd duizenden data bases
- Positieve impact op DevOps resources en de total cost of ownership

## <a name="how-does-intelligent-insights-work"></a>Hoe werkt Intelligent Insights

Intelligent Insights de prestaties van de data base geanalyseerd door de data base-workload van het afgelopen uur te vergelijken met de afgelopen zeven dagen werk belasting voor de basis lijn. De werk belasting van de data base bestaat uit query's die het belangrijkst zijn voor de prestaties van de data base, zoals de meest herhaalde en grootste query's. Omdat elke Data Base uniek is op basis van de structuur, de gegevens, het gebruik en de toepassing, is elke basis van de werk belasting die wordt gegenereerd, specifiek en uniek voor een afzonderlijk exemplaar. Intelligent Insights, onafhankelijk van de basis lijn van de werk belasting, bewaakt ook absolute operationele drempels en detecteert problemen met buitensporige wacht tijden, kritieke uitzonde ringen en problemen met query parameterizations die van invloed kunnen zijn op de prestaties.

Wanneer er een probleem is opgetreden bij de uitvoering van een prestatie vermindering van meerdere waargenomen metrische gegevens met behulp van kunst matige intelligentie, wordt de analyse uitgevoerd. Er wordt een diagnostisch logboek gegenereerd met een intelligent inzicht in wat er gebeurt met uw data base. Met Intelligent Insights kunt u eenvoudig het prestatie probleem van de data base volgen tot oplossing. Elk gedetecteerde probleem wordt bijgehouden door de levens cyclus van de eerste detectie van het probleem en de controle van de prestatie verbetering tot de voltooiing ervan.

![Werk stroom voor analyse van database prestaties](./media/sql-database-intelligent-insights/intelligent-insights-concept.png)

De metrische gegevens die worden gebruikt om prestatie problemen met de data base te meten en te detecteren, zijn gebaseerd op de query duur, time-outaanvragen, buitensporige wacht tijden en mislukte aanvragen. Zie de sectie [detectie metrieken](sql-database-intelligent-insights.md#detection-metrics) van dit document voor meer informatie over metrische gegevens.

Geïdentificeerd SQL Database prestatie verminderingen worden vastgelegd in het diagnostische logboek met intelligente vermeldingen die bestaan uit de volgende eigenschappen:

| Eigenschap             | Details              |
| :------------------- | ------------------- |
| database gegevens | Meta gegevens over een Data Base waarop een inzicht is gedetecteerd, zoals een resource-URI. |
| Waargenomen tijds bereik | Begin-en eind tijd voor de periode van het gedetecteerde inzicht. |
| Beïnvloede metrische gegevens | Metrische gegevens waardoor een inzicht werd gegenereerd: <ul><li>De duur van de query wordt verhoogd [seconden].</li><li>Overmatig wacht tijd [seconden].</li><li>Time-outaanvragen [percentage].</li><li>Gefoutde aanvragen [percentage].</li></ul>|
| Impact waarde | Waarde van een gemeten meting. |
| Beïnvloede query's en fout codes | Hash-of fout code opvragen. Deze kunnen worden gebruikt om de betrokken query's eenvoudig te correleren. Metrische gegevens die bestaan uit een toename van de query duur, de wacht tijd, het aantal time-outs of de fout codes worden weer gegeven. |
| Detecties | De detectie die tijdens de uitvoering van een gebeurtenis is geïdentificeerd bij de data base. Er zijn 15 detectie patronen. Zie [problemen met database prestaties oplossen met intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md)voor meer informatie. |
| Hoofdoorzaakanalyse | Analyse van de hoofd oorzaak van het probleem dat in een lees bare indeling is geïdentificeerd. Sommige inzichten bevatten waar mogelijk een aanbeveling voor de verbetering van de prestaties. |
|||

Voor een praktische beschrijving over het gebruik van Intelligent Insights met Azure SQL-analyse en voor typische gebruiks scenario's raadpleegt u de Inge sloten video:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Get-Intelligent-Insights-for-Improving-Azure-SQL-Database-Performance/player]
>

Intelligent Insights schijnen bij het detecteren en oplossen van problemen met SQL Database prestaties. Zie [problemen oplossen Azure SQL database prestatie problemen met intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md)om intelligent Insights te gebruiken voor het oplossen van problemen met de SQL database en de prestaties van de beheerde exemplaar database.

## <a name="intelligent-insights-options"></a>Intelligent Insights opties

Intelligent Insights beschik bare opties in Azure SQL Database zijn:

| Intelligent Insights optie | Ondersteuning voor één data base en gepoolde data base | Ondersteuning voor instance data base |
| :----------------------------- | ----- | ----- |
| **Intelligent Insights configureren** : Configureer intelligent Insights analyse voor uw data bases. | Ja | Ja | 
| **Stream inzichten op Azure SQL-analyse** --streamen naar Azure SQL-analyse bewakings oplossing voor Azure SQL database. | Ja | Ja | 
| **Stream Insights naar Event hub** -stream insights naar Event hubs voor verdere aangepaste integraties. | Ja | Ja | 
| **Stream inzichten om** inzicht te krijgen in azure Storage streamen om te Azure Storage voor verdere analyse en lange termijn archivering. | Ja | Ja |

## <a name="configure-intelligent-insights"></a>Intelligent Insights configureren

De uitvoer van de Intelligent Insights is een slim logboek voor diagnostische gegevens over prestaties. Dit logboek kan op verschillende manieren worden gebruikt om het te streamen naar Azure SQL-analyse, Azure Event Hubs en Azure Storage of een product van derden.

- Gebruik het product met [Azure SQL-analyse](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql) om inzichten weer te geven via de gebruikers interface van de Azure Portal. Dit is de geïntegreerde Azure-oplossing en de meest gang bare manier om inzichten weer te geven.
- Gebruik het product met Azure Event Hubs voor de ontwikkeling van aangepaste scenario's voor bewaking en waarschuwingen
- Gebruik het product met Azure Storage voor het ontwikkelen van aangepaste toepassingen, bijvoorbeeld voor aangepaste rapportage, gegevens archivering op lange termijn, enzovoort.

Integratie van Intelligent Insights met andere producten Azure SQL-analyse, Azure Event hub, Azure Storage of producten van derden voor gebruik wordt uitgevoerd via eerst Intelligent Insights logboek registratie (het "SQLInsights"-logboek) in de diagnose de Blade instellingen van een Data Base en configureer vervolgens Intelligent Insights logboek gegevens die moeten worden gestreamd naar een van deze producten.

Zie [Azure SQL database metrische gegevens en logboek registratie van diagnostische](sql-database-metrics-diag-logging.md)gegevens voor meer informatie over het inschakelen van intelligent Insights logboek registratie en het configureren van de logboeken die moeten worden gestreamd naar een verbruikte product.

### <a name="set-up-with-azure-sql-analytics"></a>Instellen met Azure SQL-analyse

Azure SQL-analyse oplossing biedt Graphical User Interface-, rapportage-en waarschuwings functies voor database prestaties, samen met de Intelligent Insights diagnostische logboek gegevens.

> [!TIP]
> Snel aan de slag: de eenvoudigste manier om aan de slag te gaan met behulp van Intelligent Insights is het gebruik ervan samen met Azure SQL-analyse waarmee u een Graphical User Interface krijgt voor problemen met de prestaties van de data base. Azure SQL-analyse oplossing toevoegen vanuit Marketplace, een werk ruimte in deze oplossing maken en vervolgens voor elke Data Base waarvoor u Intelligent Insights wilt inschakelen, streaming van het logboek "SQLInsights" configureren op de Blade Diagnostische instellingen van een Data Base naar de werk ruimte van Azure SQL-analyse.
>

Een vereiste is om Azure SQL-analyse toe te voegen aan uw Azure Portal dash board van de Marketplace en om een werk ruimte te maken, raadpleegt u [Azure SQL-analyse configureren](../azure-monitor/insights/azure-sql.md#configuration)

Als u Intelligent Insights met Azure SQL-analyse wilt gebruiken, configureert u Intelligent Insights logboek gegevens die moeten worden gestreamd naar Azure SQL-analyse werk ruimte die u in de vorige stap hebt gemaakt, raadpleegt u [Azure SQL database statistieken en diagnostische gegevens registratie](sql-database-metrics-diag-logging.md).

In het volgende voor beeld ziet u een Intelligent Insights die wordt weer gegeven via Azure SQL-analyse:

![Intelligent Insights-rapport](./media/sql-database-intelligent-insights/intelligent-insights-azure-sql-analytics.png)

### <a name="set-up-with-event-hubs"></a>Instellen met Event Hubs

Als u Intelligent Insights met Event Hubs wilt gebruiken, configureert u Intelligent Insights logboek gegevens die moeten worden gestreamd naar Event Hubs, raadpleegt u [Logboeken van Azure Diagnostics naar Event hubs](../azure-monitor/platform/resource-logs-stream-event-hubs.md).

Zie [wat te doen met metrische gegevens en Diagnostische logboeken in Event hubs](sql-database-metrics-diag-logging.md#what-to-do-with-metrics-and-diagnostics-logs-in-event-hubs)om event hubs te gebruiken voor het instellen van aangepaste bewaking en waarschuwingen.

### <a name="set-up-with-azure-storage"></a>Instellen met Azure Storage

Als u Intelligent Insights met opslag wilt gebruiken, moet u Intelligent Insights logboek gegevens zodanig configureren dat ze naar de opslag worden gestreamd. Zie [streamen in azure Storage](sql-database-metrics-diag-logging.md#stream-diagnostic-telemetry-into-azure-storage).

### <a name="custom-integrations-of-intelligent-insights-log"></a>Aangepaste integraties van Intelligent Insights logboek

Zie [het logboek voor diagnostische gegevens over de Intelligent Insights-data base](sql-database-intelligent-insights-use-diagnostics-log.md)gebruiken om intelligent Insights te gebruiken met hulpprogram ma's van derden of voor aangepaste waarschuwingen en bewakings ontwikkeling.

## <a name="detection-metrics"></a>Detectie gegevens

De metrische gegevens die worden gebruikt voor detectie modellen die Intelligent Insights genereren, zijn gebaseerd op bewaking:

- Queryduur
- Time-outaanvragen
- Buitensporige wacht tijd
- Aanvragen met fouten

Query duur en time-outaanvragen worden gebruikt als primaire modellen bij het detecteren van problemen met de prestaties van de data base-workload. Ze worden gebruikt, omdat ze direct meten wat er gebeurt met de workload. Om alle mogelijke oorzaken van de prestatie vermindering van de werk belasting te detecteren, worden er buitensporige wacht tijden en aanvragen met een fout gebruikt als extra modellen om problemen aan te geven die van invloed zijn op de prestaties van de werk belasting.

Het systeem beschouwt automatisch wijzigingen aan de werk belasting en wijzigingen in het aantal query aanvragen dat is gedaan aan de data base om dynamische en out-of-the-out-performance drempel waarden voor data bases dynamisch te bepalen.

Alle metrische gegevens worden beschouwd als samen in verschillende relaties via een weten schappelijk afgeleid data model dat elk prestatie probleem gecategoriseerd heeft. Informatie die via een intelligent inzicht wordt verstrekt, omvat:

- Details van het prestatie probleem dat is gedetecteerd.
- Er is een analyse van de hoofd oorzaak van het probleem gedetecteerd.
- Aanbevelingen voor het verbeteren van de prestaties van de bewaakte SQL database, waar mogelijk.

## <a name="query-duration"></a>Queryduur

Het model voor het degraderen van de duur van de query analyseert afzonderlijke query's en detecteert de toename in de tijd die nodig is voor het compileren en uitvoeren van een query vergeleken met de basis lijn van de prestaties.

Als SQL Database ingebouwde intelligentie een aanzienlijke toename van de query compilatie of de uitvoerings tijd van query's detecteert die van invloed is op de prestaties van de werk belasting, worden deze query's gemarkeerd als prestatie problemen met de prestaties van de query duur.

Het Intelligent Insights Diagnostische logboeken voert de query-hash uit van de query die is gedegradeerd voor prestaties. De query-hash geeft aan of de prestaties verslechteren bij het verg Roten van de query compilatie of uitvoerings tijd, waardoor de duur van de query wordt verhoogd.

## <a name="timeout-requests"></a>Time-outaanvragen

Het model voor de degradatie van time-outaanvragen analyseert afzonderlijke query's en detecteert toename in time-outs op het uitvoerings niveau van de query en de totale time-outs van aanvragen op database niveau vergeleken met de periode van de prestatie basislijn.

Sommige van de query's kunnen een time-out ondervinden, zelfs voordat ze de uitvoerings fase bereiken. Door middel van afgebroken werk nemers versus aanvragen die zijn gedaan, SQL Database ingebouwde Intelligence-metingen en analyseert alle query's die de data base hebben bereikt, ongeacht of ze wel of niet aan de uitvoerings fase zijn verkregen.

Na het aantal time-outs voor het uitvoeren van query's of het aantal afgebroken aanvraag werkers overschrijdt de drempel waarde die door het systeem wordt beheerd, wordt een diagnose logboek gevuld met intelligente inzichten.

De gegenereerde inzichten bevatten het aantal time-outaanvragen en het aantal time-out query's. De indicatie van de prestatie vermindering is gerelateerd aan een time-out voor de uitvoering van de uitvoerings fase, of het algehele database niveau. Wanneer de toename van time-outs significant wordt geacht voor de prestaties van de data base, worden deze query's gemarkeerd als prestatie problemen tijdens de time-out van de prestaties.

## <a name="excessive-wait-times"></a>Buitensporige wacht tijden

Het model voor buitensporige wacht tijden bewaakt afzonderlijke database query's. Er worden ongebruikelijk hoge query-wacht statistieken gedetecteerd die de door het systeem beheerde absolute drempel waarden overschrijden. De volgende query met buitensporige wacht tijden meet waarden worden waargenomen met behulp van de nieuwe functie SQL Server, query Store wait-statistieken (sys. query_store_wait_stats):

- Bron limieten bereiken
- Resource limieten voor elastische Pools bereiken
- Buitensporig aantal werk-of sessie-threads
- Buitensporige database vergrendeling
- Geheugen druk
- Andere wacht statistieken

Het bereiken van resource limieten of bronnen limieten voor elastische Pools duidt op het verbruik van beschik bare resources voor een abonnement of in de elastische groep met absolute drempel waarden. Deze statistieken wijzen op de prestaties van de werk belasting. Een buitensporig aantal werk-of sessie-threads geeft een toestand aan waarin het aantal werkthreads of sessies gepaard absolute drempel waarden heeft gepasseerd. Deze statistieken wijzen op de prestaties van de werk belasting.

Buitensporige database vergrendeling geeft aan dat het aantal vergren delingen op een Data Base gepaard absolute drempel waarden heeft. Dit stat geeft aan dat de prestaties van de werk belasting worden verminderd. Geheugen druk is een voor waarde waarin het aantal threads dat geheugen vraagt, een absolute drempel overschrijdt. Dit stat geeft aan dat de prestaties van de werk belasting worden verminderd.

Met de detectie van andere wacht tijden wordt aangegeven in welke omstandigheden diverse metrische gegevens die via de query Store worden gemeten, een absolute drempel overschrijden. Deze statistieken wijzen op de prestaties van de werk belasting.

Afhankelijk van de beschik bare wacht tijden, worden de gegevens in het logboek van de Intelligent Insights Diagnostics-pogingen om de hashes te controleren die invloed hebben op de prestaties en de betrokken query's, de metrische gegevens die ervoor zorgen dat query's in uitvoering wachten en gemeten wacht tijd.

## <a name="errored-requests"></a>Aanvragen met fouten

Het model voor het degraderen van de fout aanvragen bewaakt afzonderlijke query's en detecteert een toename in het aantal query's dat is opgetreden in vergelijking met de basislijn periode. Dit model bewaakt ook kritieke uitzonde ringen die kruised absolute drempel waarden die worden beheerd door SQL Database ingebouwde intelligentie. Het systeem houdt automatisch rekening met het aantal query aanvragen dat is gedaan aan de data base en de accounts voor eventuele wijzigingen in de werk belasting in de bewaakte periode.

Wanneer de gemeten toename in fout aanvragen ten opzichte van het totale aantal aanvragen wordt beschouwd als significant voor de prestaties van de werk belasting, worden de betrokken query's gemarkeerd als fouten bij het oplossen van problemen met de prestaties.

Het Intelligent Insights logboek voert een uitvoer uit van het aantal aanvragen dat is gefoutd. Hiermee wordt aangegeven of de prestaties afnemen van een verslechterde fout in aanvragen of de drempel waarde voor een bewaakte kritieke uitzonde ring en de gemeten tijd van de verslechtering van de prestaties.

Als een van de bewaakte kritieke uitzonde ringen de absolute drempel waarden overschrijdt die worden beheerd door het systeem, wordt een intelligent inzicht gegenereerd met kritieke uitzonderings Details.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [oplossen van problemen met de prestaties van SQL database met intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md).
- Gebruik het [Intelligent Insights SQL database prestatie logboek voor diagnostische gegevens](sql-database-intelligent-insights-use-diagnostics-log.md).
- Meer informatie over het [bewaken van SQL database met behulp van SQL Analytics](../azure-monitor/insights/azure-sql.md).
- Meer informatie over het [verzamelen en gebruiken van logboek gegevens van uw Azure-resources](../azure-monitor/platform/platform-logs-overview.md).
