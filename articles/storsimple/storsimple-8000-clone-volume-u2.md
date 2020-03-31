---
title: Kloon een volume op StorSimple 8000 serie | Microsoft Documenten
description: Beschrijft de verschillende kloontypen en het gebruik en legt uit hoe u een back-upset gebruiken om een individueel volume te klonen op een apparaat uit de StorSimple 8000-serie.
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
ms.date: 12/05/2017
ms.author: alkohli
ms.openlocfilehash: 84734aefb72a3330d99c5707b461de2cd5e30484
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255000"
---
# <a name="use-the-storsimple-device-manager-service-in-azure-portal-to-clone-a-volume"></a>StorSimple Device Manager-service in Azure-portal gebruiken om een volume te klonen

## <a name="overview"></a>Overzicht

In deze zelfstudie wordt beschreven hoe u een back-upset gebruiken om een individueel volume te klonen via het blade van de **back-upcatalogus.** Het verklaart ook het verschil tussen *voorbijgaande* en *permanente* klonen. De richtlijnen in deze zelfstudie zijn van toepassing op alle storSimple 8000-serie apparaten met Update 3 of hoger.

Het storSimple Device **Manager-service Back-upcatalogusblad** toont alle back-upsets die worden gemaakt wanneer handmatige of geautomatiseerde back-ups worden gemaakt. U vervolgens een volume selecteren in een back-upset die u wilt klonen.

 ![Lijst met back-upset](./media/storsimple-8000-clone-volume-u2/bucatalog.png)

## <a name="considerations-for-cloning-a-volume"></a>Overwegingen voor het klonen van een volume

Houd rekening met de volgende informatie bij het klonen van een volume.

- Een kloon gedraagt zich op dezelfde manier als een normaal volume. Elke bewerking die mogelijk is op een volume is beschikbaar voor de kloon.

- Controle en standaardback-up worden automatisch uitgeschakeld op een gekloond volume. U moet een gekloond volume configureren voor eventuele back-ups.

