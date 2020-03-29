---
title: StorSimple Virtual Array-apparaatoverzichtsblad | Microsoft Documenten
description: Beschrijft het apparaatoverzichtsblad voor StorSimple Device Manager en legt uit hoe u het gebruiken om de status van uw StorSimple Virtual Array te controleren.
services: storsimple
documentationcenter: ''
author: manuaery
manager: syadav
editor: ''
ms.assetid: a13c1ea7-6428-4234-84a6-0ebf51670a85
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/29/2016
ms.author: manuaery
ms.openlocfilehash: 9edc0b552f5c2f38e646bc4b44dd8df5c16b0457
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61408479"
---
# <a name="use-the-device-summary-blade-for-storsimple-device-manager-connected-to-storsimple-virtual-array"></a>Het apparaatoverzichtsblad gebruiken voor StorSimple Device Manager dat is aangesloten op StorSimple Virtual Array

## <a name="overview"></a>Overzicht

Het StorSimple Device Manager-apparaatblad biedt een overzicht van een StorSimple Virtual Array die is geregistreerd bij een bepaalde StorSimple Device Manager, waarbij de apparaatproblemen worden benadrukt die de aandacht van een systeembeheerder nodig hebben. Deze zelfstudie introduceert het apparaatoverzichtsblad, legt de inhoud en functie uit en beschrijft de taken die u vanaf dit blad uitvoeren.

Op het overzichtsblad van het apparaat worden de volgende gegevens weergegeven:

![Apparaatdashboard](./media/storsimple-virtual-array-device-summary/device-blade.png)



## <a name="management"></a>Beheer

In het StorSimple-apparaatblad ziet u de opties voor het beheren van uw StorSimple-apparaat. U ziet de management commando's over de bovenkant van het blad en aan de linkerkant. Gebruik deze opties om aandelen of volumes toe te voegen of bij te werken of te mislukken via uw virtuele array.

Het gebied essentials vangt een aantal van de belangrijke eigenschappen, zoals de status, het model, de softwareversie en een link naar de **web-gebruikersinterface** van de array. Als u zich op een intern netwerk bevindt, u de [lokale webgebruikersinterface](storsimple-ova-web-ui-admin.md) rechtstreeks starten om uw virtuele array te beheren.

![Apparaatbenodigdheden](./media/storsimple-virtual-array-device-summary/device-essentials.png)

## <a name="storsimple-device-summary"></a>StorSimple-apparaatoverzicht

* De tegel **Waarschuwingen** biedt een momentopname van alle actieve waarschuwingen voor uw virtuele array, gegroepeerd op waarschuwingsernst. Klik op de tegel om het zwaard **Waarschuwingen** te openen en klik vervolgens op een afzonderlijke waarschuwing om aanvullende details over die waarschuwing weer te geven, inclusief aanbevolen acties. U de waarschuwing ook wissen als het probleem is opgelost.

* De tegel **Capaciteit** geeft de primaire opslag weer die is ingericht en over het virtuele apparaat blijft ten opzichte van de totale opslagruimte die beschikbaar is voor hetzelfde. **Provisioned** verwijst naar de hoeveelheid opslagruimte die is voorbereid en toegewezen voor gebruik, **Remaining** verwijst naar de resterende capaciteit die kan worden ingericht over dit apparaat. De **resterende gelaagde** capaciteit is de beschikbare capaciteit die kan worden ingericht, inclusief cloud, terwijl **de resterende lokale** is de capaciteit die overblijft op de schijven die zijn gekoppeld aan deze virtuele array.

* In de grafiek **Gebruik** u de primaire opslag weergeven die wordt gebruikt voor uw virtuele array en de cloudopslag die in de afgelopen zeven dagen is verbruikt, de standaardperiode. Gebruik de optie **Bewerken** in de rechterbovenhoek van de grafiek om een andere tijdschaal te kiezen.

* De tegel **Aandelen** of **Volumes** geeft een overzicht van het aantal aandelen of volumes in uw apparaat gegroepeerd op status. Klik op de tegel om het lijstblad **Aandelen** of **Volumes** te openen en klik vervolgens op een afzonderlijk aandeel of volume om de eigenschappen ervan weer te geven of te wijzigen. Zie voor meer informatie hoe [u aandelen beheert](storsimple-virtual-array-manage-shares.md) of volumes [beheert](storsimple-virtual-array-manage-volumes.md).

## <a name="next-steps"></a>Volgende stappen
Leer hoe u het volgende doet:
- [Aandelen beheren op een StorSimple Virtual Array](storsimple-virtual-array-manage-shares.md)
    
- [Volumes beheren op een StorSimple Virtual Array](storsimple-virtual-array-manage-volumes.md)

