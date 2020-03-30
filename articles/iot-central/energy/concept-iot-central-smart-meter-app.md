---
title: Architecturale concepten in Azure IoT Central - Energie | Microsoft Documenten
description: In dit artikel worden belangrijke concepten geïntroduceerd met betrekking tot de architectuur van azure IoT Central-sjabloon voor energie-apps
author: op-ravi
ms.author: omravi
ms.date: 10/22/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 8f3772c1d65780337c421cfaaa7b70d7ac7186cf
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77024310"
---
# <a name="azure-iot-central---smart-meter-app-architecture"></a>Azure IoT Central - smart meter app-architectuur



In dit artikel vindt u een overzicht van de sjabloonarchitectuur voor het bewaken van slimme meters. Het onderstaande diagram toont een veelgebruikte architectuur voor slimme meter-app op Azure met behulp van het IoT Central-platform.

> [!div class="mx-imgBorder"]
> ![slimme meterarchitectuur](media/concept-iot-central-smart-meter/smart-meter-app-architecture.png)

Deze architectuur bestaat uit de volgende onderdelen. Voor sommige oplossingen is mogelijk niet elk onderdeel vereist dat hier wordt vermeld.

## <a name="smart-meters-and-connectivity"></a>Slimme meters en connectiviteit 

Een slimme meter is een van de belangrijkste apparaten onder alle energie-activa. Het registreert en communiceert gegevens over energieverbruik aan nutsbedrijven voor monitoring en andere use cases, zoals facturering en vraagrespons. Op basis van het metertype kan het verbinding maken met IoT Central via gateways of andere tussenliggende apparaten of systemen, zoals edge-apparaten en head-end systemen. Bouw IoT Central device bridge om apparaten aan te sluiten, die niet direct kunnen worden aangesloten. De IoT Central device bridge is een open-source oplossing en u vindt [hier](https://docs.microsoft.com/azure/iot-central/core/howto-build-iotc-device-bridge)de volledige details. 


## <a name="iot-central-platform"></a>IoT Centraal-platform

Azure IoT Central is een platform dat het bouwen van uw IoT-oplossing vereenvoudigt en helpt de lasten en kosten van IoT-beheer, -bewerkingen en -ontwikkeling te verminderen. Met IoT Central u eenvoudig uw IoT-assets (Internet of Things) op schaal verbinden, bewaken en beheren. Nadat u uw slimme meters hebt aangesloten op IoT Central, maakt de app-sjabloon gebruik van ingebouwde functies zoals apparaatmodellen, opdrachten en dashboards. De app-sjabloon maakt ook gebruik van de IoT Central-opslag voor scenario's voor warme paden, zoals near real-time metergegevensbewaking, analyse, regels en visualisatie. 


## <a name="extensibility-options-to-build-with-iot-central"></a>Uitbreidbaarheidsopties om te bouwen met IoT Central
Het IoT Central-platform biedt twee uitbreidbaarheidsopties: Continuous Data Export (CDE) en API's. De klanten en partners kunnen kiezen tussen deze opties op basis van hun oplossingen aan te passen aan specifieke behoeften. Een van onze partners heeft bijvoorbeeld CDE geconfigureerd met Azure Data Lake Storage (ADLS). Ze gebruiken ADLS voor het bewaren van gegevens op lange termijn en andere scenario's voor het opslaan van koudpaden, zoals batchverwerking, auditing en rapportagedoeleinden. 

## <a name="next-steps"></a>Volgende stappen

* Nu je meer hebt geleerd over de architectuur, [maak je gratis een slimme meter-app](https://apps.azureiotcentral.com/build/new/smart-meter-monitoring)
* Zie [IoT Central-overzicht](https://docs.microsoft.com/azure/iot-central/) voor meer informatie over IoT Central
