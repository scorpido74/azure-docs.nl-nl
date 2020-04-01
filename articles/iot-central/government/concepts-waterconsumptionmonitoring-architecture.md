---
title: Referentiearchitectuur voor oplossing voor waterverbruikbewaking die is gebouwd met Azure IoT Central| Microsoft Documenten
description: Leer concepten voor een oplossing voor waterverbruikbewaking die is gebouwd met Azure IoT Central.
author: miriambrus
ms.author: miriamb
ms.date: 10/23/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 3a64ca5b0c2a092f895873e097ea6beb9a235a37
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77017697"
---
# <a name="water-consumption-monitoring-reference-architecture"></a>Referentiearchitectuur voor de bewaking van waterverbruik 



Oplossingen voor waterverbruikmonitoring kunnen worden gebouwd met de **Azure IoT Central-appsjabloon** als een kick starter IoT-toepassing. Dit artikel biedt een referentiearchitectuurrichtlijnen op hoog niveau voor het bouwen van een end-to-end-oplossing. 

![Architectuur voor het monitoren van waterverbruik](./media/concepts-waterconsumptionmonitoring-architecture/concepts-waterconsumptionmonitoring-architecture1.png)

Concepten:

1. Apparaten en connectiviteit  
1. IoT Central 
2. Uitbreidbaarheid en integraties
3. Bedrijfstoepassingen

Laten we eens kijken naar belangrijke componenten die over het algemeen een rol spelen in een oplossing voor het monitoren van waterverbruik.

## <a name="devices-and-connectivity"></a>Apparaten en connectiviteit 
Deze sectie verwijst naar apparaten die worden gebruikt voor slimme wateroplossingen, zoals bewaking van de waterkwaliteit of monitoring van het waterverbruik, over het algemeen als slimme waterapparaten. Slimme waterapparaten kunnen stroommeters zijn, waterkwaliteitsmonitoren, slimme kleppen, lekdetectoren, enzovoort.

Apparaten die worden gebruikt in slimme wateroplossingen worden over het algemeen verbonden via low-power wide area networks (LPWAN), via een externe netwerkoperator. Voor dit soort apparaten u de [Azure IoT Central Device Bridge](https://docs.microsoft.com/azure/iot-central/core/howto-build-iotc-device-bridge) gebruiken om uw apparaatgegevens naar uw IoT-toepassing in Azure IoT Central te verzenden. U ook apparaatgateways hebben die IP-geschikt zijn en rechtstreeks verbinding kunnen maken met IoT Central.

## <a name="iot-central"></a>IoT Central 
Azure IoT Central is een IoT-app-platform, waarmee u snel aan de slag met uw IoT-oplossing. U uw oplossing brand, customize en integreren met services van derden.
Nadat u uw slimme waterapparaten hebt aangesloten op IoT Central, krijgt u apparaatopdracht en -controle, bewaking en waarschuwingen, gebruikersinterface met ingebouwde RBAC, configureerbare insights-dashboards en uitbreidbaarheidsopties. 


## <a name="extensibility-and-integrations"></a>Uitbreidbaarheid en integraties 
U uw IoT-toepassing uitbreiden in IoT Central en optioneel:
* transformeren en integreren van uw IoT-gegevens voor geavanceerde analyses, bijvoorbeeld het trainen van machine learning-modellen, door continue data-export vanuit IoT Central-toepassing
* workflows in andere systemen automatiseren door acties te activeren met Microsoft Flow of webhooks van IoT Central-toepassing
* programmaatrisch toegang tot uw IoT-toepassing in IoT Central via IoT Central API's

## <a name="business-applications"></a>Zakelijke toepassingen 
De IoT-gegevens kunnen worden gebruikt om verschillende soorten bedrijfstoepassingen binnen een waterbedrijf van stroom te maken. Volg de zelfstudie over hoe u integreren met Dynamics [365 Field Services](./how-to-configure-connected-field-services.md) voor meer informatie over het verbinden van uw IoT Central waterconsumption monitoring-toepassing met veldservices 


## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het [maken van een IoT](./tutorial-water-consumption-monitoring.md) Central-toepassing voor waterverbruik
* Meer informatie over [iot-sjablonen voor centrale overheden](./overview-iot-central-government.md)
* Zie [IoT Central-overzicht](https://docs.microsoft.com/azure/iot-central/core/overview-iot-central) voor meer informatie over IoT Central
