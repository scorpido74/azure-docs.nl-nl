---
title: Microsoft Azure Stack Edge met GPU technische specificaties en naleving | Microsoft Docs
description: Meer informatie over de technische specificaties en naleving voor uw Azure Stack edge-apparaat met GPU
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/26/2020
ms.author: alkohli
ms.openlocfilehash: 87c96ef6487895d3230541f0ae5fe15c5a645368
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2020
ms.locfileid: "89084049"
---
# <a name="azure-stack-edge-technical-specifications-and-compliance"></a>Technische specificaties en naleving van Azure Stack rand

De hardwareonderdelen van uw Azure Stack rand met een onboard grafische processor (GPU) voldoen aan de technische specificaties en regelgevings normen die in dit artikel worden beschreven. De technische specificaties beschrijven hardware, voedingen (PSUs), opslag capaciteit, behuizingen en omgevings standaarden.

## <a name="compute-and-memory-specifications"></a>Specificaties van Compute en geheugen

Het Azure Stack edge-apparaat heeft de volgende specificaties voor Compute en geheugen:

| Specificatie           | Waarde                  |
|-------------------------|----------------------------|
| CPU                     | 2 X Intel Xeon Silver 4214 (trapsgewijs Lake) CPU            |
| Geheugen                  | 128 (8x16 GB) GB RAM                     |


## <a name="compute-acceleration-specifications"></a>Specificaties van Compute Acceleration

Er is een GPU (graphics processing unit) opgenomen op elk Azure Stack edge-apparaat dat Kubernetes, dieper leren en machine learning scenario's mogelijk maakt.

| Specificatie           | Waarde                  |
|-------------------------|----------------------------|
| GPU   | Een of twee nVidia T4-Gpu's <br> Zie [NVIDIA T4](https://www.nvidia.com/en-us/data-center/tesla-t4/)voor meer informatie.| 


## <a name="power-supply-unit-specifications"></a>Specificaties van voedings eenheid voor voeding

Het Azure Stack edge-apparaat heeft twee 100-240 V-voedings eenheden (PSUs) met hoge prestaties. De twee PSUs bieden een redundante energie configuratie. Als een PSU mislukt, blijft het apparaat normaal op de andere PSU functioneren totdat de module failed wordt vervangen. De volgende tabel geeft een lijst van de technische specificaties van de PSUs.

| Specificatie           | 750 W PSU                  |
|-------------------------|----------------------------|
| Maximale uitvoer kracht    | 750 W                     |
| Frequency               | 50/60 Hz                   |
| Selectie van voltage bereik | Automatisch variërend: 100-240 V AC |
| Hot pluggable           | Ja                        |


## <a name="network-interface-specifications"></a>Specificaties van netwerk interface

Uw Azure Stack edge-apparaat heeft zes netwerk interfaces, PORT1-PORT6.

| Specificatie           | Beschrijving                 |
|-------------------------|----------------------------|
|  Netwerkinterfaces    | **2 X 1 GbE-interfaces** – 1 beheer interface poort 1 wordt gebruikt voor initiële installatie en is standaard statisch. Nadat de eerste installatie is voltooid, kunt u de interface gebruiken voor gegevens met elk IP-adres. Bij het opnieuw instellen wordt de interface echter teruggezet naar het statische IP-adres. <br>De andere interface poort 2 kan door de gebruiker worden geconfigureerd, kan worden gebruikt voor gegevens overdracht en is standaard DHCP. <br>**4 X 25 GbE-interfaces** : deze gegevens interfaces, poort 3 tot en met poort 6, kunnen worden geconfigureerd door de gebruiker als DHCP (standaard) of statisch. Deze kunnen ook worden gebruikt als 10 GbE-interfaces.  | 

Uw Azure Stack edge-apparaat heeft de volgende netwerkhardware:

* **Aangepaste micro soft QLogic Cavium 25G NDC-adapter** -poort 1 via poort 4.
* **Mellanox Dual Port 25G connectx-4-kanaal netwerk adapter** -poort 5 en poort 6.

Hier vindt u de Details voor de Mellanox-kaart:

