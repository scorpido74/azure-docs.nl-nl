---
title: StorSimple 8000-serie hardwareonderdelen vervanging | Microsoft Documenten
description: Beschrijft hoe u de PCM's, batterij, controllermodules, EBOD-controllers, schijven en chassis van een StorSimple-apparaat veilig vervangen.
services: storsimple
documentationcenter: ''
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
ms.custom: ''
ms.openlocfilehash: e05a37122647d4979089f0ba00b1fc15f9b84b0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60321735"
---
# <a name="replace-a-hardware-component-on-your-storsimple-8000-series-device"></a>Een hardwarecomponent vervangen op uw StorSimple 8000-serie

## <a name="overview"></a>Overzicht
De zelfstudies voor het vervangen van hardwarecomponenten beschrijven de hardwareonderdelen van uw Microsoft Azure StorSimple 8000-serie apparaat en de stappen die nodig zijn om deze te verwijderen en te vervangen. In dit artikel worden de veiligheidspictogrammen beschreven, worden verwijzingen naar de gedetailleerde zelfstudies weergegeven en worden de onderdelen vermeld die vervangbaar zijn.

> [!IMPORTANT]
> Voordat u een StorSimple-onderdeel probeert te verwijderen of te vervangen, moet u de conventies en andere [veiligheidsmaatregelen](storsimple-safety.md)van het [veiligheidspictogram](#safety-icon-conventions) bekijken.


### <a name="safety-icon-conventions"></a>Conventies voor veiligheidspictograms
In de volgende tabel worden de veiligheidspictogrammen beschreven die in deze zelfstudies worden gebruikt. Let goed op deze veiligheidspictogrammen terwijl u de stappen doorneemt om apparaatonderdelen te verwijderen en te vervangen.

| Pictogram | Tekst | Aanvullende informatie |
|:--- |:--- |:--- |
| ![Waarschuwingspictogram](./media/storsimple-hardware-component-replacement/Warning.png) |**Gevaar!** |Wijst op een gevaarlijke situatie die, indien niet vermeden, zal leiden tot de dood of ernstig letsel. Dit signaal woord is beperkt tot de meest extreme situaties. |
| ![Waarschuwingspictogram](./media/storsimple-hardware-component-replacement/Warning.png) |**Waarschuwing!** |Wijst op een gevaarlijke situatie die, indien niet vermeden, kan leiden tot de dood of ernstig letsel. |
| ![Pictogram Voorzichtigheid](./media/storsimple-hardware-component-replacement/Caution.png) |**Voorzichtigheid!** |Wijst op een gevaarlijke situatie die, indien niet vermeden, kan leiden tot lichte of matige verwondingen. |
| ![Pictogram Bericht](./media/storsimple-hardware-component-replacement/NoticeIcon.png) |**LET OP:** |Geeft informatie aan die belangrijk wordt geacht, maar niet met gevaar. |
| ![Pictogram Elektrische schok](./media/storsimple-hardware-component-replacement/Electric.png) |**Elektrisch schokgevaar** |Geeft hoge spanning aan. |
| ![Pictogram Zwaar gewicht](./media/storsimple-hardware-component-replacement/Weight.png) |**Zwaar gewicht** | |
| ![Pictogram geen gebruikersbruikbare onderdelen](./media/storsimple-hardware-component-replacement/NoUserServiceableParts.png) |**Geen gebruikersbruikbare onderdelen** |Geen toegang, tenzij goed opgeleid. |
| ![Pictogram Leesinstructies](./media/storsimple-hardware-component-replacement/ReadInstructions.png) |**Lees eerst alle instructies** | |
| ![Pictogram Tipgevaar](./media/storsimple-hardware-component-replacement/TipHazard.png) |**Tip Hazard** | |

### <a name="before-you-begin"></a>Voordat u begint
Maak uzelf vertrouwd met de veiligheidsinformatie over uw apparaat en veiligheidspictogrammen die in deze zelfstudie worden gebruikt. Ga [naar Veilig installeren en bedienen van uw StorSimple apparaat](storsimple-safety.md) voor volledige informatie. Controleer de [veiligheidsmaatregelen](storsimple-safety.md#handling-precautions) voordat u uw StorSimple-apparaat behandelt.

Voordat u een component probeert te vervangen, moet u rekening houden met de volgende informatie.

![Waarschuwing](./media/storsimple-hardware-component-replacement/Warning.png) ![Pictogram Elektrische](./media/storsimple-hardware-component-replacement/Electric.png) Schok Icon **WAARSCHUWING!**

* Verminder uzelf goed met behulp van een elektrostatische ontlading of antistatische mat bij het hanteren van modules en componenten van uw StorSimple-apparaat.
* Raak geen circuits aan. Gebruik de meegeleverde handgrepen en geleiders tijdens het hanteren van onderdelen die mogelijk ontbloot circuit.

![Waarschuwing](./media/storsimple-hardware-component-replacement/Warning.png) ![Pictogram](./media/storsimple-hardware-component-replacement/NoticeIcon.png) **NOTICE:** Kennisgeving:

Wanneer u een module vervangt, **laat u NOOIT een lege ruimte achterin de behuizing achter.** Verkrijg een vervangende of lege module voordat u het probleemonderdeel verwijdert.

## <a name="hardware-component-replacement-procedures"></a>Vervangingsprocedures voor hardwarecomponenten
Uw StorSimple 8000-serie apparaat bestaat uit verschillende plug-in modules in de primaire en/of EBOD behuizingen. De 8100 heeft een enkele primaire behuizing, terwijl de 8600 is een dubbele behuizing apparaat met een primaire behuizing en een EBOD behuizing.

De belangrijkste hardwarecomponenten in uw apparaat worden samengevat in de volgende tabellen. Klik op de koppeling in de kolom **Vervangenprocedure** om naar de bijbehorende zelfstudie te gaan.

| Onderdelen | # Aanwezig | Plug-in module? | Vervangingsprocedure |
|:--- |:--- |:--- |:--- |
| Chassis |1 |Nee |[Het chassis van uw StorSimple-apparaat vervangen](storsimple-8000-chassis-replacement.md) |
| Primaire controllers |2 |Ja |[Een controllermodule op uw StorSimple-apparaat vervangen](storsimple-8000-controller-replacement.md) |
| 764W-stroom- en koelmodules (PCM's) |2 |Ja |[Stroom- en koelmodule van StorSimple-apparaat vervangen](storsimple-8000-power-cooling-module-replacement.md) |
| Back-upbatterij |2 |Ja |[Noodaccumodule van StorSimple-apparaat vervangen](storsimple-8000-battery-replacement.md) |
| Schijfstations |12 |Ja |[Een schijfstation op uw StorSimple-apparaat vervangen](storsimple-8000-disk-drive-replacement.md) |

**Tabel 1** Hardwarecomponenten in de primaire behuizing

De primaire behuizing en de EBOD-behuizing verschillen in hun I/O-modules. Bovendien hebben de PCM's verschillende wattage. De PCM's in de primaire behuizing zijn 764 W, terwijl die in de EBOD-behuizing 580 W zijn. De PCM's in de primaire behuizing bevatten ook een back-up batterijmodule.

| Onderdelen | # Aanwezig | Plug-in module? | Vervangingsprocedure |
|:--- |:--- |:--- |:--- |
| Chassis |1 |Nee |[Het chassis van uw StorSimple-apparaat vervangen](storsimple-8000-chassis-replacement.md) |
| EBOD-controllers |2 |Ja |[Een EBOD-controller vervangen op uw StorSimple-apparaat](storsimple-8000-ebod-controller-replacement.md) |
| 580W-stroom- en koelmodules (PCM's) |2 |Ja |[Stroom- en koelmodule van StorSimple-apparaat vervangen](storsimple-8000-power-cooling-module-replacement.md) |
| Schijfstations |12 |Ja |[Een schijfstation op uw StorSimple-apparaat vervangen](storsimple-8000-disk-drive-replacement.md) |

**Tabel 2** Hardwarecomponenten in de EBOD-behuizing

De plug-in modules op het apparaat worden gemarkeerd in de volgende diagrammen aan de voor- en achterzijde. U deze diagrammen gebruiken om de locatie van de verschillende plug-inmodules te bepalen als een vervanging nodig is. Het voorste diagram toont de schijven en de achterste diagrammen van de EBOD-behuizing en de primaire behuizing tonen de plug-inmodules.

![Voorpaneel van apparaat met schijven](./media/storsimple-hardware-component-replacement/IC741028.png)

**Figuur 1** Voorzijde van het apparaat

| Label | Beschrijving |
|:--- |:--- |
| 0 - 11 |Schijven (totaal 12) |

Zowel de primaire behuizing als de EBOD-behuizing hebben aandrijfdraagmodules. Het chassis herbergt twaalf 3,5"-schijven gerangschikt in een 3 bij 4 formaat.

![Backplane van primaire behuizingsmodules van het apparaat](./media/storsimple-hardware-component-replacement/IC740994.png)

**Figuur 2** Achterkant van de primaire behuizing

| Label | Beschrijving |
|:--- |:--- |
| 1 |PCM 0 |
| 2 |PCM 1 |
| 3 |Controller 0 |
| 4 |Controller 1 |

![Backplane van apparaat EBOD-invoegmodules](./media/storsimple-hardware-component-replacement/IC769599.png)

**Figuur 3** Achterkant van de EBOD-behuizing

| Label | Beschrijving |
|:--- |:--- |
| 1 |PCM 0 |
| 2 |PCM 1 |
| 3 |EBOD-controller 0 |
| 4 |EBOD-controller 1 |

## <a name="field-replaceable-units"></a>Veldvervangbare eenheden
De volgende veldvervangbare eenheden (FRU's) zijn beschikbaar voor uw StorSimple-apparaat:

* Chassis (inclusief het geïntegreerde bedieningspaneel)
* 764 W AC PCM
* 580 W AC PCM
* Harde schijf met schijfdragermodule
* Controllermodule
* EBOD-controllermodule
* Back-up batterijmodule
* Rack montage rail kit

Neem [contact op met Microsoft Support](storsimple-8000-contact-microsoft-support.md) om een van deze vervangende eenheden te bestellen.

## <a name="next-steps"></a>Volgende stappen
Bekijk alle [veiligheidsinformatie](storsimple-safety.md) voordat u een StorSimple-hardwarecomponent probeert te vervangen.

