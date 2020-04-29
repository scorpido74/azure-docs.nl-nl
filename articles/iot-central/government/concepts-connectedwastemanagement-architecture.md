---
title: Referentie architectuur voor een oplossing voor verbonden afval beheer die is gebouwd met Azure IoT Central | Microsoft Docs
description: Leer concepten voor een oplossing voor verbonden afval beheer die is gebouwd met Azure IoT Central.
author: miriambrus
ms.author: miriamb
ms.date: 10/23/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 763fb82e0725e5a98112af3a79856c3703a5c2c3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "77016082"
---
# <a name="connected-waste-monitoring-reference-architecture"></a>Referentiearchitectuur voor verbonden afvalverwerking 



Een oplossing voor het beheer van verbonden afval kan worden gebouwd met behulp van de **Azure IOT Central app-sjabloon** als een start-toepassing voor IOT. In dit artikel vindt u richt lijnen voor referentie architectuur op hoog niveau voor het bouwen van een end-to-end-oplossing. 

![Architectuur van verbonden afval beheer](./media/concepts-connectedwastemanagement-architecture/concepts-connectedwastemanagement-architecture1.png)


Concepten:

1. Apparaten en connectiviteit  
1. IoT Central 
2. Uitbreid baarheid en integraties
3. Zakelijke toepassingen

Laten we eens kijken naar de belangrijkste onderdelen die in het algemeen een deel spelen in een oplossing voor water consumptie bewaking.

## <a name="devices-and-connectivity"></a>Apparaten en connectiviteit 
Apparaten die worden gebruikt in open omgevingen, zoals afval bakken, kunnen worden verbonden via LPWAN (Wide Area Networks) via een externe netwerk provider. Voor deze typen apparaten kunt u de [azure IOT Central Device Bridge](https://docs.microsoft.com/azure/iot-central/core/howto-build-iotc-device-bridge) gebruiken om uw apparaatgegevens te verzenden naar uw IOT-toepassing in azure IOT Central. Het is ook mogelijk dat de gateways van het IP-adres compatibel zijn en rechtstreeks verbinding kunnen maken met IoT Central.

## <a name="iot-central"></a>IoT Central 
Azure IoT Central is een IoT-app-platform, waarmee u snel aan de slag kunt met uw IoT-oplossing. U kunt uw oplossing voorzien van een merk, aanpassen en integreren met services van derden.
Nadat u uw slimme water apparaten hebt aangesloten op IoT Central, krijgt u de opdracht en controle, bewaking en waarschuwingen van het apparaat, de gebruikers interface met ingebouwde RBAC, Configureer bare inzichten-Dash boards en uitbreidings opties. 

## <a name="extensibility-and-integrations"></a>Uitbreid baarheid en integraties 
U kunt uw IoT-toepassing uitbreiden in IoT Central en optioneel:
* Transformeer en Integreer uw IoT-gegevens voor geavanceerde analyses, bijvoorbeeld trainingen machine learning modellen, door doorlopende gegevens export vanuit IoT Central toepassing. 
* werk stromen automatiseren in andere systemen door acties te activeren met behulp van Microsoft Flow of webhooks van IoT Central toepassing
* programmatische toegang tot uw IoT-toepassing in IoT Central via IoT Central-Api's.

## <a name="business-applications"></a>Zakelijke toepassingen 
De IoT-gegevens kunnen worden gebruikt om een groot aantal zakelijke toepassingen in een verspild hulp programma uit te scha kelen. Als u wilt weten hoe u verbinding kunt maken met de IoT Central verbonden afval beheer toepassing met veld Services, volgt u de zelf studie over het [integreren met Dynamics 365 Field Services](./how-to-configure-connected-field-services.md) 

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het [maken van een verbonden afval beheer](./tutorial-connected-waste-management.md) IOT Central toepassing
* Meer informatie over [IOT Central government-sjablonen](./overview-iot-central-government.md)
* Zie [IOT Central Overview](https://docs.microsoft.com/azure/iot-central/core/overview-iot-central) (Engelstalig) voor meer informatie over IOT Central

