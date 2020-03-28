---
title: Zelfstudie - Gewogen round-robin-verkeersroutering configureren met Azure Traffic Manager
description: In deze zelfstudie wordt uitgelegd hoe u balansverkeer laadt met behulp van een round-robin-methode in Traffic Manager
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76938710"
---
# <a name="tutorial-configure-the-weighted-traffic-routing-method-in-traffic-manager"></a>Zelfstudie: De gewogen verkeersrouteringsmethode configureren in Traffic Manager

Een veelvoorkomend patroon van de routeringsmethode voor verkeer is het bieden van een reeks identieke eindpunten, waaronder cloudservices en websites, en het verkeer naar elk gelijk te verzenden. In de volgende stappen wordt beschreven hoe u dit type verkeersrouteringsmethode configureert.

> [!NOTE]
> Azure Web App biedt al round-robin load balancing-functionaliteit voor websites binnen een Azure-regio (die meerdere datacenters kunnen omvatten). Met Traffic Manager u verkeer over websites in verschillende datacenters distribueren.

## <a name="to-configure-the-weighted-traffic-routing-method"></a>De gewogen verkeersrouteringsmethode configureren

1. Meld u vanuit een browser aan bij [Azure Portal](https://portal.azure.com). Als u nog geen account hebt, kunt u zich registreren voor een [gratis proefversie van één maand](https://azure.microsoft.com/free/). 
2. Zoek in de zoekbalk van de portal naar de **profielen Verkeersbeheer** en klik vervolgens op de profielnaam waarvoor u de routeringsmethode wilt configureren.
3. Controleer in het **profielblad Van Traffic Manager** of zowel de cloudservices als de websites die u in uw configuratie wilt opnemen, aanwezig zijn.
4. Klik **in** de sectie Instellingen op **Configuratie**en in het **configuratieblad** als volgt:
    1. Controleer **bij instellingen voor de routeringsmethode**of de routeringsmethode **gewogen**is . Als dit niet het is, klikt u op **Gewogen** in de vervolgkeuzelijst.
    2. Stel de **instellingen voor endpoint-monitor** als volgt identiek in voor elk eindpunt in dit profiel:
        1. Selecteer het juiste **protocol**en geef het **poortnummer** op. 
        2. Typ **voor Pad** */* een voorwaartse slash . Als u eindpunten wilt controleren, moet u een pad en bestandsnaam opgeven. Een voorwaartse slash "/" is een geldige vermelding voor het relatieve pad en impliceert dat het bestand zich in de hoofdmap bevindt (standaard).
        3. Klik boven aan de pagina op **Opslaan**.
5. Test de wijzigingen in uw configuratie als volgt:
    1.  Zoek in de zoekbalk van de portal naar de profielnaam Traffic Manager en klik op het profiel Verkeersbeheer in de resultaten die worden weergegeven.
    2.  Klik in het profielblad **Van Verkeersbeheer** op **Overzicht**.
    3.  Het **profielblad Traffic Manager** geeft de DNS-naam van uw nieuw gemaakte Traffic Manager-profiel weer. Dit kan worden gebruikt door alle clients (bijvoorbeeld door te navigeren naar het met behulp van een webbrowser) om gerouteerd naar het juiste eindpunt, zoals bepaald door het type routering. In dit geval worden alle aanvragen elk eindpunt op een round-robin-manier doorgestuurd.
6. Zodra uw Traffic Manager-profiel werkt, bewerkt u de DNS-record op uw gezaghebbende DNS-server om uw bedrijfsdomeinnaam naar de domeinnaam Traffic Manager te leiden.

![Gewogen verkeersrouteringsmethode configureren met Traffic Manager][1]

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [de routeringsmethode voor prioriteitsverkeer](traffic-manager-configure-priority-routing-method.md).
- Meer informatie over [de routeringsmethode voor prestatieverkeer](traffic-manager-configure-performance-routing-method.md).
- Meer informatie over [geografische verkeersrouteringsmethode](traffic-manager-configure-geographic-routing-method.md).
- Meer informatie over het testen van [traffic manager-instellingen](traffic-manager-testing-settings.md).

<!--Image references-->
[1]: ./media/traffic-manager-weighted-routing-method/traffic-manager-weighted-routing-method.png
