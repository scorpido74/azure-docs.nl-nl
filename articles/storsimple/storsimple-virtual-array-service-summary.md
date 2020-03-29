---
title: StorSimple Virtual Array-serviceoverzichtsblad | Microsoft Documenten
description: Beschrijft het serviceoverzichtsblad voor StorSimple Device Manager en legt uit hoe u het gebruiken om de status van uw StorSimple Virtual Array te controleren.
services: storsimple
documentationcenter: ''
author: manuaery
manager: syadav
editor: ''
ms.assetid: 8a2b9a84-b0e6-48b9-b366-d16f004241a5
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: manuaery
ms.openlocfilehash: 9c05bddaeb3c34400db1ec75c624ef00a85d9444
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "64720730"
---
# <a name="use-the-service-summary-blade-for-storsimple-device-manager-connected-to-storsimple-virtual-array"></a>Gebruik het onderhoudsoverzichtsblad voor StorSimple Device Manager dat is aangesloten op StorSimple Virtual Array
## <a name="overview"></a>Overzicht
Het serviceoverzichtsblad voor de StorSimple Device Manager biedt een overzicht van de StorSimple Virtual Arrays (ook bekend als StorSimple on-premises virtuele apparaten of virtuele apparaten) die zijn aangesloten op uw service, met de nadruk op apparaten die een systeem nodig hebben de aandacht van de beheerder. Deze zelfstudie introduceert het onderhoudsoverzichtsblad, legt de inhoud en functie uit en beschrijft de taken die u vanuit dit blad uitvoeren.

![Servicedashboard](./media/storsimple-virtual-array-service-summary/service-blade.png)

## <a name="management-commands-and-essentials"></a>Managementopdrachten en benodigdheden
In het StorSimple-overzichtsblad ziet u de opties voor het beheren van uw StorSimple Device Manager-service en de virtuele arrays die zijn geregistreerd bij deze service. U ziet de management commando's over de bovenkant van het blad en aan de linkerkant.

Gebruik deze opties om verschillende bewerkingen uit te voeren, zoals het toevoegen van shares of volumes, of het bewaken van de verschillende taken die op de virtuele arrays worden uitgevoerd.

Het gebied essentials legt een aantal belangrijke eigenschappen vast, zoals de resourcegroep, locatie en abonnement waarin uw StorSimple Device Manager is gemaakt.

## <a name="storsimple-device-manager-service-summary"></a>StorSimple Device Manager-serviceoverzicht
* De tegel **Waarschuwingen** biedt een momentopname van alle actieve waarschuwingen op alle virtuele apparaten, gegroepeerd op waarschuwingsernst. Als u op de tegel klikt, wordt het **zwaard Waarschuwingen** geopend, waar u op een afzonderlijke waarschuwing klikken om aanvullende details over die waarschuwing weer te geven, inclusief aanbevolen acties. U de waarschuwing ook wissen als het probleem is opgelost.
* De **tegeldisplays capaciteit** toont de primaire opslag die is ingericht en overblijft op alle virtuele apparaten ten opzichte van de totale opslag die beschikbaar is voor alle virtuele apparaten. **Provisioned** verwijst naar de hoeveelheid opslagruimte die is voorbereid en toegewezen voor gebruik, **Remaining** verwijst naar de resterende capaciteit die kan worden ingericht op alle virtuele apparaten. De **resterende gelaagde** capaciteit is de beschikbare capaciteit die kan worden ingericht, inclusief cloud, terwijl **de resterende lokale** is de capaciteit die overblijft op de schijven die zijn gekoppeld aan de virtuele arrays.
* In de grafiek **Gebruik** ziet u de relevante statistieken voor uw virtuele apparaten. U de primaire opslag bekijken die wordt gebruikt op alle virtuele apparaten, evenals de cloudopslag die in de afgelopen 7 dagen, de standaardperiode, is verbruikt door virtuele apparaten. Gebruik de optie **Bewerken** in de rechterbovenhoek van de grafiek om een andere tijdschaal te kiezen.
* De tegel **Apparaten** geeft een overzicht van het aantal virtuele arrays in uw StorSimple-apparaatbeheer gegroepeerd op apparaatstatus. Klik op deze tegel om het lijstblad **apparaten** te openen en klik vervolgens op een afzonderlijk apparaat om in te zoomen op het apparaatoverzicht dat specifiek is voor het apparaat. U ook apparaatspecifieke acties uitvoeren vanaf een bepaald apparaatoverzichtsblad. Ga voor meer informatie over het apparaatoverzichtsblad naar [het overzichtsblad van het apparaat](storsimple-virtual-array-device-summary.md).

## <a name="view-the-activity-logs"></a>De activiteitslogboeken weergeven
Als u de verschillende bewerkingen wilt bekijken die binnen uw StorSimple-apparaatbeheer worden uitgevoerd, klikt u op de koppeling **Activiteitslogboeken** aan de linkerkant van uw StorSimple-serviceoverzichtsblad. Dit brengt u naar het **blade activiteitslogboeken,** waar u een overzicht zien van de recente uitgevoerde bewerkingen.

![Activiteitenlogboeken](./media/storsimple-virtual-array-service-summary/activity-log.png)

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het [gebruik van de lokale web-gebruikersinterface om uw StorSimple Virtual Array te beheren.](storsimple-ova-web-ui-admin.md)

