---
title: Referentie architectuur voor de oplossing water consumptie bewaking die is gebouwd met Azure IoT Central | Microsoft Docs
description: Leer concepten voor een oplossing voor water verbruik bewaking die is gebouwd met Azure IoT Central.
author: miriambrus
ms.author: miriamb
ms.date: 10/23/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 825c6c926f6415e38fdb89517a995a79abb92edd
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72955338"
---
# <a name="water-consumption-monitoring-reference-architecture"></a>Referentie architectuur bewaking water verbruik 

Bewakings oplossingen voor water verbruik kunnen worden gebouwd met de **Azure IOT Central app-sjabloon** als een toepassing voor de start van de IOT. In dit artikel vindt u richt lijnen voor referentie architectuur op hoog niveau voor het bouwen van een end-to-end-oplossing. 

![Bewakings architectuur water verbruik](./media/concepts-waterconsumptionmonitoring-architecture/concepts-waterconsumptionmonitoring-architecture1.png)

Concepten:

1. Apparaten en connectiviteit  
1. IoT Central 
2. Uitbreid baarheid en integraties
3. Bedrijfsapplicaties

Laten we eens kijken naar de belangrijkste onderdelen die in het algemeen een deel spelen in een oplossing voor water consumptie bewaking.

## <a name="devices-and-connectivity"></a>Apparaten en connectiviteit 
In deze sectie wordt verwezen naar apparaten die worden gebruikt voor intelligente water oplossingen, zoals het bewaken van water kwaliteit of het meten van water verbruik, in het algemeen als slimme water apparaten. Smart-water apparaten kunnen stroom meters, monitors voor water kwaliteit, slimme kleppen, lekkages, enz.

Apparaten die worden gebruikt in Smart water oplossingen, worden in het algemeen verbonden via LPWAN (Wide Area Networks) via een externe netwerk provider. Voor deze typen apparaten kunt u gebruikmaken van de [azure IOT Central Device Bridge](https://docs.microsoft.com/azure/iot-central/howto-build-iotc-device-bridge) om uw apparaatgegevens te verzenden naar uw IOT-toepassing in azure IOT Central. Het is ook mogelijk dat de gateways van het IP-adres compatibel zijn en rechtstreeks verbinding kunnen maken met IoT Central.

## <a name="iot-central"></a>IoT Central 
Azure IoT Central is een IoT-app-platform, waarmee u snel aan de slag kunt met uw IoT-oplossing. U kunt uw oplossing voorzien van een merk, aanpassen en integreren met services van derden.
Nadat u uw slimme water apparaten hebt aangesloten op IoT Central, krijgt u de opdracht en controle, bewaking en waarschuwingen van het apparaat, de gebruikers interface met ingebouwde RBAC, Configureer bare inzichten-Dash boards en uitbreidings opties. 


## <a name="extensibility-and-integrations"></a>Uitbreid baarheid en integraties 
U kunt uw IoT-toepassing uitbreiden in IoT Central en optioneel:
* Transformeer en Integreer uw IoT-gegevens voor geavanceerde analyses, bijvoorbeeld trainingen machine learning modellen, door doorlopende gegevens export vanuit IoT Central toepassing
* werk stromen automatiseren in andere systemen door acties te activeren via Microsoft Flow of webhooks van IoT Central toepassing
* programmatisch toegang tot uw IoT-toepassing in IoT Central via IoT Central Api's

## <a name="business-applications"></a>Zakelijk toepassingen 
De IoT-gegevens kunnen worden gebruikt om een groot aantal zakelijke toepassingen in een water-hulp programma uit te scha kelen. Als u wilt weten hoe u met veld Services verbinding kunt maken met de toepassing voor het bewaken van IoT Central water verbruik, volgt u de zelf studie over het [integreren met Dynamics 365 Field Services](./how-to-configure-connected-field-services.md) 


## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het [maken van een water verbruik](./tutorial-water-consumption-monitoring.md) IOT Central toepassing
* Meer informatie over [IOT Central government-sjablonen](./overview-iot-central-government.md)
* Zie [IOT Central Overview](https://docs.microsoft.com/azure/iot-central/overview-iot-central) (Engelstalig) voor meer informatie over IOT Central
