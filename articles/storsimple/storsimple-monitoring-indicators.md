---
title: StorSimple-bewakingsindicatoren | Microsoft Documenten
description: Beschrijft de lichtgevende diodes (LED's) en hoorbare alarmen die worden gebruikt om de status van het StorSimple-apparaat te controleren.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 59dee7b9-ca6d-4fd9-96e6-a0071e8d248e
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: ef8acf1c3c9211168ebacc8d62647f6789c745a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60630561"
---
# <a name="use-storsimple-monitoring-indicators-to-manage-your-device"></a>StorSimple-bewakingsindicatoren gebruiken om uw apparaat te beheren


## <a name="overview"></a>Overzicht
Uw StorSimple-apparaat bevat lichtgevende diodes (LED's) en alarmen die u gebruiken om de modules en de algehele status van het StorSimple-apparaat te controleren. De bewakingsindicatoren zijn te vinden op de hardwarecomponenten van de primaire behuizing van het apparaat en de EBOD-behuizing. De bewakingsindicatoren kunnen LED's of hoorbare alarmen zijn.

Er zijn drie LED-toestanden die worden gebruikt om de status van een module aan te geven: groen, knipperend groen tot rood-oranje of rood-oranje.  

* Groene LED's vertegenwoordigen een gezonde bedrijfsstatus.  
* Knipperend groen naar rood-oranje LED's vertegenwoordigen de aanwezigheid van niet-kritieke omstandigheden die mogelijk tussenkomst van de gebruiker vereisen.  
* Rood-oranje LED's geven aan dat er een kritieke fout aanwezig is in de module.  

De rest van dit artikel beschrijft de verschillende monitoring indicator LED's, hun locaties op de StorSimple apparaat, het apparaat status op basis van de LED-toestanden, en eventuele bijbehorende hoorbare alarmen.

## <a name="front-panel-indicator-leds"></a>LED's voorpaneelindicator
Het voorpaneel, ook wel bekend als het *bedieningspaneel* of *het deelvenster Ops,* geeft de geaggregeerde status weer van alle modules in het systeem. Het voorpaneel is identiek op de StorSimple primaire en de EBOD behuizing, en is hieronder geïllustreerd.  

   ![Voorpaneel van het apparaat][1]

Het voorpaneel bevat de volgende indicatoren:  

1. Knop Dempen
2. Power indicator LED (groen/rood-oranje)
3. Module foutindicator LED (AAN rood-oranje/UIT)
4. Logische foutindicator LED (OP rood-oranje/UIT
5. Beeldscherm van eenheids-id  

Het grote verschil tussen de LED's voor het voorpaneel voor het apparaat en die voor de EBOD-behuizing is het **systeemeenheididentificatienummer** dat op het LED-display wordt weergegeven. De standaard-eenheids-ID die op het apparaat wordt weergegeven, is **00**, terwijl de standaard-eenheids-ID die op de EBOD-behuizing wordt weergegeven **01**is. Hierdoor u snel onderscheid maken tussen het apparaat en de EBOD-behuizing wanneer het apparaat is ingeschakeld. Als uw apparaat is uitgeschakeld, gebruikt u de informatie die in [Turn on a new device](storsimple-turn-device-on-or-off.md#turn-on-a-new-device) wordt verstrekt om het apparaat te onderscheiden van de EBOD-behuizing.  

## <a name="front-panel-led-status"></a>LED-status voorpaneel
Gebruik de volgende tabel om de status te identificeren die wordt aangegeven door de LED's op het voorpaneel voor het apparaat of de EBOD-behuizing.  

| Systeemvermogen | Modulefout | Logische fout | Alarm | Status |
| --- | --- | --- | --- | --- |
| Rood-amber |UIT |UIT |N.v.t. |Wisselstroom verloren, werken op back-up stroom, of AC power ON en de controller modules werden verwijderd. |
| Groen |AAN |AAN |N.v.t. |Ops-paneelaande (5s) teststatus |
| Groen |UIT |UIT |N.v.t. |Inschakelen, alle functies goed |
| Groen |AAN |N.v.t. |PCM fout LED's, ventilator fout LED's |Elke PCM-fout, ventilatorfout, over of onder temperatuur |
| Groen |AAN |N.v.t. |I/O-module-LED's |Eventuele fout van de controllermodule |
| Groen |AAN |N.v.t. |N.v.t. |Fout in de logica van de behuizing |
| Groen |Flash |N.v.t. |Module status LED op controller module. PCM fout LED's, ventilator fout LED's |Onbekend type controllermodule geïnstalleerd, I2C-busstoring, type essentiële productgegevens (VPD)-configuratiefout |

## <a name="power-cooling-module-pcm-indicator-leds"></a>LED's voor stroomkoelingsmodule (PCM)
Power cooling module (PCM) indicator LED's zijn te vinden op de achterkant van de primaire behuizing of EBOD behuizing op elke PCM-module. In dit onderwerp wordt besproken hoe u de volgende LED's gebruiken om de status van uw StorSimple-apparaat te controleren.  

* PCM-LED's voor de primaire behuizing
* PCM LED's voor de EBOD-behuizing

## <a name="pcm-leds-for-the-primary-enclosure"></a>PCM-LED's voor de primaire behuizing
Het StorSimple-apparaat heeft een 764W PCM-module met een extra batterij. In de volgende afbeelding ziet u het LED-paneel voor het apparaat.  

   ![PCM-LED's op de primaire behuizing][2]

LED-legende:

1. Wisselstroomstoring
2. Ventilatormislukking
3. Batterijstoring
4. PCM OK
5. DC-storing
6. Batterij goed  

De status van de PCM wordt aangegeven op het LED-paneel. Het pcm LED-paneel van het apparaat heeft zes LED's. Vier van deze LED's geven de status van de voeding en de ventilator weer. De overige twee LED's geven de status van de back-upbatterijmodule in het PCM aan. U de volgende tabellen gebruiken om de status van de PCM te bepalen.  

### <a name="pcm-indicator-leds-for-power-supply-and-fan"></a>PCM-indicator LED's voor voeding en ventilator
| Status | PCM OK (groen) | AC fail (oranje) | Ventilator fail (oranje) | DC-fail (oranje) |
| --- | --- | --- | --- | --- |
| Geen wisselstroom (naar behuizing) |UIT |UIT |UIT |UIT |
| Geen wisselstroom (alleen dit PCM) |UIT |AAN |UIT |AAN |
| AC aanwezig PCM ON - OK |AAN |UIT |UIT |UIT |
| PCM mislukken (ventilator mislukken) |UIT |UIT |AAN |N.v.t. |
| PCM-fout (over versterker, over spanning, over stroom) |UIT |AAN |AAN |AAN |
| PCM (ventilator uit tolerantie) |AAN |UIT |UIT |AAN |
| Stand-bymodus |Knipperen |UIT |UIT |UIT |
| PCM-firmware downloaden |UIT |Knipperen |Knipperen |Knipperen |

### <a name="pcm-indicator-leds-for-the-backup-battery"></a>PCM-indicator-LED's voor de back-upbatterij
| Status | Batterij goed (groen) | Batterijfout (oranje) |
| --- | --- | --- |
| Batterij niet aanwezig |UIT |UIT |
| Batterij aanwezig en opgeladen |AAN |UIT |
| Acculading of onderhoudsontlading |Knipperen |UIT |
| Batterij "zachte" fout (herstelbaar) |UIT |Knipperen |
| Batterij "harde" fout (niet-herstelbaar) |UIT |AAN |
| Batterij uitgeschakeld |Knipperen |UIT |

## <a name="pcm-leds-for-the-ebod-enclosure"></a>PCM LED's voor de EBOD-behuizing
De EBOD-behuizing heeft een PCM van 580W en geen extra batterij. Het PCM-paneel voor de EBOD-behuizing heeft alleen indicator-LED's voor de voedingen en de ventilator. De volgende afbeelding toont deze LED's.

   ![PCM LED's op de EBOD-behuizing][3] 

U de volgende tabel gebruiken om de status van de PCM te bepalen.  

| Status | PCM OK (groen) | AC fail (oranje) | Ventilator fail (oranje) | DC-fail (oranje) |
| --- | --- | --- | --- | --- |
| Geen wisselstroom (naar behuizing) |UIT |UIT |UIT |UIT |
| Geen wisselstroom (alleen dit PCM) |UIT |AAN |UIT |AAN |
| AC aanwezig PCM ON – OK |AAN |UIT |UIT |UIT |
| PCM mislukken (ventilator mislukken) |UIT |UIT |AAN |X |
| PCM-fout (over versterker, over spanning, over stroom |UIT |AAN |AAN |AAN |
| PCM (ventilator uit tolerantie) |AAN |UIT |UIT |AAN |
| Stand-bymodel |Knipperen |UIT |UIT |UIT |
| PCM-firmware downloaden |UIT |Knipperen |Knipperen |Knipperen |

## <a name="controller-module-indicator-leds"></a>Leds van controllermodule-indicator
Het StorSimple-apparaat bevat LED's voor de primaire controller en de EBOD-controllermodules.   

### <a name="monitoring-leds-for-the-primary-controller"></a>LED's controleren voor de primaire controller
Met de volgende illustratie u de LED's op de primaire controller identificeren. (Alle componenten worden vermeld om te helpen bij oriëntatie.)  

   ![Monitoring LED's - primaire controller][4]

Gebruik de volgende tabel om te bepalen of de controllermodule correct werkt.  

### <a name="controller-indicator-leds"></a>Controllerindicator-LED's
| Led | Beschrijving |
| --- | --- |
| ID LED (blauw) |Geeft aan dat de module wordt geïdentificeerd. Als de blauwe LED knippert op een actieve controller, dan is de controller is de actieve controller en de andere is de stand-by controller. Zie [De actieve controller op uw apparaat identificeren](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device)voor meer informatie . |
| Fout LED (oranje) |Geeft een fout in de controller aan. |
| OK LED (groen) |Steady green geeft aan dat de controller in orde is. Knipperend groen geeft een VPD-configuratiefout van de controller aan. |
| SAS activiteit LED's (groen) |Steady green geeft een verbinding aan zonder huidige activiteit. Knipperend groen geeft aan dat de verbinding aan de gang is. |
| Ethernet-status-LED's |Rechterkant geeft link/netwerkactiviteit aan: (steady green) link active, (flashing green) network activity. De linkerkant geeft de netwerksnelheid aan: (geel) 1000 Mb/s, (groen) 100 Mb/s en (UIT) 10 Mb/s. Afhankelijk van het componentmodel kan dit lampje knipperen, zelfs als de netwerkinterface niet is ingeschakeld. |
| POST LED's |Geeft de voortgang van het opstarten aan wanneer de controller is ingeschakeld. Als het StorSimple-apparaat niet wordt opgestart, helpt deze LED Microsoft Support bij het identificeren van het punt in het opstartproces waarop de storing is opgetreden. |

> [!IMPORTANT]
> Als de fout-LED brandt, is er een probleem met de controllermodule die kan worden opgelost door de controller opnieuw op te starten. Neem contact op met Microsoft Support als het opnieuw opstarten van de controller dit probleem niet oplost.  
> 
> 

### <a name="monitoring-leds-for-the-ebod-ebod-enclosure"></a>Monitoring LED's voor de EBOD (EBOD-behuizing)
Elk van de 6 Gb/s SAS EBOD-controllers heeft LED's die de status aangeven, zoals in de volgende afbeelding wordt weergegeven.  

  ![Monitoring LED's - EBOD-behuizing][5]

Gebruik de volgende tabel om te bepalen of de EBOD-controllermodule normaal werkt.  

### <a name="ebod-controller-module-indicator-leds"></a>EBOD-module-indicator-indicator LED's
| Status | I/O module OK (groen) | I/O-modulefout (oranje) | Gasthavenactiviteit (groen) |
| --- | --- | --- | --- |
| Controllermodule OK |AAN |UIT |- |
| Fout van de controllermodule |UIT |AAN |- |
| Geen externe hostpoortverbinding |- |- |UIT |
| Externe hostpoortverbinding – geen activiteit |- |- |AAN |
| Externe hostpoortverbinding - activiteit |- |- |Knipperen |
| Metagegevensvan controllermodule |Knipperen |- |- |

## <a name="disk-drive-indicator-leds-for-the-primary-enclosure-and-ebod-enclosure"></a>LED-indicatorvoor de primaire behuizing en EBOD-behuizing
Het StorSimple-apparaat heeft schijven in zowel de primaire behuizing als de EBOD-behuizing. Elk schijfstation bevat bewakingsindicator-LED's, zoals beschreven in deze sectie. 

Voor de schijven wordt de status van de schijf aangegeven door een groene LED en een rood-oranje LED gemonteerd op de voorkant van elke drive carrier module. De volgende afbeelding toont deze LED's.

  ![LED's voor schijfstations][6]

Gebruik de volgende tabel om de status van elk schijfstation te bepalen, wat weer van invloed is op de algemene LED-status voor het voorpaneel.  

### <a name="disk-drive-indicator-leds-for-the-ebod-enclosure"></a>LED's voor de Led-indicator voor de EBOD-behuizing
| Status | Activiteit OK LED (groen) | Fout LED (rood-oranje) | LED van het deelvenster Gekoppeld ops |
| --- | --- | --- | --- |
| Geen station geïnstalleerd |UIT |UIT |Geen |
| Drive geïnstalleerd en operationeel |Knipperen aan/uit met activiteit |X |Geen |
| SCSI Enclosure Services (SES) apparaatidentiteitsset |AAN |Knipperend 1 seconde aan/1 seconde af |Geen |
| SES-foutbitset voor ses-apparaten |AAN |AAN |Logische fout (rood) |
| Storing van het stroomregelcircuit |UIT |AAN |Modulefout (rood) |

## <a name="audible-alarms"></a>Hoorbare alarmen
Een StorSimple-apparaat bevat hoorbare alarmen die zijn gekoppeld aan zowel de primaire behuizing als de EBOD-behuizing. Een hoorbaar alarm bevindt zich op het voorpaneel (ook wel bekend als het ops-paneel) van beide behuizingen. Het hoorbare alarm geeft aan wanneer er een storing aanwezig is. De volgende voorwaarden activeren het alarm:  

* Ventilatorfout of -storing
* Spanning buiten bereik
* Boven of onder temperatuurconditie
* Thermische overschrijding
* Systeemfout
* Logische fout
* Stroomstoring
* Verwijdering van een stroomkoelingsmodule (PCM)  

De volgende tabel beschrijft de verschillende alarmtoestanden.  

### <a name="alarm-states"></a>Alarmtoestanden
| Alarmstatus | Actie | Actie met ingedrukt mute-knop |
| --- | --- | --- |
| S0 |Normale modus: stil |Piep twee keer |
| S1 |Foutmodus: 1 seconde aan/1 seconde uit |Overgang naar S2 of S3 (zie notities) |
| S2 |Remind-modus: intermitterende pieptoon |Geen |
| S3 |Gedempte modus: stil |Geen |
| S4 |Kritieke foutmodus: continu alarm |Niet beschikbaar: niet actief dempen |

> [!NOTE]
> * Als u in alarmtoestand S1 niet binnen 2 minuten op dempen drukt, schakelt de status automatisch over naar S2 of S3.  
> * Alarmtoestanden S1 naar S4 keren terug naar S0 nadat de storing is opgelost.  
> * Kritieke foutstatus S4 kan vanuit elke andere status worden ingevoerd.  


U het hoorbare alarm dempen door op de mute-knop op het ops-paneel te drukken. Automatisch dempen gebeurt na twee minuten als de mute-schakelaar niet handmatig wordt bediend. Wanneer het alarm wordt gedempt, blijft het klinken met korte intermitterende piepjes om aan te geven dat er nog steeds een probleem bestaat. Het alarm zal stil zijn wanneer alle problemen zijn opgelost.

De volgende tabel beschrijft de verschillende alarmomstandigheden.

### <a name="alarm-conditions"></a>Alarmomstandigheden
| Status | Severity | Alarm | LED-paneel ops |
| --- | --- | --- | --- |
| PCM alert – verlies van DC-stroom door één PCM |Fout – geen verlies van redundantie |S1 |Modulefout |
| PCM alert – verlies van DC-stroom door één PCM |Fout – ontslagverlies |S1 |Modulefout |
| PCM-ventilator mislukt |Fout – ontslagverlies |S1 |Modulefout |
| SBB-module ontdekt PCM-fout |Schuld |S1 |Modulefout |
| PCM verwijderd |Configuratiefout |Geen |Modulefout |
| Fout in de configuratie van de behuizing |Fout – kritiek |S1 |Modulefout |
| Waarschuwing lage waarschuwingstemperatuur |Waarschuwing |S1 |Modulefout |
| Hoge waarschuwingstemperatuurwaarschuwing |Waarschuwing |S1 |Modulefout |
| Over temperatuuralarm |Fout – kritiek |S1 |Modulefout |
| I2C bus storing |Fout – ontslagverlies |S1 |Modulefout |
| Communicatiefout van het deelvenster Ops (I2C) |Fout – kritiek |S1 |Modulefout |
| Controllerfout |Fout – kritiek |S1 |Modulefout |
| SBB-interfacemodulefout |Fout – kritiek |S1 |Modulefout |
| SBB-interfacemodulefout – Geen werkende modules meer over |Fout – kritiek |S4 |Modulefout |
| SBB-interfacemodule verwijderd |Waarschuwing |Geen |Modulefout |
| Schijfstoring |Waarschuwing – geen verlies van aandrijfvermogen |S1 |Modulefout |
| Schijfstoring |Fout – kritiek; verlies van aandrijfvermogen |S1 |Modulefout |
| Station verwijderd |Waarschuwing |Geen |Modulefout |
| Onvoldoende stroom beschikbaar |Waarschuwing |geen |Modulefout |

## <a name="next-steps"></a>Volgende stappen
Meer informatie over [StorSimple-hardwarecomponenten en -status](storsimple-8000-monitor-hardware-status.md).

[1]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE01.png
[2]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE02.png
[3]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE03.png
[4]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE04.png
[5]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE05.png
[6]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE06.png


