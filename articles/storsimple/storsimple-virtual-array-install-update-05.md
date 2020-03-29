---
title: Update 0.5 installeren op StorSimple Virtual Array | Microsoft Documenten
description: Beschrijft hoe u de webgebruikers van StorSimple Virtual Array gebruiken om updates toe te passen met de Azure-portal- en hotfix-methode
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
ms.date: 05/10/2017
ms.author: alkohli
ms.openlocfilehash: e09ff4bcbc141b1a1f80bc278918a291639c1885
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61445300"
---
# <a name="install-update-05-on-your-storsimple-virtual-array"></a>Installeer Update 0.5 op uw StorSimple Virtual Array

## <a name="overview"></a>Overzicht

In dit artikel worden de stappen beschreven die nodig zijn om Update 0.5 op uw StorSimple Virtual Array te installeren via de lokale webgebruikersinterface en via de Azure-portal. U moet software-updates of hotfixes toepassen om uw StorSimple Virtual Array up-to-date te houden.

Voordat u een update toepast, raden we u aan de volumes of aandelen eerst offline te halen op de host en vervolgens op het apparaat. Dit minimaliseert de kans op beschadigde gegevens. Nadat de volumes of aandelen offline zijn, moet u ook een handmatige back-up van het apparaat maken.

> [!IMPORTANT]
> - Update 0.5 komt overeen met de softwareversie **van 10.0.10290.0** op uw apparaat. Ga voor informatie over wat er nieuw is in deze update naar [Notities vrijgeven voor Update 0.5](storsimple-virtual-array-update-05-release-notes.md).
>
> - Als u Update 0.2 of hoger uitvoert, raden we u aan de updates te installeren via de Azure-portal. Als u update 0.1- of GA-softwareversies uitvoert, moet u de hotfix-methode via de lokale webgebruikersinterface gebruiken om Update 0.5 te installeren.
>
> - Houd er rekening mee dat het installeren van een update of hotfix uw apparaat opnieuw start. Gezien het feit dat de StorSimple Virtual Array een enkel knooppuntapparaat is, wordt elke I/O in uitvoering verstoord en ervaart uw apparaat downtime.

## <a name="use-the-azure-portal"></a>De Azure-portal gebruiken

Als u Update 0.2 en hoger uitvoert, raden we u aan updates te installeren via de Azure-portal. De portalprocedure vereist dat de gebruiker de updates scant, downloadt en vervolgens installeert. Deze procedure duurt ongeveer 7 minuten. Voer de volgende stappen uit om de update of hotfix te installeren.

[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-04.md)]

Nadat de installatie is voltooid, gaat u naar uw StorSimple Device Manager-service. Selecteer **Apparaten** en selecteer en klik op het apparaat dat u zojuist hebt bijgewerkt. Ga naar **Instellingen > > Apparaatupdates beheren**. De weergegeven softwareversie moet **10.0.10290.0**zijn.

## <a name="use-the-local-web-ui"></a>De lokale webgebruikersinterface gebruiken

Er zijn twee stappen bij het gebruik van de lokale web-gebruikersinterface:

* Download de update of de hotfix
* De update of de hotfix installeren

### <a name="download-the-update-or-the-hotfix"></a>Download de update of de hotfix

Voer de volgende stappen uit om de software-update te downloaden uit de Microsoft Update-catalogus.

#### <a name="to-download-the-update-or-the-hotfix"></a>De update of de hotfix downloaden

1. Start Internet Explorer [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com)en navigeer naar .

2. Als dit de eerste keer is dat u de Microsoft Update-catalogus op deze computer gebruikt, klikt u op **Installeren** wanneer u wordt gevraagd of u de invoegtoepassing voor de Microsoft Update-catalogus wilt installeren.

3. Voer in het zoekvak van de Microsoft Update Catalog het KB-nummer (Knowledge Base) in van het hotfix dat u wilt downloaden. Voer **4021576** in voor Update 0.5 en klik op **Zoeken**.
   
    De hotfix-vermelding wordt bijvoorbeeld **StorSimple Virtual Array Update 0.5**weergegeven.
   
    ![Catalogus doorzoeken](./media/storsimple-virtual-array-install-update-05/download1.png)

