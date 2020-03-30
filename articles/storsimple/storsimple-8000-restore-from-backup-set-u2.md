---
title: Herstel een volume van back-up op een StorSimple 8000-serie | Microsoft Documenten
description: Hier wordt uitgelegd hoe u de StorSimple Device Manager-service Back-upcatalogus gebruiken om een StorSimple-volume te herstellen vanaf een back-upset.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/23/2017
ms.author: alkohli
ms.openlocfilehash: 6a2e022697ced90d968075b7a4abe4163be7a539
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60723340"
---
# <a name="restore-a-storsimple-volume-from-a-backup-set"></a>Een StorSimple-volume herstellen op basis van een back-upset

## <a name="overview"></a>Overzicht

In deze zelfstudie wordt de herstelbewerking beschreven die is uitgevoerd op een apparaat uit de StorSimple 8000-serie met behulp van een bestaande back-upset. Gebruik het blade van de **back-upcatalogus** om een volume te herstellen van een lokale of cloudback-up. Het blade van de **back-upcatalogus** geeft alle back-upsets weer die worden gemaakt wanneer handmatige of geautomatiseerde back-ups worden gemaakt. De herstelbewerking van een back-upset brengt het volume onmiddellijk online terwijl gegevens op de achtergrond worden gedownload.

Een alternatieve methode om te beginnen met herstellen is om naar **Apparaten > [Uw apparaat] > Volumes**te gaan. Selecteer in het blad **Volumes** een volume, klik met de rechtermuisknop om het contextmenu aan te roepen en selecteer **Herstellen**.

## <a name="before-you-restore"></a>Voordat u herstelt

Voordat u een herstel start, bekijkt u de volgende kanttekeningen:

