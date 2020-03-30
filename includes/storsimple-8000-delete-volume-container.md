---
title: bestand opnemen
description: bestand opnemen
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 06/08/2018
ms.author: alkohli
ms.custom: include file
ms.openlocfilehash: e683d17422321b780a1c01b3011292f2e2c631cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67176082"
---
Als u een volumecontainer wilt verwijderen, moet u
 - volumes in de volumecontainer te verwijderen. Als de volumecontainer gekoppelde volumes heeft, neemt u deze volumes eerst offline. Volg de stappen in [Een volume offline uitvoeren](../articles/storsimple/storsimple-8000-manage-volumes-u2.md#take-a-volume-offline). Nadat de volumes offline zijn, u ze verwijderen. 
 - bijbehorende back-upbeleidsregels en cloudmomentopnamen verwijderen. Controleer of de volumecontainer bijbehorende back-upbeleid en cloudmomentopnamen heeft. Als dat het zo is, verwijdert u [vervolgens het back-upbeleid](../articles/storsimple/storsimple-8000-manage-backup-policies-u2.md#delete-a-backup-policy). Hierdoor worden ook de cloudsnapshots verwijderd. 
 
Wanneer de volumecontainer geen gekoppelde volumes, back-upbeleid en cloudmomentopnamen heeft, u deze verwijderen. Voer de volgende procedure uit om een volumecontainer te verwijderen.

#### <a name="to-delete-a-volume-container"></a>Een volumecontainer verwijderen
1. Ga naar de StorSimple-apparaatbeheerservice en klik op **Apparaten**. Selecteer en klik op het apparaat en ga vervolgens naar **Instellingen > > Volume-containers beheren**.

    ![Volume containers blad](./media/storsimple-8000-create-volume-container/createvolumecontainer2.png)

2. Selecteer in de tabellijst met volumecontainers de volumecontainer die u wilt verwijderen, klik met de rechtermuisknop **op ...** en selecteer **Verwijderen**.

    ![Volumecontainer verwijderen](./media/storsimple-8000-delete-volume-container/deletevolumecontainer1.png)

3. Als een volumecontainer geen gekoppelde volumes, back-upbeleid en cloudmomentopnamen heeft, kan deze worden verwijderd. Wanneer u om bevestiging wordt gevraagd, controleert u het selectievakje en schakelt u het selectievakje in met vermelding van de impact van het verwijderen van de volumecontainer. Klik **op Verwijderen** om de volumecontainer vervolgens te verwijderen.

    ![Verwijdering bevestigen](./media/storsimple-8000-delete-volume-container/deletevolumecontainer2.png)

De lijst met volumecontainers wordt bijgewerkt om de verwijderde volumecontainer weer te geven.

![](./media/storsimple-8000-delete-volume-container/deletevolumecontainer5.png)


