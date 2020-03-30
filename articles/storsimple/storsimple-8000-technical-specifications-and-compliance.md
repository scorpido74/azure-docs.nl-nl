---
title: Technische specificaties van StorSimple | Microsoft Documenten
description: Beschrijft de technische specificaties en nalevingsgegevens van wettelijke normen voor de StorSimple-hardwarecomponenten.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/02/2017
ms.author: alkohli
ms.openlocfilehash: 061194422a8c1bc449dbef0c4f04bb8e1db10dea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68965293"
---
# <a name="technical-specifications-and-compliance-for-the-storsimple-device"></a>Technische specificaties en naleving voor het StorSimple-apparaat

## <a name="overview"></a>Overzicht

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

De hardwareonderdelen van uw Microsoft Azure StorSimple-apparaat voldoen aan de technische specificaties en wettelijke normen die in dit artikel worden beschreven. De technische specificaties beschrijven de Power and Cooling Modules (PCM's), schijven, opslagcapaciteit en behuizingen. De nalevingsinformatie heeft betrekking op zaken als internationale normen, veiligheid en emissies en bekabeling.

## <a name="power-and-cooling-module-specifications"></a>Specificaties voedings- en koelmodule

Het StorSimple-apparaat heeft twee 100-240 V dual-ventilatoren, SBB-compatibele Power Cooling Modules (PCM's). Dit zorgt voor een redundante vermogensconfiguratie. Als een PCM uitvalt, blijft het apparaat normaal werken op de andere PCM totdat de defecte module is vervangen.

De EBOD-behuizing maakt gebruik van een PCM van 580 W en de primaire behuizing maakt gebruik van een PCM van 764 W. In de volgende tabellen worden de technische specificaties vermeld die aan de PCM's zijn gekoppeld.

| Specificatie | 580 W PCM (EBOD) | 764 W PCM (Primair) |
| --- | --- | --- |
| Maximaal uitgangsvermogen |580 W |764 |
| Frequency |50/60 Hz |50/60 Hz |
| Selectie van het spanningsbereik |Auto variërend: 90 – 264 V AC, 47/63 Hz |Automatische variërend: 90- 264 V AC, 47/63 Hz |
| Maximale inrush stroom |20 a |20 a |
| Correctie van de vermogensfactor |>nominale ingangsspanning van 95% |>nominale ingangsspanning van 95% |
| Harmonischen |Voldoet aan EN61000-3-2 |Voldoet aan EN61000-3-2 |
| Uitvoer |5V Stand-by spanning \@ 2.0 A |5V Stand-by spanning \@ 2,7 A |
| +5V \@ 42 A |+5V \@ 40 A | |
| +12V \@ 38 A |+12V \@ 38 A | |
| Hot pluggable |Ja |Ja |
| Schakelaars en LED's |AC-aan/UIT-schakelaar en LED's met vier statusindicatoren |AC-aan/UIT-schakelaar en zes statusindicator-LED's |
| Behuizingkoeling |Axiale koelventilatoren met variabele ventilatorsnelheidsregeling |Axiale koelventilatoren met variabele ventilatorsnelheidsregeling |

## <a name="power-consumption-statistics"></a>Statistieken over het energieverbruik

In de volgende tabel worden de typische gegevens over het energieverbruik (werkelijke waarden kunnen afwijken van het gepubliceerde) voor de verschillende modellen van het StorSimple-apparaat weergegeven.

| Voorwaarden | 240 V AC | 240 V AC | 240 V AC | 110 V AC | 110 V AC | 110 V AC |
| --- | --- | --- | --- | --- | --- | --- |
|  Ventilatoren traag, rijdt stationair |1.45 A |0,31 kW |1057,76 BTU/uur |3.19 A |0,34 kW |1160,13 BTU/uur |
|  Fans traag, rijdt toegang tot |1.54 A |0,33 kW |1126,01 BTU/uur |3.27 A |0,36 kW |1228,37 BTU/uur |
|  Fans snel, rijdt stationair, twee PSU's aangedreven |2.14 A |0,49 kW |1671,95 BTU/uur |4.99 A |0,54 kW |1842,56 BTU/uur |
|  Fans snel, rijdt stationair, een PSU aangedreven een idle |2.05 A |0,48 kW |1637,83 BTU/uur |4.58 A |0,50 kW |1706,07 BTU/uur |
|  Fans snel, drives toegang, twee PSU's aangedreven |2.26 A |0,51 kW |1740,19 BTU/uur |4.95 A |0,54 kW |1842,56 BTU/uur |
|  Fans snel, drives toegang, een PSU aangedreven een idle |2.14 A |0,49 kW |1671,95 BTU/uur |4.81 A |0,53 kW |1808,44 BTU/uur |

## <a name="disk-drive-specifications"></a>Specificaties schijfstation

Uw StorSimple-apparaat ondersteunt maximaal 12 3,5-inch seriële seriële gekoppelde SCSI-schijven (SAS). De werkelijke schijven kunnen een mix zijn van solid-state drives (SSD's) of harde schijven (HDD's), afhankelijk van de productconfiguratie. De 12 disk drive slots bevinden zich in een 3 bij 4 configuratie voor de behuizing. De EBOD-behuizing zorgt voor extra opslag voor nog eens 12 schijven. Dit zijn altijd HDD's.

## <a name="storage-specifications"></a>Opslagspecificaties

De StorSimple-apparaten hebben een mix van harde schijven en solid-state drives voor zowel de 8100 als de 8600. De totale bruikbare capaciteit voor de 8100 en 8600 is respectievelijk ongeveer 15 TB en 38 TB. In de volgende tabel worden de details van ssd,hdd en cloudcapaciteit beschreven in de context van de StorSimple-oplossingscapaciteit.

| Apparaatmodel / Capaciteit | 8100 | 8600 |
| --- | --- | --- |
| Aantal harde schijven (HDD's) |8 |19 |
| Aantal solid-state drives (SSD's) |4 |5 |
| Single HDD-capaciteit |4 TB |4 TB |
| Eén SSD-capaciteit |400 GB |800 GB |
| Reservecapaciteit |4 TB |4 TB |
| Bruikbare HDD-capaciteit |14 TB |36 TB |
| Bruikbare SSD-capaciteit |800 GB |2 TB |
| Totale bruikbare capaciteit* |~ 15 TB |~ 38 TB |
| Maximale oplossingscapaciteit (inclusief cloud) |200 TB |500 TB |

<sup>* </sup>- *De totale bruikbare capaciteit omvat de beschikbare capaciteit voor gegevens, metagegevens en buffers. U lokaal vastgemaakte volumes tot 8,5 TB inrichten op het 8100-apparaat of tot 22,5 TB op het grotere 8600-apparaat. Ga voor meer informatie naar [Lokaal vastgemaakte volumes van StorSimple](storsimple-8000-local-volume-faq.md).*

## <a name="enclosure-dimensions-and-weight-specifications"></a>Afmetingen en gewichtsspecificaties van de behuizing

In de volgende tabellen worden de verschillende specificaties van de behuizing voor afmetingen en gewicht vermeld.

### <a name="enclosure-dimensions"></a>Afmetingen behuizing

In de volgende tabel worden de afmetingen van de behuizing in millimeters en centimeters weergegeven.

| Behuizing | Millimeters | Inch |
| --- | --- | --- |
| Height |87.9 |3.46 |
| Breedte over montageflens |483 |19.02 |
| Breedte over behuizing |443 |17.44 |
| Diepte van voorste montageflens tot extremiteit van behuizing lichaam |577 |22.72 |
| Diepte van het bedieningspaneel tot het verste uiteinde van de behuizing |630.5 |24.82 |
| Diepte van montageflens tot verste uiteinde van behuizing |603 |23.74 |

### <a name="enclosure-weight"></a>Behuizingsgewicht

Afhankelijk van de configuratie kan een volledig geladen primaire behuizing 21 tot 33 kg wegen en twee personen nodig hebben om het te verwerken.

| Behuizing | Gewicht |
| --- | --- |
| Maximaal gewicht (afhankelijk van de configuratie) |30 kg – 33 kg |
| Leeg (geen schijven gemonteerd) |21 – 23 kg |

## <a name="enclosure-environment-specifications"></a>Omgevingsspecificaties voor behuizingen

In deze sectie worden de specificaties met betrekking tot de omgeving van de behuizing weergegeven. De temperatuur, vochtigheid, hoogte, schok, trillingen, oriëntatie, veiligheid en elektromagnetische compatibiliteit (EMC) zijn opgenomen in deze categorie.

### <a name="temperature-and-humidity"></a>Temperatuur en vochtigheid

| Behuizing | Omgevingstemperatuurbereik | Relatieve luchtvochtigheid in de omgeving | Maximale natte lamp |
| --- | --- | --- | --- |
| Operationele |5°C - 35°C( 41°F - 95°F) |20% - 80% niet-condenserend- |28°C |
| Niet-operationeel |-40°C - 70°C( 40°F - 158°F) |5% - 100% niet-condenserend |29°C |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>Luchtstroom, hoogte, schok, trillingen, oriëntatie, veiligheid en EMC

| Behuizing | Operationele specificaties |
| --- | --- |
| Luchtstroom |De systeemluchtstroom is van voor naar achter. Het systeem moet worden bediend met een lagedruk- uitlaatinstallatie. De tegendruk die ontstaat door rekdeuren en obstakels mag niet groter zijn dan 5 pascal (0,5 mm watermeter). |
| Hoogte, operationeel |-30 meter tot 3045 meter (-100 voet tot 10.000 voet) met een maximale bedrijfstemperatuur die met 5°C boven 7000 voet is gederated. |
| Hoogte, niet-operationeel |-305 meter tot 12.192 meter (-1.000 voet tot 40.000 voet) |
| Shock, operationeel |5g 10 ms 1/2 sine |
| Schok, niet-operationeel |30g 10 ms 1/2 sine |
| Trillingen, operationeel |0,21 g RMS 5-500 Hz willekeurig |
| Trillingen, niet-operationeel |1.04g RMS 2-200 Hz willekeurig |
| Trillingen, verhuizing |3g 2-200 Hz sine |
| Oriëntatie en montage |19-inch rackmount (2 M.E.s. |
| Rekrails |Voor minimaal 700 mm (31,50 inch) diepterekken die voldoen aan IEC 297 |
| Veiligheid en goedkeuringen |CE en UL EN 61000-3, IEC 61000-3, UL 61000-3 |
| Emc |EN55022 (CISPR - A), FCC A |

## <a name="international-standards-compliance"></a>Naleving van internationale normen

Uw Microsoft Azure StorSimple-apparaat voldoet aan de volgende internationale normen:  

* CE - EN 60950 - 1
* CB-rapport naar IEC 60950 - 1
* UL en cUL naar UL 60950 - 1

## <a name="safety-compliance"></a>Naleving van de veiligheid

Uw Microsoft Azure StorSimple-apparaat voldoet aan de volgende veiligheidsclassificaties:

* Systeemproducttypegoedkeuring: UL, cUL, CE
* Naleving van de veiligheid: UL 60950, IEC 60950, EN 60950

## <a name="emc-compliance"></a>EMC-naleving

Uw Microsoft Azure StorSimple-apparaat voldoet aan de volgende EMC-classificaties.

### <a name="emissions"></a>Emissies

Het apparaat is EMC-compatibel voor uitgevoerde en uitgestraalde emissieniveaus.

* Uitgevoerde emissiegrenswaarden: CFR 47 Deel 15B Klasse A EN55022 Klasse A CISPR Klasse A
* Emissielimietniveaus: CFR 47 Deel 15B Klasse A EN55022 Klasse A CISPR Klasse A

### <a name="harmonics-and-flicker"></a>Harmonischen en flikkering

Het apparaat voldoet aan EN61000-3-2/3.

### <a name="immunity-limit-levels"></a>Immuniteitslimietniveaus

Het apparaat voldoet aan EN55024.

## <a name="ac-power-cord-compliance"></a>Voldoen aan het netsnoer

De stekker en de volledige netsnoerassemblage moeten voldoen aan de normen die geschikt zijn voor het land/de regio waar het apparaat wordt gebruikt, en ze moeten veiligheidsgoedkeuringen hebben die in dat land/de regio aanvaardbaar zijn. In de volgende tabellen worden normen voor de VS en Europa vermeld.

### <a name="ac-power-cords---usa-must-be-nrtl-listed"></a>AC netsnoeren - VS (moet NRTL vermeld)

| Onderdeel | Specificatie |
| --- | --- |
| Snoertype |SV of SVT, minimaal 18 AWG, 3 geleider, 2,0 meter maximale lengte |
| Plug |NEMA 5-15P aarding-type bevestigingsstekker met een rating van 120 V, 10 A; of IEC 320 C14, 250 V, 10 A |
| Socket |IEC 320 C-13, 250 V, 10 A |

### <a name="ac-power-cords---europe"></a>WISSELstroomkabels - Europa

| Onderdeel | Specificatie |
| --- | --- |
| Snoertype |Geharmoniseerd, H05-VVF-3G1.0 |
| Socket |IEC 320 C-13, 250 V, 10 A |

## <a name="supported-network-cables"></a>Ondersteunde netwerkkabels

Voor de 10 GbE-netwerkinterfaces, DATA 2 en DATA 3, verwijzen we naar de [lijst met ondersteunde netwerkkabels en -modules.](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)

## <a name="next-steps"></a>Volgende stappen

U bent nu klaar om een StorSimple-apparaat in uw datacenter te implementeren. Zie [Uw on-premises apparaat implementeren](storsimple-8000-deployment-walkthrough-u2.md)voor meer informatie.

