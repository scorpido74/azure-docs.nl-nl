---
title: Update 0.6 installeren op StorSimple Virtual Array | Microsoft Documenten
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
ms.date: 05/18/2017
ms.author: alkohli
ms.openlocfilehash: 5f0be5d8378cd1640d3052f2e56c8161e2c0b203
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "62116888"
---
# <a name="install-update-06-on-your-storsimple-virtual-array"></a>Installeer Update 0.6 op uw StorSimple Virtual Array

## <a name="overview"></a>Overzicht

In dit artikel worden de stappen beschreven die nodig zijn om Update 0.6 op uw StorSimple Virtual Array te installeren via de lokale webgebruikersinterface en via de Azure-portal. U past de software-updates of hotfixes toe om uw StorSimple Virtual Array up-to-date te houden.

Voordat u een update toepast, raden we u aan de volumes of aandelen eerst offline te halen op de host en vervolgens op het apparaat. Dit minimaliseert de kans op beschadigde gegevens. Nadat de volumes of aandelen offline zijn, moet u ook een handmatige back-up van het apparaat maken.

> [!IMPORTANT]
> - Update 0.6 komt overeen met de softwareversie **van 10.0.10293.0** op uw apparaat. Ga voor informatie over wat er nieuw is in deze update naar [Notities vrijgeven voor Update 0.6](storsimple-virtual-array-update-06-release-notes.md).
>
> - Als u Update 0.2 of hoger uitvoert, raden we u aan de updates te installeren via de Azure-portal. Als u update 0.1- of GA-softwareversies uitvoert, moet u de hotfix-methode via de lokale webgebruikersinterface gebruiken om Update 0.6 te installeren.
>
> - Houd er rekening mee dat het installeren van een update of hotfix uw apparaat opnieuw start. Gezien het feit dat de StorSimple Virtual Array een enkel knooppuntapparaat is, wordt elke I/O in uitvoering verstoord en ervaart uw apparaat downtime.

## <a name="use-the-azure-portal"></a>De Azure-portal gebruiken

Als u Update 0.2 en hoger uitvoert, raden we u aan updates te installeren via de Azure-portal. De portalprocedure vereist dat de gebruiker de updates scant, downloadt en vervolgens installeert. Deze procedure duurt ongeveer 7 minuten. Voer de volgende stappen uit om de update of hotfix te installeren.

[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-04.md)]

Nadat de installatie is voltooid, gaat u naar uw StorSimple Device Manager-service. Selecteer **Apparaten** en selecteer en klik op het apparaat dat u zojuist hebt bijgewerkt. Ga naar **Instellingen > > Apparaatupdates beheren**. De weergegeven softwareversie moet **10.0.10293.0**zijn.

## <a name="use-the-local-web-ui"></a>De lokale webgebruikersinterface gebruiken

Er zijn twee stappen bij het gebruik van de lokale web-gebruikersinterface:

* Download de update of de hotfix
* De update of de hotfix installeren

### <a name="download-the-update-or-the-hotfix"></a>Download de update of de hotfix

Voer de volgende stappen uit om de software-update te downloaden uit de Microsoft Update-catalogus.

#### <a name="to-download-the-update-or-the-hotfix"></a>De update of de hotfix downloaden

1. Start Internet Explorer [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com)en navigeer naar .

2. Als u de Microsoft Update Catalog voor het eerst op deze computer gebruikt, klikt u op **Installeren** wanneer u wordt gevraagd de invoegtoepassing Microsoft Update Catalog te installeren.

3. Voer in het zoekvak van de Microsoft Update Catalog het KB-nummer (Knowledge Base) in van het hotfix dat u wilt downloaden. Voer **4023268** in voor Update 0.6 en klik op **Zoeken**.
   
    De hotfix-vermelding wordt bijvoorbeeld **StorSimple Virtual Array Update 0.6**weergegeven.
   
    ![Catalogus doorzoeken](./media/storsimple-virtual-array-install-update-06/download1.png)

4. Klik **op Downloaden**.

5. U ziet vijf bestanden om te downloaden. Download elk van deze bestanden naar een map. De map kan ook worden gekopieerd naar een netwerkshare die bereikbaar is vanaf het apparaat.

