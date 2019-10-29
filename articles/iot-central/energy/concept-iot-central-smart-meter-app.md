---
title: Architectuur concepten in azure IoT Central-energie | Microsoft Docs
description: In dit artikel worden de belangrijkste concepten besproken die betrekking hebben op de architectuur van de sjabloon voor Azure IoT Central energie-apps
author: op-ravi
ms.author: omravi
ms.date: 10/22/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 8272764e967fccfbfa88d54e688f9d7aaf4e0917
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027688"
---
# <a name="azure-iot-central---smart-meter-app-architecture"></a>Azure IoT Central-app-architectuur voor slimme meter

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Dit artikel bevat een overzicht van de app-sjabloon architectuur voor het bewaken van slimme meters. In het onderstaande diagram ziet u een veelgebruikte architectuur voor slimme meter-app op Azure met behulp van IoT Central-platform.

[!div class="mx-imgBorder"]
architectuur van de ![slimme meter](media/concept-iot-central-smart-meter/smart-meter-app-architecture.png)

Deze architectuur bestaat uit de volgende onderdelen. Voor sommige oplossingen is het niet vereist dat elk onderdeel hier wordt vermeld.

## <a name="smart-meters-and-connectivity"></a>Slimme meters en connectiviteit 

Een slimme meter is een van de belangrijkste apparaten onder alle energie-assets. Het registreert en communiceert gegevens over energie verbruik naar hulpprogram ma's voor bewaking en andere gebruiks gevallen, zoals facturering en respons op aanvraag. Op basis van het meter type kan verbinding worden gemaakt met IoT Central via gateways of via andere tussenliggende apparaten of systemen, zoals de rand apparaten en de hoofd systemen. Bouw IoT Central apparaats brug om apparaten te verbinden die niet rechtstreeks kunnen worden aangesloten. De IoT Central Device Bridge is een open-source oplossing en u vindt [hier](https://docs.microsoft.com/azure/iot-central/core/howto-build-iotc-device-bridge)de volledige informatie. 


## <a name="iot-central-platform"></a>IoT Central platform

Azure IoT Central is een platform waarmee u uw IoT-oplossing eenvoudiger maakt en de overhead en kosten van IoT-beheer,-bewerkingen en-ontwikkeling kunt verlagen. Met IoT Central kunt u eenvoudig uw Internet of Things (IoT)-assets op schaal aansluiten, bewaken en beheren. Nadat u uw slimme meters hebt verbonden met IoT Central, gebruikt de app-sjabloon ingebouwde functies zoals model modellen, opdrachten en dash boards. In de app-sjabloon wordt ook gebruikgemaakt van de IoT Central opslag voor scenario's voor warme paden zoals bijna realtime meter gegevens bewaking, analyses, regels en visualisatie. 


## <a name="extensibility-options-to-build-with-iot-central"></a>Uitbreidings opties om met IoT Central te bouwen
Het IoT Central-platform biedt twee uitbreidings opties: voortdurende gegevens export (COB) en Api's. De klanten en partners kunnen kiezen uit deze opties op basis van het aanpassen van hun oplossingen voor specifieke behoeften. Bijvoorbeeld, een van onze partners die CDE met Azure Data Lake Storage (ADLS) zijn geconfigureerd. Ze maken gebruik van ADLS voor het bewaren van lange termijn gegevens en andere scenario's voor opslag op basis van koude paden, zoals batch verwerking, controle en rapportage doeleinden. 

## <a name="next-steps"></a>Volgende stappen

* Nu u over de architectuur hebt geleerd, maakt u een [app voor slimme meter gratis](https://apps.azureiotcentral.com/build/new/smart-meter-monitoring)
* Zie [IOT Central Overview](https://docs.microsoft.com/azure/iot-central/) (Engelstalig) voor meer informatie over IOT Central
