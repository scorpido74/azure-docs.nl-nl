---
title: Prestatieproblemen oplossen met Intelligent Insights
description: Intelligent Insights helpt u bij het oplossen van azure SQL Database-prestatieproblemen.
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
ms.openlocfilehash: 739bba7ed9ab4770a762c08fccc422ce048ae11d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79214095"
---
# <a name="troubleshoot-azure-sql-database-performance-issues-with-intelligent-insights"></a>Problemen met azure SQL-database-prestaties oplossen met Intelligente Inzichten

Op deze pagina vindt u informatie over prestatieproblemen met Azure SQL Database en Managed Instance die zijn gedetecteerd via het hulpprogramma [intelligent insights.](sql-database-intelligent-insights.md) Statistieken en bronlogboeken kunnen worden gestreamd naar [Azure Monitor-logboeken,](../azure-monitor/insights/azure-sql.md) [Azure Event Hubs,](../azure-monitor/platform/resource-logs-stream-event-hubs.md) [Azure Storage](sql-database-metrics-diag-logging.md#stream-into-azure-storage)of een oplossing van derden voor aangepaste DevOps-waarschuwings- en rapportagemogelijkheden.

> [!NOTE]
> Zie het [stroomstroomdiagram Aanbevolen stroomoplossing voor probleemoplossing voor problemen](sql-database-intelligent-insights-troubleshoot-performance.md#recommended-troubleshooting-flow) met sql-database met behulp van Intelligente inzichten in dit document.

## <a name="detectable-database-performance-patterns"></a>Detecteerbare databaseprestatiepatronen

Intelligent Insights detecteert automatisch prestatieproblemen met databases in Azure SQL Database op basis van wachttijden, fouten of time-outs voor query-uitvoering. Intelligente Insights-uitvoer gedetecteerde prestatiepatronen naar het SQL Database-bronlogboek. Detecteerbare prestatiepatronen worden samengevat in de onderstaande tabel.

| Detecteerbare prestatiepatronen | Beschrijving voor Azure SQL Database en elastische pools | Beschrijving voor databases in Beheerde instantie |
| :------------------- | ------------------- | ------------------- |
| [Het bereiken van resourcelimieten](sql-database-intelligent-insights-troubleshoot-performance.md#reaching-resource-limits) | Het verbruik van beschikbare resources (DTOU's), databaseworker threads of database-aanmeldingssessies die beschikbaar zijn op het bewaakte abonnement, heeft grenzen bereikt. Dit heeft invloed op de SQL Database-prestaties. | Het verbruik van CPU-resources bereikt de limieten voor beheerde instantie. Dit heeft invloed op de databaseprestaties. |
| [Werkbelasting verhogen](sql-database-intelligent-insights-troubleshoot-performance.md#workload-increase) | Werkbelasting verhogen of continue accumulatie van werkbelasting op de database werd gedetecteerd. Dit heeft invloed op de SQL Database-prestaties. | De toename van de werkbelasting is gedetecteerd. Dit heeft invloed op de databaseprestaties. |
| [Geheugendruk](sql-database-intelligent-insights-troubleshoot-performance.md#memory-pressure) | Werknemers die geheugensubsidies hebben aangevraagd, moeten wachten op geheugentoewijzingen voor statistisch significante hoeveelheden tijd, of er bestaat een verhoogde accumulatie van werknemers die geheugensubsidies hebben aangevraagd. Dit heeft invloed op de SQL Database-prestaties. | Werknemers die geheugensubsidies hebben aangevraagd, wachten op geheugentoewijzingen voor een statistisch significante hoeveelheid tijd. Dit heeft invloed op de databaseprestaties. |
| [Vergrendelen](sql-database-intelligent-insights-troubleshoot-performance.md#locking) | Er is overmatige databasevergrendeling gedetecteerd die van invloed is op de sql-databaseprestaties. | Er is een overmatige databasevergrendeling gedetecteerd die van invloed is op de databaseprestaties. |
| [Verhoogde MAXDOP](sql-database-intelligent-insights-troubleshoot-performance.md#increased-maxdop) | De maximale mate van parallellisme-optie (MAXDOP) is gewijzigd die van invloed is op de efficiëntie van de queryuitvoering. Dit heeft invloed op de SQL Database-prestaties. | De maximale mate van parallellisme-optie (MAXDOP) is gewijzigd die van invloed is op de efficiëntie van de queryuitvoering. Dit heeft invloed op de databaseprestaties. |
| [Stelling voor pagelatch](sql-database-intelligent-insights-troubleshoot-performance.md#pagelatch-contention) | Meerdere threads proberen tegelijkertijd toegang te krijgen tot dezelfde bufferpagina's in het geheugen, wat resulteert in verhoogde wachttijden en pagelatch-twist. Dit is van invloed op de SQL-database van de prestaties. | Meerdere threads proberen tegelijkertijd toegang te krijgen tot dezelfde bufferpagina's in het geheugen, wat resulteert in verhoogde wachttijden en pagelatch-twist. Dit is van invloed op de database van de prestaties. |
| [Ontbrekende index](sql-database-intelligent-insights-troubleshoot-performance.md#missing-index) | Ontbrekende index is gedetecteerd die van invloed is op de sql-databaseprestaties. | Ontbrekende index is gedetecteerd die van invloed is op de databaseprestaties. |
| [Nieuwe query](sql-database-intelligent-insights-troubleshoot-performance.md#new-query) | Er is nieuwe query gedetecteerd die van invloed is op de algehele SQL-databaseprestaties. | Er is nieuwe query gedetecteerd die van invloed is op de algehele databaseprestaties. |
| [Verhoogde wachtstatistiek](sql-database-intelligent-insights-troubleshoot-performance.md#increased-wait-statistic) | Er werden verhoogde wachttijden in de database gedetecteerd die van invloed zijn op de prestaties van de SQL-database. | Er zijn verhoogde wachttijden in de database gedetecteerd die van invloed zijn op de prestaties van de database. |
| [TempDB Stelling](sql-database-intelligent-insights-troubleshoot-performance.md#tempdb-contention) | Meerdere threads proberen toegang te krijgen tot dezelfde TempDB-bron die een knelpunt veroorzaakt. Dit heeft invloed op de SQL Database-prestaties. | Meerdere threads proberen toegang te krijgen tot dezelfde TempDB-bron die een knelpunt veroorzaakt. Dit heeft invloed op de databaseprestaties. |
| [Elastiek zwembad DTU tekort](sql-database-intelligent-insights-troubleshoot-performance.md#elastic-pool-dtu-shortage) | Tekort aan beschikbare eDTU's in de elastische pool heeft invloed op de prestaties van SQL Database. | Niet beschikbaar voor Managed Instance omdat het vCore-model gebruikt. |
| [Regressie plannen](sql-database-intelligent-insights-troubleshoot-performance.md#plan-regression) | Nieuw plan of een wijziging in de werklast van een bestaand plan is gedetecteerd. Dit heeft invloed op de SQL Database-prestaties. | Nieuw plan of een wijziging in de werklast van een bestaand plan is gedetecteerd. Dit heeft invloed op de databaseprestaties. |
| [Wijziging van configuratiewaarde met databasebereik](sql-database-intelligent-insights-troubleshoot-performance.md#database-scoped-configuration-value-change) | Configuratiewijziging in de SQL-database is gedetecteerd die van invloed is op de prestaties van de database. | Configuratiewijziging in de database is gedetecteerd die van invloed is op de databaseprestaties. |
| [Trage client](sql-database-intelligent-insights-troubleshoot-performance.md#slow-client) | Trage toepassingsclient kan de uitvoer uit de database niet snel genoeg gebruiken. Dit heeft invloed op de SQL Database-prestaties. | Trage toepassingsclient kan de uitvoer uit de database niet snel genoeg gebruiken. Dit heeft invloed op de databaseprestaties. |
| [Downgrade van de prijslaag](sql-database-intelligent-insights-troubleshoot-performance.md#pricing-tier-downgrade) | De downgradeactie van de prijscategorie heeft de beschikbare resources verlaagd. Dit heeft invloed op de SQL Database-prestaties. | De downgradeactie van de prijscategorie heeft de beschikbare resources verlaagd. Dit heeft invloed op de databaseprestaties. |

> [!TIP]
> Schakel automatische afstemming van [Azure SQL Database in](sql-database-automatic-tuning.md)voor continue prestatieoptimalisatie van SQL Database. Deze unieke functie van SQL Database ingebouwde intelligentie bewaakt continu uw SQL-database, stemt automatisch indexen af en past queryexecution plancorrecties toe.
>

In de volgende sectie worden detecteerbare prestatiepatronen nader beschreven.

## <a name="reaching-resource-limits"></a>Het bereiken van resourcelimieten

### <a name="what-is-happening"></a>Wat gebeurt er

Dit detecteerbare prestatiepatroon combineert prestatieproblemen die verband houden met het bereiken van beschikbare resourcelimieten, werknemerslimieten en sessielimieten. Nadat dit prestatieprobleem is gedetecteerd, geeft een beschrijvingsveld van het diagnoselogboek aan of het prestatieprobleem gerelateerd is aan resource-, werknemer- of sessielimieten.

Resources in SQL Database worden meestal verwezen naar [DTU-](sql-database-what-is-a-dtu.md) of [vCore-resources.](sql-database-service-tiers-vcore.md) Het patroon van het bereiken van resourcelimieten wordt herkend wanneer gedetecteerde queryprestatiedegradatie wordt veroorzaakt door het bereiken van een van de gemeten resourcelimieten.

De resource beperkt resource geeft het aantal beschikbare gelijktijdige aanmeldingen aan in de SQL-database aan. Dit prestatiepatroon wordt herkend wanneer toepassingen die zijn verbonden met de SQL-databases het aantal beschikbare gelijktijdige aanmeldingen in de database hebben bereikt. Als toepassingen proberen meer sessies te gebruiken dan beschikbaar zijn in een database, wordt de queryprestaties beïnvloed.

Het bereiken van werknemerslimieten is een specifiek geval van het bereiken van resourcelimieten omdat beschikbare werknemers niet worden meegeteld in het DTU- of vCore-gebruik. Het bereiken van werknemerslimieten voor een database kan leiden tot de toename van resourcespecifieke wachttijden, wat resulteert in verslechtering van de queryprestaties.

### <a name="troubleshooting"></a>Problemen oplossen

De query-uitvoer query's query's van query's die van invloed zijn op de prestaties en het verbruik van resources percentages. U deze informatie gebruiken als uitgangspunt voor het optimaliseren van uw databasewerkbelasting. U met name de query's optimaliseren die de prestatiedegradatie beïnvloeden door indexen toe te voegen. Of u toepassingen optimaliseren met een gelijkmatiger workloaddistributie. Als u de workloads niet verminderen of optimalisaties uitvoeren, u overwegen de prijscategorie van uw SQL-databaseabonnement te verhogen om de hoeveelheid beschikbare resources te verhogen.

Als u de beschikbare sessielimieten hebt bereikt, u uw toepassingen optimaliseren door het aantal aanmeldingen in de database te verminderen. Als u het aantal aanmeldingen van uw toepassingen naar de database niet verminderen, u overwegen de prijscategorie van uw database te verhogen. U uw database splitsen en verplaatsen naar meerdere databases voor een meer evenwichtige werkbelastingsverdeling.

Zie [Hoe om te gaan met de limieten van maximale aanmeldingen van SQL Database voor](https://blogs.technet.microsoft.com/latam/20../../how-to-deal-with-the-limits-of-azure-sql-database-maximum-logins/)meer suggesties over het oplossen van sessielimieten. Zie [Overzicht van resourcelimieten op een SQL Database-server](sql-database-resource-limits-database-server.md) voor informatie over limieten op server- en abonnementsniveau.

## <a name="workload-increase"></a>Werkbelasting verhogen

### <a name="what-is-happening"></a>Wat gebeurt er

Dit prestatiepatroon identificeert problemen die worden veroorzaakt door een toename van de werkbelasting of, in zijn ernstigere vorm, een opstapelen de werkbelasting.

Deze detectie wordt gemaakt door een combinatie van verschillende statistieken. De metname van de basisstatistiek detecteert een toename van de werkbelasting in vergelijking met de basislijn van de afgelopen werkbelasting. De andere vorm van detectie is gebaseerd op het meten van een grote toename van actieve werknemersthreads die groot genoeg is om de queryprestaties te beïnvloeden.

In zijn ernstigere vorm kan de werkbelasting zich voortdurend opstapelen vanwege het onvermogen van de SQL-database om de werkbelasting te verwerken. Het resultaat is een continu groeiende werklastgrootte, de werklast opstapelingsconditie. Vanwege deze voorwaarde neemt de tijd dat de werkbelasting wacht op uitvoering toe. Deze voorwaarde is een van de ernstigste problemen met de prestaties van de database. Dit probleem wordt gedetecteerd door de toename van het aantal afgebroken werknemersthreads te controleren.

### <a name="troubleshooting"></a>Problemen oplossen

In het diagnoselogboek wordt het aantal query's uitgevoerd waarvan de uitvoering is toegenomen en de queryhash van de query met de grootste bijdrage aan de werkbelastingsverhoging. U deze informatie gebruiken als uitgangspunt voor het optimaliseren van de werkbelasting. De query die is geïdentificeerd als de grootste bijdrager aan de toename van de werkbelasting is vooral handig als uitgangspunt.

U overwegen de workloads gelijkmatiger te verdelen naar de database. Overweeg de query te optimaliseren die van invloed is op de prestaties door indexen toe te voegen. U uw werklast ook over meerdere databases verdelen. Als deze oplossingen niet mogelijk zijn, u overwegen de prijscategorie van uw SQL-databaseabonnement te verhogen om de hoeveelheid beschikbare resources te verhogen.

## <a name="memory-pressure"></a>Geheugenbelasting

### <a name="what-is-happening"></a>Wat gebeurt er

Dit prestatiepatroon duidt op degradatie in de huidige databaseprestaties veroorzaakt door geheugendruk, of in zijn meer ernstige vorm een geheugenopstapelvoorwaarde, in vergelijking met de afgelopen zevendaagse prestatiebasislijn.

Geheugendruk duidt op een prestatievoorwaarde waarin er een groot aantal werknemersthreads is die geheugensubsidies aanvragen in de SQL-database. Het hoge volume veroorzaakt een hoge geheugengebruiksvoorwaarde waarin de SQL-database geen geheugen kan toewijzen aan alle werknemers die daarom vragen. Een van de meest voorkomende redenen voor dit probleem is gerelateerd aan de hoeveelheid geheugen beschikbaar voor de SQL-database aan de ene kant. Aan de andere kant zorgt een toename van de werklast ervoor dat de werkthreads en de geheugendruk toenemen.

De meer ernstige vorm van geheugendruk is de geheugen opstapelen conditie. Deze voorwaarde geeft aan dat een hoger aantal werknemersthreads geheugensubsidies aanvraagt dan er query's zijn die het geheugen vrijgeven. Dit aantal werknemersthreads dat geheugensubsidies aanvraagt, kan ook voortdurend toenemen (opstapelen), omdat de SQL-databaseengine niet in staat is om geheugen efficiënt genoeg toe te wijzen om aan de vraag te voldoen. De geheugenopstapelvoorwaarde vertegenwoordigt een van de ernstigste problemen met de databaseprestaties.

### <a name="troubleshooting"></a>Problemen oplossen

De diagnostische logboek uitgangen het geheugen object op te slaan details met de klerk (dat wil zeggen, werknemer draad) gemarkeerd als de hoogste reden voor een hoog geheugengebruik en relevante tijdstempels. U deze informatie gebruiken als basis voor het oplossen van problemen.

U query's met betrekking tot de bedienden met het hoogste geheugengebruik optimaliseren of verwijderen. U er ook voor zorgen dat u geen gegevens opvraagt die u niet van plan bent te gebruiken. Goede praktijk is om altijd gebruik maken van een WHERE clausule in uw vragen. Daarnaast raden we u aan niet-geclusterde indexen te maken om de gegevens te zoeken in plaats van te scannen.

U de werkbelasting ook verminderen door deze over meerdere databases te optimaliseren of te distribueren. Of u uw werklast over meerdere databases verdelen. Als deze oplossingen niet mogelijk zijn, u overwegen de prijscategorie van uw SQL-databaseabonnement te verhogen om de hoeveelheid geheugenbronnen die beschikbaar is voor de database te verhogen.

Zie [Geheugensubsidies meditatie: De mysterieuze SQL Server-geheugenconsument met vele namen voor](https://techcommunity.microsoft.com/t5/sql-server-support/memory-grants-meditation-the-mysterious-sql-server-memory/ba-p/333994)aanvullende suggesties voor het oplossen van problemen.

## <a name="locking"></a>Vergrendelen

### <a name="what-is-happening"></a>Wat gebeurt er

Dit prestatiepatroon geeft een verslechtering aan in de huidige databaseprestaties waarbij overmatige databasevergrendeling wordt gedetecteerd in vergelijking met de prestatiebasislijn van de afgelopen zeven dagen.

In het moderne RDBMS is vergrendeling essentieel voor het implementeren van multithreaded systemen waarbij de prestaties worden gemaximaliseerd door waar mogelijk meerdere gelijktijdige werknemers en parallelle databasetransacties uit te voeren. Vergrendeling in deze context verwijst naar het ingebouwde toegangsmechanisme waarin slechts één transactie uitsluitend toegang heeft tot de rijen, pagina's, tabellen en bestanden die vereist zijn en niet kan concurreren met een andere transactie voor resources. Wanneer de transactie die de resources voor gebruik heeft vergrendeld, met hen wordt uitgevoerd, wordt het slot op deze resources vrijgegeven, waardoor andere transacties toegang hebben tot vereiste resources. Zie [Vergrendelen in de databaseengine](https://msdn.microsoft.com/library/ms190615.aspx)voor meer informatie over vergrendeling.

Als transacties die door de SQL-engine worden uitgevoerd, langdurig wachten om toegang te krijgen tot resources die zijn vergrendeld voor gebruik, veroorzaakt deze wachttijd de vertraging van de prestaties van de werkbelastinguitvoering.

### <a name="troubleshooting"></a>Problemen oplossen

In het diagnoselogboek worden vergrendelingsgegevens uitgevoerd die u gebruiken als basis voor het oplossen van problemen. U de gerapporteerde blokkeringsquery's analyseren, dat wil zeggen de query's die de degradatie van de vergrendelingsprestaties introduceren, en deze verwijderen. In sommige gevallen u de blokkeringsquery's optimaliseren.

De eenvoudigste en veiligste manier om het probleem te beperken is om transacties kort te houden en de vergrendelingsvoetafdruk van de duurste query's te verkleinen. U een grote batch bewerkingen opsplitsen in kleinere bewerkingen. Goede praktijk is om de query lock footprint te verminderen door het maken van de query zo efficiënt mogelijk. Verminder grote scans omdat ze de kans op impasses vergroten en de algehele databaseprestaties negatief beïnvloeden. Voor geïdentificeerde query's die vergrendeling veroorzaken, u nieuwe indexen maken of kolommen toevoegen aan de bestaande index om de tabelscans te voorkomen.

Zie Voor meer suggesties [het oplossen van blokkeringsproblemen die worden veroorzaakt door vergrendelingsescalatie in SQL Server.](https://support.microsoft.com/help/323630/how-to-resolve-blocking-problems-that-are-caused-by-lock-escalation-in)

## <a name="increased-maxdop"></a>Verhoogde MAXDOP

### <a name="what-is-happening"></a>Wat gebeurt er

Dit detecteerbare prestatiepatroon geeft een voorwaarde aan waarin een gekozen queryuitvoeringsplan meer is paralleldan het had moeten zijn. De SQL Database query optimizer kan de workloadprestaties verbeteren door query's parallel uit te voeren om dingen waar mogelijk te versnellen. In sommige gevallen besteden parallelle werknemers die een query verwerken meer tijd aan elkaar om resultaten te synchroniseren en samen te voegen in vergelijking met het uitvoeren van dezelfde query met minder parallelle werknemers, of zelfs in sommige gevallen in vergelijking met een thread met één werknemer.

Het expertsysteem analyseert de huidige databaseprestaties ten opzichte van de basislijnperiode. Hiermee wordt bepaald of een eerder uitvoerende query trager wordt uitgevoerd dan voorheen, omdat het queryuitvoeringsplan meer parallel loopt dan het zou moeten zijn.

De maxdop-serverconfiguratieoptie in SQL Database wordt gebruikt om te bepalen hoeveel CPU-kernen kunnen worden gebruikt om dezelfde query parallel uit te voeren.

### <a name="troubleshooting"></a>Problemen oplossen

De query-uitvoer van het diagnostiseren query-hashes met betrekking tot query's waarvoor de duur van de uitvoering is toegenomen omdat ze meer zijn parallelgelopen dan ze hadden moeten zijn. Het logboek levert ook CXP wachttijden. Deze tijd vertegenwoordigt de tijd dat een enkele organisator / coördinator thread (thread 0) wacht op alle andere threads te voltooien voordat het samenvoegen van de resultaten en vooruit. Bovendien, de diagnostische log uitgangen van de wachttijden die de slecht presterende query's wachtten in de uitvoering in het algemeen. U deze informatie gebruiken als basis voor het oplossen van problemen.

Optimaliseer of vereenvoudig vervolgens complexe query's. Goede praktijk is om te breken lange batch banen in kleinere. Zorg er bovendien voor dat u indexen hebt gemaakt om uw query's te ondersteunen. U ook handmatig de maximale mate van parallellisme (MAXDOP) afdwingen voor een query die is gemarkeerd als slecht presterend. Zie [De optie MAXDOP-serverconfiguratie configureren](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option)als u deze bewerking wilt configureren met Behulp van T-SQL.

Als u de maximaldop-serverconfiguratieoptie als standaardwaarde op nul (0) instelt, geeft dit aan dat SQL Database alle beschikbare logische CPU-kernen kan gebruiken om threads te parallelliseren voor het uitvoeren van één query. Als u MAXDOP instelt op één (1) geeft dit aan dat slechts één kern kan worden gebruikt voor één queryuitvoering. In de praktijk betekent dit dat het parallellisme wordt uitgeschakeld. Afhankelijk van de case-per-case basis, beschikbare cores voor de database en diagnostische logboekinformatie, u de MAXDOP-optie afstemmen op het aantal cores dat wordt gebruikt voor parallelle query-uitvoering die het probleem in uw geval kan oplossen.

## <a name="pagelatch-contention"></a>Stelling voor pagelatch

### <a name="what-is-happening"></a>Wat gebeurt er

Dit prestatiepatroon geeft de huidige prestatieverslechtering van de databaseworkload aan als gevolg van pagelatch-twist in vergelijking met de basislijn van de afgelopen zeven dagen werkbelasting.

Vergrendelingen zijn lichtgewicht synchronisatiemechanismen die door SQL Database worden gebruikt om multithreading mogelijk te maken. Ze garanderen consistentie van in-memory structuren die indexen, gegevenspagina's en andere interne structuren bevatten.

Er zijn veel soorten vergrendelingen beschikbaar in de SQL-database. Voor eenvoudige doeleinden worden buffervergrendelingen gebruikt om in-memory pagina's in de bufferpool te beschermen. IO-vergrendelingen worden gebruikt om pagina's te beschermen die nog niet in de buffergroep zijn geladen. Wanneer gegevens naar een pagina in de buffergroep worden geschreven of gelezen, moet een werknemersthread eerst een buffervergrendeling voor de pagina verkrijgen. Wanneer een werknemersthread toegang probeert te krijgen tot een pagina die nog niet beschikbaar is in de buffergroep in het geheugen, wordt een IO-verzoek ingediend om de vereiste informatie uit de opslag te laden. Deze opeenvolging van gebeurtenissen duidt op een meer ernstige vorm van prestatiedegradatie.

Twist op de pagina vergrendelingen treedt op wanneer meerdere threads gelijktijdig proberen om vergrendelingen te verwerven op dezelfde in-memory structuur, die een verhoogde wachttijd voor query uitvoering introduceert. In het geval van pagelatch IO stelling, wanneer gegevens moet worden benaderd vanuit de opslag, deze wachttijd is nog groter. Het kan de prestaties van de werkbelasting aanzienlijk beïnvloeden. Pagelatch stelling is de meest voorkomende scenario van threads te wachten op elkaar en concurreren om middelen op meerdere CPU-systemen.

### <a name="troubleshooting"></a>Problemen oplossen

De diagnostische log uitgangen pagelatch geschil details. U deze informatie gebruiken als basis voor het oplossen van problemen.

Omdat een pagelatch een intern controlemechanisme van SQL Database is, bepaalt deze automatisch wanneer deze moet worden gebruikt. Toepassingsbeslissingen, inclusief schemaontwerp, kunnen het gedrag van pagelatch beïnvloeden als gevolg van het deterministische gedrag van vergrendelingen.

Een methode voor het afhandelen van vergrendelingsgeschil is het vervangen van een sequentiële indexsleutel door een niet-opeenvolgende sleutel om wisselplaten gelijkmatig over een indexbereik te verdelen. Een voorloopkolom in de index verdeelt de werkbelasting doorgaans proportioneel. Een andere methode om te overwegen is tabelpartitionering. Het maken van een hash partitioneringschema met een berekende kolom op een partitietabel is een veelvoorkomende benadering voor het verzachten van overmatige vergrendelingsgeschil. In het geval van pagelatch IO stelling, de invoering van indexen helpt om deze prestaties probleem te verzachten.

Zie [Diagnose en oplossing voor vergrendeling op SQL Server](https://download.microsoft.com/download/B/9/E/B9EDF2CD-1DBF-4954-B81E-82522880A2DC/SQLServerLatchContention.pdf) (PDF-download) voor meer informatie.

## <a name="missing-index"></a>Ontbrekende index

### <a name="what-is-happening"></a>Wat gebeurt er

Dit prestatiepatroon geeft de huidige prestatieverslechtering van de databaseworkload aan in vergelijking met de basislijn van de afgelopen zeven dagen als gevolg van een ontbrekende index.

Een index wordt gebruikt om de prestaties van query's te versnellen. Het biedt snelle toegang tot tabelgegevens door het aantal gegevenssetpagina's te verminderen dat moet worden bezocht of gescand.

Specifieke query's die prestatiedegradatie hebben veroorzaakt, worden door deze detectie geïdentificeerd waarvoor het maken van indexen gunstig zou zijn voor de prestaties.

### <a name="troubleshooting"></a>Problemen oplossen

De query-query's voor query's voor de query's die zijn geïdentificeerd om de werkbelastingprestaties te beïnvloeden. U indexen voor deze query's maken. U deze query's ook optimaliseren of verwijderen als deze niet nodig zijn. Een goede prestatiepraktijk is om te voorkomen dat gegevens worden opgevraagd die u niet gebruikt.

> [!TIP]
> Wist u dat sql database ingebouwde intelligentie automatisch de best presterende indexen voor uw databases kan beheren?
>
> Voor continue prestatieoptimalisatie van SQL Database raden we u aan [sql database automatische tuning](sql-database-automatic-tuning.md)in te schakelen. Deze unieke functie van SQL Database ingebouwde intelligentie bewaakt continu uw SQL-database en stemt automatisch af en maakt indexen voor uw databases.
>

## <a name="new-query"></a>Nieuwe query

### <a name="what-is-happening"></a>Wat gebeurt er

Dit prestatiepatroon geeft aan dat een nieuwe query wordt gedetecteerd die slecht presteert en de prestaties van de werkbelasting beïnvloedt in vergelijking met de prestatiebasislijn van zeven dagen.

Het schrijven van een goed presterende query kan soms een uitdagende taak zijn. Zie [SQL-query's schrijven voor](https://msdn.microsoft.com/library/bb264565.aspx)meer informatie over het schrijven van query's. Zie [Queryafstemming](https://msdn.microsoft.com/library/ms176005.aspx)voor het optimaliseren van bestaande queryprestaties.

### <a name="troubleshooting"></a>Problemen oplossen

De diagnostische log uitgangen informatie tot twee nieuwe meest CPU-verbruikende query's, met inbegrip van hun query hashes. Omdat de gedetecteerde query van invloed is op de werkbelastingprestaties, u uw query optimaliseren. Goede praktijk is om alleen gegevens die u nodig hebt om te gebruiken op te halen. We raden ook aan om query's te gebruiken met een WHERE-clausule. We raden u ook aan complexe query's te vereenvoudigen en op te splitsen in kleinere query's. Een andere goede praktijk is om grote batchquery's op te splitsen in kleinere batchquery's. Het introduceren van indexen voor nieuwe query's is doorgaans een goede gewoonte om dit prestatieprobleem te beperken.

Overweeg [Azure SQL Database Query Performance Insight](sql-database-query-performance.md)te gebruiken.

## <a name="increased-wait-statistic"></a>Verhoogde wachtstatistiek

### <a name="what-is-happening"></a>Wat gebeurt er

Dit detecteerbare prestatiepatroon geeft een verslechtering van de prestaties van de werkbelasting aan waarbij slecht presterende query's worden geïdentificeerd in vergelijking met de afgelopen basislijn van de werkbelasting van zeven dagen.

In dit geval kan het systeem de slecht presterende query's niet classificeren onder andere standaard detecteerbare prestatiecategorieën, maar het heeft de wachtstatistiek gedetecteerd die verantwoordelijk is voor de regressie. Daarom beschouwt het hen als query's met *verhoogde wachtstatistieken*, waar de wachtstatistiek die verantwoordelijk is voor de regressie ook wordt blootgesteld.

### <a name="troubleshooting"></a>Problemen oplossen

De diagnostische log uitgangen informatie over verhoogde wachttijd details en query hashes van de getroffen query's.

Omdat het systeem de hoofdoorzaak voor de slecht presterende query's niet kon identificeren, is de diagnostische informatie een goed uitgangspunt voor handmatige probleemoplossing. U de prestaties van deze query's optimaliseren. Een goede gewoonte is om alleen gegevens die u moet gebruiken op te halen en complexe query's te vereenvoudigen en op te splitsen in kleinere query's.

Zie [Queryafstemming](https://msdn.microsoft.com/library/ms176005.aspx)voor meer informatie over het optimaliseren van de queryprestaties.

## <a name="tempdb-contention"></a>TempDB Stelling

### <a name="what-is-happening"></a>Wat gebeurt er

Dit detecteerbare prestatiepatroon geeft een databaseprestatievoorwaarde aan waarin een knelpunt van threads die toegang proberen te krijgen tot tempDB-resources bestaat. (Deze voorwaarde is niet gerelateerd aan IO.) Het typische scenario voor dit prestatieprobleem is honderden gelijktijdige query's die allemaal kleine tempDB-tabellen maken, gebruiken en vervolgens neerzetten. Het systeem ontdekte dat het aantal gelijktijdige query's met dezelfde tempDB-tabellen is toegenomen met voldoende statistische significantie om de databaseprestaties te beïnvloeden in vergelijking met de afgelopen zevendaagse prestatiebasislijn.

### <a name="troubleshooting"></a>Problemen oplossen

De diagnostische log uitgangen tempDB geschil details. U de informatie gebruiken als startpunt voor het oplossen van problemen. Er zijn twee dingen die u nastreven om dit soort stelling te verlichten en de doorvoer van de algehele werkbelasting te verhogen: U stoppen met het gebruik van de tijdelijke tabellen. U ook voor geheugen geoptimaliseerde tabellen gebruiken.

Zie [Inleiding tot voor geheugen geoptimaliseerde tabellen](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables)voor meer informatie.

## <a name="elastic-pool-dtu-shortage"></a>Elastiek zwembad DTU tekort

### <a name="what-is-happening"></a>Wat gebeurt er

Dit detecteerbare prestatiepatroon duidt op een verslechtering van de huidige prestaties van de databasewerkbelasting ten opzichte van de afgelopen basislijn van zeven dagen. Dit is te wijten aan het tekort aan beschikbare DTU's in de elastische pool van uw abonnement.

Resources in SQL Database worden meestal Aangeduid als [DTU-resources,](sql-database-purchase-models.md#dtu-based-purchasing-model)die bestaan uit een gemengde meting van CPU- en IO-bronnen (data- en transactielogboek IO). [Azure elastic pool resources](sql-database-elastic-pool.md) worden gebruikt als een pool van beschikbare eDTU-resources die worden gedeeld tussen meerdere databases voor schaaldoeleinden. Wanneer beschikbare eDTU-resources in uw elastische groep niet groot genoeg zijn om alle databases in de groep te ondersteunen, wordt een probleem met de prestaties van het tekort in de elastische groep gedetecteerd door het systeem.

### <a name="troubleshooting"></a>Problemen oplossen

De diagnostische log uitgangen informatie over de elastische pool, geeft een lijst van de top DTU-verbruikende databases, en biedt een percentage van de groep DTU gebruikt door de top-consumeren database.

Omdat deze prestatievoorwaarde is gerelateerd aan meerdere databases met dezelfde groep eDTU's in de elastische groep, richten de probleemoplossingsstappen zich op de bovenste DTU-verbruikende databases. U de werkbelasting in de topverbruikende databases verminderen, waaronder optimalisatie van de best verbruikende query's in die databases. U er ook voor zorgen dat u geen gegevens opvraagt die u niet gebruikt. Een andere benadering is het optimaliseren van toepassingen door gebruik te maken van de beste DTU-verbruikende databases en de workload opnieuw te verdelen over meerdere databases.

Als het niet mogelijk is om de huidige werkbelasting op uw belangrijkste DTU-verbruikende databases te verminderen en te optimaliseren, u overwegen de prijscategorie voor elastische zwembaden te verhogen. Een dergelijke toename resulteert in de toename van de beschikbare DTU's in de elastische pool.

## <a name="plan-regression"></a>Regressie plannen

### <a name="what-is-happening"></a>Wat gebeurt er

Dit detecteerbare prestatiepatroon geeft een voorwaarde aan waarin SQL Database een suboptimaal queryuitvoeringsplan gebruikt. Het suboptimale plan zorgt meestal voor een verhoogde queryuitvoering, wat leidt tot langere wachttijden voor de huidige en andere query's.

De SQL-database bepaalt het queryuitvoeringsplan met de minste kosten voor een queryuitvoering. Naarmate het type query's en workloads veranderen, zijn de bestaande plannen soms niet langer efficiënt of heeft SQL Database misschien geen goede beoordeling gemaakt. Als een kwestie van correctie, query uitvoeringsplannen kunnen handmatig worden gedwongen.

Dit detecteerbare prestatiepatroon combineert drie verschillende gevallen van planregressie: nieuwe planregressie, oude planregressie en bestaande plannen veranderde werkbelasting. Het specifieke type planregressie dat heeft plaatsgevonden, wordt vermeld in de eigenschap *details* in het diagnoselogboek.

De nieuwe voorwaarde voor planregressie verwijst naar een status waarin SQL Database begint met het uitvoeren van een nieuw queryuitvoeringsplan dat niet zo efficiënt is als het oude plan. De oude status van planregressie verwijst naar de status wanneer SQL Database overschakelt van het gebruik van een nieuw, efficiënter plan naar het oude plan, dat niet zo efficiënt is als het nieuwe plan. De bestaande plannen veranderd werklast regressie verwijst naar de staat waarin de oude en de nieuwe plannen voortdurend afwisselen, met de balans gaat meer in de richting van de slecht presterende plan.

Zie [Wat is planregressie in SQL Server voor](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../what-is-plan-regression-in-sql-server/)meer informatie over planregressies? .

### <a name="troubleshooting"></a>Problemen oplossen

De diagnostische logboek uitgangen van de query hashes, goede plan-ID, slechte plan-ID, en query-id's. U deze informatie gebruiken als basis voor het oplossen van problemen.

U analyseren welk plan beter presteert voor uw specifieke query's die u identificeren met de verstrekte queryhashes. Nadat u hebt bepaald welk abonnement beter werkt voor uw query's, u het handmatig forceren.

Zie Meer informatie voor meer informatie [over hoe SQL Server planregressies voorkomt.](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../you-shall-not-regress-how-sql-server-2017-prevents-plan-regressions/)

> [!TIP]
> Wist u dat sql database ingebouwde intelligentie automatisch de best presterende queryuitvoeringsplannen voor uw databases kan beheren?
>
> Voor continue prestatieoptimalisatie van SQL Database raden we u aan [sql database automatische tuning](sql-database-automatic-tuning.md)in te schakelen. Deze unieke functie van SQL Database ingebouwde intelligentie bewaakt continu uw SQL-database en stemt automatisch af en maakt de best presterende queryuitvoeringsplannen voor uw databases.
>

## <a name="database-scoped-configuration-value-change"></a>Wijziging van configuratiewaarde met databasebereik

### <a name="what-is-happening"></a>Wat gebeurt er

Dit detecteerbare prestatiepatroon geeft een voorwaarde aan waarin een wijziging in de configuratie met databasescopede prestatieregressie veroorzaakt die wordt gedetecteerd in vergelijking met het probleem van de afgelopen zeven dagen databasewerk. Dit patroon geeft aan dat een recente wijziging in de database-scoped configuratie lijkt niet gunstig te zijn voor uw database prestaties.

Wijzigingen in databasescopen kunnen voor elke afzonderlijke database worden ingesteld. Deze configuratie wordt per geval gebruikt om de individuele prestaties van uw database te optimaliseren. Voor elke afzonderlijke database kunnen de volgende opties worden geconfigureerd: MAXDOP, LEGACY_CARDINALITY_ESTIMATION, PARAMETER_SNIFFING, QUERY_OPTIMIZER_HOTFIXES en CLEAR PROCEDURE_CACHE.

### <a name="troubleshooting"></a>Problemen oplossen

De configuratiewijzigingen met databasescopen die onlangs zijn doorgevoerd en die prestatiedegradatie hebben veroorzaakt in vergelijking met het vorige werkbelastinggedrag van zeven dagen. U de configuratiewijzigingen terugdraaien naar de vorige waarden. U ook waarde afstemmen op waarde totdat het gewenste prestatieniveau is bereikt. U configuratiewaarden voor databasescope's kopiëren uit een vergelijkbare database met bevredigende prestaties. Als u de prestaties niet oplossen, gaat u terug naar de standaardwaarden van SQL Database en probeert u vanaf deze basislijn te verfijnen.

Zie Configuratie met databasescoped wijzigen [(Transact-SQL)](https://msdn.microsoft.com/library/mt629158.aspx)voor meer informatie over het optimaliseren van de configuratie met databasescopen en de syntaxis van T-SQL.

## <a name="slow-client"></a>Trage client

### <a name="what-is-happening"></a>Wat gebeurt er

Dit detecteerbare prestatiepatroon geeft een voorwaarde aan waarin de client die de SQL-database gebruikt, de uitvoer uit de database niet zo snel kan gebruiken als de database de resultaten verzendt. Omdat SQL Database geen resultaten van de uitgevoerde query's opslaat in een buffer, vertraagt deze en wacht het tot de client de verzonden query-uitvoer verbruikt voordat deze wordt voortgezet. Deze voorwaarde kan ook gerelateerd zijn aan een netwerk dat niet snel genoeg is om uitvoer van de SQL-database naar de verbruikende client te verzenden.

Deze voorwaarde wordt alleen gegenereerd als een prestatieregressie wordt gedetecteerd in vergelijking met het werkgedrag van de afgelopen zeven dagen databasewerk. Dit prestatieprobleem wordt alleen gedetecteerd als er een statistisch significante prestatieverslechtering optreedt in vergelijking met eerder prestatiegedrag.

### <a name="troubleshooting"></a>Problemen oplossen

Dit detecteerbare prestatiepatroon geeft een client-side conditie aan. Probleemoplossing is vereist bij de client-side applicatie of client-side netwerk. De diagnostische log uitgangen van de query hashes en wachttijden die lijken te wachten het meest voor de client om ze te consumeren in de afgelopen twee uur. U deze informatie gebruiken als basis voor het oplossen van problemen.

U de prestaties van uw toepassing optimaliseren voor het verbruik van deze query's. U ook mogelijke netwerklatentieproblemen overwegen. Omdat het probleem met de prestatiedegradatie is gebaseerd op wijzigingen in de laatste prestatiebasislijn van zeven dagen, u onderzoeken of recente wijzigingen in de toepassing of netwerkvoorwaarde deze prestatieregressiegebeurtenis hebben veroorzaakt.

## <a name="pricing-tier-downgrade"></a>Downgrade van prijzen

### <a name="what-is-happening"></a>Wat gebeurt er

Dit detecteerbare prestatiepatroon geeft een voorwaarde aan waarin de prijscategorie van uw SQL Database-abonnement is gedegradeerd. Vanwege de vermindering van resources (DTOU's) die beschikbaar zijn voor de database, detecteerde het systeem een daling van de huidige databaseprestaties ten opzichte van de afgelopen basislijn van zeven dagen.

Bovendien kan er een voorwaarde zijn waarin de prijslaag van uw SQL Database-abonnement is gedegradeerd en vervolgens binnen een korte periode is geüpgraded naar een hogere laag. Detectie van deze tijdelijke prestatiedegradatie wordt uitgevoerd in het detailgedeelte van het diagnoselogboek als een downgrade en upgrade van de prijslaag.

### <a name="troubleshooting"></a>Problemen oplossen

Als u uw prijscategorie hebt verlaagd, en dus de DTU's die beschikbaar zijn voor SQL Database, en u tevreden bent met de prestaties, hoeft u niets te doen. Als u uw prijscategorie hebt verlaagd en u ontevreden bent over de prestaties van uw SQL-database, u de werkbelasting van uw database verlagen of overwegen de prijslaag naar een hoger niveau te tillen.

## <a name="recommended-troubleshooting-flow"></a>Aanbevolen stroom voor probleemoplossing

 Volg het stroomdiagram voor een aanbevolen aanpak om prestatieproblemen op te lossen met behulp van Intelligent Insights.

Krijg toegang tot Intelligente inzichten via de Azure-portal door naar Azure SQL Analytics te gaan. Probeer de binnenkomende prestatiewaarschuwing te vinden en selecteer deze. Identificeer wat er gebeurt op de detectiepagina. Let op de meegeleverde hoofdoorzaakanalyse van het probleem, querytekst, querytijdtrends en incidentevolutie. Probeer het probleem op te lossen met de aanbeveling Intelligent Insights om het prestatieprobleem te beperken.

[![Stroomdiagram oplossen](./media/sql-database-intelligent-insights/intelligent-insights-troubleshooting-flowchart.png)](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/intelligent-insight/Troubleshoot%20Azure%20SQL%20Database%20performance%20issues%20using%20Intelligent%20Insight.pdf)

> [!TIP]
> Selecteer het stroomdiagram om een PDF-versie te downloaden.

Intelligent Insights heeft meestal een uur tijd nodig om de hoofdoorzaakanalyse van het prestatieprobleem uit te voeren. Als u uw probleem niet vinden in Intelligente inzichten en het is van cruciaal belang voor u, gebruikt u de Query Store om handmatig de hoofdoorzaak van het prestatieprobleem te identificeren. (Meestal zijn deze problemen minder dan een uur oud.) Zie [Prestaties controleren met behulp van het queryarchief](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- Leer [Intelligent Insights](sql-database-intelligent-insights.md) concepten.
- Gebruik het [logboek voor prestatiediagnostiek van Intelligent Insights Azure SQL Database](sql-database-intelligent-insights-use-diagnostics-log.md).
- Azure [SQL-database bewaken met Azure SQL Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql).
- Leer [logboekgegevens verzamelen en gebruiken uit uw Azure-bronnen.](../azure-monitor/platform/platform-logs-overview.md)
