---
title: Zelfstudie - Prioriteitsbeheer routering configureren met Azure Traffic Manager
description: In deze zelfstudie wordt uitgelegd hoe u de methode voor prioriteitsverkeer configureert in Traffic Manager
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: kumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: rohink
ms.openlocfilehash: ca223a19ff7ddeae95878f941f3cf295664e62b4
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76938733"
---
# <a name="tutorial-configure-priority-traffic-routing-method-in-traffic-manager"></a>Zelfstudie: Prioriteitsbeheerrouteringsmethode configureren in Traffic Manager

Ongeacht de websitemodus bieden Azure Websites al failoverfunctionaliteit voor websites binnen een datacenter (ook wel regio genoemd). Traffic Manager biedt failover voor websites in verschillende datacenters.

Een veelvoorkomend patroon voor servicefailover is om verkeer naar een primaire service te verzenden en een set identieke back-upservices voor failover te bieden. In de volgende stappen wordt uitgelegd hoe u deze geprioriteerd failover configureert met Azure-cloudservices en -websites:

## <a name="to-configure-the-priority-traffic-routing-method"></a>De routeringsmethode voor prioriteitsverkeer configureren

1. Meld u vanuit een browser aan bij [Azure Portal](https://portal.azure.com). Als u nog geen account hebt, kunt u zich registreren voor een [gratis proefversie van één maand](https://azure.microsoft.com/free/). 
2. Zoek in de zoekbalk van de portal naar de **profielen Verkeersbeheer** en klik vervolgens op de profielnaam waarvoor u de routeringsmethode wilt configureren.
3. Controleer in het **profielblad Van Traffic Manager** of zowel de cloudservices als de websites die u in uw configuratie wilt opnemen, aanwezig zijn.
4. Klik **in** de sectie Instellingen op **Configuratie**en in het **configuratieblad** als volgt:
    1. Controleer **bij instellingen voor de routeringsmethode**of de routeringsmethode **prioriteit**is. Als dit niet het is, klikt u op **Prioriteit** in de vervolgkeuzelijst.
    2. Stel de **instellingen voor endpoint-monitor** als volgt identiek in voor elk eindpunt in dit profiel:
        1. Selecteer het juiste **protocol**en geef het **poortnummer** op. 
        2. Typ **voor Pad** */* een voorwaartse slash . Als u eindpunten wilt controleren, moet u een pad en bestandsnaam opgeven. Een voorwaartse slash "/" is een geldige vermelding voor het relatieve pad en impliceert dat het bestand zich in de hoofdmap bevindt (standaard).
        3. Klik boven aan de pagina op **Opslaan**.
5. Klik **in** de sectie Instellingen op **Eindpunten**.
6. Bekijk in het **blade Endpoints** de prioriteitsvolgorde voor uw eindpunten. Wanneer u de **routeringsmethode Prioriteitsverkeer** selecteert, is de volgorde van de geselecteerde eindpunten van belang. Controleer de prioriteitsvolgorde van eindpunten.  Het primaire eindpunt bevindt zich bovenaan. Controleer de volgorde van de bestelling dubbel. alle aanvragen worden doorgestuurd naar het eerste eindpunt en als Traffic Manager detecteert dat het ongezond is, mislukt het verkeer automatisch naar het volgende eindpunt. 
7. Als u de prioriteitsvolgorde voor eindpunt wilt wijzigen, klikt u op het eindpunt en klikt u in het **weergegeven eindpuntblad** op **Bewerken** en wijzigt u de **waarde Prioriteit** indien nodig. 
8. Klik **op Opslaan** om de eindpuntinstellingen te wijzigen.
9. Nadat u de configuratiewijzigingen hebt voltooid, klikt u onder aan de pagina op **Opslaan.**
10. Test de wijzigingen in uw configuratie als volgt:
    1.  Zoek in de zoekbalk van de portal naar de profielnaam Traffic Manager en klik op het profiel Verkeersbeheer in de resultaten die worden weergegeven.
    2.  Klik in het profielblad **Van Verkeersbeheer** op **Overzicht**.
    3.  Het **profielblad Traffic Manager** geeft de DNS-naam van uw nieuw gemaakte Traffic Manager-profiel weer. Dit kan door alle clients worden gebruikt (bijvoorbeeld door er met een webbrowser naar toe te navigeren) om naar het juiste eindpunt te worden geleid, zoals bepaald door het routeringstype. In dit geval worden alle aanvragen doorgestuurd naar het eerste eindpunt en als Traffic Manager detecteert dat het ongezond is, mislukt het verkeer automatisch naar het volgende eindpunt.
11. Zodra uw Traffic Manager-profiel werkt, bewerkt u de DNS-record op uw gezaghebbende DNS-server om uw bedrijfsdomeinnaam naar de domeinnaam Traffic Manager te leiden.

![Prioriteitsbeheerroutemethode configureren met Traffic Manager][1]

## <a name="next-steps"></a>Volgende stappen


- Meer informatie over [gewogen verkeersrouteringsmethode](traffic-manager-configure-weighted-routing-method.md).
- Meer informatie over [de methode voor het routeren van prestaties](traffic-manager-configure-performance-routing-method.md).
- Meer informatie over [geografische verkeersrouteringsmethode](traffic-manager-configure-geographic-routing-method.md).
- Meer informatie over het testen van [traffic manager-instellingen](traffic-manager-testing-settings.md).

<!--Image references-->
[1]: ./media/traffic-manager-priority-routing-method/traffic-manager-priority-routing-method.png