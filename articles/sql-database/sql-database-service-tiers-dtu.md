---
title: Servicelagen - Op DTU gebaseerd aankoopmodel
description: Meer informatie over servicelagen in het op DTU gebaseerde aankoopmodel voor afzonderlijke en gepoolde databases voor reken- en opslagformaten.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 11/26/2019
ms.openlocfilehash: 2f316e57e407a0588e77f56d6e1fbe8c19ba5fee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75562116"
---
# <a name="service-tiers-in-the-dtu-based-purchase-model"></a>Service-lagen in het op DTU gebaseerde aankoopmodel

Servicelagen in het op DTU gebaseerde aankoopmodel worden onderscheiden door een reeks rekengroottes met een vaste hoeveelheid meegeleverde opslag, vaste bewaartermijn voor back-ups en een vaste prijs. Alle servicelagen in het op DTU gebaseerde aankoopmodel bieden flexibiliteit voor het wijzigen van rekengroottes met minimale [downtime;](https://azure.microsoft.com/support/legal/sla/sql-database/v1_2/) Er is echter een switch over periode waarbij connectiviteit voor een korte tijd verloren gaat met de database, die kan worden beperkt met behulp van logica voor nieuwe apparaten. Afzonderlijke databases en elastische pools worden per uur gefactureerd op basis van servicelaag en rekengrootte.

> [!IMPORTANT]
> SQL Database managed instance ondersteunt geen Op DTU gebaseerd inkoopmodel. Zie [Azure SQL Database Managed Instance](sql-database-managed-instance.md)voor meer informatie.
> [!NOTE]
> Zie [vCore-gebaseerde servicelagen](sql-database-service-tiers-vcore.md)voor informatie over vCore-gebaseerde servicelagen. Zie [Azure SQL Database-inkoopmodellen](sql-database-purchase-models.md)voor informatie over het differentiëren van DTU-gebaseerde servicelagen en vCore-gebaseerde servicelagen.

## <a name="compare-the-dtu-based-service-tiers"></a>De servicelagen op Basis van DTU vergelijken

Het kiezen van een servicelaag is in de eerste plaats afhankelijk van de vereisten voor bedrijfscontinuïteit, opslag en prestaties.

||Basic|Standard|Premium|
| :-- | --: |--:| --:|
|Doelwerkbelasting|Ontwikkeling en productie|Ontwikkeling en productie|Ontwikkeling en productie|
|Uptime SLA|99,99%|99,99%|99,99%|
|Maximale back-upretentie|7 dagen|35 dagen|35 dagen|
|CPU|Laag|Laag, Gemiddeld, Hoog|Gemiddeld, Hoog|
|IO-doorvoer (bij benadering) |1-5 IOPS per DTU| 1-5 IOPS per DTU | 25 IOPS per DTU|
|IO-latentie (bij benadering)|5 ms (lezen), 10 ms (schrijven)|5 ms (lezen), 10 ms (schrijven)|2 ms (lezen/schrijven)|
|Columnstore-indexering |N.v.t.|S3 en hoger|Ondersteund|
|In-memory OLTP|N.v.t.|N.v.t.|Ondersteund|
|||||

> [!IMPORTANT]
> De Basic-, Standard S0-, S1- en S2-servicelagen bieden minder dan één vCore (CPU).  Voor CPU-intensieve workloads wordt een servicelaag van S3 of hoger aanbevolen. 
>
>Met betrekking tot gegevensopslag worden de servicelagen Basic, Standard S0 en S1 op Standaardpaginablobs geplaatst. Standaard paginablobs gebruiken opslagmedia op basis van harde schijven (HDD) en zijn het meest geschikt voor ontwikkeling, testen en andere zelden geopende workloads die minder gevoelig zijn voor prestatievariabiliteit.
>

> [!NOTE]
> U een gratis Azure SQL-database op de basisservicelaag krijgen in combinatie met een gratis Azure-account om Azure te verkennen. Zie Een [beheerde clouddatabase maken met uw gratis Azure-account](https://azure.microsoft.com/free/services/sql-database/)voor meer informatie.

## <a name="single-database-dtu-and-storage-limits"></a>Limieten voor Enkele database DTU en opslag

Rekengroottes worden uitgedrukt in termen van Database Transaction Units (DTU's) voor afzonderlijke databases en elastische Database Transaction Units (eDTU's) voor elastische pools. Zie [DTU-gebaseerd inkoopmodel](sql-database-purchase-models.md#dtu-based-purchasing-model)voor meer informatie over DTU's en eDTU's.

||Basic|Standard|Premium|
| :-- | --: | --: | --: |
| Maximale opslaggrootte | 2 GB | 1 TB | 4 TB  |
| Maximale Dtus | 5 | 3000 | 4000 | 
|||||

> [!IMPORTANT]
> Onder bepaalde omstandigheden moet u mogelijk een database verkleinen om ongebruikte ruimte terug te winnen. Zie [Bestandsruimte beheren in Azure SQL Database](sql-database-file-space-management.md)voor meer informatie.

## <a name="elastic-pool-edtu-storage-and-pooled-database-limits"></a>EDTU-, opslag- en samengevoegde databaselimieten voor elastische groep

| | **Basic** | **Standaard** | **Premium** |
| :-- | --: | --: | --: |
| Maximale opslaggrootte per database  | 2 GB | 1 TB | 1 TB |
| Maximale opslaggrootte per groep | 156 GB | 4 TB | 4 TB |
| Maximale eDT's per database | 5 | 3000 | 4000 |
| Maximale eDT's per groep | 1600 | 3000 | 4000 |
| Maximum aantal databases per groep | 500  | 500 | 100 |
|||||

> [!IMPORTANT]
> Meer dan 1 TB aan opslag in de Premium-laag is momenteel beschikbaar in alle regio's behalve: China East, China North, Germany Central, Germany Northeast, West Central US, US DoD regions, and US Government Central. In deze regio’s is de maximale opslagruimte in de Premium-laag beperkt tot 1 TB.  Raadpleeg [P11-P15 huidige beperkingen](sql-database-single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb) voor meer informatie.  
> [!IMPORTANT]
> Onder bepaalde omstandigheden moet u mogelijk een database verkleinen om ongebruikte ruimte terug te winnen. Zie [Bestandsruimte beheren in Azure SQL Database](sql-database-file-space-management.md)voor meer informatie.

## <a name="dtu-benchmark"></a>DTU Benchmark

Fysieke kenmerken (CPU, geheugen, IO) die aan elke DTU-maatregel zijn gekoppeld, worden gekalibreerd met behulp van een benchmark die de realtime databasebelasting simuleert.

### <a name="correlating-benchmark-results-to-real-world-database-performance"></a>Correleren benchmark resultaten naar real world database prestaties

Het is belangrijk om te begrijpen dat alle benchmarks alleen representatief en indicatief zijn. De transactietarieven die met de benchmarktoepassing worden bereikt, zullen niet dezelfde zijn als die welke met andere toepassingen kunnen worden bereikt. De benchmark bestaat uit een verzameling van verschillende transactietypen die worden uitgevoerd tegen een schema dat een reeks tabellen en gegevenstypen bevat. Hoewel de benchmark dezelfde basisbewerkingen uitvoert die gemeenschappelijk zijn voor alle OLTP-workloads, vertegenwoordigt deze geen specifieke klasse van database of toepassing. Het doel van de benchmark is om een redelijke gids te bieden voor de relatieve prestaties van een database die kan worden verwacht bij het opschalen of verkleinen tussen rekengroottes. In werkelijkheid zijn databases van verschillende grootte en complexiteit, ondervinden verschillende mixen van workloads en zullen ze op verschillende manieren reageren. Een IO-intensieve toepassing kan bijvoorbeeld eerder IO-drempels raken, of een CPU-intensieve toepassing kan cpu-limieten eerder raken. Er is geen garantie dat een bepaalde database zal schalen op dezelfde manier als de benchmark onder toenemende belasting.

De benchmark en de methodologie ervan worden hieronder nader beschreven.

### <a name="benchmark-summary"></a>Benchmarkoverzicht

De benchmark meet de prestaties van een mix van basisdatabasebewerkingen die het vaakst voorkomen in oltp-workloads (online transactieverwerking). Hoewel de benchmark is ontworpen met cloud computing in het achterhoofd, zijn het databaseschema, de gegevenspopulatie en de transacties ontworpen om in grote lijnen representatief te zijn voor de basiselementen die het meest worden gebruikt in OLTP-workloads.

### <a name="schema"></a>Schema

Het schema is ontworpen om voldoende variatie en complexiteit te hebben om een breed scala aan bewerkingen te ondersteunen. De benchmark loopt tegen een database die bestaat uit zes tabellen. De tabellen vallen in drie categorieën: vaste grootte, schaalvergroting en groei. Er zijn twee tafels met een vaste grootte; drie schalingstabellen; en een groeiende tafel. Tabellen met een vaste grootte hebben een constant aantal rijen. Schalingtabellen hebben een kardinaliteit die evenredig is aan de databaseprestaties, maar niet verandert tijdens de benchmark. De groeiende tabel is zo groot als een tabel met schalen op de initiële belasting, maar vervolgens verandert de kardinaliteit in de loop van het uitvoeren van de benchmark als rijen worden ingevoegd en verwijderd.

Het schema bevat een mix van gegevenstypen, waaronder geheel getal, numeriek, teken en datum/tijd. Het schema bevat primaire en secundaire sleutels, maar geen buitenlandse sleutels - dat wil zeggen, er zijn geen referentiële integriteitsbeperkingen tussen tabellen.

Een programma voor het genereren van gegevens genereert de gegevens voor de eerste database. Gehele en numerieke gegevens worden gegenereerd met verschillende strategieën. In sommige gevallen worden waarden willekeurig verdeeld over een bereik. In andere gevallen wordt een set waarden willekeurig gepermuteerd om ervoor te zorgen dat een specifieke verdeling wordt gehandhaafd. Tekstvelden worden gegenereerd uit een gewogen lijst met woorden om realistisch uitziende gegevens te produceren.

De database is formaat op basis van een "schaalfactor.". De schaalfactor (afgekort als SF) bepaalt de kardinaliteit van de schaal- en groeitabellen. Zoals hieronder beschreven in de sectie Gebruikers en Pacing, de database grootte, het aantal gebruikers, en maximale prestaties alle schaal in verhouding tot elkaar.

### <a name="transactions"></a>Transacties

De werklast bestaat uit negen transactietypen, zoals in de onderstaande tabel. Elke transactie is ontworpen om een bepaalde set systeemkenmerken in de database-engine en systeemhardware te markeren, met een hoog contrast met de andere transacties. Deze aanpak maakt het gemakkelijker om de impact van verschillende componenten op de algehele prestaties te beoordelen. De transactie 'Read Heavy' produceert bijvoorbeeld een aanzienlijk aantal leesbewerkingen van schijf.

| Transactietype | Beschrijving |
| --- | --- |
| Lees Lite |SELECTEER; in het geheugen; alleen-lezen |
| Media lezen |SELECTEER; meestal in het geheugen; alleen-lezen |
| Lees Heavy |SELECTEER; meestal niet in het geheugen; alleen-lezen |
| Update Lite |UPDATE; in het geheugen; lezen-schrijven |
| Zwaar bijwerken |UPDATE; meestal niet in het geheugen; lezen-schrijven |
| Lite invoegen |INVOEGEN; in het geheugen; lezen-schrijven |
| Zwaar invoegen |INVOEGEN; meestal niet in het geheugen; lezen-schrijven |
| Verwijderen |VERWIJDEREN; mix van in-memory en niet in het geheugen; lezen-schrijven |
| CPU Zwaar |SELECTEER; in het geheugen; relatief zware CPU-belasting; alleen-lezen |

### <a name="workload-mix"></a>Werkbelastingmix

Transacties worden willekeurig geselecteerd uit een gewogen verdeling met de volgende algemene mix. De totale mix heeft een lees/schrijfverhouding van ongeveer 2:1.

| Transactietype | % van de mix |
| --- | --- |
| Lees Lite |35 |
| Media lezen |20 |
| Lees Heavy |5 |
| Update Lite |20 |
| Zwaar bijwerken |3 |
| Lite invoegen |3 |
| Zwaar invoegen |2 |
| Verwijderen |2 |
| CPU Zwaar |10 |

### <a name="users-and-pacing"></a>Gebruikers en pacing

De benchmarkworkload wordt aangestuurd vanuit een tool die transacties indient via een reeks verbindingen om het gedrag van een aantal gelijktijdige gebruikers te simuleren. Hoewel alle verbindingen en transacties zijn machine gegenereerd, voor de eenvoud verwijzen we naar deze verbindingen als "gebruikers." Hoewel elke gebruiker onafhankelijk van alle andere gebruikers werkt, voeren alle gebruikers dezelfde stappencyclus uit die hieronder worden weergegeven:

1. Een databaseverbinding tot stand brengen.
2. Herhaal dit totdat het aangegeven is om af te sluiten:
   - Selecteer willekeurig een transactie (uit een gewogen verdeling).
   - Voer de geselecteerde transactie uit en meet de reactietijd.
   - Wacht op een pacing vertraging.
3. Sluit de databaseverbinding.
4. Afsluiten.

De pacing vertraging (in stap 2c) wordt willekeurig geselecteerd, maar met een verdeling die een gemiddelde van 1,0 seconde heeft. Zo kan elke gebruiker gemiddeld hoogstens één transactie per seconde genereren.

### <a name="scaling-rules"></a>Regels schalen

Het aantal gebruikers wordt bepaald door de databasegrootte (in schaalfactoreenheden). Er is één gebruiker voor elke vijf scale-factor units. Door de vertraging kan één gebruiker gemiddeld één transactie per seconde genereren.

Een scale-factor van 500 (SF=500) database heeft bijvoorbeeld 100 gebruikers en kan een maximumsnelheid van 100 TPS bereiken. Om een hogere TPS-snelheid te stimuleren, zijn meer gebruikers en een grotere database nodig.

### <a name="measurement-duration"></a>Meetduur

Een geldige benchmarkrun vereist een steady-state meetduur van ten minste één uur.

### <a name="metrics"></a>Metrische gegevens

De belangrijkste statistieken in de benchmark zijn doorvoer en responstijd.

- Doorvoer is de essentiële prestatiemaatstaf in de benchmark. Doorvoer wordt gerapporteerd in transacties per tijdseenheid, waarbij alle transactietypen worden meegeteld.
- Responstijd is een maat voor de voorspelbaarheid van de prestaties. De responstijdbeperking varieert afhankelijk van de serviceklasse, waarbij hogere serviceklassen een strengere responstijdvereiste hebben, zoals hieronder wordt weergegeven.

| Klasse van dienst | Doorvoermeting | Vereiste reactietijd |
| --- | --- | --- |
| Premium |Transacties per seconde |95e percentiel op 0,5 seconden |
| Standard |Transacties per minuut |90e percentiel op 1,0 seconden |
| Basic |Transacties per uur |80e percentiel op 2,0 seconden |

## <a name="next-steps"></a>Volgende stappen

- Zie [SQL Database DTU-gebaseerde resourcelimieten voor afzonderlijke databases voor](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes)meer informatie over specifieke rekenformaten en opties voor opslaggroottes die beschikbaar zijn voor afzonderlijke databases.
- Zie [SQL Database DTU-gebaseerde resourcelimieten](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes)voor meer informatie over specifieke rekenformaten en opties voor opslaggrootte die beschikbaar zijn voor elastische pools.