4. Klik **op Downloaden**. 

5. U ziet twee bestanden om te downloaden, een *.msu* en een *.cab-bestand.* Download elk van deze bestanden naar een map. De map kan ook worden gekopieerd naar een netwerkshare die bereikbaar is vanaf het apparaat.

6. Open de map waar de bestanden zich bevinden.
    ![Bestanden in het pakket](./media/storsimple-virtual-array-install-update-05/update05folder.png)

    U ziet:
    -  Een Microsoft Update `WindowsTH-KB3011067-x64`Standalone Package-bestand . Dit bestand wordt gebruikt om de apparaatsoftware bij te werken.
    - Een Genève Monitoring `GenevaMonitoringAgentPackageInstaller`Agent Package bestand . Dit bestand wordt gebruikt om de MDS-agent (Monitoring and Diagnostics) bij te werken. Dubbelklik op het cabinebestand. Er wordt een .msi weergegeven. Selecteer het bestand, klik met de rechtermuisknop en haal het bestand **uit.** U gebruikt het _.msi-bestand_ om de agent bij te werken.

        ![MDS-agentupdatebestand extraheren](./media/storsimple-virtual-array-install-update-05/extract-geneva-monitoring-agent-installer.png)
        
    

### <a name="install-the-update-or-the-hotfix"></a>De update of de hotfix installeren

Zorg ervoor dat de update of hotfix-update lokaal op je host wordt gedownload of toegankelijk is via een netwerkshare voordat je de update hebt geïnstalleerd.

Gebruik deze methode om updates te installeren op een apparaat met GA- of Update 0.1-softwareversies. Deze procedure duurt minder dan 2 minuten. Voer de volgende stappen uit om de update of hotfix te installeren.

#### <a name="to-install-the-update-or-the-hotfix"></a>De update of de hotfix installeren

1. Ga in de lokale webgebruikersinterface naar **Maintenance** > **Software Update**.
   
    ![apparaat bijwerken](./media/storsimple-virtual-array-install-update-05/update1m.png)

2. Voer in **Het bestandspad bijwerken**de bestandsnaam in voor de update of de hotfix. U ook naar het installatiebestand voor updates of hotfix bladeren als u in een netwerkshare wordt geplaatst. Klik op **Toepassen**.
   
    ![apparaat bijwerken](./media/storsimple-virtual-array-install-update-05/update2m.png)

3. Er wordt een waarschuwing weergegeven. Aangezien dit een enkel knooppunt apparaat, nadat de update is toegepast, het apparaat opnieuw wordt opgestart en er is downtime. Klik op het controlepictogram.
   
   ![apparaat bijwerken](./media/storsimple-virtual-array-install-update-05/update3m.png)

4. De update wordt gestart. Nadat het apparaat is bijgewerkt, wordt het opnieuw opgestart. De lokale gebruikersinterface is in deze duur niet toegankelijk.
   
    ![apparaat bijwerken](./media/storsimple-virtual-array-install-update-05/update5m.png)

5. Nadat de herstart is voltooid, wordt u naar de **aanmeldingspagina** geleid. Ga naar > **Onderhoudssoftware-update**om te controleren of de **Maintenance**apparaatsoftware is bijgewerkt in de lokale webgebruikersinterface. De weergegeven softwareversie moet **10.0.0.0.0.10290.0** zijn voor Update 0.5.
   
   > [!NOTE]
   > We rapporteren de softwareversies op een iets andere manier in de lokale webgebruikersinterface en de Azure-portal. De lokale webgebruikersinterface rapporteert bijvoorbeeld **10.0.0.0.0.10290** en de Azure-portal rapporteert **10.0.10290.0** voor dezelfde versie.
   
    ![apparaat bijwerken](./media/storsimple-virtual-array-install-update-05/update6m.png)

6. De volgende stap is het bijwerken van de MDS-agent. Ga op de pagina **Software-update** naar het `GenevaMonitoringAgentPackageInstaller.msi` **bestandspad bijwerken** en blader naar het bestand. Herhaal stap 2-4. Nadat de virtuele array opnieuw is opgestart, meldt u zich aan bij de lokale webgebruikersinterface.

De update is nu voltooid.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het beheren van uw StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

