---
title: Installatie update 5 op StorSimple 8000 serie apparaat | Microsoft Documenten
description: Hier wordt uitgelegd hoe u StorSimple 8000 Series Update 5 installeert op uw StorSimple 8000-serie apparaat.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/13/2017
ms.author: alkohli
ms.openlocfilehash: d86e77ef0148c0fac3dfa31153364de153b094ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267883"
---
# <a name="install-update-5-on-your-storsimple-device"></a>Update 5 installeren op uw StorSimple-apparaat

## <a name="overview"></a>Overzicht

In deze zelfstudie wordt uitgelegd hoe u Update 5 installeert op een StorSimple-apparaat waarop een eerdere softwareversie wordt uitgevoerd via de Azure-portal en de hotfix-methode wordt gebruikt. De hotfix-methode wordt gebruikt wanneer u Update 5 probeert te installeren op een apparaat waarop pre-Update 3-versies worden uitgevoerd. De hotfix-methode wordt ook gebruikt wanneer een gateway is geconfigureerd op een andere netwerkinterface dan GEGEVENS 0 van het StorSimple-apparaat en u probeert bij te werken vanaf een pre-Update 1-softwareversie.

Update 5 bevat apparaatsoftware, Storport en Spaceport, beveiligingsupdates van het besturingssysteem en OS-updates en firmware-updates voor schijven.  De apparaatsoftware, Spaceport, Storport, beveiliging en andere OS-updates zijn niet-storende updates. De niet-storende of regelmatige updates kunnen worden toegepast via de Azure-portal of via de hotfix-methode. De firmware-updates van de schijf zijn storende updates en worden toegepast wanneer het apparaat in onderhoudsmodus is via de hotfix-methode met behulp van de Windows PowerShell-interface van het apparaat.

> [!IMPORTANT]
> * Update 5 is een verplichte update en moet onmiddellijk worden geïnstalleerd. Zie [Update 5 release notes voor](storsimple-update5-release-notes.md)meer informatie.
> * Voorafgaand aan de installatie wordt een reeks handmatige en automatische voorcontroles uitgevoerd om de status van het apparaat te bepalen in termen van hardwarestatus en netwerkconnectiviteit. Deze pre-checks worden alleen uitgevoerd als u de updates van de Azure-portal toepast.
> * We raden u ten zeerste aan om bij het bijwerken van een apparaat met versies voorafgaand aan update 3 de updates te installeren met behulp van de hotfix-methode. Als u problemen ondervindt, [logdan een ondersteuningsticket in.](storsimple-8000-contact-microsoft-support.md)
> * We raden u aan de software en andere regelmatige updates te installeren via de Azure-portal. U mag alleen naar de Windows PowerShell-interface van het apparaat gaan (om updates te installeren) als de gatewaycontrole vooraf wordt bijgewerkt in de portal. Afhankelijk van de versie die u bijwerkt, kan het 4 uur (of meer) duren voordat de updates zijn geïnstalleerd. De updates van de onderhoudsmodus moeten worden geïnstalleerd via de Windows PowerShell-interface van het apparaat. Aangezien updates in de onderhoudsmodus storende updates zijn, leiden deze tot een downtime voor uw apparaat.
> * Als u de optionele StorSimple Snapshot Manager uitvoert, moet u ervoor zorgen dat u de Snapshot Manager-versie hebt geüpgraded naar Update 5 voordat u het apparaat bijwerkt.


