---
title: Architecturale concepten in Azure IoT Central - Energie | Microsoft Documenten
description: In dit artikel worden belangrijke concepten geïntroduceerd met betrekking tot de architectuur van Azure IoT Central
author: op-ravi
ms.author: omravi
ms.date: 10/23/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 44171a08e69cfa058e0a9e75e3220fb996b7789d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77018003"
---
# <a name="azure-iot-central---solar-panel-app-architecture"></a>Azure IoT Central - architectuur voor zonnepanelen-apps




Dit artikel geeft een overzicht van de sjabloonarchitectuur voor het monitoren van zonnepanelen. Het onderstaande diagram toont een veelgebruikte architectuur voor een zonnepaneel-app op Azure met behulp van het IoT Central-platform.

> [!div class="mx-imgBorder"]
> ![slimme meterarchitectuur](media/concept-iot-central-solar-panel/solar-panel-app-architecture.png)

Deze architectuur bestaat uit de volgende onderdelen. Voor sommige toepassingen zijn mogelijk niet alle hier vermelde onderdelen nodig.

## <a name="solar-panels-and-connectivity"></a>Zonnepanelen en connectiviteit 

Zonnepanelen zijn een van de belangrijke bronnen van hernieuwbare energie. Afhankelijk van het type zonnepaneel en het instellen, u het aansluiten met behulp van gateways of andere tussenliggende apparaten en eigen systemen. Mogelijk moet u IoT Central device bridge bouwen om apparaten aan te sluiten, die niet rechtstreeks kunnen worden aangesloten. De IoT Central device bridge is een open-source oplossing en u vindt [hier](https://docs.microsoft.com/azure/iot-central/core/howto-build-iotc-device-bridge)de volledige details. 



## <a name="iot-central-platform"></a>IoT Centraal-platform
Azure IoT Central is een platform dat het bouwen van uw IoT-oplossing vereenvoudigt en helpt de lasten en kosten van IoT-beheer, -bewerkingen en -ontwikkeling te verminderen. Met IoT Central u eenvoudig uw IoT-assets (Internet of Things) op schaal verbinden, bewaken en beheren. Nadat u uw zonnepanelen hebt aangesloten op IoT Central, maakt de app-sjabloon gebruik van ingebouwde functies zoals apparaatmodellen, opdrachten en dashboards. De app-sjabloon maakt ook gebruik van de IoT Central-opslag voor scenario's voor warme paden, zoals near real-time metergegevensbewaking, analyse, regels en visualisatie.


## <a name="extensibility-options-to-build-with-iot-central"></a>Uitbreidbaarheidsopties om te bouwen met IoT Central
Het IoT Central-platform biedt twee uitbreidbaarheidsopties: Continuous Data Export (CDE) en API's. De klanten en partners kunnen kiezen tussen deze opties op basis van hun oplossingen aan te passen aan specifieke behoeften. Een van onze partners heeft bijvoorbeeld CDE geconfigureerd met Azure Data Lake Storage (ADLS). Ze gebruiken ADLS voor langetermijnscenario's voor gegevensbewaring en andere cold path-opslagscenario's, zoals batchverwerking, auditing en rapportagedoeleinden. 

## <a name="next-steps"></a>Volgende stappen

* Nu je hebt geleerd over de architectuur, [maak zonnepaneel app gratis](https://apps.azureiotcentral.com/build/new/solar-panel-monitoring)
* Zie [IoT Central-overzicht](https://docs.microsoft.com/azure/iot-central/) voor meer informatie over IoT Central