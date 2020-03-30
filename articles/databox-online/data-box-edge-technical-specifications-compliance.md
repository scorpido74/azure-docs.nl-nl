---
title: Technische specificaties en naleving van Microsoft Azure Data Box Edge| Microsoft Documenten
description: Meer informatie over de technische specificaties en naleving voor uw Azure Data Box Edge
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/02/2020
ms.author: alkohli
ms.openlocfilehash: b646ee9b727d5adf4ec1c8b5c769b3d8f5c0fc1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252036"
---
# <a name="azure-data-box-edge-technical-specifications"></a>Technische specificaties azure data box edge

De hardwareonderdelen van uw Microsoft Azure Data Box Edge-apparaat voldoen aan de technische specificaties en wettelijke normen die in dit artikel worden beschreven. De technische specificaties beschrijven de Voedingen (PSU's), opslagcapaciteit, behuizingen en milieunormen. 

## <a name="compute-memory-specifications"></a>Compute, geheugenspecificaties

Het Data Box Edge-apparaat heeft de volgende specificaties voor rekenkracht en geheugen:

| Specificatie           | Waarde                  |
|-------------------------|----------------------------|
| CPU    | 2 X 10 core CPU                     |
| Geheugen              | 128 GB RAM                  |


## <a name="fpga-specifications"></a>FPGA-specificaties

Een Field Programmeble Gate Array (FPGA) is opgenomen op elk Data Box Edge-apparaat waarmee MACHINE Learning (ML) scenario's mogelijk maakt. 

| Specificatie           | Waarde                  |
|-------------------------|----------------------------|
| Fpga   | Intel Arria 10 <br> Beschikbare Deep Neural Network (DNN) modellen zijn dezelfde als die [ondersteund door cloud FPGA-exemplaren](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-fpga-web-service#whats-supported-on-azure).| 


## <a name="power-supply-unit-specifications"></a>Specificaties van de voeding

Het Data Box Edge-apparaat heeft twee 100-240 V Power supply units (PSU's) met krachtige ventilatoren. De twee PSU's bieden een redundante vermogensconfiguratie. Als een VOEDING uitvalt, blijft het apparaat normaal werken op de andere voeding totdat de defecte module is vervangen. In de volgende tabel worden de technische specificaties van de PSU's weergegeven.

| Specificatie           | 750 W VOEDING                  |
|-------------------------|----------------------------|
| Maximaal uitgangsvermogen    | 750 W                     |
| Frequency               | 50/60 Hz                   |
| Selectie van het spanningsbereik | Auto variërend: 100-240 V AC |
| Hot pluggable           | Ja                        |

<!--## Power consumption statistics

The following table lists the typical power consumption data (actual values may vary from the published) for the Data Box Edge device.-->

## <a name="network-interface-specifications"></a>Specificaties netwerkinterface

U Data Box Edge-apparaat heeft 6 netwerkinterfaces, PORT1- PORT6.

| Specificatie           | Beschrijving                 |
|-------------------------|----------------------------|
|  Netwerkinterfaces    | 2 X 1 GbE-interfaces: Eén beheerinterface, niet configureerbaar door gebruikers, gebruikt voor eerste installatie. De andere gegevensinterface kan door gebruikers worden geconfigureerd, kan worden gebruikt voor gegevensoverdracht en is standaard DHCP. <br>2 X 25 GbE-interfaces: Deze kunnen ook als 10 GbE-interfaces werken. Deze gegevensinterfaces kunnen door gebruikers worden geconfigureerd als DHCP (standaard) of statisch. <br> 2 X 25 GbE-interfaces: Deze gegevensinterfaces kunnen door gebruikers worden geconfigureerd als DHCP (standaard) of statisch.                  |

## <a name="storage-specifications"></a>Opslagspecificaties

De Data Box Edge-apparaten hebben 9 X 2,5" NVMe SSD's, elk met een capaciteit van 1,6 TB. Van deze SSD's, 1 is een besturingssysteem schijf, en de andere 8 zijn data schijven. De totale bruikbare capaciteit voor het apparaat is ongeveer 12,5 TB. De volgende tabel bevat de details voor de opslagcapaciteit van het apparaat.

|     Specificatie                          |     Waarde             |
|--------------------------------------------|-----------------------|
|    Aantal solid-state drives (SSD's)     |    8                  |
|    Eén SSD-capaciteit                     |    1,6 TB             |
|    Totale capaciteit                          |    12,8 TB            |
|    Totale bruikbare capaciteit*                  |    ~ 12,5 TB            |

**Enige ruimte is gereserveerd voor intern gebruik.*

## <a name="enclosure-dimensions-and-weight-specifications"></a>Afmetingen en gewichtsspecificaties van de behuizing

In de volgende tabellen worden de verschillende specificaties van de behuizing voor afmetingen en gewicht vermeld.

### <a name="enclosure-dimensions"></a>Afmetingen behuizing

In de volgende tabel worden de afmetingen van de behuizing in millimeters en centimeters weergegeven.

|     Behuizing     |     Millimeters     |     Inch     |
|-------------------|---------------------|----------------|
|    Height         |    44.45            |    1.75"          |
|    Breedte          |    434.1           |    17.09"          |
|    Lengte          |    740.4           |    29.15"          |

In de volgende tabel worden de afmetingen van het verzendpakket in millimeters en centimeters weergegeven.

|     Pakket     |     Millimeters     |     Inch     |
|-------------------|---------------------|----------------|
|    Height         |    311.2            |    12.25"          |
|    Breedte          |    642.8          |    25.31"          |
|    Lengte          |   1,051.1          |    41.38"          |

### <a name="enclosure-weight"></a>Behuizingsgewicht

Het apparaatpakket weegt 66 lbs. en vereist twee personen om het te behandelen. Het gewicht van het apparaat is afhankelijk van de configuratie van de behuizing.

|     Behuizing                                 |     Gewicht          |
|-----------------------------------------------|---------------------|
|    Totaalgewicht inclusief de verpakking       |    61 pond.          |
|    Gewicht van het apparaat                       |    35 pond.          |

## <a name="enclosure-environment-specifications"></a>Omgevingsspecificaties voor behuizingen

In dit gedeelte worden de specificaties weergegeven met betrekking tot de omgeving van de behuizing, zoals temperatuur, vochtigheid en hoogte.

### <a name="temperature-and-humidity"></a>Temperatuur en vochtigheid

|     Behuizing         |     Omgevingstemperatuurbereik     |     Relatieve luchtvochtigheid in de omgeving     |     Maximaal dauwpunt     |
|-----------------------|--------------------------------------|--------------------------------------|---------------------------|
|    Operationele        |    10°C - 35°C (50°F - 86°F)         |    10% - 80% niet-condenserend.         |    29°C            |
|    Niet-operationeel    |    -40°C tot 65°C (-40°F - 149°F)     |    5% - 95% niet-condenserend.          |    33°C            |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>Luchtstroom, hoogte, schok, trillingen, oriëntatie, veiligheid en EMC

|     Behuizing                           |     Operationele specificaties                                                                                                                                                                                         |
|-----------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    Luchtstroom                              |    De systeemluchtstroom is van voor naar achter. Het systeem moet worden bediend met een lagedruk- uitlaatinstallatie. <!--Back pressure created by rack doors and obstacles should not exceed 5 pascals (0.5 mm water gauge).-->    |
|    Maximale hoogte, operationeel        |    3048 meter (10.000 voet) met maximale bedrijfstemperatuur die wordt ontgewaardeerd, bepaald door [de specificaties van de bedrijfstemperatuurderating.](#operating-temperature-de-rating-specifications)                                                                                |
|    Maximale hoogte, niet-operationeel    |    12.000 meter                                                                                                                                                                                         |
|    Shock, operationeel                   |    6 G voor 11 milliseconden in 6 oriëntaties                                                                                                                                                                         |
|    Schok, niet-operationeel               |    71 G voor 2 milliseconden in 6 oriëntaties                                                                                                                                                                           |
|    Trillingen, operationeel               |    0,26 G<sub>RMS</sub> 5 Hz tot 350 Hz willekeurig                                                                                                                                                                                     |
|    Trillingen, niet-operationeel           |    1,88 G<sub>RMS</sub> 10 Hz tot 500 Hz gedurende 15 minuten (alle zes kanten getest.)                                                                                                                                                  |
|    Oriëntatie en montage             |    19"-rackmount                                                                                                                                                                                        |
|    Veiligheid en goedkeuringen                 |    EN 60950-1:2006 +A1:2010 +A2:2013 +A11:2009 +A12:2011/IEC 60950-1:2005 ed2 +A1:2009 +A2:2013 EN 62311:2008                                                                                                                                                                       |
|    Emc                                  |    FCC A, ICES-003 <br>EN 55032:2012/CISPR 32:2012  <br>EN 55032:2015/CISPR 32:2015  <br>EN 55024:2010 +A1:2015/CISPR 24:2010 +A1:2015  <br>EN 61000-3-2:2014/IEC 61000-3-2:2014 (klasse D)   <br>EN 61000-3-3:2013/IEC 61000-3-3:2013                                                                                                                                                                                         |
|    Energie             |    Verordening (EU) nr. 617/2013                                                                                                                                                                                        |
|    Rohs           |    EN 50581:2012                                                                                                                                                                                        |


### <a name="operating-temperature-de-rating-specifications"></a>Specificaties voor het ontgraden van bedrijfstemperatuur

|     Bedrijfstemperatuur de-rating     |     Omgevingstemperatuurbereik                                                         |
|--------------------------------------------|------------------------------------------------------------------------------------------|
|    Tot 35°C                       |    De maximumtemperatuur wordt verlaagd met 1°C/300 m (1°F/547 ft) boven 950 m (3,117 ft).    |
|    35°C tot 40°C (95°F tot 104°F)            |    De maximumtemperatuur wordt verlaagd met 1°C/175 m (1°F/319 ft) boven 950 m (3,117 ft).    |
|    40°C tot 45°C (104°F tot 113°F)           |    De maximumtemperatuur wordt verlaagd met 1°C/125 m boven 950 m (3.117 ft).    |


## <a name="next-steps"></a>Volgende stappen

- [Uw Azure Data Box Edge implementeren](data-box-edge-deploy-prep.md)
