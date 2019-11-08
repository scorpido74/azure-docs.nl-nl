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
ms.openlocfilehash: c5d70e5a8784e88d3f2906d500768735dced1ee0
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73828132"
---
# <a name="what-is-industrial-iot-iiot"></a>Wat is industrieel IoT (IIoT)

IIoT is de industriële Internet of Things. IIoT verbetert de industriële efficiency door de toepassing van IoT in de productie branche. 

## <a name="improve-industrial-efficiencies"></a>Industriële efficiency verbeteringen verbeteren

Verbeter uw operationele productiviteit en winstgevendheid met een Connected Factory Solution Accelerator. Laat uw industriële uitrusting en apparaten verbinding maken in de cloud en bewaak deze—inclusief de machines die al op de werkvloer werkzaam zijn. Analyseer uw IoT-gegevens voor inzichten waarmee u de prestaties van de gehele werkvloer kunt verbeteren.

Verminder het tijdrovende proces van de toegang tot de fabrieks machines met OPC, en richt uw tijd op het bouwen van IIoT-oplossingen. Stroom lijn het beheer van certificaten en industriële activa met de OPC-kluis en vertrouwt erop dat de connectiviteit van de Asset wordt beveiligd. Deze micro services bieden een REST-achtige API boven op de [Azure-onderdelen van de industriële IOT](https://github.com/Azure/azure-iiot-opc-ua). De service-API biedt u de mogelijkheid om de functionaliteit van de Edge-module te beheren. 

![Overzicht van industriële IoT](media/overview-iot-industrial/overview.png)

> [!NOTE]
> Zie de GitHub- [opslag plaats](https://github.com/Azure/azure-iiot-services)voor meer informatie over de industriële IOT-services van Azure.
Als u niet bekend bent met de werking van Azure IoT Edge modules, begint u met de volgende artikelen:
- [Informatie over Azure IoT Edge](../iot-edge/about-iot-edge.md)
- [Azure IoT Edge modules](../iot-edge/iot-edge-modules.md)

## <a name="connected-factory"></a>Verbonden factory

[Connected Factory](../iot-accelerators/iot-accelerators-connected-factory-features.md) is een implementatie van de Azure-industriële IOT-referentie architectuur van micro soft die kan worden aangepast om te voldoen aan specifieke zakelijke vereisten. De volledige oplossings code is open-source en beschikbaar in de Connected Factory Solution Accelerator GitHub-opslag plaats. U kunt de implementatie gebruiken als uitgangspunt voor een commercieel product en om binnen enkele minuten een vooraf gebouwde oplossing te implementeren in uw Azure-abonnement. 

## <a name="factory-floor-connectivity"></a>Connectiviteit in de fabriek

OPC twee is een IIoT-onderdeel dat detectie en registratie van apparaten automatiseert, en biedt externe controle van industriële apparaten via REST-Api's. OPC dubbele, maakt gebruik van Azure IoT Edge en IoT Hub om verbinding te maken met de Cloud en het Factory-netwerk. OPC/IIoT ontwikkel aars kunnen zich richten op het bouwen van IIoT-toepassingen zonder dat u zich zorgen hoeft te maken over een veilige toegang tot de on-premises machines.

## <a name="security"></a>Beveiliging

OPC-kluis is een implementatie van OPC UA Global Discovery server (GDS) die de levens cyclus van certificaten voor OPC UA-server en client toepassingen in de cloud kan configureren, registreren en beheren. OPC-kluis vereenvoudigt de implementatie en het onderhoud van de connectiviteit van beveiligde activa in de industriële ruimte. Door certificaat beheer te automatiseren, beschik bare OPC-kluis fabrieks operators van de hand matige en complexe processen die zijn gekoppeld aan connectiviteit en certificaat beheer.

## <a name="next-steps"></a>Volgende stappen

Nu u een inleiding hebt gehad op industrieel IoT en de bijbehorende onderdelen, is dit de voorgestelde volgende stap:

> [!div class="nextstepaction"]
> [Wat is OPC, twee](overview-opc-twin.md)
