---
title: Een apparaat uit de StorSimple 8000-serie deactiveren en verwijderen | Microsoft Documenten
description: Beschrijft hoe u het StorSimple-apparaat uit de service verwijderen door het eerst te deactiveren en vervolgens te verwijderen.
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
ms.date: 07/23/2018
ms.author: alkohli
ms.openlocfilehash: 116ac5c4efda87b5d16336dd326d516299f6955d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61481942"
---
# <a name="deactivate-and-delete-a-storsimple-device"></a>Een StorSimple-apparaat deactiveren en verwijderen

## <a name="overview"></a>Overzicht

In dit artikel wordt beschreven hoe u een StorSimple-apparaat deactiveren en verwijderen dat is verbonden met een StorSimple Device Manager-service. De richtlijnen in dit artikel zijn alleen van toepassing op Apparaten uit de StorSimple 8000-serie, waaronder de StorSimple Cloud Appliances. Als u een StorSimple Virtual Array gebruikt, gaat u naar [Deactiveren en verwijdert u een StorSimple Virtual Array.](storsimple-virtual-array-deactivate-and-delete-device.md)

Deactivering vereert de verbinding tussen het apparaat en de bijbehorende StorSimple Device Manager-service. U een StorSimple-apparaat buiten gebruik stellen (bijvoorbeeld als u uw apparaat vervangt of upgradet of als u StorSimple niet meer gebruikt). Als dat het zo is, moet u het apparaat deactiveren voordat u het verwijderen.

Wanneer u een apparaat deactiveert, zijn alle gegevens die lokaal op het apparaat zijn opgeslagen, niet langer toegankelijk. Alleen de gegevens die zijn gekoppeld aan het apparaat dat in de cloud is opgeslagen, kunnen worden hersteld.

> [!WARNING]
> Deactivering is een permanente bewerking en kan niet ongedaan worden gemaakt. Een gedeactiveerd apparaat kan niet worden geregistreerd bij de StorSimple Device Manager-service, tenzij het is gereset naar fabrieksstandaardinstellingen.
>
> Het fabrieksresetproces verwijdert alle gegevens die lokaal op uw apparaat zijn opgeslagen. Daarom moet u een cloudmomentopname van al uw gegevens maken voordat u een apparaat deactiveert. Met deze cloudsnapshot u alle gegevens in een later stadium herstellen.

Na het lezen van deze tutorial, zult u in staat zijn om:

* Deactiveer een apparaat en verwijder de gegevens.
* Deactiveer een apparaat en bewaar de gegevens.

> [!NOTE]
> Voordat u een fysiek apparaat of cloudtoestel van StorSimple deactiveert, stopt of verwijdert u clients en hosts die afhankelijk zijn van dat apparaat.


## <a name="deactivate-and-delete-data"></a>Gegevens deactiveren en verwijderen

Als u geïnteresseerd bent in het volledig verwijderen van het apparaat en de gegevens op het apparaat niet wilt bewaren, voert u de volgende stappen uit.

#### <a name="to-deactivate-the-device-and-delete-the-data"></a>Het apparaat deactiveren en de gegevens verwijderen

1. Voordat u een apparaat deactiveert, moet u alle volumecontainers (en de volumes) die aan het apparaat zijn gekoppeld, verwijderen. U volumecontainers pas verwijderen nadat u de bijbehorende back-ups hebt verwijderd.

    > [!NOTE]
    > Voordat u een Fysiek Apparaat of cloudtoestel van StorSimple deactiveert, moet u ervoor zorgen dat de gegevens uit de verwijderde volumecontainer daadwerkelijk van het apparaat worden verwijderd. U de grafieken voor het cloudverbruik controleren en wanneer u het cloudgebruik ziet dalen vanwege de back-ups die u hebt verwijderd, u doorgaan met het deactiveren van het apparaat. Als u het apparaat deactiveert voordat deze daling plaatsvindt, worden de gegevens gestrand in het opslagaccount en worden kosten gegenereerd.

2. Deactiveer het apparaat als volgt:
   
   1. Ga naar de StorSimple-apparaatbeheerservice en klik op **Apparaten**. Selecteer in het blade **Apparaten** het apparaat dat u wilt deactiveren, klik met de rechtermuisknop en klik op **Deactiveren**.

        ![StorSimple-apparaat deactiveren](./media/storsimple-8000-deactivate-and-delete-device/deactivate1.png)
   2. Typ in het blad **Deactiveren** de naam van het apparaat om te bevestigen en klik op **Deactiveren**. Het deactiveren begint en duurt enkele minuten.

        ![StorSimple-apparaat deactiveren](./media/storsimple-8000-deactivate-and-delete-device/deactivate2.png)

3. Na deactivering u het apparaat volledig verwijderen. Als u een apparaat verwijdert, wordt het verwijderd uit de lijst met apparaten die met de service zijn verbonden. De service kan het verwijderde apparaat dan niet meer beheren. Gebruik de volgende stappen om het apparaat te verwijderen:
   
   1. Ga naar de StorSimple-apparaatbeheerservice en klik op **Apparaten**. Selecteer **in** het blade Apparaten het gedeactiveerde apparaat dat u wilt verwijderen, klik met de rechtermuisknop en klik op **Verwijderen**.

        ![StorSimple-apparaat deactiveren](./media/storsimple-8000-deactivate-and-delete-device/deactivate5.png)
   2. Typ in het blad **Verwijderen** de apparaatnaam om te bevestigen en klik op **Verwijderen**. Het verwijderen duurt enkele minuten.

        ![StorSimple-apparaat deactiveren](./media/storsimple-8000-deactivate-and-delete-device/deactivate6.png)
   3. Nadat de verwijdering is voltooid, wordt u hiervan op de hoogte gesteld. De lijst met apparaten wordt ook bijgewerkt om de verwijdering weer te geven.

