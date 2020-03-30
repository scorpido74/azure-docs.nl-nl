---
title: Failover en disaster recovery naar een StorSimple Cloud Appliance
description: Meer informatie over hoe u falen ten opzichte van uw fysieke apparaat uit de StorSimple 8000-serie naar een cloudtoestel.
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
ms.date: 07/03/2017
ms.author: alkohli
ms.openlocfilehash: 347b899608d4322a7873b9f80f38ca1c767194d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77468742"
---
# <a name="fail-over-to-your-storsimple-cloud-appliance"></a>Niet overgaan naar uw StorSimple Cloud Appliance

## <a name="overview"></a>Overzicht

In deze zelfstudie worden de stappen beschreven die nodig zijn om te mislukken ten opzichte van een fysiek apparaat uit de StorSimple 8000-serie naar een StorSimple Cloud Appliance als er een ramp is. StorSimple gebruikt de functie failoverfunctie voor apparaten om gegevens te migreren van een fysiek bronapparaat in het datacenter naar een cloudtoestel dat wordt uitgevoerd in Azure. De richtlijnen in deze zelfstudie zijn van toepassing op fysieke apparaten uit de StorSimple 8000-serie en cloudapparaten met softwareversies Update 3 en hoger.

Ga voor meer informatie over apparaatfailover en hoe het wordt gebruikt om te herstellen van een ramp, naar [Failover en disaster recovery voor StorSimple 8000-serie apparaten.](storsimple-8000-device-failover-disaster-recovery.md)

Als u niet over een Fysiek apparaat van StorSimple naar een ander fysiek apparaat wilt gaan, gaat u naar [Fail over naar een Fysiek Apparaat van StorSimple.](storsimple-8000-device-failover-physical-device.md) Als u niet over een apparaat naar zichzelf wilt gaan, gaat u naar [Fail over naar hetzelfde Fysieke StorSimple-apparaat](storsimple-8000-device-failover-same-device.md).

## <a name="prerequisites"></a>Vereisten

- Controleer of u de overwegingen voor de failover van het apparaat hebt gecontroleerd. Ga voor meer informatie naar [Algemene overwegingen voor het mislukken van het apparaat.](storsimple-8000-device-failover-disaster-recovery.md)

- U moet een StorSimple Cloud Appliance hebben gemaakt en geconfigureerd voordat u deze procedure uitvoert. Als u de softwareversie van Update 3 of hoger uitvoert, u overwegen een 8020-cloudtoestel voor de DR te gebruiken. Het 8020-model heeft 64 TB en maakt gebruik van Premium Storage. Ga voor meer informatie naar [Implementeren en beheren van een StorSimple Cloud Appliance.](storsimple-8000-cloud-appliance-u2.md)

## <a name="steps-to-fail-over-to-a-cloud-appliance"></a>Stappen om niet over te gaan naar een cloudtoestel

Voer de volgende stappen uit om het apparaat te herstellen naar een doel-StorSimple Cloud Appliance.

1.  Controleer of de volumecontainer die u wilt mislukken, cloudmomentopnamen heeft gekoppeld. Ga voor meer informatie naar [StorSimple Device Manager-service gebruiken om back-ups te maken.](storsimple-8000-manage-backup-policies-u2.md)
2. Ga naar de StorSimple-apparaatbeheerservice en klik op **Apparaten**. Ga in het blade **Apparaten** naar de lijst met apparaten die met uw service zijn verbonden.
    ![Apparaat selecteren](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev1.png)
3. Selecteer en klik op uw bronapparaat. Het bronapparaat heeft de volumecontainers die u wilt overlaten. Ga naar **Instellingen > Volumecontainers**.

    ![Apparaat selecteren](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev2.png)
    
4. Selecteer een volumecontainer die u naar een ander apparaat wilt overlaten. Klik op de volumecontainer om de lijst met volumes in deze container weer te geven. Selecteer een volume, klik met de rechtermuisknop en klik op **Offline halen** om het volume offline te halen.

    ![Apparaat selecteren](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev5.png)

5. Herhaal dit proces voor alle volumes in de volumecontainer.

     ![Apparaat selecteren](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev7.png)

6. Herhaal de vorige stap voor alle volumecontainers die u wilt mislukken naar een ander apparaat.

7. Ga terug naar het **apparaatblad.** Klik op de opdrachtbalk op **Mislukken over**.

    ![Klik op fail over](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev8.png)
8. Voer in **het blad Fail over** de volgende stappen uit:
   
    1. Klik op **Bron**. Selecteer de volumecontainers die niet worden overgeslagen. **Alleen de volumecontainers met bijbehorende cloudmomentopnamen en offlinevolumes worden weergegeven.**
        ![Bron selecteren](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev11.png)
    2. Klik **op Doel**. Selecteer een doelcloudtoestel in de vervolgkeuzelijst met beschikbare apparaten. **Alleen de apparaten met voldoende capaciteit voor bronvolumecontainers worden in de lijst weergegeven.**

        ![Doel selecteren](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev12.png)

    3. Controleer de failoverinstellingen onder **Overzicht** en schakel het selectievakje in dat de volumes in geselecteerde volumecontainers offline zijn. 

        ![Failoverinstellingen controleren](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev13.png)

9. Er wordt een failovertaak gemaakt. Als u de failovertaak wilt controleren, klikt u op de taakmelding.

    ![Failovertaak controleren](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev13.png)

10. Nadat de failover is voltooid, gaat u terug naar het **apparaatblad.**

    1. Selecteer het apparaat dat is gebruikt als doel voor de failover.

       ![Apparaat selecteren](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev14.png)

    2. Klik **op Volumecontainers**. Alle volumecontainers, samen met de volumes van het oude apparaat, moeten worden vermeld.

       Als de volumecontainer waarover u hebt gefaald lokaal vastgemaakte volumes heeft, worden deze volumes niet meer als gelaagde volumes uitgevoerd. Lokaal vastgemaakte volumes worden niet ondersteund op een StorSimple Cloud Appliance.

       ![Doelvolumecontainers weergeven](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev17.png)


## <a name="next-steps"></a>Volgende stappen

* Nadat u een failover hebt uitgevoerd, moet u mogelijk [uw StorSimple-apparaat deactiveren of verwijderen.](storsimple-8000-deactivate-and-delete-device.md)

* Ga voor informatie over het gebruik van de StorSimple Device Manager-service naar [De StorSimple Device Manager-service gebruiken om uw StorSimple-apparaat te beheren.](storsimple-8000-manager-service-administration.md)

