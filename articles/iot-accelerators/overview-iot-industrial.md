---
title: Overzicht van Azure industrial IoT | Microsoft Documenten
description: Dit artikel geeft een overzicht van industriële IoT. Het verklaart de aangesloten fabriek, de fabrieksvloer connectiviteit en beveiligingscomponenten in IIoT.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: c5d70e5a8784e88d3f2906d500768735dced1ee0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "73828132"
---
# <a name="what-is-industrial-iot-iiot"></a>Wat is industrieel IoT (IIoT)

IIoT is het Industriële Internet of Things. IIoT verbetert de industriële efficiëntie door de toepassing van IoT in de maakindustrie. 

## <a name="improve-industrial-efficiencies"></a>Verbetering van de industriële efficiëntie

Verbeter uw operationele productiviteit en winstgevendheid met een verbonden fabrieksoplossingsversneller. Sluit uw industriële apparatuur en apparaten in de cloud aan en monitor deze, inclusief uw machines die al op de fabrieksvloer werken. Analyseer uw IoT-gegevens op inzichten die u helpen de prestaties van de gehele fabrieksvloer te verbeteren.

Verminder het tijdrovende proces van toegang tot fabrieksvloermachines met OPC Twin en richt uw tijd op het bouwen van IIoT-oplossingen. Stroomlijn certificaatbeheer en industriële asset-integratie met OPC Vault en vertrouw erop dat assetconnectiviteit is beveiligd. Deze microservices bieden een REST-achtige API bovenop [Azure Industrial IoT-componenten.](https://github.com/Azure/azure-iiot-opc-ua) De service-API geeft u de controle over de functionaliteit van edge-modules. 

![Overzicht van industriële IoT](media/overview-iot-industrial/overview.png)

> [!NOTE]
> Zie de [GitHub-repository](https://github.com/Azure/azure-iiot-services)voor meer informatie over Azure Industrial IoT-services.
Als u niet bekend bent met de manier waarop Azure IoT Edge-modules werken, begint u met de volgende artikelen:
- [Informatie over Azure IoT Edge](../iot-edge/about-iot-edge.md)
- [Azure IoT Edge-modules](../iot-edge/iot-edge-modules.md)

## <a name="connected-factory"></a>Verbonden factory

[Connected Factory](../iot-accelerators/iot-accelerators-connected-factory-features.md) is een implementatie van microsoft's Azure Industrial IoT-referentiearchitectuur die kan worden aangepast aan specifieke bedrijfsvereisten. De volledige oplossingscode is open-source en beschikbaar op Connected Factory-oplossingsversneller GitHub-repository. U het gebruiken als uitgangspunt voor een commercieel product en binnen enkele minuten een vooraf ingebouwde oplossing implementeren in uw Azure-abonnement. 

## <a name="factory-floor-connectivity"></a>Connectiviteit op de fabrieksvloer

OPC Twin is een IIoT-component die het ontdekken en registreren van apparaten automatiseert en de afstandsbediening van industriële apparaten biedt via REST API's. OPC Twin maakt gebruik van Azure IoT Edge en IoT Hub om de cloud en het fabrieksnetwerk met elkaar te verbinden. MET OPC Twin kunnen IIoT-ontwikkelaars zich richten op het bouwen van IIoT-toepassingen zonder zich zorgen te maken over hoe ze veilig toegang kunnen krijgen tot de on-premises machines.

## <a name="security"></a>Beveiliging

OPC Vault is een implementatie van OPC UA Global Discovery Server (GDS) die de levenscyclus van certificaten voor OPC UA-server- en clienttoepassingen in de cloud kan configureren, registreren en beheren. OPC Vault vereenvoudigt de implementatie en het onderhoud van veilige asset connectiviteit in de industriële ruimte. Door certificaatbeheer te automatiseren, bevrijdt OPC Vault fabrieksoperators van de handmatige en complexe processen die verband houden met connectiviteiten en certificaatbeheer.

## <a name="next-steps"></a>Volgende stappen

Nu u een inleiding tot industriële IoT en de componenten ervan hebt gehad, is hier de voorgestelde volgende stap:

> [!div class="nextstepaction"]
> [Wat is OPC Twin?](overview-opc-twin.md)
