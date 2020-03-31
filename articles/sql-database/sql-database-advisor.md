---
title: Prestatieaanbevelingen voor databaseadviseur voor afzonderlijke en samengevoegde databases
description: Azure SQL Database bevat aanbevelingen voor afzonderlijke en gepoolde databases die de queryprestaties in Azure SQL-database kunnen verbeteren.
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
ms.openlocfilehash: bd7473813722fd413947535413b98d493058634a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79214140"
---
# <a name="database-advisor-performance-recommendations-for-single-and-pooled-databases"></a>Prestatieaanbevelingen voor Database Advisor voor afzonderlijke en samengevoegde databases

Azure SQL Database leert en past zich aan met uw toepassing. Voor afzonderlijke en gepoolde databases heeft SQL Database een aantal databaseadviseurs die aangepaste aanbevelingen bieden waarmee u de prestaties maximaliseren. Deze databaseadviseurs beoordelen en analyseren voortdurend de gebruiksgeschiedenis en geven aanbevelingen op basis van werkbelastingpatronen die de prestaties helpen verbeteren.

## <a name="performance-overview"></a>Prestatieoverzicht

Prestatieoverzicht biedt een overzicht van uw database en helpt u bij het afstemmen van prestaties en het oplossen van problemen.

![Prestatieoverzicht voor Azure SQL Database](./media/sql-database-performance/performance-overview-annotated.png)

- De tegel **Aanbevelingen** bevat een uitsplitsing van de aanbevelingen voor het afstemmen van uw database (top drie aanbevelingen worden weergegeven als er meer zijn). Als u op deze tegel klikt, gaat u naar **[prestatieaanbevelingsopties](sql-database-advisor-portal.md#viewing-recommendations)**.
- De tegel **Tuning-activiteit** biedt een overzicht van de lopende en voltooide tuningacties voor uw database, zodat u snel inzicht krijgt in de geschiedenis van de tuningactiviteit. Als u op deze tegel klikt, gaat u naar de volledige weergave van de stemgeschiedenis voor uw database.
- De tegel **Automatisch afstemmen** toont de **[automatische afstemmingsconfiguratie](sql-database-automatic-tuning-enable.md)** voor uw database (stemopties die automatisch worden toegepast op uw database). Als u op deze tegel klikt, wordt het dialoogvenster automatiseringsconfiguratie geopend.
- De tegel **Databasequery's** toont de samenvatting van de queryprestaties voor uw database (algemeen DTU-gebruik en topquery's voor resourceverbruiken). Als u op deze tegel klikt, gaat u naar **[Query Performance Insight](sql-database-query-performance.md)**.

## <a name="performance-recommendation-options"></a>Opties voor prestatieaanbeveling

Prestatieaanbevelingsopties die beschikbaar zijn voor afzonderlijke en samengevoegde databases in Azure SQL Database zijn:

| Prestatieaanbeveling | Ondersteuning voor één database en gepoolde database | Ondersteuning voor instantiedatabase |
| :----------------------------- | ----- | ----- |
| **Indexaanbevelingen maken** - Beveelt het maken van indexen aan die de prestaties van uw werkbelasting kunnen verbeteren. | Ja | Nee |
| **Drop index aanbevelingen** - Beveelt verwijdering van overbodige en dubbele indexen dagelijks, met uitzondering van unieke indexen, en indexen die niet werden gebruikt voor een lange tijd (>90 dagen). Houd er rekening mee dat deze optie niet compatibel is met toepassingen met behulp van partitieschakelen en indexhints. Het laten vallen van ongebruikte indexen wordt niet ondersteund voor Premium- en Business Critical-servicelagen. | Ja | Nee |
| **Aanbevelingen voor query's parameteriseren (voorbeeld)** - Beveelt gedwongen parameterisatie aan in gevallen waarin u een of meer query's hebt die voortdurend opnieuw worden gecompileerd, maar uiteindelijk met hetzelfde queryuitvoeringsplan terechtkomen. | Ja | Nee |
| **Aanbevelingen voor schemaproblemen oplossen (voorbeeld)** - Aanbevelingen voor schemacorrectie worden weergegeven wanneer de SQL Database-service een afwijking opmerkt in het aantal schemagerelateerde SQL-fouten dat zich in uw SQL-database voordoet. Microsoft is momenteel deprecating "Fix schema probleem" aanbevelingen. | Ja | Nee |

![Prestatieaanbevelingen voor Azure SQL Database](./media/sql-database-performance/performance-recommendations-annotated.png)