6. Open de map waar de bestanden zich bevinden.
    ![Bestanden in het pakket](./media/storsimple-virtual-array-install-update-06/update06folder.png)

    U ziet:
    -  Een Microsoft Update `WindowsTH-KB3011067-x64`Standalone Package-bestand . Dit bestand wordt gebruikt om de apparaatsoftware bij te werken.
    - Een Genève Monitoring `GenevaMonitoringAgentPackageInstaller`Agent Package bestand . Dit bestand wordt gebruikt om de MDS-agent (Monitoring and Diagnostics) bij te werken. Dubbelklik op het cabinebestand. Er wordt een _.msi_ weergegeven. Selecteer het bestand, klik met de rechtermuisknop en haal het bestand **uit.** U gebruikt het _.msi-bestand_ om de agent bij te werken.

        ![MDS-agentupdatebestand extraheren](./media/storsimple-virtual-array-install-update-06/extract-geneva-monitoring-agent-installer.png)

        > [!IMPORTANT]
        > U hoeft de MDS-agent niet bij te werken als u StorSimple Update 0.5 (0.0.10293.0) uitvoert.

    - Drie bestanden die kritieke Windows-beveiligingsupdates bevatten, `windows8.1-kb4012213-x64`,`windows8.1-kb3205400-x64`en `windows8.1-kb4019213-x64`.


### <a name="install-the-update-or-the-hotfix"></a>De update of de hotfix installeren

Zorg ervoor dat de update of hotfix-update lokaal op je host wordt gedownload of toegankelijk is via een netwerkshare voordat je de update hebt geïnstalleerd.

Gebruik deze methode om updates te installeren op een apparaat met GA- of Update 0.1-softwareversies. Deze procedure duurt ongeveer 12 minuten. Voer de volgende stappen uit om de update of hotfix te installeren.

#### <a name="to-install-the-update-or-the-hotfix"></a>De update of de hotfix installeren

1. Ga in de lokale webgebruikersinterface naar **Maintenance** > **Software Update**. Noteer de softwareversie die u uitvoert. Als u **10.0.10290.0**uitvoert, hoeft u de MDS-agent niet in stap 6 bij te werken.
   
    ![apparaat bijwerken](./media/storsimple-virtual-array-install-update-05/update1m.png)

2. Voer in **Het bestandspad bijwerken**de bestandsnaam in voor de update of de hotfix. U ook naar het installatiebestand voor updates of hotfix bladeren als u in een netwerkshare wordt geplaatst. Klik op **Toepassen**.
   
    ![apparaat bijwerken](./media/storsimple-virtual-array-install-update-05/update2m.png)

3. Er wordt een waarschuwing weergegeven. Gezien de virtuele array is een enkel knooppunt apparaat, nadat de update is toegepast, het apparaat opnieuw opstart en er is downtime. Klik op het controlepictogram.
   
   ![apparaat bijwerken](./media/storsimple-virtual-array-install-update-05/update3m.png)

4. De update wordt gestart. Nadat het apparaat is bijgewerkt, wordt het opnieuw opgestart. De lokale gebruikersinterface is in deze duur niet toegankelijk.
   
    ![apparaat bijwerken](./media/storsimple-virtual-array-install-update-05/update5m.png)

5. Nadat de herstart is voltooid, wordt u naar de **aanmeldingspagina** geleid. Ga naar > **Onderhoudssoftware-update**om te controleren of de **Maintenance**apparaatsoftware is bijgewerkt in de lokale webgebruikersinterface. De weergegeven softwareversie moet **10.0.0.0.10293** voor Update 0.6 zijn.
   
   > [!NOTE]
   > We rapporteren de softwareversies op een iets andere manier in de lokale webgebruikersinterface en de Azure-portal. De lokale webgebruikersinterface rapporteert bijvoorbeeld **10.0.0.0.0.10293** en de Azure-portal rapporteert **10.0.10293.0** voor dezelfde versie.
   
    ![apparaat bijwerken](./media/storsimple-virtual-array-install-update-06/update6m.png)

6. Sla deze stap over als u StorSimple Virtual Array Update 0.5 **(10.0.10290.0)** uitvoert voordat u deze update toepaste. U maakte een notitie van de softwareversie in stap 1 voordat u begon te updaten. Als u Update 0.5 hebt uitgevoerd, is uw MDS-agent al up-to-date.

    Als u een softwareversie uitvoert vóór Update 0.5, is de volgende stap voor u het bijwerken van de MDS-agent. Ga op de pagina **Software-update** naar het `GenevaMonitoringAgentPackageInstaller.msi` **bestandspad bijwerken** en blader naar het bestand. Herhaal stap 2-4. Nadat de virtuele array opnieuw is opgestart, meldt u zich aan bij de lokale webgebruikersinterface.

7. Herhaal stap 2-4 om de Windows-beveiligingsoplossingen te installeren met behulp van bestanden `windows8.1-kb4012213-x64`,`windows8.1-kb3205400-x64`en `windows8.1-kb4019213-x64`. De virtuele array wordt na elke installatie opnieuw opgestart en u moet zich aanmelden bij de lokale webgebruikersinterface.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het beheren van uw StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

