---
title: Zelfstudie - Fail over StorSimple fysiek apparaat naar hetzelfde apparaat
description: Meer informatie over hoe u falen op uw StorSimple-apparaat op hetzelfde apparaat.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/23/2017
ms.author: alkohli
ms.openlocfilehash: dde01f9b91ff5a04ddb3fcc8d5f0c535278b0539
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398079"
---
# <a name="fail-over-your-storsimple-physical-device-to-same-device"></a>Fail over uw StorSimple fysieke apparaat naar hetzelfde apparaat

## <a name="overview"></a>Overzicht

Deze zelfstudie beschrijft de stappen die nodig zijn om te mislukken over een StorSimple 8000-serie fysiek apparaat voor zichzelf als er een ramp. StorSimple gebruikt de failoverfunctie voor apparaten om gegevens van een fysiek bronapparaat in het datacenter te migreren naar een ander fysiek apparaat. De richtlijnen in deze zelfstudie zijn van toepassing op fysieke apparaten uit de StorSimple 8000-serie met softwareversies Update 3 en hoger.

Ga voor meer informatie over apparaatfailover en hoe het wordt gebruikt om te herstellen van een ramp, naar [Failover en disaster recovery voor StorSimple 8000-serie apparaten.](storsimple-8000-device-failover-disaster-recovery.md)

Als u niet over een fysiek apparaat naar een ander fysiek apparaat wilt gaan, gaat u naar [Fail over naar hetzelfde Fysieke StorSimple-apparaat](storsimple-8000-device-failover-physical-device.md). Als u niet wilt falen over een StorSimple-fysiek apparaat naar een StorSimple Cloud Appliance, gaat u naar [Fail over naar een StorSimple Cloud Appliance.](storsimple-8000-device-failover-cloud-appliance.md)


## <a name="prerequisites"></a>Vereisten

- Controleer of u de overwegingen voor de failover van het apparaat hebt gecontroleerd. Ga voor meer informatie naar [Algemene overwegingen voor het mislukken van het apparaat.](storsimple-8000-device-failover-disaster-recovery.md)


## <a name="steps-to-fail-over-to-the-same-device"></a>Stappen om niet naar hetzelfde apparaat te gaan

Voer de volgende stappen uit als u niet naar hetzelfde apparaat moet gaan.

1. Maak cloudsnapshots van alle volumes in uw apparaat. Ga voor meer informatie naar [StorSimple Device Manager-service gebruiken om back-ups te maken.](storsimple-8000-manage-backup-policies-u2.md)
2. Stel uw apparaat terug op standaardinstellingen in de fabriek. Volg de gedetailleerde instructies [om een StorSimple-apparaat opnieuw in te stellen naar de standaardinstellingen in de fabriek.](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings)
3. Ga naar de StorSimple Device Manager-service en selecteer **Apparaten**. In het **apparaatblad** moet het oude apparaat worden weergegeven als **Offline**.

    ![Bronapparaat offline](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev2.png)

4. Configureer uw apparaat en registreer het opnieuw met uw StorSimple Device Manager-service. Het nieuw geregistreerde apparaat moet worden weergegeven als **klaar om in te stellen**. De apparaatnaam voor het nieuwe apparaat is hetzelfde als het oude apparaat, maar wordt toegevoegd met een cijfer om aan te geven dat het apparaat standaard is gereset naar de fabriek en opnieuw is geregistreerd.

    ![Nieuw geregistreerd apparaat klaar om in te stellen](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev3.png)
5. Voer voor het nieuwe apparaat de installatie van het apparaat in. Ga voor meer informatie naar [Stap 4: Minimale apparaatinstelling voltooien.](storsimple-8000-deployment-walkthrough-u2.md#step-4-complete-minimum-device-setup) Op **het** apparaatblad verandert de status van het apparaat in **Online**.

   > [!IMPORTANT]
   > **Voer eerst de minimale configuratie in of uw DR kan mislukken.**

    ![Nieuw geregistreerd apparaat online](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev7.png)

6. Selecteer het oude apparaat (status offline) en klik op de opdrachtbalk op **Mislukkeren**. Selecteer **in het blad Fail over** het oude apparaat als bron en geef het doelapparaat op als het nieuw geregistreerde apparaat.

    ![Overzicht van failover](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev11.png)

    Raadpleeg fail over naar een ander fysiek apparaat voor gedetailleerde instructies.

7. Er wordt een taak voor het herstellen van apparaten gemaakt die u controleren vanuit het blade **Jobs.**

8. Nadat de taak is voltooid, gaat u naar het nieuwe apparaat en navigeert u naar het blad van de **volumecontainers.** Controleer of alle volumecontainers van het oude apparaat zijn gemigreerd naar het nieuwe apparaat.

   ![Volumecontainers gemigreerd](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev13.png)

9. Nadat de failover is voltooid, u het oude apparaat uit de portal deactiveren en verwijderen. Selecteer het oude apparaat (offline), klik met de rechtermuisknop en selecteer **Vervolgens Deactiveren**. Nadat het apparaat is gedeactiveerd, wordt de status van het apparaat bijgewerkt.

     ![Bronapparaat gedeactiveerd](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev14.png)

10. Selecteer het gedeactiveerde apparaat, klik met de rechtermuisknop en selecteer **Verwijderen**. Hiermee wordt het apparaat uit de lijst met apparaten verwijderd.

    ![Bronapparaat verwijderd](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev15.png)



## <a name="next-steps"></a>Volgende stappen

* Nadat u een failover hebt uitgevoerd, moet u mogelijk [uw StorSimple-apparaat deactiveren of verwijderen.](storsimple-8000-deactivate-and-delete-device.md)
* Ga voor informatie over het gebruik van de StorSimple Device Manager-service naar [De StorSimple Device Manager-service gebruiken om uw StorSimple-apparaat te beheren.](storsimple-8000-manager-service-administration.md)

