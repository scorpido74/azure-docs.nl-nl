---
title: Apparaatoverzicht gebruiken in de StorSimple Device Manager-service
description: Beschrijft het overzicht van de StorSimple Device Manager-serviceapparaat en hoe u deze gebruiken om opslagstatistieken en verbonden initiators weer te geven en het serienummer en IQN te vinden.
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
ms.date: 07/03/2017
ms.author: alkohli
ms.openlocfilehash: 7801cdc6573799330e5173db82dead8d4f2cd83f
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398255"
---
# <a name="use-the-device-summary-in-storsimple-device-manager-service"></a>Het apparaatoverzicht gebruiken in de StorSimple Device Manager-service

## <a name="overview"></a>Overzicht
Het storSimple-apparaatoverzichtsblad geeft u een overzicht van informatie voor een specifiek StorSimple-apparaat, in tegenstelling tot het serviceoverzichtsblad, dat u informatie geeft over alle apparaten die zijn opgenomen in uw Microsoft Azure StorSimple-oplossing.

Het apparaatoverzichtsblad biedt een overzicht van een Apparaat uit de StorSimple 8000-serie dat is geregistreerd bij een bepaalde StorSimple Device Manager, waarbij de apparaatproblemen worden benadrukt die de aandacht van een systeembeheerder nodig hebben. Deze zelfstudie introduceert het apparaatoverzichtsblad, legt de inhoud en functie uit en beschrijft de taken die u vanaf dit blad uitvoeren.

Op het overzichtsblad van het apparaat worden de volgende gegevens weergegeven:

![Apparaatoverzichtsblad](./media/storsimple-8000-device-dashboard/device-summary1.png)

## <a name="management-command-bar"></a>Opdrachtbalk beheer

In het StorSimple-apparaatblad ziet u de opties voor het beheren van uw StorSimple-apparaat. U ziet de management commando's over de bovenkant van het blad en aan de linkerkant. Gebruik deze opties om aandelen of volumes toe te voegen of bij te werken of te mislukken via uw apparaat.

![Opdrachtbalk beheer](./media/storsimple-8000-device-dashboard/device-summary2.png)

## <a name="essentials"></a>Essentials

Het essentiële gebied vangt een aantal van de belangrijke eigenschappen, zoals de status, het model, doel IQN, en de software-versie. 

![Apparaatbenodigdheden](./media/storsimple-8000-device-dashboard/device-summary3.png)

## <a name="monitoring"></a>Bewaking

* De tegel **Waarschuwingen** biedt een momentopname van alle actieve waarschuwingen voor uw apparaat, gegroepeerd op waarschuwingsernst.

    ![Waarschuwingstegel](./media/storsimple-8000-device-dashboard/device-summary4.png)

    Klik op de tegel om het zwaard **Waarschuwingen** te openen en klik vervolgens op een afzonderlijke waarschuwing om aanvullende details over die waarschuwing weer te geven, inclusief aanbevolen acties. U de waarschuwing ook wissen als het probleem is opgelost.

    ![Klik op waarschuwingstegel](./media/storsimple-8000-device-dashboard/device-summary10.png)

* De tegel **Status en status** biedt inzicht in de status van de hardwarecomponent voor een apparaat, inclusief de apparaatstatus. De status van het apparaat kan offline, online, gedeactiveerd of klaar zijn om in te stellen.

    ![Tegel Status en gezondheid](./media/storsimple-8000-device-dashboard/device-summary5.png)

* De tegel **Volumes** geeft een overzicht van het aantal volumes in uw apparaat gegroepeerd op status.

    ![Tegel Volumes](./media/storsimple-8000-device-dashboard/device-summary6.png)

    Klik op de tegel om het lijstblad **volumes** te openen en klik vervolgens op een afzonderlijk volume om de eigenschappen ervan weer te geven of te wijzigen.
    
    ![Klik op de tegel Volumes](./media/storsimple-8000-device-dashboard/device-summary9.png)
    
    Zie voor meer informatie hoe u [volumes beheert](storsimple-8000-manage-volumes-u2.md).

