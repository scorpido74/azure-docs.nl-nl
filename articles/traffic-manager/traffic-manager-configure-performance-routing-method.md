---
title: Prestatiebeheerrouteringsmethode configureren met Azure Traffic Manager | Microsoft Documenten
description: In dit artikel wordt uitgelegd hoe u Traffic Manager configureert om verkeer naar het eindpunt met de laagste latentie te leiden
services: traffic-manager
manager: twooley
documentationcenter: ''
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: rohink
ms.openlocfilehash: f5e9b7690c28793a35c692a6125a6b11c7a140a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938757"
---
# <a name="configure-the-performance-traffic-routing-method"></a>De routeringsmethode voor prestatieverkeer configureren

Met de methode voor het routeren van het prestatieverkeer u verkeer naar het eindpunt leiden met de laagste latentie van het netwerk van de client. Doorgaans is het datacenter met de laagste latentie het dichtst in geografische afstand. Deze routeringsmethode kan geen rekening houden met realtime wijzigingen in de netwerkconfiguratie of -belasting.

##  <a name="to-configure-performance-routing-method"></a>Prestatieringsmethode configureren

1. Meld u vanuit een browser aan bij [Azure Portal](https://portal.azure.com). Als u nog geen account hebt, kunt u zich registreren voor een [gratis proefversie van één maand](https://azure.microsoft.com/free/). 
2. Zoek in de zoekbalk van de portal naar de **profielen Verkeersbeheer** en klik vervolgens op de profielnaam waarvoor u de routeringsmethode wilt configureren.
3. Controleer in het **profielblad Van Traffic Manager** of zowel de cloudservices als de websites die u in uw configuratie wilt opnemen, aanwezig zijn.
4. Klik **in** de sectie Instellingen op **Configuratie**en in het **configuratieblad** als volgt:
    1. Selecteer **Prestaties**voor instellingen voor **routeringsmethode**voor **routeringsmethode** .
    2. Stel de **instellingen voor endpoint-monitor** als volgt identiek in voor elk eindpunt in dit profiel:
        1. Selecteer het juiste **protocol**en geef het **poortnummer** op. 
        2. Typ **voor Pad** */* een voorwaartse slash . Als u eindpunten wilt controleren, moet u een pad en bestandsnaam opgeven. Een voorwaartse slash "/" is een geldige vermelding voor het relatieve pad en impliceert dat het bestand zich in de hoofdmap bevindt (standaard).
        3. Klik boven aan de pagina op **Opslaan**.
5.  Test de wijzigingen in uw configuratie als volgt:
    1.  Zoek in de zoekbalk van de portal naar de profielnaam Traffic Manager en klik op het profiel Verkeersbeheer in de resultaten die worden weergegeven.
    2.  Klik in het profielblad **Van Verkeersbeheer** op **Overzicht**.
    3.  Het **profielblad Traffic Manager** geeft de DNS-naam van uw nieuw gemaakte Traffic Manager-profiel weer. Dit kan door alle clients worden gebruikt (bijvoorbeeld door er met een webbrowser naar toe te navigeren) om naar het juiste eindpunt te worden geleid, zoals bepaald door het routeringstype. In dit geval worden alle aanvragen doorgestuurd naar het eindpunt met de laagste latentie van het netwerk van de client.
6. Zodra uw Traffic Manager-profiel werkt, bewerkt u de DNS-record op uw gezaghebbende DNS-server om uw bedrijfsdomeinnaam naar de domeinnaam Traffic Manager te leiden.

![Prestatiebeheerrouteringsmethode configureren met Traffic Manager][1]

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [gewogen verkeersrouteringsmethode](traffic-manager-configure-weighted-routing-method.md).
- Meer informatie over [de routeringsmethode met prioriteit](traffic-manager-configure-priority-routing-method.md).
- Meer informatie over [geografische verkeersrouteringsmethode](traffic-manager-configure-geographic-routing-method.md).
- Meer informatie over het testen van [traffic manager-instellingen](traffic-manager-testing-settings.md).

<!--Image references-->
[1]: ./media/traffic-manager-performance-routing-method/traffic-manager-performance-routing-method.png