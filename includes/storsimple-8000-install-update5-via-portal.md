---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: d2df7388018c463ba58b57be46945915210b84b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67176233"
---
#### <a name="to-install-an-update-from-the-azure-portal"></a>Een update installeren via Azure Portal

1. Selecteer uw apparaat op de pagina van de StorSimple-service.

    ![Apparaat selecteren](./media/storsimple-8000-install-update5-via-portal/update1.png)

2. Ga naar > **Apparaatinstellingen-updates**. **Device settings**

    ![Klik op Apparaatupdates](./media/storsimple-8000-install-update5-via-portal/update2.png)

2. Er verschijnt een melding als er nieuwe updates beschikbaar zijn. U ook in het blade **Apparaatupdates** klikken op **Updates scannen**. Er wordt een taak gemaakt om te zoeken naar beschikbare updates. U ontvangt een melding wanneer de taak is voltooid.

    ![Klik op Apparaatupdates](./media/storsimple-8000-install-update5-via-portal/update3.png)

3. We raden u aan de releaseopmerkingen te bekijken voordat u een update toepast op uw apparaat. Als u updates wilt toepassen, klikt u op **Updates installeren**. Bekijk in het blade **Voor regelmatige updates bevestigen** de vereisten die u moet voltooien voordat u updates toepast. Schakel het selectievakje in om aan te geven dat u klaar bent om het apparaat bij te werken en klik op **Installeren**.

    ![Klik op Apparaatupdates](./media/storsimple-8000-install-update5-via-portal/update4.png)

6. Er wordt een reeks vereiste controles gestart. Deze controles zijn onder andere:
   
   * **Statuscontroles controllers** om te controleren of beide apparaatcontrollers in orde en online zijn.
   * **Statuscontroles hardwareonderdelen** om te controleren of alle hardwareonderdelen op uw StorSimple-apparaat in orde zijn.
   * **DATA 0-controles** om te controleren of DATA 0 is ingeschakeld op uw apparaat. Als deze interface niet is ingeschakeld, schakelt u deze in en probeert u het vervolgens opnieuw.

     De update wordt alleen gedownload en geïnstalleerd als alle controles zijn voltooid. U krijgt een melding wanneer de controles worden uitgevoerd. Als de prechecks mislukken, dan krijgt u de redenen voor het mislukken. Deze problemen aanpakken en vervolgens de bewerking opnieuw proberen. Mogelijk moet u contact opnemen met Microsoft-ondersteuning als u deze problemen niet zelf kunt oplossen.

7. Nadat de voorcontroles zijn voltooid, wordt een updatetaak gemaakt. U krijgt een melding wanneer de bijwerktaak is gemaakt.
   
    ![Maken van bijwerktaak](./media/storsimple-8000-install-update5-via-portal/update6.png)
   
    De update wordt vervolgens op het apparaat toegepast.

9. De update neemt enkele uren in beslag. Selecteer de bijwerktaak en klik op **Details** om de details van de taak te bekijken.

    ![Maken van bijwerktaak](./media/storsimple-8000-install-update5-via-portal/update8.png)

     U ook de voortgang van de updatetaak volgen vanuit **apparaatinstellingen > Vacatures.** Op het blade **Jobs** ziet u de voortgang van de update.

     ![Maken van bijwerktaak](./media/storsimple-8000-install-update5-via-portal/update7.png)

10. Nadat de taak is voltooid, navigeert u naar de **apparaatinstellingen > apparaatupdates**. De softwareversie moet nu worden bijgewerkt.