* In de grafiek **Gebruik** u de primaire opslag weergeven die op uw apparaat wordt gebruikt en de cloudopslag die in de afgelopen zeven dagen is verbruikt, de standaardperiode.

     ![Gebruikstegel](./media/storsimple-8000-device-dashboard/device-summary7.png)
    
     Als u een andere tijdschaal wilt kiezen, gebruikt u de optie **Bewerken** in de rechterbovenhoek van de grafiek.

     ![Gebruiksdiagram bewerken](./media/storsimple-8000-device-dashboard/device-summary12.png)

     In deze grafiek u statistieken weergeven voor de totale primaire opslag (de hoeveelheid gegevens die door hosts naar uw apparaat is geschreven) en de totale cloudopslag die uw apparaat gedurende een bepaalde periode verbruikt.
  
     In deze context verwijst *primaire opslag* naar de totale hoeveelheid gegevens die door de host is geschreven en kan deze worden opgesplitst in volumetype: *primaire gelaagde opslag* omvat zowel lokaal opgeslagen gegevens als gegevens die zijn gelaagd naar de cloud. *Primaire lokaal vastgemaakte opslag* bevat alleen gegevens die lokaal zijn opgeslagen. *Cloudopslag*daarentegen is een meting van de totale hoeveelheid gegevens die in de cloud is opgeslagen. Deze opslag omvat gelaagde gegevens en back-ups. De gegevens die in de cloud zijn opgeslagen, worden gededuplicateen en gecomprimeerd, terwijl primaire opslag de hoeveelheid opslagruimte aangeeft die wordt gebruikt voordat de gegevens worden gededupliceerd en gecomprimeerd. (U deze twee getallen vergelijken om een idee te krijgen van de compressiesnelheid.) Voor zowel primaire als cloudopslag zijn de weergegeven bedragen gebaseerd op de trackingfrequentie die u configureert. Als u bijvoorbeeld een frequentie van één week kiest, worden in de grafiek gegevens weergegeven voor elke dag in de vorige week.

     Als u wilt zien hoeveel cloudopslag in de loop van de tijd wordt verbruikt, selecteert u de optie **GEBRUIKTE cloudopslagopslag.** Als u de totale opslag wilt zien die door de host is geschreven, selecteert u de **gebruikte primaire trapstenopslag** en de gebruikte primaire locatieopslag voor **lokale opslag.** 
     Zie [De StorSimple Device Manager-service gebruiken om uw StorSimple-apparaat te controleren](storsimple-monitor-device.md)voor meer informatie.


* De tegel **Capaciteit** geeft de primaire opslag weer die is ingericht en over het apparaat blijft ten opzichte van de totale opslagruimte die beschikbaar is voor hetzelfde apparaat. **Provisioned** verwijst naar de hoeveelheid opslagruimte die is voorbereid en toegewezen voor gebruik, **Remaining** verwijst naar de resterende capaciteit die kan worden ingericht over dit apparaat. 

    ![Gebruikstegel](./media/storsimple-8000-device-dashboard/device-summary8.png)

    Klik op deze tegel om te bekijken hoe de capaciteit is ingericht voor gelaagde en lokaal vastgemaakte volumes. De **resterende gelaagde** capaciteit is de beschikbare capaciteit die kan worden ingericht inclusief cloud, terwijl **de resterende lokale** is de capaciteit die overblijft op de schijven die zijn aangesloten op dit apparaat.

    ![Klik op gebruiksgrafiek](./media/storsimple-8000-device-dashboard/device-summary13.png)


## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het [StorSimple-serviceoverzichtsblad](storsimple-8000-service-dashboard.md).
* Meer informatie over [het gebruik van de StorSimple Device Manager-service om uw StorSimple-apparaat te beheren.](storsimple-8000-manager-service-administration.md)

