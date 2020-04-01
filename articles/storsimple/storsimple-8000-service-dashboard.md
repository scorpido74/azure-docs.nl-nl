---
title: Serviceoverzichtsblad gebruiken voor apparaat uit de StorSimple 8000-serie
description: Beschrijft het StorSimple service summary blade en legt uit hoe je het gebruiken om de gezondheid van uw StorSimple-oplossing te controleren.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2017
ms.author: alkohli
ms.openlocfilehash: 14c37227a7e8598ee6f8f3adc62acd69dd274475
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2020
ms.locfileid: "80396496"
---
# <a name="use-the-service-summary-blade-for-storsimple-8000-series-device"></a>Gebruik het serviceoverzichtsblad voor het apparaat uit de StorSimple 8000-serie

## <a name="overview"></a>Overzicht

Het overzichtsblad van de StorSimple Device Manager-service biedt een overzicht van alle apparaten die zijn aangesloten op de StorSimple Device Manager-service, waarbij de apparaten worden gemarkeerd die de aandacht van een systeembeheerder nodig hebben. In deze zelfstudie wordt het onderhoudsoverzichtsblad geïntroduceerd, wordt de inhoud en functie van het dashboard uitgelegd en worden de taken beschreven die u vanaf deze pagina uitvoeren.

![Serviceoverzicht](./media/storsimple-8000-service-dashboard/service-summary1.png)


## <a name="management-commands"></a>Opdrachten voor beheer

In het StorSimple-serviceoverzichtsblad ziet u de opties voor het beheren van uw StorSimple Device Manager-service en de apparaten uit de StorSimple 8000-serie die op deze service zijn geregistreerd. U ziet de management commando's over de bovenkant van het blad en aan de linkerkant.

![Opdrachtbalk](./media/storsimple-8000-service-dashboard/service-summary2.png)

Gebruik deze opties om verschillende bewerkingen uit te voeren, zoals het toevoegen van shares of volumes, of het bewaken van de verschillende taken die op de StorSimple-apparaten worden uitgevoerd.


## <a name="essentials"></a>Essentials

Het gebied essentials legt een aantal belangrijke eigenschappen vast, zoals de resourcegroep, locatie en abonnement waarin uw StorSimple Device Manager is gemaakt.

![Essentials](./media/storsimple-8000-service-dashboard/service-summary3.png)

## <a name="storsimple-device-manager-service-summary"></a>StorSimple Device Manager-serviceoverzicht

* De tegel **Waarschuwingen** biedt een momentopname van alle actieve waarschuwingen op alle apparaten, gegroepeerd op waarschuwingsernst.

    ![Tegel Waarschuwingen](./media/storsimple-8000-service-dashboard/service-summary4.png)

    Als u op de tegel klikt, wordt het **zwaard Waarschuwingen** geopend, waar u op een afzonderlijke waarschuwing klikken om aanvullende details over die waarschuwing weer te geven, inclusief aanbevolen acties. U de waarschuwing ook wissen als het probleem is opgelost.

    ![Klik op de tegel Waarschuwingen](./media/storsimple-8000-service-dashboard/service-summary8.png)

* De **tegeldisplays capaciteit** toont de primaire opslag die is ingericht en overblijft op alle apparaten ten opzichte van de totale opslag die beschikbaar is op alle apparaten. **Provisioned** verwijst naar de hoeveelheid opslagruimte die is voorbereid en toegewezen voor gebruik, **Remaining** verwijst naar de resterende capaciteit die kan worden ingericht op alle apparaten.

    ![Capaciteitstegel](./media/storsimple-8000-service-dashboard/service-summary6.png)

    De **resterende gelaagde** capaciteit is de beschikbare capaciteit die kan worden ingericht inclusief cloud, terwijl de **resterende lokale** is de capaciteit die overblijft op de schijven die zijn aangesloten op de StorSimple 8000-serie apparaten.


* In de **grafiek Gebruik** u de relevante statistieken voor uw apparaten bekijken. U de primaire opslag bekijken die op alle apparaten wordt gebruikt en de cloudopslag die in de afgelopen zeven dagen, de standaardperiode, is verbruikt door apparaten. 

    ![Gebruikstegel](./media/storsimple-8000-service-dashboard/service-summary7.png) 

    Als u een andere tijdschaal wilt kiezen, gebruikt u de optie **Bewerken** in de rechterbovenhoek van de grafiek.

     ![Klik op gebruikstegel](./media/storsimple-8000-service-dashboard/service-summary10.png)

     ![Grafiekgegevens exporteren](./media/storsimple-8000-service-dashboard/service-summary11.png)

* De tegel **Apparaten** geeft een overzicht van het aantal Apparaten uit de StorSimple 8000-serie in uw StorSimple-apparaatbeheer gegroepeerd op apparaatstatus. 

    ![Tegel Apparaten](./media/storsimple-8000-service-dashboard/service-summary5.png)

    Klik op deze tegel om het lijstblad **apparaten** te openen en klik vervolgens op een afzonderlijk apparaat om in te zoomen op het apparaatoverzicht dat specifiek is voor het apparaat. U ook apparaatspecifieke acties uitvoeren vanaf een bepaald apparaatoverzichtsblad. Ga voor meer informatie over het apparaatoverzichtsblad naar [het overzichtsblad van het apparaat](storsimple-8000-device-dashboard.md).

    ![Klik op de tegel Apparaten](./media/storsimple-8000-service-dashboard/service-summary9.png)

## <a name="view-the-activity-logs"></a>De activiteitslogboeken weergeven

Als u de verschillende bewerkingen wilt bekijken die binnen uw StorSimple-apparaatbeheer worden uitgevoerd, klikt u op de koppeling **Activiteitslogboeken** aan de linkerkant van uw StorSimple-serviceoverzichtsblad. Dit brengt u naar het **blade activiteitslogboeken,** waar u een overzicht zien van de recente uitgevoerde bewerkingen.

![Activiteitenlogboeken](./media/storsimple-8000-service-dashboard/activity-logs1.png)
## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [gebruik van de StorSimple Device Manager-service om uw StorSimple-apparaat te beheren.](storsimple-8000-manager-service-administration.md)

