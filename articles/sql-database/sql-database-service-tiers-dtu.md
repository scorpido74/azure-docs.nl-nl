---
title: Service lagen-op DTU gebaseerd inkoop model
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
ms.date: 11/26/2019
ms.openlocfilehash: 6dee7642ac7ac0544db5b88981483bd1ea0f745e
ms.sourcegitcommit: 36eb583994af0f25a04df29573ee44fbe13bd06e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2019
ms.locfileid: "74539310"
---
# <a name="service-tiers-in-the-dtu-based-purchase-model"></a>Service lagen in het op DTU gebaseerde aankoop model

Service lagen in het op DTU gebaseerde aankoop model worden gedifferentieerd met een bereik van reken grootten met een vaste hoeveelheid inbegrepen opslag, een vaste Bewaar periode voor back-ups en vaste prijzen. Alle service lagen in het op DTU gebaseerde aankoop model bieden flexibiliteit bij het wijzigen van de reken grootte met minimale [downtime](https://azure.microsoft.com/support/legal/sla/sql-database/v1_2/). Er is echter een switch over een periode waarin de verbinding gedurende korte tijd naar de data base verloren gaat, wat kan worden verholpen met de logica voor opnieuw proberen. Afzonderlijke data bases en elastische Pools worden per uur gefactureerd op basis van de servicelaag en de reken grootte.

> [!IMPORTANT]
> SQL Database beheerde instantie biedt geen ondersteuning voor een op DTU gebaseerd aankoop model. Zie [Azure SQL database Managed instance](sql-database-managed-instance.md)(Engelstalig) voor meer informatie.
> [!NOTE]
> Zie op [vCore gebaseerde](sql-database-service-tiers-vcore.md)service lagen voor meer informatie over vCore. Zie [Azure SQL database-aankoop modellen](sql-database-purchase-models.md)voor meer informatie over gedifferentieerde DTU-gebaseerde service lagen en vCore-gebaseerde service lagen.

## <a name="compare-the-dtu-based-service-tiers"></a>De service lagen op basis van DTU vergelijken

Het kiezen van een servicelaag is voornamelijk afhankelijk van de bedrijfs continuïteit, opslag en prestatie vereisten.

||Basic|Standard|Premium|
| :-- | --: |--:| --:|
|Doel-workload|Ontwikkeling en productie|Ontwikkeling en productie|Ontwikkeling en productie|
|SLA voor actieve tijdsduur|99,99%|99,99%|99,99%|
|Maximale retentie van back-ups|7 dagen|35 dagen|35 dagen|
|CPU|Laag|Laag, gemiddeld, hoog|Gemiddeld, hoog|
|I/o-door Voer (ongeveer) |1-5 IOPS per DTU| 1-5 IOPS per DTU | 25 IOPS per DTU|
|I/o-latentie (bij benadering)|5 ms (lezen), 10 MS (schrijven)|5 ms (lezen), 10 MS (schrijven)|2 ms (lezen/schrijven)|
|Column Store-indexering |N/A|S3 en hoger|Ondersteund|
|OLTP in het geheugen|N/A|N/A|Ondersteund|
|||||

> [!IMPORTANT]
> De service lagen Basic, Standard S0, S1 en S2 bieden minder dan één vCore (CPU).  Voor CPU-intensieve workloads wordt een servicelaag van S3 of hoger aanbevolen. 
>
>Met betrekking tot gegevens opslag worden de service lagen basis, standaard S0 en S1 op standaard pagina-blobs geplaatst. Standaard pagina-blobs maken gebruik van schijven op basis van harde schijven (HDD) en zijn het meest geschikt voor ontwikkelings-, test-en andere weinig frequent gebruikte workloads die minder gevoelig zijn voor prestatie variaties.
>

> [!NOTE]
> In combi natie met een gratis Azure-account kunt u Azure een gratis Azure-SQL database in de laag basis service krijgen. Zie [een beheerde Cloud database maken met uw gratis Azure-account](https://azure.microsoft.com/free/services/sql-database/)voor meer informatie.

## <a name="single-database-dtu-and-storage-limits"></a>Eenmalige DTU en opslag limieten voor data bases

Reken grootten worden uitgedrukt in termen van Dtu's (data base Trans Action units) voor individuele data bases en elastische data base Trans Action units (Edtu's) voor elastische Pools. Zie voor meer informatie over Dtu's en Edtu's op [DTU gebaseerd inkoop model](sql-database-purchase-models.md#dtu-based-purchasing-model)?

||Basic|Standard|Premium|
| :-- | --: | --: | --: |
| Maximale opslag grootte | 2 GB | 1 TB | 4 TB  |
| Maximum aantal Dtu's | 5 | 3000 | 4000 | 
|||||

> [!IMPORTANT]
> In sommige gevallen moet u mogelijk een Data Base verkleinen om ongebruikte ruimte te claimen. Zie [Bestands ruimte beheren in Azure SQL database](sql-database-file-space-management.md)voor meer informatie.

## <a name="elastic-pool-edtu-storage-and-pooled-database-limits"></a>EDTU-, opslag-en gegroepeerde database limieten voor elastische Pools

| | **Basic** | **Standard** | **Premium** |
| :-- | --: | --: | --: |
| Maximale opslag grootte per data base  | 2 GB | 1 TB | 1 TB |
| Maximale opslag grootte per groep | 156 GB | 4 TB | 4 TB |
| Maximum aantal Edtu's per data base | 5 | 3000 | 4000 |
| Maximum aantal Edtu's per pool | 1600 | 3000 | 4000 |
| Maximum aantal data bases per pool | 500  | 500 | 100 |
|||||

> [!IMPORTANT]
> Meer dan 1 TB aan opslag ruimte in de Premium-laag is momenteel beschikbaar in alle regio's behalve: China-oost, China-noord, Duitsland-centraal, Duitsland-noordoost, VS-West-Centraal, US DoD regio's en Amerikaanse overheid centraal. In deze regio’s is de maximale opslagruimte in de Premium-laag beperkt tot 1 TB.  Raadpleeg [P11-P15 huidige beperkingen](sql-database-single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb) voor meer informatie.  
> [!IMPORTANT]
> In sommige gevallen moet u mogelijk een Data Base verkleinen om ongebruikte ruimte te claimen. Zie [Bestands ruimte beheren in Azure SQL database](sql-database-file-space-management.md)voor meer informatie.

## <a name="dtu-benchmark"></a>DTU-Bench Mark

Fysieke kenmerken (CPU, geheugen, IO) die zijn gekoppeld aan elke DTU-meting worden gekalibreerd met behulp van een bench Mark waarmee de werk belasting van de werkelijke data base wordt gesimuleerd.

### <a name="correlating-benchmark-results-to-real-world-database-performance"></a>Correlatie van Bench Mark-resultaten voor echte prestaties van data base

Het is belang rijk om te begrijpen dat alle benchmarks alleen representatief en indicatief zijn. De transactie tarieven die worden behaald met de Bench Mark-toepassing, zijn niet hetzelfde als de trans acties die kunnen worden behaald met andere toepassingen. De Bench Mark bestaat uit een verzameling van verschillende transactie typen die worden uitgevoerd op basis van een schema met een bereik van tabellen en gegevens typen. Hoewel de benchmark bewerking dezelfde basis bewerkingen uitvoert die gemeen schappelijk zijn voor alle OLTP-workloads, vertegenwoordigt deze methode geen specifieke klasse van data base of toepassing. Het doel van de Bench Mark is het bieden van een redelijke hand leiding voor de relatieve prestaties van een Data Base die kan worden verwacht bij het omhoog of omlaag schalen van de reken groottes. In werkelijkheid hebben data bases een verschillende grootte en complexiteit, waardoor verschillende combi Naties van werk belastingen optreden en op verschillende manieren kan worden gereageerd. Zo kan een IO-intensieve toepassing de drempel waarden voor IO eerder aanraken of kan een CPU-intensieve toepassing CPU-limieten eerder aanraken. Er is geen garantie dat een bepaalde data base op dezelfde manier wordt geschaald als de Bench Mark onder toenemende belasting.

De Bench Mark en de bijbehorende methodologie worden hieronder uitvoerig beschreven.

### <a name="benchmark-summary"></a>Bench Mark-samen vatting

De benchmark meet de prestaties van een combi natie van basis database bewerkingen die het vaakst voor komen in workloads voor online transactie verwerking (OLTP). Hoewel de Bench Mark is ontworpen met het oog op Cloud Computing, zijn het database schema, de gegevens populatie en de trans acties zo ontworpen dat ze algemeen representatief zijn voor de basis elementen die het meest worden gebruikt in OLTP-workloads.

### <a name="schema"></a>Schema

Het schema is ontworpen om te beschikken over voldoende verscheidenheid en complexiteit voor het ondersteunen van een breed scala aan bewerkingen. De Bench Mark wordt uitgevoerd op een Data Base die bestaat uit zes tabellen. De tabellen worden onderverdeeld in drie categorieën: vaste grootte, schalen en groei. Er zijn twee tabellen met een vaste grootte. drie schaal tabellen; en één groeiende tabel. Tabellen met een vaste grootte hebben een constant aantal rijen. Schaal tabellen hebben een kardinaliteit die evenredig is met de prestaties van de data base, maar wordt niet gewijzigd tijdens de Bench Mark. De groeiende tabel is even groot als een schaal tabel bij de eerste belasting, maar vervolgens wordt de kardinaliteit gewijzigd tijdens het uitvoeren van de Bench Mark als rijen worden ingevoegd en verwijderd.

Het schema bevat een combi natie van gegevens typen, inclusief geheel getal, numeriek, teken en datum/tijd. Het schema bevat primaire en secundaire sleutels, maar geen refererende sleutels, dat wil zeggen, er zijn geen referentiële integriteits beperkingen tussen tabellen.

Een programma voor het genereren van gegevens genereert de gegevens voor de eerste data base. Geheel getal en numerieke gegevens worden met verschillende strategieën gegenereerd. In sommige gevallen worden waarden wille keurig verdeeld over een bereik. In andere gevallen wordt een verzameling waarden wille keurig permutatieen om ervoor te zorgen dat een specifieke distributie wordt gehandhaafd. Tekst velden worden gegenereerd op basis van een gewogen lijst met woorden voor het produceren van realistische Zoek gegevens.

De grootte van de data base is gebaseerd op een ' schaal factor '. De schaal factor (afgekort als SF) bepaalt de kardinaliteit van de schaal-en groeiende tabellen. Zoals hieronder wordt beschreven in de sectie gebruikers en pacing, worden de grootte van de data base, het aantal gebruikers en de maximale prestaties helemaal op elkaar afgestemd.

### <a name="transactions"></a>Transacties

De werk belasting bestaat uit negen transactie typen, zoals wordt weer gegeven in de volgende tabel. Elke trans actie is ontworpen om een bepaalde set systeem kenmerken te markeren in de data base-engine en systeemhardware, met een hoog contrast van de andere trans acties. Deze aanpak maakt het gemakkelijker om de impact van verschillende onderdelen te beoordelen op de algehele prestaties. De trans actie ' read Heavy ' produceert bijvoorbeeld een groot aantal lees bewerkingen van de schijf.

| Transactie type | Beschrijving |
| --- | --- |
| Lite lezen |UITGESCHAKELD in-Memory; alleen-lezen |
| Medium lezen |UITGESCHAKELD voornamelijk in-Memory; alleen-lezen |
| Dik lezen |UITGESCHAKELD voornamelijk niet in het geheugen. alleen-lezen |
| Update Lite |BIJWERK in-Memory; lezen/schrijven |
| Zware update |BIJWERK voornamelijk niet in het geheugen. lezen/schrijven |
| Lite plaatsen |INVOEGEN in-Memory; lezen/schrijven |
| Dik invoegen |INVOEGEN voornamelijk niet in het geheugen. lezen/schrijven |
| Verwijderen |VERWIJDERD combi natie van in-Memory en niet in het geheugen. lezen/schrijven |
| CPU-zwaar |UITGESCHAKELD in-Memory; relatief zware CPU-belasting; alleen-lezen |

### <a name="workload-mix"></a>Mix van workload

Trans acties worden wille keurig geselecteerd uit een gewogen distributie met de volgende totale mix. De totale mix heeft een lees-schrijf verhouding van ongeveer 2:1.

| Transactie type | % van mix |
| --- | --- |
| Lite lezen |35 |
| Medium lezen |20 |
| Dik lezen |5 |
| Update Lite |20 |
| Zware update |3 |
| Lite plaatsen |3 |
| Dik invoegen |2 |
| Verwijderen |2 |
| CPU-zwaar |10 |

### <a name="users-and-pacing"></a>Gebruikers en pacing

De Bench Mark-werk belasting is gebaseerd op een hulp programma dat trans acties verzendt over een reeks verbindingen om het gedrag van een aantal gelijktijdige gebruikers te simuleren. Hoewel alle verbindingen en trans acties worden gegenereerd, kunnen we voor het gemak de volgende verbindingen hebben als "gebruikers". Hoewel elke gebruiker onafhankelijk van alle andere gebruikers werkt, voeren alle gebruikers dezelfde stappen uit die hieronder worden weer gegeven:

1. Een database verbinding tot stand brengen.
2. Herhalen tot het signaal is afgesloten:
   - Selecteer een trans actie in wille keurige volg orde (van een gewogen distributie).
   - De geselecteerde trans actie uitvoeren en de reactie tijd meten.
   - Wacht tot een pacing vertraging.
3. Sluit de database verbinding.
4. Toepassing.

De pacing vertraging (in stap 2c) wordt wille keurig geselecteerd, maar met een distributie met een gemiddelde van 1,0 seconden. Daarom kan elke gebruiker gemiddeld Maxi maal één trans actie per seconde genereren.

### <a name="scaling-rules"></a>Schaal regels

Het aantal gebruikers wordt bepaald door de grootte van de data base (in Scale-factor units). Er is één gebruiker voor elke vijf schaal factor eenheden. Vanwege de pacing vertraging kan één gebruiker gemiddeld per seconde een trans actie genereren.

Een schaal factor van 500-data base (SF = 500) heeft bijvoorbeeld 100 gebruikers en kan een maximum aantal van 100 TPS opleveren. Voor een hogere TPS-rate hebt u meer gebruikers en een grotere data base nodig.

### <a name="measurement-duration"></a>Metings duur

Een geldige Bench Mark-uitvoeringsrun vereist een meting van een stationaire duur van ten minste één uur.

### <a name="metrics"></a>Metrische gegevens

De belangrijkste metrische gegevens in de Bench Mark zijn door Voer en reactie tijd.

- De door Voer is de essentiële prestatie meting in de Bench Mark. De door Voer wordt gerapporteerd in trans acties per tijds eenheid, waarbij alle transactie typen worden geteld.
- De reactie tijd is een meting van de prestaties voor voorspel baarheid. De beperking voor de reactie tijd is afhankelijk van de service klasse, met hogere service klassen met een meer strikte reactie tijd, zoals hieronder wordt weer gegeven.

| Service klasse | Maat eenheid voor door Voer | Vereiste reactie tijd |
| --- | --- | --- |
| Premium |Transacties per seconde |95e percentiel van 0,5 seconden |
| Standard |Transacties per minuut |negen tigste percentiel van 1,0 seconden |
| Basic |Transacties per uur |80th percentiel van 2,0 seconden |

## <a name="next-steps"></a>Volgende stappen

- Zie [SQL database op DTU gebaseerde resource limieten voor afzonderlijke data bases](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes)voor meer informatie over de beschik bare reken grootten en opties voor opslag grootte die beschikbaar zijn voor individuele data bases.
- Zie [SQL database op DTU gebaseerde resource limieten](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes)voor meer informatie over de beschik bare berekenings grootten en opties voor opslag grootte die beschikbaar zijn voor elastische Pools.
