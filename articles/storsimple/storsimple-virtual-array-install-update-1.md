---
title: Update 1.0 installeren op StorSimple Virtual Array | Microsoft Documenten
description: Beschrijft hoe u de webgebruikers van StorSimple Virtual Array gebruiken om updates toe te passen met de Azure-portal- en hotfix-methode
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
ms.date: 11/02/2017
ms.author: alkohli
ms.openlocfilehash: fa53213e577028628d48db91704578e23888f2a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254506"
---
# <a name="install-update-10-on-your-storsimple-virtual-array"></a>Installeer Update 1.0 op uw StorSimple Virtual Array

## <a name="overview"></a>Overzicht

In dit artikel worden de stappen beschreven die nodig zijn om Update 1.0 op uw StorSimple Virtual Array te installeren via de lokale webgebruikersinterface en via de Azure-portal.

U past de software-updates of hotfixes toe om uw StorSimple Virtual Array up-to-date te houden. Voordat u een update toepast, raden we u aan de volumes of aandelen eerst offline te halen op de host en vervolgens op het apparaat. Dit minimaliseert de kans op beschadigde gegevens. Nadat de volumes of aandelen offline zijn, moet u ook een handmatige back-up van het apparaat maken.

> [!IMPORTANT]
> - Update 1.0 komt overeen met de softwareversie **van 10.0.10296.0** op uw apparaat. Ga voor informatie over wat er nieuw is in deze update naar [Notities vrijgeven voor Update 1.0](storsimple-virtual-array-update-1-release-notes.md).
>
> - Houd er rekening mee dat het installeren van een update of hotfix uw apparaat opnieuw start. Gezien het feit dat de StorSimple Virtual Array een enkel knooppuntapparaat is, wordt elke I/O in uitvoering verstoord en ervaart uw apparaat downtime.
>
> - Update 1 is alleen beschikbaar in de Azure-portal als de virtuele array Update 0.6 uitvoert. Voor virtuele arrays met pre-Update 0.6-versies moet u Update 0.6 eerst installeren en vervolgens Update 1 installeren.

## <a name="use-the-azure-portal"></a>De Azure-portal gebruiken

Als u Update 0.2 en hoger uitvoert, raden we u aan updates te installeren via de Azure-portal. De portalprocedure vereist dat de gebruiker de updates scant, downloadt en vervolgens installeert. Afhankelijk van de softwareversie die uw virtuele array uitvoert, is het toepassen van update via de Azure-portal anders.

 - Als op uw virtuele array update 0.6 wordt uitgevoerd, installeert de Azure-portal update 1 (10.0.10296.0) rechtstreeks op uw apparaat. Deze procedure duurt ongeveer 7 minuten.
 - Als uw virtuele array een versie vóór Update 0.6 uitvoert, wordt de update in twee fasen uitgevoerd. De Azure-portal installeert update 0.6 (10.0.10293.0) eerst op uw apparaat. De virtuele array wordt opnieuw opgestart en de portal installeert vervolgens Update 1 (10.0.10296.0) op uw apparaat. Deze procedure duurt ongeveer 15 minuten.


[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-1.md)]

Nadat de installatie is voltooid, gaat u naar uw StorSimple Device Manager-service. Selecteer **Apparaten** en selecteer en klik op het apparaat dat u zojuist hebt bijgewerkt. Ga naar **Instellingen > > Apparaatupdates beheren**. De weergegeven softwareversie moet **10.0.10296.0**zijn.

![Softwareversie na update](./media/storsimple-virtual-array-install-update-1/azupdate17m1.png)

## <a name="use-the-local-web-ui"></a>De lokale webgebruikersinterface gebruiken

Er zijn twee stappen bij het gebruik van de lokale web-gebruikersinterface:

* Download de update of de hotfix
* De update of de hotfix installeren

> [!IMPORTANT] 
> **Ga alleen door met deze update als u Update 0.6 (10.0.10293.0) uitvoert. Als u een eerdere versie uitvoert, [installeert u update 0.6](storsimple-virtual-array-install-update-06.md) eerst op uw apparaat en past u Update 1 toe.**

### <a name="download-the-update-or-the-hotfix"></a>Download de update of de hotfix

Als op uw virtuele array update 0.6 wordt uitgevoerd, voert u de volgende stappen uit om Update 1 uit de Microsoft Update-catalogus te downloaden.

#### <a name="to-download-the-update-or-the-hotfix"></a>De update of de hotfix downloaden

1. Start Internet Explorer [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com)en navigeer naar .

2. Als u de Microsoft Update Catalog voor het eerst op deze computer gebruikt, klikt u op **Installeren** wanneer u wordt gevraagd de invoegtoepassing Microsoft Update Catalog te installeren.

