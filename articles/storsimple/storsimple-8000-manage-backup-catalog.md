---
title: Uw StorSimple-back-upcatalogus beheren | Microsoft Documenten
description: Hier wordt uitgelegd hoe u de cataloguspagina van de StorSimple Device Manager-serviceback-upcatalogus gebruikt om back-upsets weer te geven, te selecteren en te verwijderen.
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
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: 07d9e03f1631ebce88a7a7c2e33be62f21dda522
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60319620"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-your-backup-catalog"></a>De StorSimple Device Manager-service gebruiken om uw back-upcatalogus te beheren
## <a name="overview"></a>Overzicht
Het blade van de StorSimple Device **Manager-service Back-upcatalogus** toont alle back-upsets die worden gemaakt wanneer handmatige of geplande back-ups worden gemaakt. U deze pagina gebruiken om alle back-ups voor een back-upbeleid of een volume weer te geven, back-ups te selecteren of te verwijderen of een back-up te gebruiken om een volume te herstellen of te klonen.

In deze zelfstudie wordt uitgelegd hoe u een back-upset weergeven, selecteren en verwijderen. Ga voor meer informatie over het herstellen van uw apparaat van back-up, naar [Uw apparaat herstellen vanaf een back-upset.](storsimple-8000-restore-from-backup-set-u2.md) Ga naar [Een StorSimple-volume](storsimple-8000-clone-volume-u2.md)klonen om te leren hoe u een volume klonen.

![Back-upcatalogus](./media/storsimple-8000-manage-backup-catalog/bucatalog.png) 

Het blade **back-upcatalogus** biedt een query om de selectie van de back-upset te verkleinen. U de opgehaalde back-upsets filteren op basis van de volgende parameters:

* **Apparaat** – Het apparaat waarop de back-upset is gemaakt.
* **Back-upbeleid of -volume:** het back-upbeleid of het back-upvolume dat aan deze back-upset is gekoppeld.
* **Van en naar** – De datum en het tijdsbereik waarop de back-upset is gemaakt.

De gefilterde back-upsets worden vervolgens getabuleerd op basis van de volgende kenmerken:

* **Naam** : de naam van het back-upbeleid of het volume dat is gekoppeld aan de back-upset.
* **Grootte** : de werkelijke grootte van de back-upset.
* **Gemaakt op** - de datum en tijd waarop de back-ups zijn gemaakt. 
* **Type** : Back-upsets kunnen lokale momentopnamen of cloudmomentopnamen zijn. Een lokale momentopname is een back-up van al uw volumegegevens die lokaal op het apparaat zijn opgeslagen, terwijl een cloudmomentopname verwijst naar de back-up van volumegegevens die zich in de cloud bevinden. Lokale momentopnamen bieden snellere toegang, terwijl cloudmomentopnamen worden gekozen voor gegevenstolerantie.
* **Geïnitieerd door** – De back-ups kunnen automatisch worden gestart door een planning of handmatig door een gebruiker. U een back-upbeleid gebruiken om back-ups te plannen. U ook de optie **Back-up maken** maken om een handmatige back-up te maken.

## <a name="list-backup-sets-for-a-backup-policy"></a>Back-upsets voor een back-upbeleid weergeven
Voer de volgende stappen uit om alle back-ups voor een back-upbeleid weer te geven.

#### <a name="to-list-backup-sets"></a>Back-upsets weergeven
1. Ga naar uw StorSimple Device Manager-service en klik op **Back-upcatalogus**.

