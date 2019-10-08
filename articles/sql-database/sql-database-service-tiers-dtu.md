---
title: 'Azure SQL Database Service lagen: op DTU gebaseerd aankoop model | Microsoft Docs'
description: Meer informatie over service lagen in het op DTU gebaseerde aankoop model voor enkelvoudige en gepoolde data bases voor de berekenings-en opslag grootte.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 09/06/2019
ms.openlocfilehash: e735d8832912f5b512b7cfe015ca47efbe641da7
ms.sourcegitcommit: be344deef6b37661e2c496f75a6cf14f805d7381
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/07/2019
ms.locfileid: "72000440"
---
# <a name="service-tiers-in-the-dtu-based-purchase-model"></a>Service lagen in het op DTU gebaseerde aankoop model

Service lagen in het op DTU gebaseerde aankoop model worden gedifferentieerd met een bereik van reken grootten met een vaste hoeveelheid inbegrepen opslag, een vaste Bewaar periode voor back-ups en vaste prijzen. Alle service lagen in het op DTU gebaseerde aankoop model bieden flexibiliteit bij het wijzigen van de reken grootte met minimale [downtime](https://azure.microsoft.com/support/legal/sla/sql-database/v1_2/). Er is echter een switch over een periode waarin de verbinding gedurende korte tijd naar de data base verloren gaat, wat kan worden verholpen met de logica voor opnieuw proberen. Individuele databases en elastische pools worden gefactureerd per uur op basis van de servicelaag en compute-grootte.

> [!IMPORTANT]
> SQL Database beheerde instantie biedt geen ondersteuning voor een op DTU gebaseerd aankoop model. Zie voor meer informatie, [Azure SQL Database Managed Instance](sql-database-managed-instance.md).
> [!NOTE]
> Zie voor meer informatie over Servicelagen op vCore gebaseerde [vCore-gebaseerde service-lagen](sql-database-service-tiers-vcore.md). Zie voor meer informatie over Servicelagen op basis van DTU en vCore-gebaseerde service-lagen differentiëren [modellen aanschaffen van Azure SQL Database](sql-database-purchase-models.md).

## <a name="compare-the-dtu-based-service-tiers"></a>De DTU-gebaseerde service-lagen vergelijken

Een servicelaag kiezen afhankelijk is voornamelijk van zakelijke continuïteit-, opslag- en prestatievereisten.

||Basic|Standard|Premium|
| :-- | --: |--:| --:|
|Specifieke workload|Ontwikkeling en productie|Ontwikkeling en productie|Ontwikkeling en productie|
|SLA voor actieve tijdsduur|99,99%|99,99%|99,99%|
|Maximale retentie van back-ups|7 dagen|35 dagen|35 dagen|
|CPU|Laag|Laag, Gemiddeld, hoog|Gemiddeld, hoog|
|I/o-doorvoer (bij benadering) |1-5 IOPS per DTU| 1-5 IOPS per DTU | 25 IOPS per DTU|
|I/o-latentie (bij benadering)|5 ms (lezen), 10 ms (schrijven)|5 ms (lezen), 10 ms (schrijven)|2 ms (lezen/schrijven)|
|Columnstore-indexering |N/A|S3 en hoger|Ondersteund|
|In-memory OLTP|N/A|N/A|Ondersteund|
|||||

> [!NOTE]
> In combi natie met een gratis Azure-account kunt u Azure een gratis Azure-SQL database in de laag basis service krijgen. Zie voor meer informatie, [maken van een beheerde clouddatabase met uw gratis Azure-account](https://azure.microsoft.com/free/services/sql-database/).

## <a name="single-database-dtu-and-storage-limits"></a>DTU- en opslaglimieten van individuele database

COMPUTE-grootten worden uitgedrukt in termen van Database Transaction Units (dtu's) voor individuele databases en elastische Database Transaction Units (edtu's) voor elastische pools. Zie voor meer informatie over dtu's en Edtu's [DTU gebaseerde aankoopmodel](sql-database-purchase-models.md#dtu-based-purchasing-model)?

||Basic|Standard|Premium|
| :-- | --: | --: | --: |
| Maximale opslagruimte | 2 GB | 1 TB | 4 TB  |
| Maximum aantal dtu 's | 5 | 3000 | 4000 | 
|||||

> [!IMPORTANT]
> In sommige gevallen is het wellicht voor het verkleinen van een database voor het vrijmaken van ongebruikte ruimte. Zie voor meer informatie, [bestandsruimte in Azure SQL Database beheren](sql-database-file-space-management.md).

## <a name="elastic-pool-edtu-storage-and-pooled-database-limits"></a>Elastische pool-eDTU-, opslag- en limieten voor gegroepeerde databases

| | **Basic** | **Standard** | **Premium** |
| :-- | --: | --: | --: |
| Maximale opslagruimte per database  | 2 GB | 1 TB | 1 TB |
| Maximale opslagruimte per groep | 156 GB | 4 TB | 4 TB |
| Maximum aantal edtu's per database | 5 | 3000 | 4000 |
| Maximum aantal edtu's per groep | 1600 | 3000 | 4000 |
| Maximum aantal databases per pool | 500  | 500 | 100 |
|||||

> [!IMPORTANT]
> Voor de Premium-laag is er meer dan 1 TB aan opslagruimte beschikbaar in alle regio's, met uitzondering van: China - oost, China - noord, Duitsland - centraal, Duitsland - noordoost, US - west-centraal, US - DoD-regio's en US Government - centraal. In deze regio’s is de maximale opslagruimte in de Premium-laag beperkt tot 1 TB.  Raadpleeg [P11-P15 huidige beperkingen](sql-database-single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb) voor meer informatie.  
> [!IMPORTANT]
> In sommige gevallen is het wellicht voor het verkleinen van een database voor het vrijmaken van ongebruikte ruimte. Zie [Bestands ruimte beheren in Azure SQL database](sql-database-file-space-management.md)voor meer informatie.

## <a name="dtu-benchmark"></a>DTU-Benchmark

Fysieke kenmerken (CPU, geheugen, i/o) die is gekoppeld aan elke meting DTU gekalibreerd met behulp van een benchmark die echte database-workload wordt gesimuleerd.

### <a name="correlating-benchmark-results-to-real-world-database-performance"></a>Benchmarkresultaten op de prestaties van de echte wereld database correleren

Het is belangrijk om te begrijpen dat alle benchmarks representatieve en indicatieve slechts zijn. De transactietarieven bereikt met de benchmark-toepassing zich niet hetzelfde als die kunnen worden behaald met andere toepassingen. De benchmark bestaat uit een verzameling van andere transactie typen worden uitgevoerd op basis van een schema met een scala aan tabellen en gegevenstypen. Terwijl de benchmark de dezelfde basisbewerkingen die gemeenschappelijk voor alle OLTP-werkbelastingen oefent zijn, vertegenwoordigt een specifieke klasse van de database of een toepassing niet meer. Het doel van de benchmark-is voor een redelijke handleiding voor de relatieve prestaties van een database die kan worden verwacht wanneer u omhoog of omlaag schalen tussen compute-grootten. Verschillende combinaties van werkbelastingen optreden en reageren op verschillende manieren in werkelijkheid databases van verschillende grootte en complexiteit zijn. Bijvoorbeeld, een i/o-intensieve toepassingen sneller i/o-drempelwaarden kan bereikt of een toepassing CPU-intensieve CPU-limieten sneller bereikt. Er is geen garantie dat een bepaalde database wordt geschaald op dezelfde manier als u de benchmark onder toenemende belasting.

De benchmark- en de methoden worden hieronder in detail beschreven.

### <a name="benchmark-summary"></a>Benchmark-overzicht

De benchmark meet de prestaties van een combi natie van basis database bewerkingen die het vaakst voor komen in workloads voor online transactie verwerking (OLTP). Hoewel de benchmark is ontworpen met cloudcomputing in gedachten, het databaseschema, invullen van de gegevens en transacties zijn ontworpen voor breed representatief zijn voor de basiselementen die het meest gebruikt in een OLTP-workloads.

### <a name="schema"></a>Schema

Het schema is ontworpen voor onvoldoende diversiteit en complexiteit voor de ondersteuning van een breed scala aan bewerkingen hebben. De benchmark wordt uitgevoerd op basis van een database die bestaat uit zes tabellen. De tabellen kunnen worden onderverdeeld in drie categorieën: vaste grootte, schalen en groeit. Er zijn twee vaste grootte tabellen. drie vergroten/verkleinen tabellen. en één groeiende tabel. Vaste grootte tabellen hebben een constant aantal rijen. Vergroten/verkleinen tabellen hebben een kardinaliteit die evenredig is met de prestaties van de database is, maar niet wijzigen tijdens de benchmark. Het formaat van de groeiende tabel wordt aangepast als een tabel vergroten/verkleinen op de eerste keer wordt geladen, maar de kardinaliteit wijzigingen tijdens het uitvoeren van de benchmark rijen zijn ingevoegd en verwijderd.

Het schema bevat een combinatie van gegevenstypen, inclusief geheel getal, numerieke tekens en datum/tijd. Het schema bevat primaire en secundaire sleutels, maar geen refererende sleutels - dat wil zeggen, er zijn geen beperkingen voor referentiële integriteit tussen tabellen.

De gegevens voor de initiële database wordt gegenereerd door een programma van gegevens genereren. Geheel getal zijn en numerieke gegevens wordt gegenereerd met verschillende strategieën gehanteerd. In sommige gevallen worden waarden willekeurig verdeeld over een bereik. In andere gevallen wordt een set waarden is willekeurig gepermuteerde om ervoor te zorgen dat een specifieke-distributie die wordt onderhouden. Tekstvelden zijn gegenereerd op basis van een lijst met woorden realistische uitziende gegevens produceren.

De database wordt de grootte op basis van een 'schaalfactor'. De schaalfactor (afgekort als SF) bepaalt de kardinaliteit van de schaal en tabellen groeit. Zoals hieronder wordt beschreven in de sectie gebruikers en Pacing, de grootte van de database, het aantal gebruikers en de maximale prestaties, al naar gelang van elkaar schalen.

### <a name="transactions"></a>Transacties

De werkbelasting bestaat uit negen transactietypen, zoals weergegeven in de onderstaande tabel. Elke transactie is ontworpen voor het markeren van een bepaalde set kenmerken in de database-engine en systeem hardware, met een hoog contrast uit de andere transacties. Deze aanpak maakt het eenvoudiger om de impact van de verschillende onderdelen voor de algehele prestaties vast te stellen. De transactie "Lezen zware" levert bijvoorbeeld een groot aantal leesbewerkingen van schijf.

| Transactietype | Description |
| --- | --- |
| Lite lezen |SELECTEER; in het geheugen; alleen-lezen |
| Gemiddeld lezen |SELECTEER; voornamelijk in het geheugen; alleen-lezen |
| Zware lezen |SELECTEER; meestal niet in het geheugen; alleen-lezen |
| Update Lite |BIJWERKEN. in het geheugen; lezen / schrijven |
| Zware bijwerken |BIJWERKEN. meestal niet in het geheugen; lezen / schrijven |
| Lite invoegen |INVOEGEN. in het geheugen; lezen / schrijven |
| Zware invoegen |INVOEGEN. meestal niet in het geheugen; lezen / schrijven |
| Verwijderen |VERWIJDEREN; combinatie van in het geheugen en niet in het geheugen; lezen / schrijven |
| CPU-intensief |SELECTEER; in het geheugen; relatief intensief CPU-belasting; alleen-lezen |

### <a name="workload-mix"></a>Combinatie van de werkbelasting

Transacties worden willekeurig geselecteerd uit een gewogen distributie met de volgende algemene combinatie. De algehele mix heeft een verhouding lezen/schrijven van ongeveer 2:1.

| Transactietype | % van de combinatie |
| --- | --- |
| Lite lezen |35 |
| Gemiddeld lezen |20 |
| Zware lezen |5 |
| Update Lite |20 |
| Zware bijwerken |3 |
| Lite invoegen |3 |
| Zware invoegen |2 |
| Verwijderen |2 |
| CPU-intensief |10 |

### <a name="users-and-pacing"></a>Gebruikers en pacing

De benchmark-werkbelasting wordt aangestuurd vanuit een hulpprogramma waarmee transacties worden verzonden via een reeks verbindingen om te simuleren dat het gedrag van een aantal gelijktijdige gebruikers. Hoewel alle verbindingen en transacties machine gegenereerd zijn, voor het gemak verwijzen we naar deze verbindingen als 'gebruikers'. Hoewel elke gebruiker onafhankelijk van alle andere gebruikers werkt, voert u alle gebruikers de dezelfde cyclus van stappen die hieronder wordt weergegeven:

1. Een databaseverbinding tot stand brengen.
2. Herhalen totdat een signaal ontvangen om af te sluiten:
   - Selecteer een transactie in willekeurige volgorde (uit een gewogen distributie).
   - De geselecteerde transactie uitvoeren en de reactietijd meten.
   - Wachten op een pacing vertraging.
3. Sluit de verbinding met de database.
4. Afsluiten.

De pacing vertraging (in stap 2c) willekeurig is geselecteerd, maar met een distributie die een gemiddelde van 1.0 seconde heeft. Elke gebruiker kan dus gemiddeld genereren voor maximaal één transactie per seconde.

### <a name="scaling-rules"></a>Regels voor vergroten/verkleinen

Het aantal gebruikers wordt bepaald door de grootte van de database (in eenheden van de schaal van meerdere factoren). Er is een gebruiker voor elke vijf schaalfactor eenheden. Vanwege de pacing vertraging, één gebruiker gemiddeld hoogstens één transactie per seconde genereren.

Bijvoorbeeld, een scale-factor van 500 (SF = 500) database heeft 100 gebruikers en kunnen maar liefst maximaal 100 TPS. Om een hogere TPS vereist tarief meer gebruikers en een grotere database.

### <a name="measurement-duration"></a>Duur van de meting

Een geldige benchmark uitvoeren vereist een stabiele status meting duur van ten minste één uur.

### <a name="metrics"></a>Metrische gegevens

De belangrijkste metrische gegevens in de benchmark zijn doorvoer en reactietijd.

- Doorvoer is de maateenheid voor essentiële prestaties in de benchmark. Doorvoer wordt gerapporteerd transacties per van-tijdseenheid, alle transactietypen tellen.
- Reactietijd is een maateenheid voor voorspelbare prestaties. De beperking van de tijd reactie is afhankelijk van de klasse van de service, met hogere klassen van de service met een strengere antwoord tijd nodig is, zoals hieronder wordt weergegeven.

| Klasse van Service | Meten van doorvoer | Antwoord tijd nodig is |
| --- | --- | --- |
| Premium |Transacties per seconde |95e percentiel op 0,5 seconden |
| Standard |Transacties per minuut |90e percentiel op 1.0 seconden |
| Basic |Transacties per uur |80e percentiel op 2.0 seconden |

## <a name="next-steps"></a>Volgende stappen

- Zie voor meer informatie over specifieke-grootten en opties voor opslag beschikbaar voor individuele databases Reken, [SQL Database DTU gebaseerde resourcelimieten voor individuele databases](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes).
- Zie voor meer informatie over specifieke-grootten en opties voor opslag beschikbaar voor elastische pools Reken, [SQL Database DTU gebaseerde resourcelimieten](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes).
