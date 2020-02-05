---
title: Zelf studie-gewogen Round-Robin verkeers routering configureren met Azure Traffic Manager
description: In deze zelf studie wordt uitgelegd hoe u het verdelen van verkeer met behulp van een round-robin methode in Traffic Manager
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: rohink
ms.openlocfilehash: 8bdc710b36cae70d29d32333f431b8a9dda154cc
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/01/2020
ms.locfileid: "76938710"
---
# <a name="tutorial-configure-the-weighted-traffic-routing-method-in-traffic-manager"></a>Zelf studie: de routerings methode voor het gewogen verkeer configureren in Traffic Manager

Een veelgebruikte methode voor het routeren van route ring is om een set identieke eind punten te bieden, waaronder Cloud Services en websites, en verkeer naar beide te verzenden. In de volgende stappen wordt beschreven hoe u dit type verkeers routerings methode kunt configureren.

> [!NOTE]
> De Azure-web-app biedt al Round-Robin taakverdelings functionaliteit voor websites binnen een Azure-regio (die mogelijk meerdere data centers omvat). Met Traffic Manager kunt u verkeer distribueren over websites in verschillende data centers.

## <a name="to-configure-the-weighted-traffic-routing-method"></a>De routerings methode voor het gewogen verkeer configureren

1. Meld u vanuit een browser aan bij [Azure Portal](https://portal.azure.com). Als u nog geen account hebt, kunt u zich registreren voor een [gratis proefversie van één maand](https://azure.microsoft.com/free/). 
2. Zoek in de zoek balk van de portal naar de **Traffic Manager-profielen** en klik vervolgens op de naam van het profiel waarvoor u de routerings methode wilt configureren.
3. Controleer op de Blade **Traffic Manager profiel** of de Cloud Services en websites die u wilt gebruiken in uw configuratie aanwezig zijn.
4. Klik in de sectie **instellingen** op **configuratie**en voer op de Blade **configuratie** de volgende stappen uit:
    1. Controleer voor de **instellingen voor verkeers routerings methode**of de methode voor verkeers routering wordt **gewogen**. Als dat niet het geval is, klikt u op **gewogen** in de vervolg keuzelijst.
    2. Stel de **instellingen voor de eindpunt monitor** identiek voor alle eind punten in dit profiel als volgt in:
        1. Selecteer het juiste **protocol**en geef het **poort** nummer op. 
        2. Typ bij **pad** een voorwaartse slash */* . Als u eind punten wilt controleren, moet u een pad en bestands naam opgeven. Een slash (/) is een geldige vermelding voor het relatieve pad en impliceert dat het bestand zich in de hoofdmap (standaard) bevindt.
        3. Klik boven aan de pagina op **Opslaan**.
5. Test de wijzigingen in uw configuratie als volgt:
    1.  Zoek in de zoek balk van de portal naar de naam van het Traffic Manager profiel en klik op het profiel Traffic Manager in de resultaten die worden weer gegeven.
    2.  Klik op de Blade **Traffic Manager** profiel op **overzicht**.
    3.  Op de Blade **Traffic Manager profiel** wordt de DNS-naam van het zojuist gemaakte Traffic Manager profiel weer gegeven. Dit kan worden gebruikt door clients (bijvoorbeeld door ernaar te navigeren met een webbrowser) om naar het juiste eind punt te gaan, zoals bepaald door het routerings type. In dit geval worden alle aanvragen elk eind punt in een Round-Robin gerouteerd.
6. Nadat uw Traffic Manager profiel werkt, bewerkt u de DNS-record op uw gezaghebbende DNS-server om de domein naam van uw bedrijf te laten verwijzen naar de Traffic Manager domein naam.

![De methode voor de route ring van gewogen verkeer configureren met Traffic Manager][1]

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [routerings methode voor prioriteits verkeer](traffic-manager-configure-priority-routing-method.md).
- Meer informatie over de [routerings methode voor prestatie verkeer](traffic-manager-configure-performance-routing-method.md).
- Meer informatie over [geografische verkeersrouteringsmethode](traffic-manager-configure-geographic-routing-method.md).
- Meer informatie over het [testen van Traffic Manager instellingen](traffic-manager-testing-settings.md).

<!--Image references-->
[1]: ./media/traffic-manager-weighted-routing-method/traffic-manager-weighted-routing-method.png