Zie [Aanbevelingen toepassen](sql-database-advisor-portal.md#applying-recommendations)om prestatieaanbevelingen toe te passen. Zie [Uitvoeren van](sql-database-advisor-portal.md#monitoring-operations)toezicht op de status van aanbevelingen als u de status van aanbevelingen wilt weergeven.

U ook de volledige geschiedenis van tuningacties vinden die in het verleden zijn toegepast.

## <a name="create-index-recommendations"></a>Indexaanbevelingen maken

SQL Database controleert continu de query's die worden uitgevoerd en identificeert de indexen die de prestaties kunnen verbeteren. Nadat er voldoende vertrouwen is dat een bepaalde index ontbreekt, wordt een nieuwe **aanbeveling voor de index maken** gemaakt.

Azure SQL Database bouwt vertrouwen op door de prestatiewinst te schatten die de index door de tijd zou brengen. Afhankelijk van de geschatte prestatiewinst worden aanbevelingen gecategoriseerd als hoog, gemiddeld of laag.

Indexen die worden gemaakt met behulp van aanbevelingen worden altijd gemarkeerd als automatisch gemaakte indexen. U zien welke indexen automatisch worden gemaakt door te kijken naar de [sys.indexes-weergave](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-indexes-transact-sql). Automatisch gemaakte indexen blokkeren geen OPDRACHTEN voor ALTER/RENAME.

Als u de kolom met een automatisch gemaakte index eroverheen probeert te laten vallen, wordt de opdracht doorgegeven. De automatisch gemaakte index wordt ook met de opdracht verwijderd. Reguliere indexen blokkeren de opdracht ALTER/RENAME op kolommen die zijn geïndexeerd.

Nadat de aanbeveling voor het maken van index is toegepast, vergelijkt Azure SQL Database de prestaties van de query's met de basislijnprestaties. Als de nieuwe index de prestaties verbetert, wordt de aanbeveling gemarkeerd als geslaagd en is het impactrapport beschikbaar. Als de index de prestaties niet heeft verbeterd, wordt deze automatisch teruggedraaid. SQL Database gebruikt dit proces om ervoor te zorgen dat aanbevelingen de prestaties van de database verbeteren.

Elke **indexaanbeveling maken** heeft een back-offbeleid dat het toepassen van de aanbeveling niet toestaat als het resourcegebruik van een database of groep hoog is. Het back-off beleid houdt rekening met CPU, Data IO, Log IO en beschikbare opslag.

Als CPU, data IO of log IO hoger is dan 80% in de vorige 30 minuten, wordt de aanbeveling voor de indexering van het maken uitgesteld. Als de beschikbare opslag lager is dan 10% nadat de index is gemaakt, gaat de aanbeveling in een foutstatus. Als, na een paar dagen, automatische tuning nog steeds van mening is dat de index gunstig zou zijn, begint het proces opnieuw.

Dit proces wordt herhaald totdat er voldoende opslagruimte beschikbaar is om een index te maken, of totdat de index niet meer als gunstig wordt beschouwd.

## <a name="drop-index-recommendations"></a>Indexaanbevelingen laten vallen

Naast het detecteren van ontbrekende indexen, analyseert SQL Database continu de prestaties van bestaande indexen. Als een index niet wordt gebruikt, raadt Azure SQL Database aan deze te laten vallen. Het laten vallen van een index wordt aanbevolen in twee gevallen:

- De index is een duplicaat van een andere index (dezelfde geïndexeerde en opgenomen kolom, partitieschema en filters).
- De index is gedurende een langere periode (93 dagen) niet gebruikt.

Drop index aanbevelingen gaan ook door de verificatie na implementatie. Als de prestaties verbeteren, is het impactrapport beschikbaar. Als de prestaties degradeeren, wordt de aanbeveling teruggedraaid.

## <a name="parameterize-queries-recommendations-preview"></a>Aanbevelingen voor query's parameteriseren (voorbeeld)

*Aanbevelingen voor parameterquery's* worden weergegeven wanneer u een of meer query's hebt die voortdurend opnieuw worden gecompileerd, maar uiteindelijk met hetzelfde queryuitvoeringsplan terechtkomen. Deze voorwaarde creëert een mogelijkheid om geforceerde parameterisatie toe te passen. Gedwongen parameterisering maakt het op zijn beurt mogelijk om queryplannen in de cache te zetten en in de toekomst opnieuw te gebruiken, wat de prestaties verbetert en het gebruik van resources vermindert.

Elke query die wordt uitgegeven tegen SQL Server moet in eerste instantie worden gecompileerd om een uitvoeringsplan te genereren. Elk gegenereerd plan wordt toegevoegd aan de plancache. Latere uitvoeringen van dezelfde query kunnen dit plan opnieuw gebruiken vanuit de cache, waardoor extra compilatie overbodig wordt.

Query's met niet-geparameteriseerde waarden kunnen leiden tot prestatieoverhead omdat het uitvoeringsplan telkens opnieuw wordt gecompileerd wanneer de niet-geparameteriseerde waarden verschillend zijn. In veel gevallen genereren dezelfde query's met verschillende parameterwaarden dezelfde uitvoeringsplannen. Deze plannen worden echter nog steeds afzonderlijk toegevoegd aan de plancache.

Het proces van het opnieuw samenstellen van uitvoeringsplannen maakt gebruik van databasebronnen, verhoogt de duur van de query en overloopt de plancache. Deze gebeurtenissen zorgen er op hun beurt voor dat plannen uit de cache worden verwijderd. Dit SQL Server-gedrag kan worden gewijzigd door de geforceerde parameterisatieoptie in de database in te stellen.

Om u te helpen de impact van deze aanbeveling in te schatten, krijgt u een vergelijking tussen het werkelijke CPU-gebruik en het verwachte CPU-gebruik (alsof de aanbeveling is toegepast). Deze aanbeveling kan u helpen cpu-besparingen te behalen. Het kan u ook helpen de queryduur en overhead voor de plancache te verlagen, wat betekent dat meer van de plannen in de cache kunnen blijven en opnieuw kunnen worden gebruikt. U deze aanbeveling snel toepassen door de opdracht **Toepassen te** selecteren.

Nadat u deze aanbeveling hebt toegepast, maakt het gedwongen parameterisatie binnen enkele minuten in uw database mogelijk. Het begint het monitoringproces, dat ongeveer 24 uur duurt. Na deze periode u het validatierapport bekijken. Dit rapport toont het CPU-gebruik van uw database 24 uur voor en nadat de aanbeveling is toegepast. SQL Database Advisor heeft een veiligheidsmechanisme dat de toegepaste aanbeveling automatisch terugdraait als prestatieregressie is gedetecteerd.

## <a name="fix-schema-issues-recommendations-preview"></a>Aanbevelingen voor schemaproblemen oplossen (voorbeeld)

> [!IMPORTANT]
> Microsoft is momenteel deprecating "Fix schema probleem" aanbevelingen. We raden u aan [Intelligent Insights](sql-database-intelligent-insights.md) te gebruiken om problemen met de prestaties van uw database te controleren, inclusief schemaproblemen die de aanbevelingen voor het probleem van het schema eerder hebben behandeld.

**Aanbevelingen voor schemaproblemen worden** weergegeven wanneer de SQL Database-service een afwijking opmerkt in het aantal schemagerelateerde SQL-fouten dat zich in uw SQL-database voordoet. Deze aanbeveling wordt meestal weergegeven wanneer uw database binnen een uur meerdere schemagerelateerde fouten (ongeldige kolomnaam, ongeldige objectnaam, enzovoort) tegenkomt.

'Schemaproblemen' zijn een klasse met syntaxisfouten in SQL Server. Deze treden op wanneer de definitie van de SQL-query en de definitie van het databaseschema niet zijn uitgelijnd. Een van de kolommen die door de query worden verwacht, ontbreekt mogelijk in de doeltabel of vice versa.

De aanbeveling 'Schemaprobleem oplossen' wordt weergegeven wanneer de Azure SQL Database-service een afwijking opmerkt in het aantal schemagerelateerde SQL-fouten dat zich in uw SQL-database voordoet. In de volgende tabel worden de fouten weergegeven die verband houden met schemaproblemen:

| SQL-foutcode | Bericht |
| --- | --- |
| 201 |Procedure of functie '*' verwacht parameter '*' , die niet werd geleverd. |
| 207 |Ongeldige kolomnaam '*'. |
| 208 |Ongeldige objectnaam '*'. |
| 213 |Kolomnaam of aantal opgegeven waarden komt niet overeen met de tabeldefinitie. |
| 2812 |Kon geen opgeslagen procedure '*' vinden. |
| 8144 |Procedure of functie * heeft te veel argumenten opgegeven. |

## <a name="custom-applications"></a>Aangepaste toepassingen

Ontwikkelaars kunnen overwegen aangepaste toepassingen te ontwikkelen met prestatieaanbevelingen voor Azure SQL Database. Alle aanbevelingen in de portal voor een database zijn toegankelijk via [Get-AzSqlDatabaseRecommendedAction](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserecommendedaction) API.

## <a name="next-steps"></a>Volgende stappen

- Zie Automatische afstemming van Azure [SQL Database](sql-database-automatic-tuning.md)voor meer informatie over het automatisch afstemmen van database-indexen en queryuitvoeringsplannen.
- Zie [Azure SQL Intelligent Insights](sql-database-intelligent-insights.md)voor meer informatie over het automatisch monitoren van databaseprestaties met geautomatiseerde diagnostiek en analyse van de hoofdoorzaak van prestatieproblemen.
- Zie [Queryprestatiestatistieken](sql-database-query-performance.md) voor meer informatie over en bekijk de prestatie-impact van uw topquery's.
