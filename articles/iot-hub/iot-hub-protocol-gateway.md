---
title: Azure IoT-protocolgateway | Microsoft Documenten
description: Een Azure IoT-protocolgateway gebruiken om iot-hub-mogelijkheden en protocolondersteuning uit te breiden zodat apparaten verbinding kunnen maken met uw hub met protocollen die niet door IoT Hub worden ondersteund.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/11/2017
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: bfd000e2b69f052e25f0ea6cd286b0ca3aef7519
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759707"
---
# <a name="support-additional-protocols-for-iot-hub"></a>Aanvullende protocollen voor IoT Hub ondersteunen

Azure IoT Hub ondersteunt native communicatie via de MQTT-, AMQP- en HTTPS-protocollen. In sommige gevallen kunnen apparaten of veldgateways een van deze standaardprotocollen mogelijk niet gebruiken en vereisen ze aanpassing van het protocol. In dergelijke gevallen u een aangepaste gateway gebruiken. Een aangepaste gateway maakt protocolaanpassing voor IoT Hub-eindpunten mogelijk door het verkeer van en naar IoT Hub te overbruggen. U de [Azure IoT-protocolgateway](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md) gebruiken als een aangepaste gateway om protocolaanpassing voor IoT Hub mogelijk te maken.

## <a name="azure-iot-protocol-gateway"></a>Azure IoT-protocolgateway

De Azure IoT-protocolgateway is een framework voor protocolaanpassing dat is ontworpen voor grootschalige, bidirectionele apparaatcommunicatie met IoT Hub. De protocolgateway is een pass-through-component die apparaatverbindingen via een specifiek protocol accepteert. Het overbrugt het verkeer naar IoT Hub via AMQP 1.0.

U de protocolgateway in Azure op een zeer schaalbare manier implementeren met Azure Service Fabric, Azure Cloud Services-werkrollen of Windows Virtual Machines. Bovendien kan de protocolgateway worden geïmplementeerd in on-premises omgevingen, zoals veldgateways.

De Azure IoT-protocolgateway bevat een MQTT-protocoladapter waarmee u het MQTT-protocolgedrag zo nodig aanpassen. Aangezien IoT Hub ingebouwde ondersteuning biedt voor het MQTT v3.1.1-protocol, moet u alleen overwegen om de MQTT-protocoladapter te gebruiken als protocolaanpassingen of specifieke vereisten voor extra functionaliteit vereist zijn.

De MQTT-adapter demonstreert ook het programmeermodel voor het bouwen van protocoladapters voor andere protocollen. Bovendien u met het Azure IoT-protocol gateway-gateway-model aangepaste componenten aansluiten voor gespecialiseerde verwerking, zoals aangepaste verificatie, berichttransformaties, compressie/decompressie of versleuteling/decryptie van verkeer tussen de apparaten en IoT Hub.

Voor flexibiliteit worden de Azure IoT-protocolgateway en MQTT-implementatie geleverd in een open-source softwareproject. U het open-sourceproject gebruiken om ondersteuning toe te voegen voor verschillende protocollen en protocolversies of om de implementatie voor uw scenario aan te passen. 

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de Azure IoT-protocolgateway en het gebruik en de implementatie ervan als onderdeel van uw IoT-oplossing:

* [Azure IoT-protocolgatewayrepository op GitHub](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md)

* [Handleiding voor Azure IoT-protocolgateway](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/docs/DeveloperGuide.md)

Zie voor meer informatie over het plannen van uw IoT Hub-implementatie:

* [Vergelijken met gebeurtenishubs](iot-hub-compare-event-hubs.md)

* [Schalen, hoge beschikbaarheid en herstel na noodgevallen](iot-hub-scaling.md)

* [Handleiding voor IoT Hub-ontwikkelaars](iot-hub-devguide.md)