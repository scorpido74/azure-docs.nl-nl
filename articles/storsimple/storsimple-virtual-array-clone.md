---
title: Kloon een StorSimple Virtual Array back-up | Microsoft Documenten
description: Meer informatie over het klonen van een back-up en het herstellen van een bestand uit uw StorSimple Virtual Array.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: af6e979c-55e3-477c-b53e-a76a697f80c9
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2016
ms.author: alkohli
ms.openlocfilehash: feffbb634af62d70a840febcf2a04afb7bdeeddd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60580859"
---
# <a name="clone-from-a-backup-of-your-storsimple-virtual-array"></a>Kloon uit een back-up van uw StorSimple Virtual Array

## <a name="overview"></a>Overzicht

In dit artikel wordt stap voor stap beschreven hoe u een back-upset van uw aandelen of volumes kloont op uw Microsoft Azure StorSimple Virtual Array. De gekloonde back-up wordt gebruikt om een verwijderd of verloren bestand te herstellen. Het artikel bevat ook gedetailleerde stappen om een item-level recovery uit te voeren op uw StorSimple Virtual Array geconfigureerd als een bestandsserver.

## <a name="clone-shares-from-a-backup-set"></a>Shares van een back-upset klonen

**Voordat u shares probeert te klonen, moet u ervoor zorgen dat u voldoende ruimte op het apparaat hebt om deze bewerking te voltooien.** Voer in de [Azure-portal](https://portal.azure.com/)de volgende stappen uit om vanuit een back-up te klonen.

#### <a name="to-clone-a-share"></a>Een aandeel klonen

1. Blader naar **het blade van Apparaten.** Selecteer en klik op uw apparaat en klik vervolgens op **Shares**. Selecteer het aandeel dat u wilt klonen en klik met de rechtermuisknop op het aandeel om het contextmenu aan te roepen. Selecteer **Klonen**.
   
   ![Een back-up klonen](./media/storsimple-virtual-array-clone/cloneshare1.png)
2. Klik in het **kloonblad** op **Back-up > selecteren** en ga als volgt te werk: 
   
   a.    Filter een back-up op dit apparaat op basis van het tijdsbereik. U kiezen uit **afgelopen 7 dagen,** **afgelopen 30 dagen**en afgelopen **jaar.**
   
   b.    Selecteer in de lijst met gefilterde back-ups een back-up om uit te klonen.
   
   c.    Klik op **OK**.
   
   ![Een back-up klonen](./media/storsimple-virtual-array-clone/cloneshare3.png)
3. Klik in het **kloonblad** op **Doelinstellingen** en ga als volgt te werk:
   
   a.    Geef een naam van een aandeel op. De naam van het aandeel moet 3-127 tekens bevatten.
   
   b.    Geef eventueel een beschrijving voor het gekloonde aandeel.
   
   c.    U het type aandeel waarop u herstelt niet wijzigen. Een gelaagd aandeel wordt gekloond als een gelaagd en een lokaal vastgemaakt aandeel als lokaal vastgemaakt.
   
   d.    De capaciteit is ingesteld als gelijk aan de grootte van het aandeel waarvan u kloont.
   
   e.    Wijs de beheerders voor dit aandeel toe. U de eigenschappen van het aandeel wijzigen via Verkenner nadat de kloon is voltooid.
   
   f.    Klik op **OK**.
   
   ![Een back-up klonen](./media/storsimple-virtual-array-clone/cloneshare6.png)

4. Klik **op Kloon** om een kloontaak te starten. Nadat de taak is voltooid, wordt de kloonbewerking gestart en wordt u hiervan op de hoogte gesteld. Als u de voortgang van de kloon wilt controleren, gaat u naar het blad **Jobs** en klikt u op de taak om taakgegevens weer te geven.
5. Nadat de kloon is gemaakt, navigeert u terug naar het blad **Aandelen** op uw apparaat.
6. U nu het nieuwe gekloonde aandeel bekijken in de lijst met aandelen op uw apparaat. Een gelaagd aandeel wordt gekloond als gelaagd en een lokaal vastgemaakt aandeel als een lokaal vastgemaakt aandeel.
   
   ![Een back-up klonen](./media/storsimple-virtual-array-clone/cloneshare10.png)

## <a name="clone-volumes-from-a-backup-set"></a>Volumes van een back-upset klonen

Als u wilt klonen van een back-up, moet u in de Azure-portal stappen uitvoeren die vergelijkbaar zijn met die welke u bij het klonen van een share moet uitvoeren. De kloonbewerking kloont de back-up naar een nieuw volume op hetzelfde virtuele apparaat; u niet klonen naar een ander apparaat.

#### <a name="to-clone-a-volume"></a>Een volume klonen

1. Blader naar **het blade van Apparaten.** Selecteer en klik op uw apparaat en klik vervolgens op **Volumes**. Selecteer het volume dat u wilt klonen, klik met de rechtermuisknop op het volume om het contextmenu aan te roepen. Selecteer **Klonen**.
   
   ![Een volume klonen](./media/storsimple-virtual-array-clone/clonevolume1.png)
2. Klik in het **kloonblad** op **Back-up** en voer het volgende uit: 
   
   a.    Filter een back-up op dit apparaat op basis van het tijdsbereik. U kiezen uit **afgelopen 7 dagen,** **afgelopen 30 dagen**en afgelopen **jaar.** 
   
   b.    Selecteer in de lijst met gefilterde back-ups een back-up om uit te klonen.
   
   c.    Klik op **OK**.
   
   ![Een back-up klonen](./media/storsimple-virtual-array-clone/clonevolume3.png)
3. Klik in het **kloonblad** op **Doelvolumeinstellingen** en voer het volgende uit::
   
   a. De naam van het apparaat wordt automatisch ingevuld.
   
   b. Geef een volumenaam op voor het **gekloonde volume**. De volumenaam moet 3 tot 127 tekens bevatten.
   
   c. Het volumetype wordt automatisch ingesteld op het oorspronkelijke volume. Een gelaagd volume wordt gekloond als gelaagd en een lokaal vastgemaakt volume als lokaal vastgemaakt.
   
   d. Klik voor de **verbonden hosts**op **Selecteren**.
   
   ![Een back-up klonen](./media/storsimple-virtual-array-clone/clonevolume4.png)
4. Kies in het **blade van Verbonden hosts** uit een bestaande ACR of voeg een nieuwe ACR toe. Als u een nieuwe ACR wilt toevoegen, moet u een ACR-naam en de host IQN opgeven. Klik **op Selecteren**.
   
   ![Een back-up klonen](./media/storsimple-virtual-array-clone/clonevolume5.png)
5. Klik **op Clone** om een kloontaak te starten.
   
   ![Een back-up klonen](./media/storsimple-virtual-array-clone/clonevolume6.png)  
6. Nadat de kloontaak is gemaakt, wordt het klonen gestart. Zodra de kloon is gemaakt, wordt deze weergegeven op het volumesblad op uw apparaat. Houd er rekening mee dat een gelaagd volume wordt gekloond als gelaagd en een lokaal vastgemaakt volume wordt gekloond als een lokaal vastgemaakt volume.
   
   ![Een back-up klonen](./media/storsimple-virtual-array-clone/clonevolume8.png)
7. Zodra het volume online op de lijst met volumes wordt weergegeven, is het volume beschikbaar voor gebruik. Vernieuw in de iSCSI-initiatorhost de lijst met doelen in het venster Eigenschappen van iSCSI-initiators. Een nieuw doel dat de gekloonde volumenaam bevat, moet onder de statuskolom als 'inactief' worden weergegeven.
8. Selecteer het doel en klik op **Verbinding maken**. Nadat de initiator is verbonden met het doel, moet de status worden gewijzigd **in Verbonden**.
9. In het venster **Schijfbeheer** worden de gemonteerde volumes weergegeven zoals weergegeven in de volgende afbeelding. Klik met de rechtermuisknop op het gedetecteerde volume (klik op de naam van de schijf) en klik vervolgens op **Online**.

> [!IMPORTANT]
> Wanneer u een volume of een aandeel probeert te klonen uit een back-upset, kan er in de portal nog steeds een doelvolume of aandeel worden gemaakt in de portal. Het is belangrijk dat u dit doelvolume verwijdert of deelt in de portal om eventuele toekomstige problemen die uit dit element voortvloeien, te minimaliseren.
> 
> 

## <a name="item-level-recovery-ilr"></a>Herstel op itemniveau (ILR, item-level recovery)

Deze release introduceert het item-level recovery (ILR) op een StorSimple Virtual Array geconfigureerd als een bestandsserver. Met de functie u gedetailleerde herstel van bestanden en mappen doen vanuit een cloudback-up van alle shares op het StorSimple-apparaat. U verwijderde bestanden ophalen uit recente back-ups met behulp van een selfservicemodel.

Elk aandeel heeft een *map .backups* met de meest recente back-ups. U naar de gewenste back-up navigeren, relevante bestanden en mappen kopiëren vanuit de back-up en deze herstellen. Met deze functie worden oproepen naar beheerders voor het herstellen van bestanden uit back-ups geëlimineerd.

1. Bij het uitvoeren van de ILR u de back-ups bekijken via Verkenner. Klik op het specifieke aandeel waarvoor u de back-up wilt bekijken. U ziet een *map .back-ups* die is gemaakt onder het aandeel dat alle back-ups opslaat. Vouw de map *.backups* uit om de back-ups weer te geven. De map toont de geëxplodeerde weergave van de gehele back-uphiërarchie. Deze weergave wordt on-demand gemaakt en duurt meestal slechts een paar seconden om te maken.
   
   De laatste vijf back-ups worden op deze manier weergegeven en kunnen worden gebruikt om een item-level recovery uit te voeren. De vijf recente back-ups omvatten zowel de standaard geplande en de handmatige back-ups.
   
   * **Geplande back-ups** met de naam &lt;Device&gt;DailySchedule-YYYYMMDD-HHMMSS-UTC.
   * **Handmatige back-ups** genoemd als Ad-hoc-YYYMMDD-HHMMSS-UTC.
     
     ![](./media/storsimple-virtual-array-clone/image14.png)

2. Identificeer de back-up met de meest recente versie van het verwijderde bestand. Hoewel de mapnaam een UTC-tijdstempel bevat in elk van de voorgaande gevallen, is het tijdstip waarop de map is gemaakt de werkelijke apparaattijd waarop de back-up is gestart. Gebruik de maptijdstempel om de back-ups te zoeken en te identificeren.

3. Zoek de map of het bestand dat u wilt herstellen in de back-up die u in de vorige stap hebt geïdentificeerd. Houd er rekening mee dat u alleen de bestanden of mappen bekijken waarvoor u machtigingen hebt. Als u geen toegang hebt tot bepaalde bestanden of mappen, neemt u contact op met een beheerder van delen. De beheerder kan Verkenner gebruiken om de machtigingen voor delen te bewerken en u toegang te geven tot het specifieke bestand of de specifieke map. It is a recommended best practice that the share administrator is a user group instead of a single user.

4. Kopieer het bestand of de map naar het juiste aandeel op uw StorSimple-bestandsserver.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [beheren van uw StorSimple Virtual Array met behulp van de lokale web-gebruikersinterface.](storsimple-ova-web-ui-admin.md)