## <a name="deactivate-and-retain-data"></a>Gegevens deactiveren en bewaren

Als u geïnteresseerd bent in het verwijderen van het apparaat, maar de gegevens wilt behouden, voert u de volgende stappen uit:

#### <a name="to-deactivate-a-device-and-retain-the-data"></a>Een apparaat deactiveren en de gegevens bewaren
1. Het apparaat deactiveren. Alle volumecontainers en de momentopnamen van het apparaat blijven.
   
   1. Ga naar de StorSimple-apparaatbeheerservice en klik op **Apparaten**. Selecteer in het blade **Apparaten** het apparaat dat u wilt deactiveren, klik met de rechtermuisknop en klik op **Deactiveren**.

         ![StorSimple-apparaat deactiveren](./media/storsimple-8000-deactivate-and-delete-device/deactivate1.png)
   2. Typ in het blad **Deactiveren** de naam van het apparaat om te bevestigen en klik op **Deactiveren**. Het deactiveren begint en duurt enkele minuten.

         ![StorSimple-apparaat deactiveren](./media/storsimple-8000-deactivate-and-delete-device/deactivate2.png)
2. U nu falen over de volumecontainers en de bijbehorende momentopnamen. Ga voor procedures naar [Failover en noodherstel voor uw StorSimple-apparaat.](storsimple-8000-device-failover-disaster-recovery.md)
3. Na deactivering en failover u het apparaat volledig verwijderen. Als u een apparaat verwijdert, wordt het verwijderd uit de lijst met apparaten die met de service zijn verbonden. De service kan het verwijderde apparaat dan niet meer beheren. Voer de volgende stappen uit om het apparaat te verwijderen:
   
   1. Ga naar de StorSimple-apparaatbeheerservice en klik op **Apparaten**. Selecteer **in** het blade Apparaten het gedeactiveerde apparaat dat u wilt verwijderen, klik met de rechtermuisknop en klik op **Verwijderen**.

       ![StorSimple-apparaat deactiveren](./media/storsimple-8000-deactivate-and-delete-device/deactivate5.png)
   2. Typ in het blad **Verwijderen** de apparaatnaam om te bevestigen en klik op **Verwijderen**. Het verwijderen duurt enkele minuten.

       ![StorSimple-apparaat deactiveren](./media/storsimple-8000-deactivate-and-delete-device/deactivate6.png)
   3. Nadat de verwijdering is voltooid, wordt u hiervan op de hoogte gesteld. De lijst met apparaten wordt ook bijgewerkt om de verwijdering weer te geven.

     
## <a name="deactivate-and-delete-a-cloud-appliance"></a>Een cloudtoestel deactiveren en verwijderen

Voor een StorSimple Cloud Appliance wordt de activering van de portal deallocates en verwijdert de virtuele machine en de resources die zijn gemaakt toen deze is ingericht. Wanneer het cloudapparaat is gedeactiveerd, kan het niet meer worden hersteld naar de oorspronkelijke staat.

![StorSimple Cloud Appliance deactiveren](./media/storsimple-8000-deactivate-and-delete-device/deactivate7.png)

Deactivering resulteert in de volgende acties:

* De StorSimple Cloud Appliance wordt uit de service verwijderd.
* De virtuele machine voor het StorSimple Cloud Appliance wordt verwijderd.
* De osschijf en gegevensschijven die voor het StorSimple Cloud Appliance zijn gemaakt, blijven behouden. Als u deze entiteiten niet gebruikt, moet u ze handmatig verwijderen.
* De gehoste service en het virtuele netwerk die zijn gemaakt tijdens het inrichten, blijven behouden. Als u deze entiteiten niet gebruikt, moet u ze handmatig verwijderen.
* Cloudsnapshots die door het StorSimple Cloud Appliance zijn gemaakt, blijven behouden.

Nadat het cloudtoestel is gedeactiveerd, u het verwijderen uit de lijst met apparaten. Selecteer het gedeactiveerde apparaat, klik met de rechtermuisknop en klik op **Verwijderen**. StorSimple waarschuwt u zodra het apparaat is verwijderd en de lijst met apparaten wordt bijgewerkt.

## <a name="next-steps"></a>Volgende stappen

* Als u het gedeactiveerde apparaat wilt herstellen naar fabrieksstandaardinstellingen, gaat u naar [Het apparaat opnieuw instellen naar de standaardinstellingen van de fabriek.](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings)
* Neem voor technische ondersteuning [contact op met Microsoft Support](storsimple-8000-contact-microsoft-support.md).
* Ga voor meer informatie over het gebruik van de StorSimple Device Manager-service naar [De StorSimple Device Manager-service gebruiken om uw StorSimple-apparaat te beheren.](storsimple-8000-manager-service-administration.md)

