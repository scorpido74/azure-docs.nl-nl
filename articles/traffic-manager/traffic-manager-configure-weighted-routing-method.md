---
title: 'Zelfstudie: Gewogen round robin-verkeersroutering configureren met Azure Traffic Manager'
description: In deze zelfstudie wordt uitgelegd hoe u verkeer in balans kunt brengen in Traffic Manager met behulp van een round robin-methode
services: traffic-manager
documentationcenter: ''
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: rohink
ms.openlocfilehash: b06df3814661b64f7fa46cd5e3b8f4dea9d51710
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/11/2020
ms.locfileid: "84711663"
---
# <a name="tutorial-configure-the-weighted-traffic-routing-method-in-traffic-manager"></a>Zelfstudie: De gewogen verkeersrouteringsmethode configureren in Traffic Manager

Een veelgebruikte methode voor het routeren van verkeer is om een set identieke eindpunten te bieden, waaronder cloudservices en websites, en verkeer gelijkmatig naar beide te verzenden. In de volgende stappen wordt beschreven hoe u dit type verkeersrouteringsmethode kunt configureren.

> [!NOTE]
> De Azure-web-app biedt al round robin-taakverdeling voor websites binnen een Azure-regio (die mogelijk meerdere datacentra omvat). Met Traffic Manager kunt u verkeer distribueren over websites in verschillende datacentra.

## <a name="to-configure-the-weighted-traffic-routing-method"></a>Ga als volgt te werk om de gewogen verkeersrouteringsmethode te configureren

1. Meld u vanuit een browser aan bij [Azure Portal](https://portal.azure.com). Als u nog geen account hebt, kunt u zich registreren voor een [gratis proefversie van één maand](https://azure.microsoft.com/free/). 
2. Zoek in de zoekbalk van de portal naar de **Traffic Manager-profielen** en klik vervolgens op de profielnaam waarvoor u de routeringsmethode wilt configureren.
3. Verifieer in de blade van het **Traffic Manager-profiel** of zowel de cloudservices als websites die u in uw configuratie wilt opnemen aanwezig zijn.
4. Klik in de sectie **Instellingen** op **Configuratie** en voer in de blade **Configuratie** de volgende configuratie uit:
    1. Controleer voor **instellingen voor de methode voor verkeersroutering** of de methode voor verkeersroutering op **Gewogen** is ingesteld. Klik op **Gewogen** in de vervolgkeuzelijst als dat niet het geval is.
    2. Stel de **Controle-instellingen voor het eindpunt** voor alle eindpunten in dit profiel hetzelfde in en doe dit als volgt:
        1. Selecteer het juiste **Protocol** en geef het **Poortnummer** op. 
        2. Typ bij **Pad** een slash */* . Als u eindpunten wilt controleren, moet u een pad en bestandsnaam opgeven. Een slash (/) is een geldige vermelding voor het relatieve pad en impliceert dat het bestand zich in de hoofdmap bevindt (standaard).
        3. Klik bovenaan de pagina op **Opslaan**.
5. Test de wijzigingen in uw configuratie als volgt:
    1.  Zoek in de zoekbalk van de portal naar de naam van het Traffic Manager-profiel en klik in de weergegeven resultaten op het Traffic Manager-profiel.
    2.  Klik in de blade met het **Traffic Manager**-profiel op **Overzicht**.
    3.  De blade **Traffic Manager-profiel** geeft de DNS-naam weer van het Traffic Manager-profiel dat u zojuist hebt gemaakt. Dit kan door clients worden gebruikt (bijvoorbeeld door ernaar te navigeren met een webbrowser) om naar het juiste eindpunt te gaan, zoals wordt bepaald door het routeringstype. In dit geval worden alle aanvragen naar elk eindpunt gerouteerd op basis van round robin.
6. Als uw Traffic Manager-profiel werkt, kunt u de DNS-record op uw gezaghebbende DNS-server zo bewerken dat de domeinnaam van uw bedrijf verwijst naar de Traffic Manager-domeinnaam.

![De gewogen verkeersrouteringsmethode configureren met Traffic Manager][1]

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [geprioriteerde verkeersrouteringsmethode](traffic-manager-configure-priority-routing-method.md).
- Meer informatie over [verkeersroutering op basis van prestaties](traffic-manager-configure-performance-routing-method.md).
- Meer informatie over [geografische verkeersrouteringsmethode](traffic-manager-configure-geographic-routing-method.md).
- Meer informatie over het [testen van Traffic Manager-instellingen](traffic-manager-testing-settings.md).

<!--Image references-->
[1]: ./media/traffic-manager-weighted-routing-method/traffic-manager-weighted-routing-method.png
