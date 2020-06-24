---
title: Routerings methode voor prestatie verkeer configureren met behulp van Azure Traffic Manager | Microsoft Docs
description: In dit artikel wordt uitgelegd hoe u Traffic Manager configureert om verkeer te routeren naar het eind punt met de laagste latentie
services: traffic-manager
manager: twooley
documentationcenter: ''
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: rohink
ms.openlocfilehash: 5299c72d96fd7453d7df352ca842b4ede35f41fd
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/11/2020
ms.locfileid: "84707664"
---
# <a name="configure-the-performance-traffic-routing-method"></a>De routerings methode voor prestatie verkeer configureren

Met de methode voor het routerings verkeer van prestaties kunt u verkeer naar het eind punt sturen met de laagste latentie van het netwerk van de client. Normaal gesp roken is het Data Center met de laagste latentie het dichtst bij de geografische afstand. Met deze routerings methode voor verkeer kan geen realtime-wijzigingen in de netwerk configuratie of het laden worden verwerkt.

##  <a name="to-configure-performance-routing-method"></a>Routerings methode voor prestaties configureren

1. Meld u vanuit een browser aan bij [Azure Portal](https://portal.azure.com). Als u nog geen account hebt, kunt u zich registreren voor een [gratis proefversie van één maand](https://azure.microsoft.com/free/). 
2. Zoek in de zoek balk van de portal naar de **Traffic Manager-profielen** en klik vervolgens op de naam van het profiel waarvoor u de routerings methode wilt configureren.
3. Controleer op de Blade **Traffic Manager profiel** of de Cloud Services en websites die u wilt gebruiken in uw configuratie aanwezig zijn.
4. Klik in de sectie **instellingen** op **configuratie**en voer op de Blade **configuratie** de volgende stappen uit:
    1. Voor de **instellingen voor verkeers routerings methode**voor **routerings methode** selecteert u **prestaties**.
    2. Stel de **instellingen voor de eindpunt monitor** identiek voor alle eind punten in dit profiel als volgt in:
        1. Selecteer het juiste **protocol**en geef het **poort** nummer op. 
        2. Typ **Path** een slash voor het pad */* . Als u eind punten wilt controleren, moet u een pad en bestands naam opgeven. Een slash (/) is een geldige vermelding voor het relatieve pad en impliceert dat het bestand zich in de hoofdmap (standaard) bevindt.
        3. Klik boven aan de pagina op **Opslaan**.
5.  Test de wijzigingen in uw configuratie als volgt:
    1.  Zoek in de zoek balk van de portal naar de naam van het Traffic Manager profiel en klik op het profiel Traffic Manager in de resultaten die worden weer gegeven.
    2.  Klik op de Blade **Traffic Manager** profiel op **overzicht**.
    3.  Op de Blade **Traffic Manager profiel** wordt de DNS-naam van het zojuist gemaakte Traffic Manager profiel weer gegeven. Dit kan worden gebruikt door clients (bijvoorbeeld door ernaar te navigeren met een webbrowser) om naar het juiste eind punt te gaan, zoals bepaald door het routerings type. In dit geval worden alle aanvragen doorgestuurd naar het eind punt met de laagste latentie van het netwerk van de client.
6. Nadat uw Traffic Manager profiel werkt, bewerkt u de DNS-record op uw gezaghebbende DNS-server om de domein naam van uw bedrijf te laten verwijzen naar de Traffic Manager domein naam.

![Routerings methode voor prestatie verkeer configureren met Traffic Manager][1]

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [gewogen verkeersrouteringsmethode](traffic-manager-configure-weighted-routing-method.md).
- Meer informatie over [de routeringsmethode met prioriteit](traffic-manager-configure-priority-routing-method.md).
- Meer informatie over [geografische verkeersrouteringsmethode](traffic-manager-configure-geographic-routing-method.md).
- Meer informatie over het [testen van Traffic Manager instellingen](traffic-manager-testing-settings.md).

<!--Image references-->
[1]: ./media/traffic-manager-performance-routing-method/traffic-manager-performance-routing-method.png