* **U moet het volume offline halen** : neem het volume offline op zowel de host als het apparaat voordat u de herstelbewerking start. Hoewel de herstelbewerking automatisch het volume online brengt op het apparaat, moet u het apparaat handmatig online op de host brengen. U het volume online op de host brengen zodra het volume online is op het apparaat. (U hoeft niet te wachten tot de herstelbewerking is voltooid.) Ga voor procedures naar [Een volume offline nemen.](storsimple-8000-manage-volumes-u2.md#take-a-volume-offline)

* **Volumetype na herstel** : verwijderde volumes worden hersteld op basis van het type in de momentopname; dat wil zeggen dat volumes die lokaal zijn vastgemaakt, worden hersteld als lokaal vastgemaakte volumes en volumes die zijn gelaagd, worden hersteld als gelaagde volumes.

    Voor bestaande volumes overschrijft het huidige gebruikstype van het volume het type dat in de momentopname is opgeslagen. Als u bijvoorbeeld een volume herstelt van een momentopname die is gemaakt toen het volumetype werd gelaagd en dat volumetype nu lokaal is vastgemaakt (vanwege een conversiebewerking die is uitgevoerd), wordt het volume hersteld als een lokaal vastgemaakt volume. Als een bestaand lokaal vastgemaakt volume is uitgebreid en vervolgens is hersteld van een oudere momentopname die is gemaakt toen het volume kleiner was, behoudt het herstelde volume de huidige uitgebreide grootte.

    U een volume niet converteren van een gelaagd volume naar een lokaal vastgemaakt volume of van een lokaal vastgemaakt volume naar een gelaagd volume terwijl het volume wordt hersteld. Wacht tot de herstelbewerking is voltooid en vervolgens u het volume converteren naar een ander type. Ga naar [Het volumetype wijzigen](storsimple-8000-manage-volumes-u2.md#change-the-volume-type)voor informatie over het converteren van een volume. 

* **De volumegrootte wordt weergegeven in het herstelde volume:** dit is een belangrijke overweging als u een lokaal vastgemaakt volume herstelt dat is verwijderd (omdat lokaal vastgemaakte volumes volledig zijn ingericht). Zorg ervoor dat u voldoende ruimte hebt voordat u een lokaal vastgemaakt volume probeert te herstellen dat eerder is verwijderd.

* **U een volume niet uitbreiden terwijl het wordt hersteld:** Wacht totdat de herstelbewerking is voltooid voordat u probeert het volume uit te vouwen. Ga voor informatie over het uitbreiden van een volume naar [Een volume wijzigen](storsimple-8000-manage-volumes-u2.md#modify-a-volume).

* **U een back-up uitvoeren terwijl u een lokaal volume herstelt:** Ga voor procedures naar [De StorSimple Device Manager-service gebruiken om back-upbeleid te beheren.](storsimple-8000-manage-backup-policies-u2.md)

* **U een herstelbewerking annuleren:** als u de hersteltaak annuleert, wordt het volume teruggedraaid naar de status waarin het zich bevond voordat u de herstelbewerking initieerde. Ga voor procedures naar [Een taak annuleren.](storsimple-8000-manage-jobs-u2.md#cancel-a-job)

## <a name="how-does-restore-work"></a>Hoe werkt herstel

Voor apparaten met Update 4 of hoger wordt een herstel op basis van heatmap geïmplementeerd. Als de host verzoeken om toegang tot gegevens het apparaat bereiken, worden deze aanvragen bijgehouden en wordt een heatmap gemaakt. Hoge aanvraagsnelheid resulteert in gegevensbrokken met hogere warmte, terwijl een lagere aanvraagsnelheid zich vertaalt in brokken met een lagere warmte. U moet de gegevens ten minste twee keer openen om als _heet_te worden gemarkeerd. Een bestand dat wordt gewijzigd, wordt ook gemarkeerd als _hot_. Zodra u het herstel initieert, vindt proactieve hydratatie van gegevens plaats op basis van de heatmap. Voor versies die eerder zijn dan update 4, worden de gegevens gedownload tijdens het herstellen op basis van alleen toegang.

De volgende kanttekeningen zijn van toepassing op heatmap-gebaseerde herstelt:

* Heatmap-tracking is alleen ingeschakeld voor gelaagde volumes en lokaal vastgemaakte volumes worden niet ondersteund.

* Herstel op basis van Heatmap wordt niet ondersteund bij het klonen van een volume naar een ander apparaat. 

* Als er een in-place herstellen en een lokale momentopname voor het volume te herstellen bestaat op het apparaat, dan doen we niet rehydrateren (als gegevens is al lokaal beschikbaar). 

* Wanneer u herstelt, worden standaard de rehydratietaken gestart die gegevens op basis van de heatmap proactief hydrateren. 

In Update 4 kunnen Windows PowerShell-cmdlets worden gebruikt om rehydratietaken op te vragen, een rehydratietaak te annuleren en de status van de rehydratietaak te krijgen.

* `Get-HcsRehydrationJob`- Deze cmdlet krijgt de status van de rehydratiebaan. Een enkele rehydratietaak wordt geactiveerd voor één volume.

* `Set-HcsRehydrationJob`- Met deze cmdlet u pauzeren, stoppen, de rehydratietaak hervatten wanneer de rehydratie aan de gang is.

Ga voor meer informatie over rehydratiecmdlets naar [Windows PowerShell-cmdlet-verwijzing voor StorSimple](https://technet.microsoft.com/library/dn688168.aspx).

Bij automatische rehydratie worden doorgaans hogere tijdelijke leesprestaties verwacht. De werkelijke omvang van verbeteringen is afhankelijk van verschillende factoren, zoals toegangspatroon, gegevensverloop en gegevenstype. 

Om een rehydratietaak te annuleren, u de PowerShell-cmdlet gebruiken. Als u rehydratietaken permanent wilt uitschakelen voor alle toekomstige herstelwerkzaamheden, neemt [u contact op met Microsoft Support](storsimple-8000-contact-microsoft-support.md).

## <a name="how-to-use-the-backup-catalog"></a>De back-upcatalogus gebruiken

Het blade **back-upcatalogus** biedt een query waarmee u de selectie voor back-upseten verkleinen. U de back-upsets filteren die zijn opgehaald op basis van de volgende parameters:

* **Tijdbereik** : de datum en het tijdsbereik waarop de back-upset is gemaakt.
* **Apparaat** – Het apparaat waarop de back-upset is gemaakt.
* **Back-upbeleid** of **-volume:** het back-upbeleid of het back-upvolume dat aan deze back-upset is gekoppeld.

De gefilterde back-upsets worden vervolgens getabuleerd op basis van de volgende kenmerken:

* **Naam** : de naam van het back-upbeleid of het volume dat is gekoppeld aan de back-upset.
* **Type** : Back-upsets kunnen lokale momentopnamen of cloudmomentopnamen zijn. Een lokale momentopname is een back-up van al uw volumegegevens die lokaal op het apparaat zijn opgeslagen, terwijl een cloudmomentopname verwijst naar de back-up van volumegegevens die zich in de cloud bevinden. Lokale momentopnamen bieden snellere toegang, terwijl cloudmomentopnamen worden gekozen voor gegevenstolerantie.
* **Grootte** : de werkelijke grootte van de back-upset.
* **Gemaakt op** - De datum en tijd waarop de back-ups zijn gemaakt. 
* **Volumes** : het aantal volumes dat is gekoppeld aan de back-upset.
* **Gestart** - De back-ups kunnen automatisch worden gestart volgens een schema of handmatig door een gebruiker. (U een back-upbeleid gebruiken om back-ups te plannen. U ook de optie **Back-up maken** maken om een interactieve of on-demand back-up te maken.)

## <a name="how-to-restore-your-storsimple-volume-from-a-backup"></a>Uw StorSimple-volume herstellen vanuit een back-up

U het blade van de **back-upcatalogus** gebruiken om uw StorSimple-volume te herstellen vanuit een specifieke back-up. Houd er echter rekening mee dat het herstellen van een volume het volume teruggeeft naar de status waarin het zich bevond toen de back-up werd genomen. Alle gegevens die na de back-upbewerking zijn toegevoegd, gaan verloren.

> [!WARNING]
> Als u herstelt vanuit een back-up, worden de bestaande volumes van de back-up vervangen. Dit kan leiden tot het verlies van gegevens die zijn geschreven nadat de back-up is genomen.


### <a name="to-restore-your-volume"></a>Uw volume herstellen
1. Ga naar uw StorSimple Device Manager-service en klik op **Back-upcatalogus**.

2. Selecteer als volgt een back-upset:
   
   1. Geef het tijdsbereik op.
   2. Selecteer het juiste apparaat.
   3. Kies in de vervolgkeuzelijst het volume- of back-upbeleid voor de back-up die u wilt selecteren.
   4. Klik **op Toepassen** om deze query uit te voeren.

      De back-ups die zijn gekoppeld aan het geselecteerde volume- of back-upbeleid moeten worden weergegeven in de lijst met back-upsets.
   
      ![Lijst met back-upset](./media/storsimple-8000-restore-from-backup-set-u2/bucatalog.png)     
     
3. Vouw de back-upset uit om de bijbehorende volumes weer te geven. Deze volumes moeten offline worden gehaald op de host en het apparaat voordat u ze herstellen. Krijg toegang tot de volumes op het **volumesblad** van uw apparaat en volg de stappen in [Een volume offline uitvoeren](storsimple-8000-manage-volumes-u2.md#take-a-volume-offline) om ze offline te halen.
   
   > [!IMPORTANT]
   > Zorg ervoor dat u de volumes eerst offline hebt gehaald op de host, voordat u de volumes offline haalt op het apparaat. Als u de volumes niet offline haalt op de host, kan dit mogelijk leiden tot gegevensbeschadiging.
   
4. Navigeer terug naar het tabblad **Back-upcatalogus** en selecteer een back-upset. Klik met de rechtermuisknop en selecteer vervolgens in het contextmenu **Herstellen**.

    ![Lijst met back-upset](./media/storsimple-8000-restore-from-backup-set-u2/restorebu1.png)

5. U wordt gevraagd om een bevestiging. Controleer de herstelgegevens en schakel het selectievakje Bevestiging in.
   
    ![Bevestigingspagina](./media/storsimple-8000-restore-from-backup-set-u2/restorebu2.png)

7. Klik **op Herstellen**. Hiermee wordt een hersteltaak gestart die u weergeven door toegang te krijgen tot de pagina **Vacatures.**

   ![Bevestigingspagina](./media/storsimple-8000-restore-from-backup-set-u2/restorebu5.png)

8. Nadat het herstel is voltooid, controleert u of de inhoud van uw volumes wordt vervangen door volumes van de back-up.


## <a name="if-the-restore-fails"></a>Als het herstel mislukt

U ontvangt een waarschuwing als de herstelbewerking om welke reden dan ook mislukt. Als dit gebeurt, vernieuwt u de back-uplijst om te controleren of de back-up nog steeds geldig is. Als de back-up geldig is en u herstelt vanuit de cloud, kunnen verbindingsproblemen het probleem veroorzaken.

Als u de herstelbewerking wilt voltooien, neemt u het volume offline op de host en probeert u de herstelbewerking opnieuw. Houd er rekening mee dat eventuele wijzigingen in de volumegegevens die tijdens het herstelproces zijn uitgevoerd, verloren gaan.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [het beheren van StorSimple-volumes.](storsimple-8000-manage-volumes-u2.md)
* Meer informatie over het [gebruik van de StorSimple Device Manager-service om uw StorSimple-apparaat te beheren.](storsimple-8000-manager-service-administration.md)

