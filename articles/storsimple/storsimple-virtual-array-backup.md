---
title: Zelfstudie microsoft Azure StorSimple Virtual Array-back-up | Microsoft Documenten
description: Beschrijft hoe u een back-up storSimple Virtual Array aandelen en volumes.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: e3cdcd9e-33b1-424d-82aa-b369d934067e
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a61dcca1f78b6ba444a2deefcf6b8bb4fd5c5087
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60581296"
---
# <a name="back-up-shares-or-volumes-on-your-storsimple-virtual-array"></a>Back-ups maken van aandelen of volumes op uw StorSimple Virtual Array

## <a name="overview"></a>Overzicht

De StorSimple Virtual Array is een on-premises virtueel apparaat voor hybride cloudopslag dat kan worden geconfigureerd als bestandsserver of iSCSI-server. Met de virtuele array kan de gebruiker geplande en handmatige back-ups maken van alle shares of volumes op het apparaat. Wanneer geconfigureerd als een bestandsserver, staat het ook item-level recovery toe. In deze zelfstudie wordt beschreven hoe u geplande en handmatige back-ups maakt en herstel op itemniveau uitvoert om een verwijderd bestand op uw virtuele array te herstellen.

Deze zelfstudie is alleen van toepassing op de StorSimple Virtual Arrays. Ga voor informatie over 8000-serie naar [Een back-up maken voor het apparaat uit de 8000-serie](storsimple-manage-backup-policies-u2.md)

## <a name="back-up-shares-and-volumes"></a>Back-up van aandelen en volumes

Back-ups bieden point-in-time bescherming, verbeteren de herstelbaarheid en minimaliseren de hersteltijden voor aandelen en volumes. U op twee manieren een back-up maken van een aandeel of volume op uw StorSimple-apparaat: **Gepland** of **handmatig**. Elk van de methoden wordt besproken in de volgende secties.

## <a name="change-the-backup-start-time"></a>De begintijd van de back-up wijzigen

> [!NOTE]
> In deze release worden geplande back-ups gemaakt door een standaardbeleid dat dagelijks op een bepaald tijdstip wordt uitgevoerd en een back-up maakt van alle aandelen of volumes op het apparaat. Het is op dit moment niet mogelijk om aangepaste beleidsregels voor geplande back-ups te maken.


Uw StorSimple Virtual Array heeft een standaard back-upbeleid dat begint op een bepaald tijdstip van de dag (22:30) en één keer per dag een back-up maakt van alle aandelen of volumes op het apparaat. U het tijdstip waarop de back-up wordt gestart wijzigen, maar de frequentie en de retentie (die het aantal back-ups aangeeft dat moet worden behouden) kunnen niet worden gewijzigd. Tijdens deze back-ups wordt een back-up van het hele virtuele apparaat gemaakt. Dit kan van invloed zijn op de prestaties van het apparaat en de workloads die op het apparaat worden geïmplementeerd. Daarom raden we u aan deze back-ups te plannen voor daluren.

 Als u de standaardbegintijd van de back-up wilt wijzigen, voert u de volgende stappen uit in de [Azure-portal.](https://portal.azure.com/)

#### <a name="to-change-the-start-time-for-the-default-backup-policy"></a>De begintijd voor het standaardback-upbeleid wijzigen

1. Ga naar **Apparaten**. De lijst met apparaten die zijn geregistreerd bij uw StorSimple Device Manager-service wordt weergegeven. 
   
    ![naar apparaten navigeren](./media/storsimple-virtual-array-backup/changebuschedule1.png)

2. Selecteer en klik op uw apparaat. Het **ondersmes instellingen** wordt weergegeven. Ga naar **Beleid voor > back-up beheren**.
   
    ![uw apparaat selecteren](./media/storsimple-virtual-array-backup/changebuschedule2.png)

3. In het blade **Back-upbeleid** is de standaardbegintijd 22:30 uur. U de nieuwe begintijd voor de dagelijkse planning in de apparaattijdzone opgeven.
   
    ![navigeren naar back-upbeleid](./media/storsimple-virtual-array-backup/changebuschedule5.png)

4. Klik op **Opslaan**.

### <a name="take-a-manual-backup"></a>Neem een handmatige back-up

Naast geplande back-ups u op elk gewenst moment een handmatige (on-demand) back-up van apparaatgegevens maken.

#### <a name="to-create-a-manual-backup"></a>Een handmatige back-up maken

1. Ga naar **Apparaten**. Selecteer uw apparaat en klik met de rechtermuisknop **...** helemaal rechts in de geselecteerde rij. Selecteer **Back-up maken**in het contextmenu.
   
    ![navigeren om een back-up te maken](./media/storsimple-virtual-array-backup/takebackup1m.png)

2. Klik in het **back-upblad Nemen** op **Back-up maken.** Hiermee wordt een back-up gemaakt van alle shares op de bestandsserver of alle volumes op uw iSCSI-server. 
   
    ![back-up vanaf](./media/storsimple-virtual-array-backup/takebackup2m.png)
   
    Er wordt een on-demand back-up gestart en u ziet dat er een back-uptaak is gestart.
   
    ![back-up vanaf](./media/storsimple-virtual-array-backup/takebackup3m.png) 
   
    Zodra de taak is voltooid, wordt u opnieuw op de hoogte gebracht. Het back-upproces begint dan.
   
    ![back-uptaak gemaakt](./media/storsimple-virtual-array-backup/takebackup4m.png)

3. Als u de voortgang van de back-ups wilt bijhouden en de taakgegevens wilt bekijken, klikt u op de melding. Dit brengt u naar **Job details**.
   
     ![back-uptaakgegevens](./media/storsimple-virtual-array-backup/takebackup5m.png)

4. Nadat de back-up is voltooid, gaat u naar **de catalogus beheer > back-up**. U ziet een cloudmomentopname van alle aandelen (of volumes) op uw apparaat.
   
    ![Voltooide back-up](./media/storsimple-virtual-array-backup/takebackup19m.png) 

## <a name="view-existing-backups"></a>Bestaande back-ups weergeven
Als u de bestaande back-ups wilt weergeven, voert u de volgende stappen uit in de Azure-portal.

#### <a name="to-view-existing-backups"></a>Bestaande back-ups weergeven

1. Ga naar **Devices** blade. Selecteer en klik op uw apparaat. Ga **in** het blad Instellingen naar **Management > Back-upcatalogus**.
   
    ![Navigeren naar een back-upcatalogus](./media/storsimple-virtual-array-backup/viewbackups1.png)
2. Geef de volgende criteria op die moeten worden gebruikt voor het filteren:
   
   - **Tijdsbereik** – kan **afgelopen 1 uur**zijn, afgelopen **24 uur**, afgelopen 7 **dagen**, **afgelopen 30 dagen**, afgelopen **jaar**en **aangepaste datum**.
    
   - **Apparaten** – kies uit de lijst met bestandsservers of iSCSI-servers die zijn geregistreerd bij uw StorSimple Device Manager-service.
   
   - **Gestart** – kan automatisch worden **gepland** (door een back-upbeleid) of **handmatig** worden gestart (door u).
   
     ![Back-ups filteren](./media/storsimple-virtual-array-backup/viewbackups2.png)

3. Klik op **Toepassen**. De gefilterde lijst met back-ups wordt weergegeven in het blade van de **back-upcatalogus.** Opmerking er zijn slechts 100 back-upelementen die op een bepaald moment kunnen worden weergegeven.
   
    ![Bijgewerkte back-upcatalogus](./media/storsimple-virtual-array-backup/viewbackups3.png)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het beheren van uw StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

