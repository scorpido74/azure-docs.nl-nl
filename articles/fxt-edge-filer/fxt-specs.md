---
title: Microsoft Azure FXT Edge Filer specificaties | Microsoft Documenten
description: Fysieke en milieuspecificaties voor Azure FXT Edge Filer-hardware
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: rohogue
ms.openlocfilehash: c06b0c79e01257eebf566b9752269cb88c072d8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264724"
---
# <a name="azure-fxt-edge-filer-specifications"></a>Azure FXT Edge Filer-specificaties

In dit artikel worden de hardwarespecificaties voor Azure FXT Edge Filer-hardwareknooppunten uitgelegd. In de praktijk worden drie of meer knooppunten samen geconfigureerd om het geclusterde cachesysteem te bieden.

## <a name="hardware-specifications"></a>Hardwarespecificaties

| Onderdeel | FXT 6600 | FXT 6400 |
|----------|-----------|-----------|
| CPU-kernen |  16 | 16 |
| Dram  | 1536 GB | 768 GB |
| Netwerkpoorten | 6 x 25/10 Gb + 2 x 1 Gb | 6 x 25/10 Gb + 2 x 1 Gb |
| NVMe SSD-capaciteit | 25,6 TB | 12,8 TB |

## <a name="drive-specifications"></a>Specificaties van het station

Het systeem heeft tien drive bays, toegankelijk vanaf de voorkant. Elke bewoonde schijf is gelabeld aan de rechterkant met capaciteitsinformatie. 

Stationnummers worden afgedrukt op de ruimte tussen stations. In de Azure FXT Edge Filer is schijf 0 het schijfje linksboven en schijf 1 zit er direct onder.

![foto van één bay voor harde schijf in het FXT-chassis, met schijfnummers en capaciteitslabels](media/fxt-drives-photo.png)

| Stationsnummers    |  Gebruiken   |  Specificaties |
|------------------|--------|-----------------|
| 0, 1             | OS     | 480 GB SATA SSD |
| 2, 3, 4, 5, 6, 7, 8, 9 | Gegevens   | FXT 6600: 3,2 TB NVMe SSD <br> FXT 6400: 1,6 TB NVMe SSD |


## <a name="dimensions-and-weight"></a>Afmetingen en gewicht

De Azure FXT Edge Filer is ontworpen om in een standaard 19"-materiaalrek te passen en is één rackunit hoog (1U). 

<!-- 10x2.5 inches version -->

| Filer-afmetingen           |                          |
|-----------------------------|--------------------------|
| Height                      | 42,8 mm (1,68 inch)    |
| Breedte (inclusief rackoren) | 482,0 mm (18,97 inch)  |
| Breedte - hoofdbehuizing      | 434,0 mm (17,08 inch) |
| Diepte - rackoren naar achterkant van de hoofdbehuizing                   | 733,82 mm (29,61 inch) |
| Diepte - rack oren tot het verst achter uitsteeksel                 | 772,67 mm (30,42 inch) |
| Diepte - rack oren tot het verste vooruitsteeksel, zonder bezel | 22,0 mm (0,87 inch)  |
| Diepte - rack oren tot het verste vooruitsteeksel, met lunette    | 35,84 mm |

| Gewicht | |
|-----------------|----------------------|
| Knooppuntgewicht (zonder verpakking, zonder accessoires) | 18,1 kg |
| Nettogewicht (zonder verpakking, inclusief accessoires) | 23,1 kg|
| Bruto gewicht (zoals verzonden, inclusief alle verpakking) |  29,0 kg |

### <a name="shipping-dimensions"></a>Verzendafmetingen

| Dimensie pakket | Millimeters | Inch |
|-------------------|-------------|--------|
| Height            | 311.2       | 12.25" |
| Breedte             | 642.8       | 25.31" |
| Lengte            | 1,051.1     | 41.38" |

## <a name="power-and-thermal-specifications"></a>Energie- en thermische specificaties

In deze sectie worden vermogensclassificaties en metingen voor de Azure FXT Edge Filer gegeven.

### <a name="nameplate-ratings"></a>Naamplaatclassificaties

| Naamplaatjeclassificaties voor FXT 6000-seriemodellen |
|----------------|
| 100 - 240V~    |
| 10A - 5A (X2)  |
| 50/60Hz         |

<!-- matches the Dell regulatory label exactly -->

### <a name="power-and-thermal-measurements"></a>Vermogens- en thermische metingen 

Azure FXT Edge Filer-knooppunten maken gebruik van ventilatoren met variabele snelheid, dus het vermogen is afhankelijk van temperatuur en belasting. Maximale ventilatorsnelheden kunnen worden bereikt bij bepaalde combinaties van hoge belasting en verhoogde omgevingstemperaturen. 

Deze grafieken geven stroomverbruik en thermische output metingen voor veelgebruikte spanningsfrequentiecombinaties. 

| FXT 6600 vermogen bij kamertemperatuur <br />(22° C, 71,6° F) | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz | 
|---------|---|---|---|---|---|
| Spanning (V) | 100 | 120 | 208 | 230 | 240 | 
| Frequentie (Hz) | 60 | 60 | 60 | 50 | 50 |
| Huidige (A) | 5.02 | 4.16 |2.40 | 2.20 | 2.16 |
| Schijnbare kracht (VA) | 502 | 499 | 499 | 506 | 518|
| Power Factor | 0.99 | 0.99 |0.98 | 0.98 | 0.98 |
| Echte kracht (W) | 497 |494 | 489 | 496 | 508 |
| Thermische dissipatie (BTU/Hr) |1696 | 1686 | 1669 | 1692 | 1733 |

