---
title: 'Zelf studie: Configure Priority Traffic routing with Azure Traffic Manager'
description: In deze zelf studie wordt uitgelegd hoe u de methode voor de route ring van prioriteits verkeer configureert in Traffic Manager
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "76938733"
---
# <a name="tutorial-configure-priority-traffic-routing-method-in-traffic-manager"></a>Zelf studie: een routerings methode voor prioriteits verkeer configureren in Traffic Manager

Azure websites bieden al een failover-functionaliteit voor websites binnen een Data Center (ook wel een regio genoemd), ongeacht de website modus. Traffic Manager biedt failover voor websites in verschillende data centers.

Een algemeen patroon voor failover van de service is het verzenden van verkeer naar een primaire service en het bieden van een set identieke back-upservices voor failover. In de volgende stappen wordt uitgelegd hoe u deze failover met prioriteit configureert met Azure Cloud Services en websites:

## <a name="to-configure-the-priority-traffic-routing-method"></a>De routerings methode voor prioriteits verkeer configureren

1. Meld u vanuit een browser aan bij [Azure Portal](https://portal.azure.com). Als u nog geen account hebt, kunt u zich registreren voor een [gratis proefversie van één maand](https://azure.microsoft.com/free/). 
2. Zoek in de zoek balk van de portal naar de **Traffic Manager-profielen** en klik vervolgens op de naam van het profiel waarvoor u de routerings methode wilt configureren.
3. Controleer op de Blade **Traffic Manager profiel** of de Cloud Services en websites die u wilt gebruiken in uw configuratie aanwezig zijn.
4. Klik in de sectie **instellingen** op **configuratie**en voer op de Blade **configuratie** de volgende stappen uit:
    1. Controleer voor de instellingen van de **routerings methode voor verkeer**of de methode voor verkeers routering **prioriteit**heeft. Als dat niet het geval is, klikt u op **prioriteit** in de vervolg keuzelijst.
    2. Stel de **instellingen voor de eindpunt monitor** identiek voor alle eind punten in dit profiel als volgt in:
        1. Selecteer het juiste **protocol**en geef het **poort** nummer op. 
        2. Typ **Path** een slash */* voor het pad. Als u eind punten wilt controleren, moet u een pad en bestands naam opgeven. Een slash (/) is een geldige vermelding voor het relatieve pad en impliceert dat het bestand zich in de hoofdmap (standaard) bevindt.
        3. Klik boven aan de pagina op **Opslaan**.
5. Klik in de sectie **instellingen** op **eind punten**.
6. Controleer de volg orde van de prioriteit voor uw eind punten op de Blade **eind punten** . Wanneer u de methode voor de route ring van **prioriteits** verkeer selecteert, is de volg orde van de geselecteerde eind punten van belang. Controleer de prioriteits volgorde van eind punten.  Het primaire eind punt bevindt zich bovenaan. Controleer de volg orde waarin deze wordt weer gegeven. alle aanvragen worden doorgestuurd naar het eerste eind punt en als Traffic Manager detecteert dat het een slechte status heeft, wordt automatisch een failover uitgevoerd naar het volgende eind punt. 
7. Als u de volg orde van de prioriteit wilt wijzigen, klikt u op het eind punt en klikt u op de Blade **eind punt** dat wordt weer gegeven op **bewerken** en wijzigt u de **prioriteits** waarde naar behoefte. 
8. Klik op **Opslaan** om de instellingen van het eind punt te wijzigen.
9. Nadat u de configuratie wijzigingen hebt voltooid, klikt u onder aan de pagina op **Opslaan** .
10. Test de wijzigingen in uw configuratie als volgt:
    1.  Zoek in de zoek balk van de portal naar de naam van het Traffic Manager profiel en klik op het profiel Traffic Manager in de resultaten die worden weer gegeven.
    2.  Klik op de Blade **Traffic Manager** profiel op **overzicht**.
    3.  Op de Blade **Traffic Manager profiel** wordt de DNS-naam van het zojuist gemaakte Traffic Manager profiel weer gegeven. Dit kan worden gebruikt door clients (bijvoorbeeld door ernaar te navigeren met een webbrowser) om naar het juiste eind punt te gaan, zoals bepaald door het routerings type. In dit geval worden alle aanvragen doorgestuurd naar het eerste eind punt en als Traffic Manager detecteert dat het een slechte status heeft, wordt automatisch een failover uitgevoerd naar het volgende eind punt.
11. Nadat uw Traffic Manager profiel werkt, bewerkt u de DNS-record op uw gezaghebbende DNS-server om de domein naam van uw bedrijf te laten verwijzen naar de Traffic Manager domein naam.

![Routerings methode voor prioriteits verkeer configureren met Traffic Manager][1]

## <a name="next-steps"></a>Volgende stappen


- Meer informatie over [gewogen verkeersrouteringsmethode](traffic-manager-configure-weighted-routing-method.md).
- Meer informatie over de [routerings methode voor prestaties](traffic-manager-configure-performance-routing-method.md).
- Meer informatie over [geografische verkeersrouteringsmethode](traffic-manager-configure-geographic-routing-method.md).
- Meer informatie over het [testen van Traffic Manager instellingen](traffic-manager-testing-settings.md).

<!--Image references-->
[1]: ./media/traffic-manager-priority-routing-method/traffic-manager-priority-routing-method.png