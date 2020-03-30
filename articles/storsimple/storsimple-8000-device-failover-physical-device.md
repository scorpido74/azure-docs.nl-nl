---
title: Failover, disaster recovery naar een ander StorSimple 8000-apparaat
description: Meer informatie over hoe u falen ten opzichte van uw StorSimple 8000-serie fysieke apparaat naar een ander fysiek apparaat.
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
ms.date: 05/03/2017
ms.author: alkohli
ms.openlocfilehash: 9e4e890ab5491e46ffe5ea0e1c168d168f9cc729
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77468606"
---
# <a name="fail-over-to-a-storsimple-8000-series-physical-device"></a>Fail over naar een StorSimple 8000 serie fysiek apparaat

## <a name="overview"></a>Overzicht

Deze zelfstudie beschrijft de stappen die nodig zijn om te mislukken over een StorSimple 8000-serie fysiek apparaat naar een ander StorSimple fysiek apparaat als er een ramp. StorSimple gebruikt de failoverfunctie voor apparaten om gegevens van een fysiek bronapparaat in het datacenter te migreren naar een ander fysiek apparaat. De richtlijnen in deze zelfstudie zijn van toepassing op fysieke apparaten uit de StorSimple 8000-serie met softwareversies Update 3 en hoger.

Ga voor meer informatie over apparaatfailover en hoe het wordt gebruikt om te herstellen van een ramp, naar [Failover en disaster recovery voor StorSimple 8000-serie apparaten.](storsimple-8000-device-failover-disaster-recovery.md)

Als u niet wilt falen over een StorSimple-fysiek apparaat naar een StorSimple Cloud Appliance, gaat u naar [Fail over naar een StorSimple Cloud Appliance.](storsimple-8000-device-failover-cloud-appliance.md) Als u niet over een fysiek apparaat voor zichzelf wilt falen, gaat u naar [Fail over naar hetzelfde Fysieke StorSimple-apparaat](storsimple-8000-device-failover-same-device.md).


## <a name="prerequisites"></a>Vereisten

- Controleer of u de overwegingen voor de failover van het apparaat hebt gecontroleerd. Ga voor meer informatie naar [Algemene overwegingen voor het mislukken van het apparaat.](storsimple-8000-device-failover-disaster-recovery.md)

- U moet een fysiek apparaat uit de StorSimple 8000-serie hebben geïmplementeerd in het datacenter. Het apparaat moet de softwareversie van Update 3 of hoger uitvoeren. Ga voor meer informatie naar [Uw on-premises StorSimple-apparaat implementeren.](storsimple-8000-deployment-walkthrough-u2.md)


## <a name="steps-to-fail-over-to-a-physical-device"></a>Stappen om niet over te gaan naar een fysiek apparaat

Voer de volgende stappen uit om uw apparaat te herstellen naar een fysiek doelapparaat.

1. Controleer of de volumecontainer die u wilt mislukken, cloudmomentopnamen heeft gekoppeld. Ga voor meer informatie naar [StorSimple Device Manager-service gebruiken om back-ups te maken.](storsimple-8000-manage-backup-policies-u2.md)
2. Ga naar uw StorSimple-apparaatbeheer en klik op **Apparaten**. Ga in het blade **Apparaten** naar de lijst met apparaten die met uw service zijn verbonden.
    ![Apparaat selecteren](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev1.png)
3. Selecteer en klik op uw bronapparaat. Het bronapparaat heeft de volumecontainers die u wilt overlaten. Ga naar **Instellingen > Volumecontainers**.
4. Selecteer een volumecontainer die u naar een ander apparaat wilt overlaten. Klik op de volumecontainer om de lijst met volumes in deze container weer te geven. Selecteer een volume, klik met de rechtermuisknop en klik op **Offline halen** om het volume offline te halen. Herhaal dit proces voor alle volumes in de volumecontainer.
5. Herhaal de vorige stap voor alle volumecontainers die u wilt mislukken naar een ander apparaat.
6. Ga terug naar het **apparaatblad.** Klik op de opdrachtbalk op **Mislukken over**.
    ![Klik op fail over](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev2.png)
    
7. Voer in **het blad Fail over** de volgende stappen uit:
   
   1. Klik op **Bron**. De volumecontainers met volumes die zijn gekoppeld aan cloudmomentopnamen worden weergegeven. Alleen de weergegeven containers komen in aanmerking voor failover. Selecteer in de lijst met volumecontainers de volumecontainers die u wilt overlaten. **Alleen de volumecontainers met bijbehorende cloudmomentopnamen en offlinevolumes worden weergegeven.**

       ![Bron selecteren](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev5.png)
   2. Klik **op Doel**. Selecteer een doelapparaat in de vervolgkeuzelijst met beschikbare apparaten voor de volumecontainers die in de vorige stap zijn geselecteerd. Alleen de apparaten met voldoende capaciteit voor bronvolumecontainers worden in de lijst weergegeven.

        ![Doel selecteren](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev6.png)

   3. Controleer ten slotte alle failoverinstellingen onder **Samenvatting**. Nadat u de instellingen hebt gecontroleerd, schakelt u het selectievakje in dat de volumes in geselecteerde volumecontainers offline zijn. Klik op **OK**.

       ![Failoverinstellingen controleren](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev8.png)
  
8. StorSimple creëert een failover-taak. Klik op de taakmelding om de failovertaak te controleren via het **blade Jobs.**

    Als de volumecontainer die u niet hebt overhebt lokale volumes heeft, ziet u afzonderlijke hersteltaken voor elk lokaal volume (niet voor gelaagde volumes) in de container. Deze hersteltaken kunnen behoorlijk wat tijd in beslag nemen. Het is waarschijnlijk dat de failover taak eerder kan voltooien. Deze volumes krijgen pas lokale garanties nadat de herstelwerkzaamheden zijn voltooid.

    ![Failovertaak controleren](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev13.png)

9. Nadat de failover is voltooid, gaat u naar het **mes van apparaten.**
   
   1. Selecteer het apparaat dat is gebruikt als doelapparaat voor het failoverproces.

       ![Apparaat selecteren](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev14.png)

   2. Ga naar het **blad Volume Containers.** Alle volumecontainers, samen met de volumes van het oude apparaat, moeten worden vermeld.

       ![Doelvolumecontainers weergeven](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev16.png)


## <a name="next-steps"></a>Volgende stappen

* Nadat u een failover hebt uitgevoerd, moet u mogelijk [uw StorSimple-apparaat deactiveren of verwijderen.](storsimple-8000-deactivate-and-delete-device.md)
* Ga voor informatie over het gebruik van de StorSimple Device Manager-service naar [De StorSimple Device Manager-service gebruiken om uw StorSimple-apparaat te beheren.](storsimple-8000-manager-service-administration.md)