| FXT 6600 vermogen bij maximale ventilatorsnelheden | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz | 
|---------|---|---|---|---|---|
| Spanning (V) | 100 |120 | 208 | 230 | 240| 
| Frequentie (Hz) | 60 | 60 | 60 | 50 | 50 |
| Huidige (A) | 5.98 | 5.01 | 2.81 | 2.55 | 2.48 |
| Schijnbare kracht (VA) | 598 | 601 | 584 | 587 | 595 |
| Power Factor | 0.99 | 0.99 | 0.98 | 0.98 | 0.98 |
| Echte kracht (W) | 592 | 595 | 573 | 575 | 583 |
| Thermische dissipatie (BTU/Hr) | 2020 |2031 | 1954 | 1961 | 1990 |

| FXT 6400 vermogen bij kamertemperatuur <br />(22° C, 71,6° F) | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz | 
|---------|---|---|---|---|---|
| Spanning (V) | 100 | 120 | 208 | 230 | 240 |
| Frequentie (Hz) |60 | 60 | 60 | 50 | 50 |
| Huidige (A) | 4,63 | 3.86 | 2.24 | 2.04 | 1.94 |
| Schijnbare kracht (VA) | 463 | 463 | 466 | 469 | 466 |
| Power Factor | 0.99 | 0.99 | 0.98 | 0.98 | 0.98 | 
| Echte kracht (W) | 458 | 459 | 457 | 460 | 456 |
| Thermische dissipatie (BTU/Hr) | 1564 | 1565 | 1558 | 1569 | 1557 |

| FXT 6400 vermogen bij maximale ventilatorsnelheden | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz |
|---------|---|---|---|---|---|
| Spanning (V) | 100 | 120 | 208 | 230 | 240 |
| Frequentie (Hz) | 60 | 60 | 60 | 50 | 50 |
| Huidige (A) | 5.15 | 4.28 | 2.48 | 2.28 | 2.13 |
| Schijnbare kracht (VA) | 515 | 514 | 516 | 524 | 511 |
| Power Factor | 0.99 | 0.99 | 0.98 | 0.98 | 0.98 |
| Echte kracht (W) | 510 | 508 | 506 | 514 | 501 |
| Thermische dissipatie (BTU/Hr) | 1740 | 1735 | 1725 | 1753 | 1709 |

## <a name="environmental-requirements"></a>Milieu-eisen

Deze sectie geeft specificaties voor de omgeving van de hardware.

### <a name="temperature-and-humidity"></a>Temperatuur en vochtigheid

| Milieuattribuut   | Actieradius                   | Niet-werkend bereik         |
|---------------------------|-----------------------------------|-----------------------------|
| Omgevingstemperatuurbereik | 10°C tot 35°C (50 - 86°F)          | -40°C tot 65°C (-40 - 149°F) |
| Relatieve luchtvochtigheid in de omgeving | 10% - 80% niet-condenserend          | 5% - 95% niet-condenserend     |
| Maximaal dauwpunt         | 29°C                       | 33°C                 |
| Hoogte                  | tot 3048 meter (10.000 voet), afhankelijk van de temperatuur de-rating hieronder genoteerd | tot 12.000 meter (39.370 voet) |

> [!NOTE] 
> **Hoogtetemperatuur de-rating:** De maximumtemperatuur wordt verlaagd met 1°C/300 m (1°F/547 ft) boven 950 m (3,117 ft).

### <a name="airflow-shock-and-vibration"></a>Luchtstroom, schok en trillingen 

| Kenmerk         | Specificatie |
|-------------------|---------------|
| Luchtstroom                    | De systeemluchtstroom is van voor naar achter. Het systeem moet worden bediend met een uitlaatinstallatie aan de lage druk. |
| Shock, operationeel         | 6 G voor 11 milliseconden (getest in 6 oriëntaties) |
| Schok, niet-operationeel     | 71 G voor 2 milliseconden (getest in 6 oriëntaties) |
| Trillingen, operationeel     | 0,26 G<sub>RMS</sub> 5 Hz tot 350 Hz willekeurig         |
| Trillingen, niet-operationeel | 1,88 G<sub>RMS</sub> 10 Hz tot 500 Hz gedurende 15 minuten (alle zes kanten getest)  |

## <a name="safety-regulation-compliance"></a>Naleving van de veiligheidsregelgeving 

Azure FXT Edge Filer voldoet aan de vermelde regelgeving. 

| Categorie       | Wettelijke specificatie | 
|----------------|--------------------------|
| Algemene veiligheid | EN 60950-1:2006 +A1:2010 +A2:2013 +A11:2009 +A12:2011/IEC 60950-1:2005 ed2 +A1:2009 +A2:2013 <br>EN 62311:2008 | 
| Emc            | FCC A, ICES-003  <br>EN 55032:2012/CISPR 32:2012  <br>EN 55032:2015/CISPR 32:2015  <br>EN 55024:2010 +A1:2015/CISPR 24:2010 +A1:2015  <br>EN 61000-3-2:2014/IEC 61000-3-2:2014 (klasse D)   <br>EN 61000-3-3:2013/IEC 61000-3-3:2013 |
| Energie         | Verordening (EU) nr. 617/2013  |
| Rohs           |    EN 50581:2012   |