- Een lokaal vastgemaakt volume wordt gekloond als een gelaagd volume. Als u het gekloonde volume lokaal moet vastmaken, u de kloon converteren naar een lokaal vastgemaakt volume nadat de kloonbewerking is voltooid. Ga naar [Het volumetype wijzigen](storsimple-8000-manage-volumes-u2.md#change-the-volume-type)voor informatie over het converteren van een gelaagd volume naar een lokaal vastgemaakt volume.

- Als u een gekloond volume probeert om te zetten van gelaagd naar lokaal vastgemaakt onmiddellijk na het klonen (wanneer het nog steeds een tijdelijke kloon is), mislukt de conversie met het volgende foutbericht:

    `Unable to modify the usage type for volume {0}. This can happen if the volume being modified is a transient clone and hasn’t been made permanent. Take a cloud snapshot of this volume and then retry the modify operation.`

    Deze fout wordt alleen ontvangen als u op een ander apparaat kloont. U het volume converteren naar lokaal vastgemaakt als u de tijdelijke kloon eerst converteert naar een permanente kloon. Maak een cloudmomentopname van de tijdelijke kloon om deze om te zetten in een permanente kloon.

## <a name="create-a-clone-of-a-volume"></a>Een kloon van een volume maken

U een kloon maken op hetzelfde apparaat, een ander apparaat of zelfs een cloudtoestel met behulp van een lokale of cloudmomentopname.

In de onderstaande procedure wordt beschreven hoe u een kloon maakt uit de back-upcatalogus.  Een alternatieve methode om kloon te starten is om naar **Volumes**te gaan, een volume te selecteren, vervolgens met de rechtermuisknop te klikken om het contextmenu aan te roepen en **Kloon**te selecteren.

Voer de volgende stappen uit om een kloon van uw volume te maken vanuit de back-upcatalogus.

#### <a name="to-clone-a-volume"></a>Een volume klonen

1. Ga naar uw StorSimple Device Manager-service en klik op **Back-upcatalogus**.

2. Selecteer als volgt een back-upset:
   
   1. Selecteer het juiste apparaat.
   2. Kies in de vervolgkeuzelijst het volume- of back-upbeleid voor de back-up die u wilt selecteren.
   3. Geef het tijdsbereik op.
   4. Klik **op Toepassen** om deze query uit te voeren.

      De back-ups die zijn gekoppeld aan het geselecteerde volume- of back-upbeleid moeten worden weergegeven in de lijst met back-upsets.
   
      ![Lijst met back-upset](./media/storsimple-8000-clone-volume-u2/bucatalog.png)
     
3. Vouw de back-upset uit om het gekoppelde volume weer te geven en selecteer een volume in een back-upset. Klik met de rechtermuisknop en selecteer vervolgens in het contextmenu **Kloon**.

   ![Lijst met back-upset](./media/storsimple-8000-clone-volume-u2/clonevol3b.png) 

3. Ga in het **kloonblad** de volgende stappen uit:
   
   1. Een doelapparaat identificeren. Dit is de locatie waar de kloon wordt gemaakt. U hetzelfde apparaat kiezen of een ander apparaat opgeven.

      > [!NOTE]
      > Zorg ervoor dat de benodigde capaciteit voor de kloon lager is dan de beschikbare capaciteit op het doelapparaat.
       
   2. Geef een unieke volumenaam op voor uw kloon. De naam moet tussen 3 en 127 tekens bevatten.
      
       > [!NOTE]
       > Het veld **Kloonvolume als** wordt **getierd,** zelfs als u een lokaal vastgemaakt volume kloont. U deze instelling niet wijzigen. Als u echter het gekloonde volume ook lokaal moet vastmaken, u de kloon converteren naar een lokaal vastgemaakt volume nadat u de kloon hebt gemaakt. Ga naar [Het volumetype wijzigen](storsimple-8000-manage-volumes-u2.md#change-the-volume-type)voor informatie over het converteren van een gelaagd volume naar een lokaal vastgemaakt volume.
          
   3. Geef **onder Verbonden hosts**een toegangscontrolerecord (ACR) op voor de kloon. U een nieuwe ACR toevoegen of kiezen uit de bestaande lijst. De ACR bepaalt welke hosts toegang hebben tot deze kloon.
      
       ![Lijst met back-upset](./media/storsimple-8000-clone-volume-u2/clonevol3a.png) 

   4. Klik **op Kloon** om de bewerking te voltooien.

4. Er wordt een kloontaak gestart en u krijgt een melding wanneer de kloon is gemaakt. Klik op de taakmelding of ga naar het blad **Jobs** om de kloontaak te controleren.

    ![Lijst met back-upset](./media/storsimple-8000-clone-volume-u2/clonevol5.png)

7. Nadat de kloontaak is voltooid, gaat u naar uw apparaat en klikt u op **Volumes**. In de lijst met volumes ziet u de kloon die zojuist is gemaakt in dezelfde volumecontainer met het bronvolume.

    ![Lijst met back-upset](./media/storsimple-8000-clone-volume-u2/clonevol6.png)

Een kloon die op deze manier wordt gemaakt, is een tijdelijke kloon. Zie Tijdelijke versus permanente [klonen](#transient-vs-permanent-clones)voor meer informatie over kloontypen.


## <a name="transient-vs-permanent-clones"></a>Voorbijgaande versus permanente klonen
Tijdelijke klonen worden alleen gemaakt wanneer u naar een ander apparaat kloont. U een specifiek volume klonen van een back-upset naar een ander apparaat dat wordt beheerd door StorSimple Device Manager. De tijdelijke kloon heeft verwijzingen naar de gegevens in het oorspronkelijke volume en gebruikt die gegevens om lokaal op het doelapparaat te lezen en te schrijven.

Nadat u een cloudmomentopname van een tijdelijke kloon hebt gemaakt, is de resulterende kloon een *permanente* kloon. Tijdens dit proces wordt een kopie van de gegevens gemaakt in de cloud en wordt de tijd om deze gegevens te kopiëren bepaald door de grootte van de gegevens en de Azure-latencies (dit is een Azure-naar-Azure-kopie). Dit proces kan dagen tot weken duren. De tijdelijke kloon wordt een permanente kloon en heeft geen verwijzingen naar de oorspronkelijke volumegegevens waaruit deze is gekloond.

## <a name="scenarios-for-transient-and-permanent-clones"></a>Scenario's voor tijdelijke en permanente klonen
In de volgende secties worden voorbeeldsituaties beschreven waarin tijdelijke en permanente klonen kunnen worden gebruikt.

### <a name="item-level-recovery-with-a-transient-clone"></a>Herstel op objectniveau met een tijdelijke kloon
U moet een eenjarig Microsoft PowerPoint-presentatiebestand herstellen. Uw IT-beheerder identificeert de specifieke back-up van die tijd en filtert vervolgens het volume. De beheerder kloont vervolgens het volume, lokaliseert het bestand dat u zoekt en verstrekt het aan u. In dit scenario wordt een tijdelijke kloon gebruikt.

### <a name="testing-in-the-production-environment-with-a-permanent-clone"></a>Testen in de productieomgeving met een permanente kloon
U moet een testfout in de productieomgeving verifiëren. U maakt een kloon van het volume in de productieomgeving en maakt vervolgens een cloudmomentopname van deze kloon om een onafhankelijk gekloond volume te maken. In dit scenario wordt een permanente kloon gebruikt.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het [herstellen van een StorSimple-volume vanuit een back-upset.](storsimple-8000-restore-from-backup-set-u2.md)
* Meer informatie over het [gebruik van de StorSimple Device Manager-service om uw StorSimple-apparaat te beheren.](storsimple-8000-manager-service-administration.md)

