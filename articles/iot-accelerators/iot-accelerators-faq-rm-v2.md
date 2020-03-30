---
title: Veelgestelde vragen over oplossingen voor externe bewaking - Azure | Microsoft Documenten
description: Dit artikel beantwoordt de veelgestelde vragen voor de Remote Monitoring oplossing versnellers.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: c410ac7f41adb623a7198320a1edced097778569
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73826236"
---
# <a name="frequently-asked-questions-for-remote-monitoring-solution-accelerator"></a>Veelgestelde vragen voor Remote Monitoring oplossingversneller

Zie ook, de algemene [FAQ](iot-accelerators-faq.md).

### <a name="how-much-does-it-cost-to-provision-the-new-remote-monitoring-solution"></a>Hoeveel kost het om de nieuwe remote monitoring oplossing te leveren?

De nieuwe oplossingsversneller biedt twee implementatieopties:

* Een *basisoptie* die voor ontwikkelaars wordt ontworpen die lagere ontwikkelingskosten zoeken of klanten die een manifestatie of bewijs van concept willen bouwen.
* Een *standaardoptie* die is ontworpen voor bedrijven die een productieklare infrastructuur willen implementeren.

### <a name="how-can-i-ensure-i-keep-my-costs-down-while-i-develop-my-solution"></a>Hoe kan ik ervoor zorgen dat ik mijn kosten laag houd terwijl ik mijn oplossing ontwikkel?

Naast twee gedifferentieerde implementaties heeft de nieuwe oplossing voor externe bewaking een instelling om alle gesimuleerde apparaten op aanvraag in- of uit te schakelen. Het uitschakelen van de simulatie vermindert de gegevens ingenomen in de oplossing en, dus, de totale kosten.

### <a name="what-is-the-difference-between-the-basic-and-standard-deployment-options-how-do-i-decide-between-the-two-deployment-options"></a>Wat is het verschil tussen de basis- en standaardimplementatieopties? Hoe kan ik kiezen tussen de twee implementatieopties?

Elke implementatieoptie speelt in op verschillende behoeften. De basisimplementatie is ontworpen om aan de slag te gaan en PoC en kleine pilots te ontwikkelen. Het biedt een gestroomlijnde architectuur met de minimale benodigde resources en lagere kosten. De standaardimplementatie is ontworpen om een productieklare oplossing te bouwen en aan te passen en biedt een implementatie met de nodige elementen om dat te realiseren. Voor betrouwbaarheid en schaal worden microservices van toepassingen gebouwd als Docker-containers en geïmplementeerd met behulp van een orchestrator (Kubernetes standaard). De orchestrator is verantwoordelijk voor de implementatie, schaalvergroting en het beheer van de toepassing. U moet een optie kiezen op basis van uw huidige behoeften. U de ene, de andere of een combinatie van beide gebruiken, afhankelijk van uw projectfase.

### <a name="how-do-i-configure-a-dynamic-map-on-the-dashboard"></a>Hoe configureer ik een dynamische kaart op het dashboard?

Zie Kaartsleutel bijwerken voor meer informatie [om apparaten op een dynamische kaart te bekijken.](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map)

### <a name="where-can-i-find-information-about-the-previous-version-of-the-remote-monitoring-solution"></a>Waar kan ik informatie vinden over de vorige versie van de Remote Monitoring-oplossing?

De vorige versie van de Remote Monitoring oplossingsversneller stond bekend als de IoT Suite Remote Monitoring vooraf geconfigureerde oplossing. U vindt de gearchiveerde [https://docs.microsoft.com/previous-versions/azure/iot-suite/](https://docs.microsoft.com/previous-versions/azure/iot-suite/)documentatie op .

### <a name="next-steps"></a>Volgende stappen

U kunt ook enkele van de andere functies en mogelijkheden van de IoT-oplossingsversnellers bekijken:

* [Ontdek de mogelijkheden van de remote monitoring oplossingsversneller](quickstart-remote-monitoring-deploy.md)
* [Een overzicht van de oplossingsversneller voor Predictief onderhoud](iot-accelerators-predictive-overview.md)
* [Connected Factory-oplossingsversneller implementeren](quickstart-connected-factory-deploy.md)
* [Fundamentele IoT-beveiliging](/azure/iot-fundamentals/iot-security-ground-up)
