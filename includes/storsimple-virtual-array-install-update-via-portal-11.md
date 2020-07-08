---
title: bestand opnemen
description: bestand opnemen
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 07/18/2018
ms.author: alkohli
ms.custom: include file
ms.openlocfilehash: 65d5a88f7b5d059deb633f062639e455c64ef2f7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "67176559"
---
#### <a name="to-install-updates-via-the-azure-portal"></a>Updates installeren via Azure Portal

1. Ga naar uw StorSimple Device Manager en selecteer **Apparaten**. Selecteer in de lijst met apparaten die zijn verbonden met uw service, het apparaat dat u wilt bijwerken.

2. Klik onder **instellingen**op **updates voor apparaten**.  

3. Als er software-updates beschikbaar zijn, wordt er een bericht weergegeven. U kunt ook op **Scannen** klikken om naar updates te zoeken. Noteer de software versie die u gebruikt. 

    ![apparaat bijwerken](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate3m1.png)

    U ontvangt een melding wanneer de scan wordt gestart en voltooid.
 
4. Nadat de updates zijn gescand, klikt u op **Updates downloaden**. Bekijk de opmerkingen bij de release onder **nieuwe updates**. Nadat de updates zijn gedownload, moet u de installatie bevestigen. Klik op **OK**.

    ![apparaat bijwerken](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate6m.png)

    U ontvangt een melding wanneer de upload wordt gestart en wanneer deze is voltooid.

5. Klik onder **updates voor apparaten**op **installeren**.

     ![apparaat bijwerken](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate11m1.png)

6. Onder **nieuwe updates**wordt u gewaarschuwd dat de update is verstoord. Als de virtuele matrix een apparaat met een enkel knooppunt is, wordt het apparaat opnieuw opgestart nadat het is bijgewerkt. Dit verstoort alle actieve IO. Klik op **OK** om de updates te installeren.

    ![apparaat bijwerken](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate12m.png)

    U ontvangt een melding wanneer de installatietaak wordt gestart.

7.  Nadat de installatie taak is voltooid, klikt u op **taak koppeling weer geven** . Met deze actie gaat u naar de Blade **updates installeren** . Hier kunt u gedetailleerde informatie over de taak weergeven. 

    ![apparaat bijwerken](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate16m1.png)

8. Als u bent begonnen met een virtuele array met software versie update 1 (10.0.10296.0), voert u nu update 1,1 uit en bent u klaar. U kunt de resterende stappen overs Laan. 

    Als u bent begonnen met een virtuele array met software versie update 0,6 (10.0.10293.0), bent u nu bijgewerkt met het bijwerken van 1,0. U ziet een ander bericht dat aangeeft dat er updates beschikbaar zijn. Herhaal stap 4-7 om update 1,1 te installeren.

    