| Parameter           | Beschrijving                 |
|-------------------------|----------------------------|
| Model    | Connectx®-4 LX EN netwerk interface kaart                      |
| Model beschrijving               | 25GbE Dual Port SFP28; PCIe 3.0 X8; BVGS R6                    |
| Onderdeel nummer van apparaat (R640) | MCX4121A-ACAT  |
| PSID (R640)           | MT_2420110034                         |

Voor een volledige lijst met ondersteunde kabels, switches en transceivers voor deze netwerk kaarten gaat u naar:

- [Samenwerkings matrix QLogic Cavium 25G NDC-adapter](https://www.marvell.com/documents/xalflardzafh32cfvi0z/).
- [Mellanox Dual Port 25G connectx-4-kanaal netwerk adapter compatibele producten](https://docs.mellanox.com/display/ConnectX4LxFirmwarev14271016/Firmware+Compatible+Products).  

## <a name="storage-specifications"></a>Opslag specificaties

De Azure Stack edge-apparaten hebben vijf 2,5 ' NVMe DC P4610 Ssd's, elk met een capaciteit van 1,6 TB. De opstart schijf is een SATA SSD van 240 GB. De totale bruikbare capaciteit van het apparaat is ongeveer 8,28 TB. De volgende tabel geeft een overzicht van de opslag capaciteit van het apparaat.

|     Specificatie                          |     Waarde             |
|--------------------------------------------|-----------------------|
|    Boot SATA-schijven (Solid-State Drive)      |    1                  |
|    Aantal NVMe-Ssd's                     |    5                  |
|    Capaciteit van opstart SSD                       |    240 GB             |
|    Eén NVMe-SSD-capaciteit                |    1,6 TB             |
|    Totale capaciteit                          |    8,28 TB            |
|    Totale bruikbare capaciteit *                  |    ~ 7,95 TB          |
|    SAS-controller                          |    HBA330 12 Gbps     |


**Er is ruimte gereserveerd voor intern gebruik.*

<!--Remove based on feedback from Ravi
## Other hardware specifications

Your Azure Stack Edge device also contains the following hardware:

* iDRAC baseboard management
* Performance fans
* Custom ID module-->

## <a name="enclosure-dimensions-and-weight-specifications"></a>Afmetingen van behuizing en gewichts specificaties

In de volgende tabellen staan de verschillende specificaties van de behuizing voor dimensies en gewicht.

### <a name="enclosure-dimensions"></a>Afmetingen van behuizing

De volgende tabel bevat de afmetingen van de 1U-behuizing in millimeters en inches.

|     Sluit     |     Millimeters     |     Mm     |
|-------------------|---------------------|----------------|
|    Hoogte         |    44,45            |    1,75 '       |
|    Breedte          |    434,1            |    17,09 '      |
|    Lengte         |    740,4            |    29,15 '      |

De volgende tabel bevat de afmetingen van het verzend pakket in millimeters en inches.

|     Pakket     |     Millimeters     |     Mm     |
|-------------------|---------------------|----------------|
|    Hoogte         |    311,2            |    12,25 '          |
|    Breedte          |    642,8            |    25,31 '          |
|    Lengte         |    1.051,1          |    41,38 '          |

### <a name="enclosure-weight"></a>Gewicht van behuizing

Het apparaatstuurprogrammapakket weegt 66 lbs. en er zijn twee personen nodig om deze af te handelen. Het gewicht van het apparaat is afhankelijk van de configuratie van de behuizing.

|     Sluit                                 |     Gewicht          |
|-----------------------------------------------|---------------------|
|    Totaal gewicht inclusief de verpakking       |    61 lbs.          |
|    Gewicht van het apparaat                       |    35 lbs.          |

## <a name="enclosure-environment-specifications"></a>Specificaties van behuizing-omgeving

In deze sectie vindt u de specificaties met betrekking tot de behuizing-omgeving, zoals Tempe ratuur, vochtigheid en hoogte.

### <a name="temperature-and-humidity"></a>Tempe ratuur en vochtigheid

|     Sluit         |     Omgevings temperatuur bereik     |     Relatieve lucht vochtigheid     |     Maximum Dew-punt     |
|-----------------------|--------------------------------------|--------------------------------------|---------------------------|
|    Operationeel        |    10 °C-35 °C (50 °F-86 °F)         |    10%-80% niet-condenserend.         |    29 °C (84 °F)            |
|    Niet-operationeel    |    -40 °C tot 65 °C (-40 °F-149 °F)     |    5%-95% niet-condenserend.          |    33 °C (91 °F)            |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>Lucht stroom, hoogte, schokken, trillingen, oriëntatie, veiligheid en EMC

|     Sluit                           |     Operationele specificaties                                                                                                                                                                                         |
|-----------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    Lucht stroom                              |    De stroom van het systeem is aan de achterzijde. Het systeem moet worden bediend met een installatie met een lage druk, een achterlopende uitlaat. <!--Back pressure created by rack doors and obstacles should not exceed 5 pascals (0.5 mm water gauge).-->    |
|    Maximum hoogte, operationeel        |    3048 meters (10.000 meter) met een maximale gebruiks temperatuur van de nominale waarde bepaald door [de specificaties van de gebruiks temperatuur van de beoordeling](#operating-temperature-de-rating-specifications).                                                                                |
|    Maximale hoogte, niet-operationeel    |    12.000 meters (39.370 meter)                                                                                                                                                                                         |
|    Schokken, operationeel                   |    6 G voor 11 milliseconden in 6 standen                                                                                                                                                                         |
|    Schokken, niet-operationeel               |    71 G voor 2 milliseconden in 6 standen                                                                                                                                                                           |
|    Trillingen, operationeel               |    0,26 G<sub>RMS</sub> 5 hz tot 350 Hz wille keurig                                                                                                                                                                                     |
|    Trillingen, niet-operationeel           |    1,88 G<sub>RMS</sub> 10 hz tot 500 Hz gedurende 15 minuten (alle zes zijden zijn getest)                                                                                                                                                  |
|    Afdruk stand en koppelen             |    Standard 19-rack koppeling (1U)                                                                                                                                                                                       |
|    Veiligheid en goed keuringen                 |    EN 60950-1:2006 + a1:2010 + a2:2013 + A11:2009 + A12:2011/IEC 60950-1:2005 ed2 + a1:2009 + a2:2013 EN 62311:2008                                                                                                                                                                       |
|    |                                  |    FCC A, ICES-003 <br>EN 55032:2012/CISPR 32:2012  <br>EN 55032:2015/CISPR 32:2015  <br>EN 55024:2010 + A1:2015/CISPR 24:2010 + A1:2015  <br>EN 61000-3-2:2014/IEC 61000-3-2:2014 (klasse D)   <br>EN 61000-3-3:2013/IEC 61000-3-3:2013                                                                                                                                                                                         |
|    Energie             |    Verordening (EU) van de Commissie. 617/2013                                                                                                                                                                                        |
|    BvGS           |    EN 50581:2012                                                                                                                                                                                        |

### <a name="operating-temperature-de-rating-specifications"></a>Specificaties voor de gebruiks temperatuur van de beoordeling

|     De beoordeling van de gebruiks temperatuur     |     Omgevings temperatuur bereik                                                         |
|--------------------------------------------|------------------------------------------------------------------------------------------|
|    Maxi maal 35 °C (95 °F)                       |    De maximale Tempe ratuur wordt verlaagd met 1 °C/300 m (1 °F/547 ft) boven 950 m (3.117 ft).    |
|    35 °C tot 40 °C (95 °F tot 104 °F)            |    De maximale Tempe ratuur wordt verlaagd met 1 °C/175 m (1 °F/319 ft) van meer dan 950 m (3.117 ft).    |
|    40 °C tot 45 °C (104 °F tot 113 °F)           |    De maximale Tempe ratuur wordt verlaagd met 1 °C/125 m (1 °F/228 ft) van meer dan 950 m (3.117 ft).    |

## <a name="next-steps"></a>Volgende stappen

[Uw Azure Stack Edge implementeren](azure-stack-edge-gpu-deploy-prep.md)