2. Filter de selecties als volgt:
   
   1. Geef het tijdsbereik op.
   2. Selecteer het juiste apparaat.
   3. Filter **op back-upbeleid** om de bijbehorende back-ups weer te geven.
   3. Kies in de vervolgkeuzelijst back-upbeleid de optie **Alles** om alle back-ups op het geselecteerde apparaat weer te geven.
   4. Klik **op Toepassen** om deze query uit te voeren.
      
      De back-ups die zijn gekoppeld aan het geselecteerde back-upbeleid moeten worden weergegeven in de lijst met back-upsets.

      ![Ga naar back-upcatalogus](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

## <a name="select-a-backup-set"></a>Een back-upset selecteren
Voer de volgende stappen uit om een back-upset voor een volume- of back-upbeleid te selecteren.

#### <a name="to-select-a-backup-set"></a>Een back-upset selecteren
1. Ga naar uw StorSimple Device Manager-service en klik op **Back-upcatalogus**.
2. Filter de selecties als volgt:
   
   1. Geef het tijdsbereik op. 
   2. Selecteer het juiste apparaat. 
   3. Filter op volume- of back-upbeleid voor de back-up die u wilt selecteren.
   4. Klik **op Toepassen** om deze query uit te voeren.
      
      De back-ups die zijn gekoppeld aan het geselecteerde volume- of back-upbeleid moeten worden weergegeven in de lijst met back-upsets.

      ![Ga naar back-upcatalogus](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

3. Selecteer en vouw een back-upset uit. U nu de back-upsets zien die zijn opgesplitst in de volumes die deze bevatten. De opties **Voor herstel** en **verwijderen** zijn beschikbaar via het contextmenu (met de rechtermuisknop) voor de back-upset. U een van deze acties uitvoeren op de back-upset die u hebt geselecteerd.

    ![Ga naar back-upcatalogus](./media/storsimple-8000-manage-backup-catalog/bucatalog2.png)

## <a name="delete-a-backup-set"></a>Een back-upset verwijderen
Verwijder een back-up wanneer u de bijbehorende gegevens niet langer wilt bewaren. Voer de volgende stappen uit om een back-upset te verwijderen.

#### <a name="to-delete-a-backup-set"></a>Een back-upset verwijderen
 Ga naar uw StorSimple Device Manager-service en klik op **Back-upcatalogus**.
1. Filter de selecties als volgt:
   
   1. Geef het tijdsbereik op. 
   2. Selecteer het juiste apparaat. 
   3. Filter op volume- of back-upbeleid voor de back-up die u wilt selecteren.
   4. Klik **op Toepassen** om deze query uit te voeren.
      
      De back-ups die zijn gekoppeld aan het geselecteerde volume- of back-upbeleid moeten worden weergegeven in de lijst met back-upsets.

      ![Ga naar back-upcatalogus](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

1. Selecteer en vouw een back-upset uit. U nu de back-upsets zien die zijn opgesplitst in de volumes die deze bevatten. De opties **Voor herstel** en **verwijderen** zijn beschikbaar via het contextmenu (met de rechtermuisknop) voor de back-upset. Klik met de rechtermuisknop op de geselecteerde back-upset en selecteer in het contextmenu **Verwijderen**.

    ![Ga naar back-upcatalogus](./media/storsimple-8000-manage-backup-catalog/bucatalog3.png)

1. Controleer de weergegeven informatie en klik op **Verwijderen**wanneer u om bevestiging wordt gevraagd. De geselecteerde back-up wordt definitief verwijderd.

    ![Ga naar back-upcatalogus](./media/storsimple-8000-manage-backup-catalog/bucatalog4.png)  

1. U wordt op de hoogte gesteld wanneer de verwijdering aan de gang is en wanneer deze is voltooid. Nadat de verwijdering is gedaan, vernieuwt u de query op deze pagina. De verwijderde back-upset wordt niet meer weergegeven in de lijst met back-upsets.

    ![Ga naar back-upcatalogus](./media/storsimple-8000-manage-backup-catalog/bucatalog7.png)

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het [gebruik van de back-upcatalogus om uw apparaat te herstellen van een back-upset.](storsimple-8000-restore-from-backup-set-u2.md)
* Meer informatie over het [gebruik van de StorSimple Device Manager-service om uw StorSimple-apparaat te beheren.](storsimple-8000-manager-service-administration.md)

