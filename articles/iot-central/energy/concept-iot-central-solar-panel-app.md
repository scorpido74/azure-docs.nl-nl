---
title: Architectuur concepten in azure IoT Central-energie | Microsoft Docs
description: In dit artikel worden de belangrijkste concepten besproken die betrekking hebben op de architectuur van Azure IoT Central
author: op-ravi
ms.author: omravi
ms.date: 10/23/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 44171a08e69cfa058e0a9e75e3220fb996b7789d
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77018003"
---
# <a name="azure-iot-central---solar-panel-app-architecture"></a>Architectuur van Azure IoT Central-zonne paneel-app




Dit artikel bevat een overzicht van de architectuur van de sjabloon voor het bewaken van het zonne deel venster. In het onderstaande diagram ziet u een veelgebruikte architectuur voor een zonne-panel toepassing op Azure met behulp van IoT Central-platform.

> [!div class="mx-imgBorder"]
> architectuur van de ![slimme meter](media/concept-iot-central-solar-panel/solar-panel-app-architecture.png)

Deze architectuur bestaat uit de volgende onderdelen. Voor sommige toepassingen zijn mogelijk niet alle hier vermelde onderdelen nodig.

## <a name="solar-panels-and-connectivity"></a>Zonne panelen en connectiviteit 

Zonne panelen zijn een van de aanzienlijke bronnen van Hernieuw bare energie. Afhankelijk van het type zonne paneel en instellen, kunt u het verbinden met behulp van gateways of andere tussenliggende apparaten en eigen systemen. Mogelijk moet u IoT Central apparaats brug bouwen om apparaten te verbinden die niet rechtstreeks kunnen worden aangesloten. De IoT Central Device Bridge is een open-source oplossing en u vindt [hier](https://docs.microsoft.com/azure/iot-central/core/howto-build-iotc-device-bridge)de volledige informatie. 



## <a name="iot-central-platform"></a>IoT Central platform
Azure IoT Central is een platform waarmee u uw IoT-oplossing eenvoudiger maakt en de overhead en kosten van IoT-beheer,-bewerkingen en-ontwikkeling kunt verlagen. Met IoT Central kunt u eenvoudig uw Internet of Things (IoT)-assets op schaal aansluiten, bewaken en beheren. Nadat u uw zonne panels hebt verbonden met IoT Central, gebruikt de app-sjabloon ingebouwde functies zoals modellen, opdrachten en dash boards. In de app-sjabloon wordt ook gebruikgemaakt van de IoT Central opslag voor scenario's voor warme paden zoals bijna realtime meter gegevens bewaking, analyses, regels en visualisatie.


## <a name="extensibility-options-to-build-with-iot-central"></a>Uitbreidings opties om met IoT Central te bouwen
Het IoT Central-platform biedt twee uitbreidings opties: voortdurende gegevens export (COB) en Api's. De klanten en partners kunnen kiezen uit deze opties op basis van het aanpassen van hun oplossingen voor specifieke behoeften. Bijvoorbeeld, een van onze partners die CDE met Azure Data Lake Storage (ADLS) zijn geconfigureerd. Ze maken gebruik van ADLS voor het bewaren van lange termijn gegevens en andere scenario's voor opslag op basis van koude paden, zoals batch verwerking, controle en rapportage doeleinden. 

## <a name="next-steps"></a>Volgende stappen

* Nu u over de architectuur hebt geleerd, kunt u de [app voor een zonne-panel gratis maken](https://apps.azureiotcentral.com/build/new/solar-panel-monitoring)
* Zie [IOT Central Overview](https://docs.microsoft.com/azure/iot-central/) (Engelstalig) voor meer informatie over IOT Central