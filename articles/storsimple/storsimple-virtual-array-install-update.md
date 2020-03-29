---
title: Updates installeren op een Microsoft Azure StorSimple Virtual Array | Microsoft Documenten
description: Beschrijft hoe u de webgebruikers van StorSimple Virtual Array gebruiken om updates toe te passen met behulp van de portal- en hotfix-methode
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 9997a97b-9382-43ed-b56e-61369335c987
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7bf064ff01693f7a65c756a99c435d7f1a39840e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61409252"
---
# <a name="install-updates-on-your-storsimple-virtual-array---azure-portal"></a>Updates installeren op uw StorSimple Virtual Array - Azure-portal

## <a name="overview"></a>Overzicht

In dit artikel worden de stappen beschreven die nodig zijn om updates op uw StorSimple Virtual Array te installeren via de lokale webgebruikersinterface en via de Azure-portal. U moet software-updates of hotfixes toepassen om uw StorSimple Virtual Array up-to-date te houden. 

Houd er rekening mee dat het installeren van een update of hotfix uw apparaat opnieuw start. Gezien het feit dat de StorSimple Virtual Array een enkel knooppuntapparaat is, wordt elke I/O in uitvoering verstoord en ervaart uw apparaat downtime. 

Voordat u een update toepast, raden we u aan de volumes of aandelen eerst offline te halen op de host en vervolgens op het apparaat. Dit minimaliseert de kans op beschadigde gegevens.

> [!IMPORTANT]
> Als u update 0.1- of GA-softwareversies uitvoert, moet u de hotfix-methode via de lokale webgebruikersinterface gebruiken om update 0.3 te installeren. Als u Update 0.2 uitvoert, raden we u aan de updates te installeren via de klassieke Azure-portal.
 

## <a name="use-the-local-web-ui"></a>De lokale webgebruikersinterface gebruiken

Er zijn twee stappen bij het gebruik van de lokale web-gebruikersinterface:

* Download de update of de hotfix
* De update of de hotfix installeren

### <a name="download-the-update-or-the-hotfix"></a>Download de update of de hotfix

Voer de volgende stappen uit om de software-update te downloaden uit de Microsoft Update-catalogus.

#### <a name="to-download-the-update-or-the-hotfix"></a>De update of de hotfix downloaden

1. Start Internet Explorer [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com)en navigeer naar .

2. Als dit de eerste keer is dat u de Microsoft Update-catalogus op deze computer gebruikt, klikt u op **Installeren** wanneer u wordt gevraagd of u de invoegtoepassing voor de Microsoft Update-catalogus wilt installeren.

3. Voer in het zoekvak van de Microsoft Update Catalog het KB-nummer (Knowledge Base) in van het hotfix dat u wilt downloaden. Voer **3182061** in voor Update 0.3 en klik op **Zoeken**.
   
    De hotfix-vermelding wordt bijvoorbeeld **StorSimple Virtual Array Update 0.3**weergegeven.
   
    ![Catalogus doorzoeken](./media/storsimple-virtual-array-install-update/download1.png)

4. Klik op**toevoegen**. De update wordt toegevoegd aan het mandje.

5. Klik op **Mandje weergeven**.

6. Klik **op Downloaden**. Typ of **blader naar** een lokale locatie waar u de downloads wilt weergeven. De updates worden naar de opgegeven locatie gedownload en in een submap met dezelfde naam als de update geplaatst. De map kan ook worden gekopieerd naar een netwerkshare die bereikbaar is vanaf het apparaat.

7. Open de gekopieerde map, u ziet een `WindowsTH-KB3011067-x64`Microsoft Update Standalone Package-bestand . Dit bestand wordt gebruikt om de update of hotfix te installeren.

### <a name="install-the-update-or-the-hotfix"></a>De update of de hotfix installeren

Zorg ervoor dat de update of hotfix-update lokaal op je host wordt gedownload of toegankelijk is via een netwerkshare voordat je de update hebt geïnstalleerd. 

Gebruik deze methode om updates te installeren op een apparaat met GA- of Update 0.1-softwareversies. Deze procedure duurt minder dan 2 minuten. Voer de volgende stappen uit om de update of hotfix te installeren.

#### <a name="to-install-the-update-or-the-hotfix"></a>De update of de hotfix installeren

1. Ga in de lokale webgebruikersinterface naar **Maintenance** > **Software Update**.
   
    ![apparaat bijwerken](./media/storsimple-virtual-array-install-update/update1m.png)

2. Voer in **Het bestandspad bijwerken**de bestandsnaam in voor de update of de hotfix. U ook naar het installatiebestand voor updates of hotfix bladeren als u in een netwerkshare wordt geplaatst. Klik op **Toepassen**.
   
    ![apparaat bijwerken](./media/storsimple-virtual-array-install-update/update2m.png)

3. Er wordt een waarschuwing weergegeven. Aangezien dit een enkel knooppunt apparaat, nadat de update is toegepast, het apparaat opnieuw wordt opgestart en er is downtime. Klik op het controlepictogram.
   
   ![apparaat bijwerken](./media/storsimple-virtual-array-install-update/update3m.png)

4. De update wordt gestart. Nadat het apparaat is bijgewerkt, wordt het opnieuw opgestart. De lokale gebruikersinterface is in deze duur niet toegankelijk.
   
    ![apparaat bijwerken](./media/storsimple-virtual-array-install-update/update5m.png)

5. Nadat de herstart is voltooid, wordt u naar de **aanmeldingspagina** geleid. Ga naar > **Onderhoudssoftware-update**om te controleren of de **Maintenance**apparaatsoftware is bijgewerkt in de lokale webgebruikersinterface. De weergegeven softwareversie moet **10.0.0.0.0.10288.0** voor Update 0.3 zijn.
   
   > [!NOTE]
   > We rapporteren de softwareversies op een iets andere manier in de lokale webgebruikersinterface en de Azure-portal. De lokale webgebruikersinterface rapporteert bijvoorbeeld **10.0.0.0.0.10288** en de Azure-portal rapporteert **10.0.10288.0** voor dezelfde versie.
   
    ![apparaat bijwerken](./media/storsimple-virtual-array-install-update/update6m.png)

## <a name="use-the-azure-portal"></a>De Azure-portal gebruiken

Als u Update 0.2 uitvoert, raden we u aan updates te installeren via de Azure-portal. De portalprocedure vereist dat de gebruiker de updates scant, downloadt en vervolgens installeert. Deze procedure duurt ongeveer 7 minuten. Voer de volgende stappen uit om de update of hotfix te installeren.

[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal.md)]

Nadat de installatie is voltooid (zoals aangegeven door de taakstatus op 100 %), ga naar uw StorSimple Device Manager-service. Selecteer **Apparaten** en selecteer en klik vervolgens op het apparaat dat u wilt bijwerken in de lijst met apparaten die met deze service zijn verbonden. Ga in het blad **Instellingen** naar De sectie **Beheren** en selecteer **Apparaatupdates**. De weergegeven softwareversie moet **10.0.10288.0**zijn.


## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het beheren van uw StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

