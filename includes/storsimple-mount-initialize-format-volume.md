---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 592131bf6cca4c4c3c827de23742e8d52bcb4d1c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "67176090"
---
#### <a name="to-mount-initialize-and-format-a-volume"></a>Een volume koppelen, initialiseren en formatteren
1. Start de Microsoft iSCSI-initiator.
2. Klik in het venster **Eigenschappen iSCSI-initiator** op het tabblad **Detectie** op **Portal detecteren**.
3. Geef in het dialoogvenster **Doelportaal detecteren** het IP-adres op van de netwerkinterface met iSCSI-functionaliteit en klik vervolgens op **OK**. 
4. Ga in het venster **Eigenschappen iSCSI-initiator** op het tabblad **Doelen** naar **Gedetecteerde doelen**. De status van het apparaat moet worden weergegeven als **Inactief**.
5. Selecteer het doelapparaat en klik vervolgens op **Verbinden**. Nadat het apparaat is verbonden, moet de status zijn gewijzigd in **Verbonden**. (Zie [Installing and Configuring Microsoft iSCSI Initiator][1] (Microsoft iSCSI-initiator installeren en configureren) voor meer informatie over het gebruik van de Microsoft iSCSI-initiator.)
6. Druk op uw Windows-hots op de Windows-toets + X en klik vervolgens op **Uitvoeren**. 
7. Typ in het dialoogvenster **Uitvoeren****Diskmgmt.msc**. Klik op **OK**. Het dialoogvenster **Schijfbeheer** wordt weergegeven. In het rechterdeelvenster worden de volumes op uw host weergeven.
8. In het venster **Schijfbeheer** worden de gekoppelde volumes weergegeven zoals in de volgende afbeelding. Klik met de rechtermuisknop op het gedetecteerde volume (klik op de naam van de schijf) en klik vervolgens op **Online**.
   
     ![Opmaakvolume initialiseren](./media/storsimple-mount-initialize-format-volume/HCS_InitializeFormatVolume-include.png) 
9. Klik met de rechtermuisknop op het volume (klik op de naam van de schijf) en klik vervolgens op **Initialiseren**.
10. Om een eenvoudig volume te formatteren, moet u de volgende stappen uitvoeren:
    
    1. Selecteer het volume, klik er met de rechtermuisknop op (klik op het juiste gebied) en klik vervolgens op **Nieuw eenvoudig volume**.
    2. Geef in de wizard Nieuw eenvoudig volume de volumegrootte en de stationsletter op en configureer het volume als een NTFS-bestandssysteem.
    3. Geef een clustergrootte van 64 kB op. Deze clustergrootte werkt goed samen met de ontdubbelingsalgoritmen die worden gebruikt in de StorSimple-oplossing.
    4. Voer een snelle formattering uit.

![](./media/storsimple-mount-initialize-format-volume/Video_icon.png) Video beschikbaar video **beschikbaar**

Als u een video wilt bekijken over het koppelen, initialiseren en formatteren van een StorSimple-oplossing, klikt u [hier](https://azure.microsoft.com/documentation/videos/mount-initialize-and-format-a-storsimple-volume/).

<!--Link references-->
[1]: https://technet.microsoft.com/library/ee338480(WS.10).aspx