[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-5-via-the-azure-portal"></a>Update 5 installeren via de Azure-portal
Voer de volgende stappen uit om uw apparaat bij te werken naar [Update 5](storsimple-update5-release-notes.md).

> [!NOTE]
> Microsoft haalt aanvullende diagnostische informatie van het apparaat. Als gevolg hiervan, wanneer ons operationele team apparaten identificeert die problemen hebben, zijn we beter uitgerust om informatie van het apparaat te verzamelen en problemen te diagnosticeren.

[!INCLUDE [storsimple-8000-install-update4-via-portal](../../includes/storsimple-8000-install-update5-via-portal.md)]

Controleer of uw apparaat **De StorSimple 8000-serie Update 5 (6.3.9600.17845)** gebruikt. De **laatst bijgewerkte datum** moet worden gewijzigd.

U ziet nu dat de updates voor de onderhoudsmodus beschikbaar zijn (dit bericht kan tot 24 uur worden weergegeven nadat u de updates hebt geïnstalleerd). De stappen voor het installeren van de update van de onderhoudsmodus worden in de volgende sectie beschreven.

[!INCLUDE [storsimple-8000-install-maintenance-mode-updates](../../includes/storsimple-8000-install-maintenance-mode-updates.md)]

## <a name="install-update-5-as-a-hotfix"></a>Update 5 installeren als hotfix

De softwareversies die kunnen worden bijgewerkt met behulp van de hotfix-methode zijn:

* Update 0.1, 0.2, 0.3
* Update 1, 1.1, 1.2
* Update 2, 2.1, 2.2
* Update 3, 3.1
* Update 4

> [!NOTE] 
> De aanbevolen methode om Update 5 te installeren is via de Azure-portal wanneer u probeert bij te werken vanaf update 3 en latere versie. Wanneer u een apparaat met versies uitvoert vóór update 3, gebruikt u deze procedure. U deze procedure ook gebruiken als u de gatewaycontrole niet hebt gecontroleerd wanneer u de updates probeert te installeren via de Azure-portal. De controle mislukt wanneer u een gateway hebt toegewezen aan een niet-DATA 0-netwerkinterface en uw apparaat een softwareversie eerder uitvoert dan Update 1.

De hotfix-methode omvat de volgende drie stappen:

1. Download de hotfixes uit de Microsoft Update Catalog.
2. Installeer en verifieer de hotfixes voor de normale modus.
3. Installeer en controleer de hotfix van de onderhoudsmodus.

#### <a name="download-updates-for-your-device"></a>Updates downloaden voor uw apparaat

U moet de volgende hotfixes downloaden en installeren in de voorgeschreven volgorde en de voorgestelde mappen:

| Bestellen | KB | Beschrijving | Updatetype | Installatietijd |Installeren in map|
| --- | --- | --- | --- | --- | --- |
| 1. |KB4037264 |Software-update<br> Download zowel _HcsSoftwareUpdate.exe als_ _CisMSDAgent.exe_ |Standaard <br></br>Niet storend |~ 25 minuten |FirstOrderUpdate|

Als u wordt bijgewerkt vanaf een apparaat met update 4, hoeft u alleen cumulatieve updates van het besturingssysteem te installeren als updates voor de tweede bestelling.

| Bestellen | KB | Beschrijving | Updatetype | Installatietijd |Installeren in map|
| --- | --- | --- | --- | --- | --- |
| 2A. |KB4025336 |Besturingssysteem cumulatieve updates pakket <br> Windows Server 2012 R2-versie downloaden |Standaard <br></br>Niet storend |- |SecondOrderUpdate|

Als u het apparaat met update 3 of eerder installeert, installeert u naast de cumulatieve updates ook het volgende.

| Bestellen | KB | Beschrijving | Updatetype | Installatietijd |Installeren in map|
| --- | --- | --- | --- | --- | --- |
| 2B. |KB4011841 <br> KB4011842 |LSI-stuurprogramma- en firmware-updates <br> USM-firmware-update (versie 3.38) |Standaard <br></br>Niet storend |~ 3 uur <br> (inclusief 2A. + 2B. + 2C.)|SecondOrderUpdate|
| 2C. |KB3139398 <br> KB3142030 <br> KB3108381 <br> KB3153704 <br> KB3174644 <br> KB3139914   |Besturingssysteem beveiligingsupdates pakket <br> Windows Server 2012 R2-versie downloaden |Standaard <br></br>Niet storend |- |SecondOrderUpdate|
| 2D. |KB3146621 <br> KB3103616 <br> KB3121261 <br> KB3123538 |OS-updatespakket <br> Windows Server 2012 R2-versie downloaden |Standaard <br></br>Niet storend |- |SecondOrderUpdate|


Mogelijk moet u ook firmware-updates voor schijven installeren bovenop alle updates die in de voorgaande tabellen worden weergegeven. U controleren of u de firmware-updates van de schijf nodig hebt door de `Get-HcsFirmwareVersion` cmdlet uit te voeren. Als u deze firmwareversies `XMGJ`uitvoert: `F6C2` `VR08`, `N003` `0107`, `XGEG` `KZ50`, , , hoeft u deze updates niet te installeren.

| Bestellen | KB | Beschrijving | Updatetype | Installatietijd | Installeren in map|
| --- | --- | --- | --- | --- | --- |
| 3. |KB4037263 |Schijffirmware |Onderhoud <br></br>Storend |~ 30 minuten | ThirdOrderUpdate |

<br></br>

> [!IMPORTANT]
> * Als het wordt bijgewerkt vanaf update 4, is de totale installatietijd bijna 4 uur.
> * Voordat u deze procedure gebruikt om de update toe te passen, moet u ervoor zorgen dat zowel de apparaatcontrollers online zijn als dat alle hardwarecomponenten in orde zijn.

Voer de volgende stappen uit om de hotfixes te downloaden en te installeren.

[!INCLUDE [storsimple-install-update5-hotfix](../../includes/storsimple-install-update5-hotfix.md)]

[!INCLUDE [storsimple-8000-install-troubleshooting](../../includes/storsimple-8000-install-troubleshooting.md)]

## <a name="next-steps"></a>Volgende stappen
Meer informatie over de [update 5-versie](storsimple-update5-release-notes.md).