3. Voer in het zoekvak van de Microsoft Update Catalog het KB-nummer (Knowledge Base) in van het hotfix dat u wilt downloaden. Voer **4047203** voor Update 1.0 in en klik op **Zoeken**.
   
    De hotfix-vermelding wordt bijvoorbeeld **StorSimple Virtual Array Update 1.0**weergegeven.
   
    ![Catalogus doorzoeken](./media/storsimple-virtual-array-install-update-1/download1.png)

4. Klik **op Downloaden**.

5. Download de twee bestanden naar een map. U de map ook kopiëren naar een netwerkshare dat bereikbaar is vanaf het apparaat.

6. Open de map waar de bestanden zich bevinden.

    ![Bestanden in het pakket](./media/storsimple-virtual-array-install-update-1/update01folder.png)

    U ziet twee bestanden:
    -  Een Microsoft Update `WindowsTH-KB3011067-x64`Standalone Package-bestand . Dit bestand wordt gebruikt om de apparaatsoftware bij te werken.
    - Een bestand dat cumulatieve `windows8.1-kb4034681-x64`updates voor augustus bevat. Ga voor meer informatie over wat er in deze rollup is opgenomen naar [maandelijkse beveiligingsrollup van augustus.](https://support.microsoft.com/help/4034681/windows-8-1-windows-server-2012-r2-update-kb40346810)

### <a name="install-the-update-or-the-hotfix"></a>De update of de hotfix installeren

Controleer voorafgaand aan de update of hotfix-installatie of:

 - Je hebt de update of de hotfix lokaal gedownload op je host of toegankelijk via een netwerkshare.
 - Uw virtuele array wordt uitgevoerd met Update 0.6 (10.0.10293.0). Als u een versie uitvoert vóór Update 0.6, [installeert u update 0.6](storsimple-virtual-array-install-update-06.md) eerst en installeert u Update 1.

Deze procedure duurt ongeveer 4 minuten. Voer de volgende stappen uit om de update of hotfix te installeren.

#### <a name="to-install-the-update-or-the-hotfix"></a>De update of de hotfix installeren

1. Ga in de lokale webgebruikersinterface naar **Maintenance** > **Software Update**. Noteer de softwareversie die u uitvoert. **Ga alleen door met deze update als u Update 0.6 (10.0.10293.0) uitvoert. Als u een eerdere versie uitvoert, [installeert u update 0.6](storsimple-virtual-array-install-update-06.md) eerst op uw apparaat en past u Update 1 toe.**
   
    ![apparaat bijwerken](./media/storsimple-virtual-array-install-update-1/update1m.png)

2. Voer in **Het bestandspad bijwerken**de bestandsnaam in voor de update of de hotfix. U ook naar het installatiebestand voor updates of hotfix bladeren als u in een netwerkshare wordt geplaatst. Klik op **Toepassen**.
   
    ![apparaat bijwerken](./media/storsimple-virtual-array-install-update-1/update2m.png)

3. Er wordt een waarschuwing weergegeven. Gezien de virtuele array is een enkel knooppunt apparaat, nadat de update is toegepast, het apparaat opnieuw opstart en er is downtime. Klik op het controlepictogram.
   
   ![apparaat bijwerken](./media/storsimple-virtual-array-install-update-1/update3m.png)

4. De update wordt gestart. Nadat het apparaat is bijgewerkt, wordt het opnieuw opgestart. De lokale gebruikersinterface is in deze duur niet toegankelijk.
   
    ![apparaat bijwerken](./media/storsimple-virtual-array-install-update-1/update5m.png)

5. Nadat de herstart is voltooid, wordt u naar de **aanmeldingspagina** geleid. Ga naar > **Onderhoudssoftware-update**om te controleren of de **Maintenance**apparaatsoftware is bijgewerkt in de lokale webgebruikersinterface. De weergegeven softwareversie moet **10.0.0.0.10296** voor Update 1.0 zijn.
   
   > [!NOTE]
   > We rapporteren de softwareversies op een iets andere manier in de lokale webgebruikersinterface en de Azure-portal. De lokale webgebruikersinterface rapporteert bijvoorbeeld **10.0.0.0.0.10296** en de Azure-portal rapporteert **10.0.10296.0** voor dezelfde versie.
   
    ![apparaat bijwerken](./media/storsimple-virtual-array-install-update-1/update6m.png)

6. Herhaal stap 2-4 om de Windows-beveiligingsoplossing te installeren met behulp van bestand `windows8.1-kb4012213-x64`. De virtuele array wordt opnieuw opgestart na de installatie en u moet zich aanmelden bij de lokale web-gebruikersinterface.

> [!NOTE]
> Als u Update 1 rechtstreeks hebt toegepast op een apparaat waarop een versie wordt uitgevoerd vóór Update 0.6, mist u enkele updates. Neem contact op met Microsoft Support voor de volgende stappen.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het beheren van uw StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).
