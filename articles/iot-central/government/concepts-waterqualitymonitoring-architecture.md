---
title: Referentie architectuur voor de oplossing voor water kwaliteit bewaking die is gebouwd met Azure IoT Central | Microsoft Docs
description: Leer concepten voor een oplossing voor water kwaliteit die is gebouwd met Azure IoT Central.
author: miriambrus
ms.author: miriamb
ms.date: 10/23/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: ac81854b5d4ad15f058693a972cf7b79acd01c96
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72956766"
---
# <a name="water-quality-monitoring-reference-architecture"></a>Referentie architectuur voor het controleren van water kwaliteit 

De oplossing voor het controleren van water kwaliteit kan worden gebouwd met de **Azure IOT Central app-sjabloon** als een toepassing voor het maken van een starter IOT. In dit artikel vindt u richt lijnen voor referentie architectuur op hoog niveau voor het bouwen van een end-to-end-oplossing. 


![Bewakings architectuur voor water kwaliteit](./media/concepts-waterqualitymonitoring-architecture/concepts-waterqualitymonitoring-architecture1.png)

Concepten:

1. Apparaten en connectiviteit  
1. IoT Central 
2. Uitbreid baarheid en integraties
3. Bedrijfsapplicaties

Laten we eens kijken naar belang rijke onderdelen die doorgaans een deel spelen in een oplossing voor water kwaliteit.

## <a name="devices-and-connectivity"></a>Apparaten en connectiviteit 
In deze sectie wordt verwezen naar apparaten die worden gebruikt voor de bewaking van water kwaliteit of water verbruik, in het algemeen als slimme water apparaten. Smart-water apparaten kunnen stroom meters, monitors voor water kwaliteit, slimme kleppen, lekkages, enz.

Apparaten die worden gebruikt in Smart water oplossingen, worden in het algemeen verbonden via LPWAN (Wide Area Networks) via een externe netwerk provider. Voor deze typen apparaten kunt u gebruikmaken van de [azure IOT Central Device Bridge](https://docs.microsoft.com/azure/iot-central/howto-build-iotc-device-bridge) om uw apparaatgegevens te verzenden naar uw IOT-toepassing in azure IOT Central. Het is ook mogelijk dat de gateways van het IP-adres compatibel zijn en rechtstreeks verbinding kunnen maken met IoT Central.

## <a name="iot-central"></a>IoT Central 
Azure IoT Central is een IoT-app-platform, waarmee u snel aan de slag kunt met uw IoT-oplossing. U kunt uw oplossing voorzien van een merk, aanpassen en integreren met services van derden.
Nadat u uw slimme water apparaten hebt aangesloten op IoT Central, krijgt u de opdracht en controle, bewaking en waarschuwingen van het apparaat, de gebruikers interface met ingebouwde RBAC, Configureer bare inzichten-Dash boards en uitbreidings opties. 

## <a name="extensibility-and-integrations"></a>Uitbreid baarheid en integraties 
U kunt uw IoT-toepassing uitbreiden in IoT Central en optioneel:
* Transformeer en Integreer uw IoT-gegevens voor geavanceerde analyses, bijvoorbeeld trainingen machine learning modellen, door doorlopende gegevens export vanuit IoT Central toepassing
* werk stromen automatiseren in andere systemen door acties te activeren via Microsoft Flow of webhooks van IoT Central toepassing
* programmatisch toegang tot uw IoT-toepassing in IoT Central via IoT Central Api's

## <a name="business-applications"></a>Bedrijfsapplicaties 
De IoT-gegevens kunnen worden gebruikt om een groot aantal zakelijke toepassingen in een water-hulp programma uit te scha kelen. Als u wilt weten hoe u de toepassing voor het controleren van IoT Central water kwaliteit kunt verbinden met veld Services, volgt u het artikel over [hoe u kunt integreren met Dynamics 365 Field Services](./how-to-configure-connected-field-services.md). 


## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het maken van een IoT Central toepassing voor [water kwaliteit](./tutorial-water-quality-monitoring.md)
* Meer informatie over [IOT Central government-sjablonen](./overview-iot-central-government.md)
* Zie [IOT Central Overview](https://docs.microsoft.com/azure/iot-central/overview-iot-central) (Engelstalig) voor meer informatie over IOT Central

