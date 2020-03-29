---
title: Installatie update 4 op StorSimple 8000-serie apparaat | Microsoft Documenten
description: Hier wordt uitgelegd hoe u StorSimple 8000 Series Update 4 installeert op uw StorSimple 8000-serie apparaat.
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
ms.date: 08/02/2017
ms.author: alkohli
ms.openlocfilehash: 5b48cbd1020cfd51fe989a9be33197f2735f21f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60860375"
---
# <a name="install-update-4-on-your-storsimple-device"></a>Update 4 installeren op uw StorSimple-apparaat

## <a name="overview"></a>Overzicht

In deze zelfstudie wordt uitgelegd hoe u Update 4 installeert op een StorSimple-apparaat waarop een eerdere softwareversie wordt uitgevoerd via de Azure-portal en de hotfix-methode wordt gebruikt. De hotfix-methode wordt gebruikt wanneer een gateway is geconfigureerd op een andere netwerkinterface dan GEGEVENS 0 van het StorSimple-apparaat en u probeert bij te werken vanaf een softwareversie voor pre-Update 1.

Update 4 bevat apparaatsoftware, USM-firmware, LSI-stuurprogramma en firmware, Storport en Spaceport, beveiligingsupdates voor het besturingssysteem en een groot aantal andere OS-updates.  De apparaatsoftware, USM-firmware, Spaceport, Storport en andere OS-updates zijn niet-storende updates. De niet-storende of regelmatige updates kunnen worden toegepast via de Azure-portal of via de hotfix-methode. De firmware-updates van de schijf zijn storende updates en kunnen alleen worden toegepast via de hotfix-methode met behulp van de Windows PowerShell-interface van het apparaat.

> [!IMPORTANT]
> * Voorafgaand aan de installatie wordt een reeks handmatige en automatische voorcontroles uitgevoerd om de status van het apparaat te bepalen in termen van hardwarestatus en netwerkconnectiviteit. Deze pre-checks worden alleen uitgevoerd als u de updates van de Azure-portal toepast.
> * We raden u aan de software en andere regelmatige updates te installeren via de Azure-portal. U mag alleen naar de Windows PowerShell-interface van het apparaat gaan (om updates te installeren) als de gatewaycontrole vooraf wordt bijgewerkt in de portal. Afhankelijk van de versie die u bijwerkt, kan het 4 uur (of meer) duren voordat de updates zijn geïnstalleerd. De updates van de onderhoudsmodus moeten ook worden geïnstalleerd via de Windows PowerShell-interface van het apparaat. Aangezien onderhoudsmodusupdates storende updates zijn, resulteren deze in een downtime voor uw apparaat.
> * Als u de optionele StorSimple Snapshot Manager uitvoert, moet u ervoor zorgen dat u de Snapshot Manager-versie hebt geüpgraded naar Update 4 voordat u het apparaat bijwerkt.


