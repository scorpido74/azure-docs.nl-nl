---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: b4c3fcb86fb098263840accc561785a40b767952
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67176223"
---
#### <a name="to-install-updates-via-the-azure-portal"></a>Updates installeren via Azure Portal

1. Ga naar uw StorSimple Device Manager en selecteer **Apparaten**. Selecteer in de lijst met apparaten die zijn verbonden met uw service, het apparaat dat u wilt bijwerken.

    ![apparaat bijwerken](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate1m.png) 

2. Klik op de blade **Instellingen** op **Apparaatupdates**.

    ![apparaat bijwerken](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate2m.png)  

3. Als er software-updates beschikbaar zijn, wordt er een bericht weergegeven. U kunt ook op **Scannen** klikken om naar updates te zoeken. Noteer de software versie die u gebruikt. 

    ![apparaat bijwerken](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate3m1.png)

    U ontvangt een melding wanneer de scan wordt gestart en voltooid.

    ![apparaat bijwerken](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate5m.png)

4. Nadat de updates zijn gescand, klikt u op **Updates downloaden**.

    ![apparaat bijwerken](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate6m.png)

5. Bekijk de opmerkingen bij de release op de Blade **nieuwe updates** . Nadat de updates zijn gedownload, moet u de installatie bevestigen. Klik op **OK**.

    ![apparaat bijwerken](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate7m.png)

6. U ontvangt een melding wanneer de upload wordt gestart en wanneer deze is voltooid.

     ![apparaat bijwerken](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate8m.png)

5. Klik op de blade **Apparaatupdates** op **Installeren**.

     ![apparaat bijwerken](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate11m1.png)

6. Op de blade **Nieuwe updates** krijgt u een waarschuwing dat de update verstorend is. Als de virtuele matrix een apparaat met een enkel knooppunt is, wordt het apparaat opnieuw opgestart nadat het is bijgewerkt. Dit verstoort alle actieve IO. Klik op **OK** om de updates te installeren.

    ![apparaat bijwerken](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate12m.png)

7. U ontvangt een melding wanneer de installatietaak wordt gestart.

    ![apparaat bijwerken](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate13m.png)

8.  Nadat de installatietaak is voltooid, klikt u op de koppeling **Taak weergeven** op de blade **Apparaatupdates** om de installatie te controleren. 

    ![apparaat bijwerken](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate15m1.png)

    Met deze actie gaat u naar de Blade **updates installeren** . Hier kunt u gedetailleerde informatie over de taak weergeven.

    ![apparaat bijwerken](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate16m1.png)

9. Als u bent begonnen met een virtuele array met software versie update 0,6 (10.0.10293.0), voert u update 1 uit en bent u klaar. U kunt de resterende stappen overs Laan. Als u bent begonnen met een virtuele matrix met een software versie vóór update 0,6 (10.0.10293.0), bent u nu bijgewerkt met het bijwerken van 0,6. U ziet een ander bericht dat aangeeft dat er updates beschikbaar zijn. Herhaal stap 4-8 om update 1 te installeren.

    ![apparaat bijwerken](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate17.png)

