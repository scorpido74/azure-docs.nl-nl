---
title: Een Microsoft Azure StorSimple Virtual Array deactiveren en verwijderen | Microsoft Documenten
description: Beschrijft hoe u het StorSimple-apparaat uit de service verwijderen door het eerst te deactiveren en vervolgens te verwijderen.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: a929f5bc-03e2-4b01-b925-973db236f19f
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: alkohli
ms.openlocfilehash: bb1a56d204a46f89213f20e317494120f0ea565e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60580535"
---
# <a name="deactivate-and-delete-a-storsimple-virtual-array"></a>Een virtuele StorSimple-matrix deactiveren en verwijderen

## <a name="overview"></a>Overzicht

Wanneer u een StorSimple Virtual Array deactiveert, breekt u de verbinding tussen het apparaat en de bijbehorende StorSimple Device Manager-service. In deze zelfstudie wordt het volgende uitgelegd:

* Een apparaat deactiveren 
* Een gedeactiveerd apparaat verwijderen

De informatie in dit artikel is alleen van toepassing op StorSimple Virtual Arrays. Ga voor informatie over 8000-series naar het [deactiveren of verwijderen van een apparaat.](storsimple-deactivate-and-delete-device.md)

## <a name="when-to-deactivate"></a>Wanneer deactiveren?

Deactivering is een permanente bewerking en kan niet ongedaan worden gemaakt. U een gedeactiveerd apparaat niet opnieuw registreren bij de StorSimple Device Manager-service. Mogelijk moet u een StorSimple Virtual Array deactiveren en verwijderen in de volgende scenario's:

* **Geplande failover:** uw apparaat is online en u bent van plan om te mislukken over uw apparaat. Als u van plan bent om te upgraden naar een groter apparaat, moet u mogelijk niet op uw apparaat. Nadat het eigendom van de gegevens is overgedragen en de failover is voltooid, wordt het bronapparaat automatisch verwijderd.
* **Ongeplande failover:** uw apparaat is offline en u moet falen op het apparaat. Dit scenario kan optreden tijdens een ramp wanneer er een storing is in het datacenter en uw primaire apparaat is uitgeschakeld. U bent van plan om te falen over het apparaat naar een secundair apparaat. Nadat het eigendom van de gegevens is overgedragen en de failover is voltooid, wordt het bronapparaat automatisch verwijderd.
* **Buitengebruik :** U wilt het apparaat buiten gebruik stellen. Dit vereist dat u het apparaat eerst deactiveert en vervolgens verwijdert. Wanneer u een apparaat deactiveert, hebt u geen toegang meer tot gegevens die lokaal zijn opgeslagen. U alleen toegang krijgen tot en herstellen van de gegevens die zijn opgeslagen in de cloud. Als u van plan bent de apparaatgegevens na deactivering te bewaren, moet u een cloudmomentopname van al uw gegevens maken voordat u een apparaat deactiveert. Met deze cloudsnapshot u alle gegevens in een later stadium herstellen.

## <a name="deactivate-a-device"></a>Een apparaat deactiveren

Voer de volgende stappen uit om uw apparaat te deactiveren.

#### <a name="to-deactivate-the-device"></a>Het apparaat deactiveren

1. Ga in uw service naar **Beheer >-apparaten.** Klik in het blade **Apparaten** op het apparaat dat u wilt deactiveren.
   
    ![Apparaat selecteren om te deactiveren](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete7.png)
2. Klik in het **dashboardblad van** uw apparaat op **... Meer** en selecteer **Deactivering van de activering in**de lijst .
   
    ![Klik op deactiveren](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete8.png)
3. Typ in het blad **Deactiveren** de naam van het apparaat en klik op **Deactiveren**. 
   
    ![Deactiveren bevestigen](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete1.png)
   
    Het deactiveren begint en duurt enkele minuten.
   
    ![Lopende activering deactiveren](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete2.png)
4. Na deactivering wordt de lijst met apparaten vernieuwd.
   
    ![Voltooien deactiveren](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete3.png)
   
    U dit apparaat nu verwijderen.

## <a name="delete-the-device"></a>Het apparaat verwijderen

Een apparaat moet eerst worden gedeactiveerd om het te verwijderen. Als u een apparaat verwijdert, wordt het verwijderd uit de lijst met apparaten die met de service zijn verbonden. De service kan het verwijderde apparaat dan niet meer beheren. De gegevens die aan het apparaat zijn gekoppeld, blijven echter in de cloud. Deze gegevens worden vervolgens in rekening gebracht.

Voer de volgende stappen uit om het apparaat te verwijderen.

#### <a name="to-delete-the-device"></a>Het apparaat verwijderen

1. Ga in uw StorSimple-apparaatbeheer naar **Beheer > Apparaten**. Selecteer in het blade **Apparaten** een gedeactiveerd apparaat dat u wilt verwijderen.
2. Klik in het **dashboardblad apparaat** op **... Meer** en klik vervolgens op **Verwijderen**.
   
   ![Apparaat selecteren om te verwijderen](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete4.png)
3. Typ in het blad **Verwijderen** de naam van uw apparaat om de verwijdering te bevestigen en klik op **Verwijderen**. Als u het apparaat verwijdert, worden de cloudgegevens die aan het apparaat zijn gekoppeld, niet verwijderd. 
   
   ![De verwijdering bevestigen](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete5.png) 
4. De verwijdering begint en duurt een paar minuten om te voltooien.
   
   ![Lopende verwijderen](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete6.png)
   
    Nadat het apparaat is verwijderd, u de bijgewerkte lijst met apparaten bekijken.

## <a name="next-steps"></a>Volgende stappen

* Ga voor informatie over hoe u mislukken naar [Failover en herstel na noodgevallen van uw StorSimple Virtual Array.](storsimple-virtual-array-failover-dr.md)

* Als u meer wilt weten over het gebruik van de StorSimple Device Manager-service, gaat u [naar De StorSimple Device Manager-service gebruiken om uw StorSimple Virtual Array te beheren.](storsimple-virtual-array-manager-service-administration.md) 

