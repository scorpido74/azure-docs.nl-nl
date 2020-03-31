---
title: Vacatures voor StorSimple 8000-serie weergeven en beheren | Microsoft Documenten
description: Beschrijft het blade van De StorSimple Device Manager-service Taken en hoe u het gebruiken om recente, huidige en geplande back-uptaken bij te houden.
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
ms.openlocfilehash: 462f8dafdffa7ee01e6ccf7945a1abfdff90db42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254870"
---
# <a name="use-the-storsimple-device-manager-service-to-view-and-manage-jobs-update-3-and-later"></a>De StorSimple Device Manager-service gebruiken om taken weer te geven en te beheren (update 3 en hoger)

## <a name="overview"></a>Overzicht
Het **blade Jobs** biedt één centrale portal voor het bekijken en beheren van taken die zijn gestart op apparaten die zijn verbonden met uw StorSimple Device Manager-service. U geplande, lopende, voltooide, geannuleerde en mislukte taken voor meerdere apparaten weergeven. De resultaten worden in een tabelvormige indeling weergegeven.

![Het blad van de Banen](./media/storsimple-8000-manage-jobs-u2/jobs1.png)

U snel de vacatures vinden waarin u geïnteresseerd bent door te filteren op velden zoals:

* **Status** : taken kunnen worden uitgevoerd, geslaagd, geannuleerd, mislukt, geannuleerd of geslaagd met fouten.
* **Tijdbereik** : taken kunnen worden gefilterd op basis van de datum en het tijdsbereik. De bereiken zijn afgelopen 1 uur, afgelopen 24 uur, afgelopen 7 dagen, afgelopen 30 dagen, afgelopen jaar of aangepaste datum.
* **Type** : Het taaktype kan een geplande back-up zijn, handmatige back-ups, back-ups herstellen, kloonvolume, volumecontainers mislukken, lokaal vastgezet volume maken, volume wijzigen, updates installeren, ondersteuningslogboeken verzamelen en cloudtoestel maken.
* **Apparaten** : er worden taken gestart op een bepaald apparaat dat is verbonden met uw service.
  
De gefilterde taken worden vervolgens getabuleerd op basis van de volgende kenmerken:
  
* **Naam** - geplande back-up, handmatige back-up, back-up herstellen, kloonvolume, falen over volumecontainers, lokaal vastgemaakt volume maken, volume wijzigen, updates installeren, ondersteuningslogboeken verzamelen of cloudappliance maken.
* **Status** : uitgevoerd, voltooid, geannuleerd, mislukt, geannuleerd of voltooid met fouten.
* **Entiteit** : de taken kunnen worden gekoppeld aan een volume, een back-upbeleid of een apparaat. Een kloontaak is bijvoorbeeld gekoppeld aan een volume, terwijl een geplande back-uptaak is gekoppeld aan een back-upbeleid. Er wordt een apparaattaak gemaakt als gevolg van een herstel (Disaster Recovery) of een herstelbewerking.
* **Apparaat** : de naam van het apparaat waarop de taak is gestart.
* **Begonnen op** – De tijd dat de taak werd gestart.
* **Duur** – De tijd die nodig is om de taak te voltooien.

De lijst met taken wordt elke 30 seconden vernieuwd.

U de volgende taakgerelateerde acties uitvoeren op deze pagina:

* Taakdetails weergeven
* Een taak annuleren

## <a name="view-job-details"></a>Taakdetails weergeven
Voer de volgende stappen uit om de details van een taak weer te geven.

#### <a name="to-view-job-details"></a>Taakgegevens bekijken
1. Ga naar uw StorSimple Device Manager-service en klik op **Vacatures**.

2. Geef in het blad **Vacatures** de taak(en) weer waarin u geïnteresseerd bent door een query uit te voeren met de juiste filters. U zoeken naar voltooide, lopende of geannuleerde taken.

    ![Het blad van de baan](./media/storsimple-8000-manage-jobs-u2/jobs1.png)

2. Selecteer en klik op een taak.

    ![Het blad van de baan](./media/storsimple-8000-manage-jobs-u2/jobs3.png)

3. In het blad taakdetails u de status, details, tijdstatistieken en gegevensstatistieken bekijken.
   
    ![Taakdetails](./media/storsimple-8000-manage-jobs-u2/jobs4.png)

## <a name="cancel-a-job"></a>Een taak annuleren
Voer de volgende stappen uit om een lopende taak te annuleren.

> [!NOTE]
> Sommige taken, zoals het wijzigen van een volume om het volumetype te wijzigen of een volume uit te breiden, kunnen niet worden geannuleerd.


### <a name="to-cancel-a-job"></a>Een taak annuleren
1. Geef op de pagina **Vacatures** de lopende taak(en) weer die u wilt annuleren door een query met de juiste filters uit te voeren. Selecteer de taak.

2. Klik met de rechtermuisknop op de geselecteerde taak om het contextmenu aan te roepen en klik op **Annuleren**.

    ![Taakdetails](./media/storsimple-8000-manage-jobs-u2/jobs2.png)

3. Klik op **Ja** als u om bevestiging wordt gevraagd. Deze baan is nu geannuleerd.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het [beheren van uw StorSimple-back-upbeleid.](storsimple-8000-manage-backup-policies-u2.md)
* Meer informatie over het [gebruik van de StorSimple Device Manager-service om uw StorSimple-apparaat te beheren.](storsimple-8000-manager-service-administration.md)

