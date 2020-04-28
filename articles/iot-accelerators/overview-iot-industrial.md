---
title: Overzicht van Azure Industrial IoT | Microsoft Docs
description: Dit artikel bevat een overzicht van industriële IoT. Hierin worden de verbonden fabriek, de fabrieks connectiviteit en beveiligings onderdelen in IIoT uitgelegd.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 3c474b57533cb6165ce7fe77defccd4cb7042b06
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82189919"
---
# <a name="what-is-industrial-iot-iiot"></a>Wat is industrieel IoT (IIoT)

IIoT is de industriële Internet of Things. IIoT verbetert de industriële efficiency door de toepassing van IoT in de productie branche. 

## <a name="improve-industrial-efficiencies"></a>Industriële efficiency verbeteringen verbeteren

Verbeter uw operationele productiviteit en winstgevendheid met een Connected Factory Solution Accelerator. Verbind en bewaak uw industriële apparaten en apparaten in de Cloud, inclusief uw machines die al op de fabriek werkzaam zijn. Analyseer uw IoT-gegevens voor inzichten waarmee u de prestaties van de hele fabrieks vloer kunt verbeteren.

Verminder het tijdrovende proces van de toegang tot de fabrieks machines met OPC, en richt uw tijd op het bouwen van IIoT-oplossingen. Stroom lijn het beheer van certificaten en industriële activa met de OPC-kluis en vertrouwt erop dat de connectiviteit van de Asset wordt beveiligd. Deze micro services bieden een REST-achtige API boven op de [Azure-onderdelen van de industriële IOT](https://github.com/Azure/Industrial-IoT). De service-API biedt u de mogelijkheid om de functionaliteit van de Edge-module te beheren. 

![Overzicht van industriële IoT](media/overview-iot-industrial/overview.png)

> [!NOTE]
> Zie de GitHub- [opslag plaats](https://github.com/Azure/Industrial-IoT) en- [documentatie](https://azure.github.io/Industrial-IoT/)voor meer informatie over de industriële IOT-services van Azure.
Als u niet bekend bent met de werking van Azure IoT Edge modules, begint u met de volgende artikelen:
- [Over Azure IoT Edge](../iot-edge/about-iot-edge.md)
- [Azure IoT Edge modules](../iot-edge/iot-edge-modules.md)

## <a name="connected-factory"></a>Verbonden factory

[Connected Factory](../iot-accelerators/iot-accelerators-connected-factory-features.md) is een implementatie van de Azure-industriële IOT-referentie architectuur van micro soft die kan worden aangepast om te voldoen aan specifieke zakelijke vereisten. De volledige oplossings code is open-source en beschikbaar in de Connected Factory Solution Accelerator GitHub-opslag plaats. U kunt deze gebruiken als uitgangs punt voor een commercieel product en binnen enkele minuten een vooraf gemaakte oplossing implementeren in uw Azure-abonnement. 

## <a name="factory-floor-connectivity"></a>Connectiviteit in de fabriek

OPC twee is een IIoT-onderdeel dat detectie en registratie van apparaten automatiseert, en biedt externe controle van industriële apparaten via REST-Api's. OPC dubbele, maakt gebruik van Azure IoT Edge en IoT Hub om verbinding te maken met de Cloud en het Factory-netwerk. OPC/IIoT ontwikkel aars kunnen zich richten op het bouwen van IIoT-toepassingen zonder dat u zich zorgen hoeft te maken over een veilige toegang tot de on-premises machines.

## <a name="security"></a>Beveiliging

OPC-kluis is een implementatie van OPC UA Global Discovery server (GDS) die de levens cyclus van certificaten voor OPC UA-server en client toepassingen in de cloud kan configureren, registreren en beheren. OPC-kluis vereenvoudigt de implementatie en het onderhoud van de connectiviteit van beveiligde activa in de industriële ruimte. Door certificaat beheer te automatiseren, beschik bare OPC-kluis fabrieks operators van de hand matige en complexe processen die zijn gekoppeld aan connectiviteit en certificaat beheer.

## <a name="next-steps"></a>Volgende stappen

Nu u een inleiding hebt gehad op industrieel IoT en de bijbehorende onderdelen, is dit de voorgestelde volgende stap:

> [!div class="nextstepaction"]
> [Wat is OPC Twin?](overview-opc-twin.md)