[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-4-via-the-azure-portal"></a>Update 4 installeren via de Azure-portal
Voer de volgende stappen uit om uw apparaat bij te werken naar [Update 4](storsimple-update4-release-notes.md).

> [!NOTE]
> Microsoft haalt aanvullende diagnostische informatie van het apparaat. Als gevolg hiervan, wanneer ons operationele team apparaten identificeert die problemen hebben, zijn we beter uitgerust om informatie van het apparaat te verzamelen en problemen te diagnosticeren. 

[!INCLUDE [storsimple-8000-install-update4-via-portal](../../includes/storsimple-8000-install-update4-via-portal.md)]

Controleer of uw apparaat De **StorSimple 8000-serie Update 4 (6.3.9600.17820)** gebruikt. De **laatst bijgewerkte datum** moet ook worden gewijzigd.

* U ziet nu dat de updates voor de onderhoudsmodus beschikbaar zijn (dit bericht kan tot 24 uur worden weergegeven nadat u de updates hebt geïnstalleerd). Onderhoudsmodus-updates zijn storende updates die leiden tot uitvaltijd van het apparaat en alleen kunnen worden toegepast via de Windows PowerShell-interface van uw apparaat.

* Download de updates van de onderhoudsmodus met behulp van de stappen die zijn vermeld [om hotfixes te downloaden](#to-download-hotfixes) om KB4011837 te zoeken en te downloaden, die firmware-updates voor schijven installeert (de andere updates moeten nu al zijn geïnstalleerd). Volg de stappen in [het installeren en controleer hotfixes](#to-install-and-verify-maintenance-mode-hotfixes) in de onderhoudsmodus om de updates van de onderhoudsmodus te installeren.

## <a name="install-update-4-as-a-hotfix"></a>Update 4 installeren als hotfix
De aanbevolen methode om Update 4 te installeren is via de Azure-portal.

Gebruik deze procedure als u de gatewaycontrole niet hebt wanneer u de updates probeert te installeren via de Azure-portal. De controle mislukt omdat u een gateway hebt toegewezen aan een niet-DATA 0-netwerkinterface en uw apparaat een softwareversie uitvoert vóór Update 1.

De softwareversies die kunnen worden bijgewerkt met behulp van de hotfix-methode zijn:

* Update 0.1, 0.2, 0.3
* Update 1, 1.1, 1.2
* Update 2, 2.1, 2.2
* Update 3, 3.1


De hotfix-methode omvat de volgende drie stappen:

1. Download de hotfixes uit de Microsoft Update Catalog.
2. Installeer en verifieer de hotfixes voor de normale modus.
3. Installeer en controleer de hotfix van de onderhoudsmodus.

#### <a name="download-updates-for-your-device"></a>Updates downloaden voor uw apparaat

U moet de volgende hotfixes downloaden en installeren in de voorgeschreven volgorde en de voorgestelde mappen:

| Bestellen | KB | Beschrijving | Updatetype | Installatietijd |Installeren in map|
| --- | --- | --- | --- | --- | --- |
| 1. |KB4011839 |Software-update |Standaard <br></br>Niet storend |~ 25 minuten |FirstOrderUpdate|
| 2A. |KB4011841 <br> KB4011842 |LSI-stuurprogramma- en firmware-updates <br> USM-firmware-update (versie 3.38) |Standaard <br></br>Niet storend |~ 3 uur <br> (inclusief 2A. + 2B. + 2C.)|SecondOrderUpdate|
| 2B. |KB3139398, KB3108381 <br> KB3205400, KB3142030 <br> KB3197873, KB3197873 <br> KB3192392, KB3153704 <br> KB3174644, KB3139914  |Besturingssysteem beveiligingsupdates pakket <br> Windows Server 2012 R2 downloaden |Standaard <br></br>Niet storend |- |SecondOrderUpdate|
| 2C. |KB3210083, KB3103616 <br> KB3146621, KB3121261 <br> KB3123538 |OS-updatespakket <br> Windows Server 2012 R2 downloaden |Standaard <br></br>Niet storend |- |SecondOrderUpdate|

Mogelijk moet u ook firmware-updates voor schijven installeren bovenop alle updates die in de voorgaande tabellen worden weergegeven. U controleren of u de firmware-updates van de schijf nodig hebt door de `Get-HcsFirmwareVersion` cmdlet uit te voeren. Als u deze firmwareversies `XMGJ`uitvoert: `F6C2` `VR08`, `N002` `0106`, `XGEG` `KZ50`, , , hoeft u deze updates niet te installeren.

| Bestellen | KB | Beschrijving | Updatetype | Installatietijd | Installeren in map|
| --- | --- | --- | --- | --- | --- |
| 3. |KB3121899 |Schijffirmware |Onderhoud <br></br>Storend |~ 30 minuten | ThirdOrderUpdate |

<br></br>

> [!IMPORTANT]
> * Deze procedure hoeft slechts één keer te worden uitgevoerd om update 4 toe te passen. U de Azure-portal gebruiken om volgende updates toe te passen.
> * Als het bijwerken vanaf update 3 of 3.1 wordt bijgewerkt, is de totale installatietijd bijna 4 uur.
> * Voordat u deze procedure gebruikt om de update toe te passen, moet u ervoor zorgen dat zowel de apparaatcontrollers online zijn als dat alle hardwarecomponenten in orde zijn.

Voer de volgende stappen uit om de hotfixes te downloaden en te installeren.

[!INCLUDE [storsimple-install-update4-hotfix](../../includes/storsimple-install-update4-hotfix.md)]

[!INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]

## <a name="next-steps"></a>Volgende stappen
Meer informatie over de [update 4-release](storsimple-update4-release-notes.md).

