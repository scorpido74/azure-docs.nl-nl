---
title: Inleiding tot IoT-oplossingsversnellers - Azure | Microsoft Docs
description: Hier vindt u informatie over Azure IoT-oplossingsversnellers. IoT-oplossingsversnellers zijn volledige, end-to-end, kant-en-klare IoT-oplossingen.
author: dominicbetts
ms.author: dobett
ms.date: 03/09/2019
ms.topic: overview
ms.custom: mvc
ms.service: iot-accelerators
services: iot-accelerators
manager: timlt
ms.openlocfilehash: 1a27d748e16f892a748cf18569c13ca3f9ead1dd
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "71309521"
---
# <a name="what-are-azure-iot-solution-accelerators"></a>Wat zijn Azure IoT-oplossingsversnellers?

Een IoT-cloudoplossing maakt doorgaans gebruik van aangepaste code en cloudservices om connectiviteit, gegevensverwerking en analyses en presentatie van apparaten te beheren.

De IoT-oplossingsverbeteringen zijn complete, eenvoudig te implementeren IoT-oplossingen waarmee veelvoorkomende IoT-scenario's kunnen worden geïmplementeerd. De scenario's zijn onder meer externe bewaking, verbonden factory, predictief onderhoud en apparaatsimulatie. Wanneer u een oplossingsversneller implementeert, bevat de implementatie alle vereiste cloudservices samen met de vereiste toepassingscode.

De oplossingsversnellers vormen het startpunt voor uw eigen IoT-oplossingen. De broncode voor alle oplossingsversnellers is open-source en beschikbaar in GitHub. U wordt aangeraden om de oplossingsverbeteringen te downloaden en aan te passen zodat ze voldoen aan uw vereisten.

U kunt de oplossingsversnellers ook gebruiken als leermiddelen voordat u helemaal uw eigen IoT-oplossing gaat maken. De oplossingsversnellers implementeren bewezen procedures voor IoT-cloudoplossingen die u kunt volgen.

De toepassingscode in elke oplossingsverbetering bevat een web-app waarmee u de oplossingsverbetering beheren.

## <a name="supported-iot-scenarios"></a>Ondersteunde IoT-scenario's

Er zijn momenteel vier oplossingsversnellers beschikbaar die u kunt implementeren:

### <a name="remote-monitoring"></a>Externe bewaking

Gebruik de [verbetering voor de externe bewakingsoplossing](iot-accelerators-remote-monitoring-sample-walkthrough.md) voor het verzamelen van telemetriegegevens van externe apparaten en om deze te beheren. Voorbeelden zijn koelingssystemen die zijn geïnstalleerd op de locatie van uw klanten of kleppen die zijn geïnstalleerd in externe pompstations.

U kunt het dashboard voor externe controle gebruiken om de telemetrie van verbonden apparaten te bekijken, nieuwe apparaten in te richten of de firmware op verbonden apparaten bij te werken:

[![Dash board van de oplossing voor externe controle](./media/about-iot-accelerators/rm-dashboard-inline.png)](./media/about-iot-accelerators/rm-dashboard-expanded.png#lightbox)

### <a name="connected-factory"></a>Verbonden factory

Gebruik de [Connected Factory-oplossingsversneller](iot-accelerators-connected-factory-features.md) voor het verzamelen van telemetriegegevens van industriële activa met een [OPC Unified Architecture](https://opcfoundation.org/about/opc-technologies/opc-ua/)-interface en om deze activa te beheren. Industriële activa kunnen assembly- en teststations in een productielijn bevatten.

U kunt de verbonden factory gebruiken om industriële apparaten te controleren en te beheren:

[![Dash board Connected Factory-oplossing](./media/about-iot-accelerators/cf-dashboard-inline.png)](./media/about-iot-accelerators/cf-dashboard-expanded.png#lightbox)

### <a name="predictive-maintenance"></a>Predictief onderhoud

Gebruik de [Predictive Maintenance-oplossingsversneller](iot-accelerators-predictive-walkthrough.md) om te voorspellen wanneer een extern apparaat naar verwachting defect raakt, zodat u onderhoud kunt plegen voordat het apparaat uitvalt. De oplossingsversneller maakt gebruik van machine learning-algoritmen om fouten op basis van telemetriegegevens van apparaten te voorspellen. Voorbeelden van dergelijke apparaten zijn vliegtuigmotoren en liften.

U kunt het dashboard voor predictief onderhoud gebruiken om de analyse voor predictief onderhoud te bekijken:

[![Dash board Connected Factory-oplossing](./media/about-iot-accelerators/pm-dashboard-inline.png)](./media/about-iot-accelerators/pm-dashboard-expanded.png#lightbox)

### <a name="device-simulation"></a>Apparaatsimulatie

Gebruik de [Device Simulation-oplossingsversneller](iot-accelerators-device-simulation-overview.md) voor het laten draaien van gesimuleerde apparaten die realistische telemetriegegevens genereren. U kunt deze oplossingsversneller gebruiken voor het testen van het gedrag van de andere oplossingsversnellers of voor het testen van uw eigen aangepaste IoT-oplossingen.

U kunt de web-app voor apparaatsimulatie gebruiken om simulaties te configureren en uit te voeren:

[![Dash board Connected Factory-oplossing](./media/about-iot-accelerators/ds-dashboard-inline.png)](./media/about-iot-accelerators/ds-dashboard-expanded.png#lightbox)

## <a name="design-principles"></a>Ontwerpprincipes

Alle oplossingsversnellers volgen dezelfde ontwerpprincipes en -doelen. Het ontwerp ervan is:

* **Schaalbaar**, zodat u verbinding kunt maken met miljoenen verbonden apparaten en deze kunt beheren.
* **Uitbreidbaar**, zodat u ze kunt aanpassen aan uw behoeften.
* **Begrijpelijk**, zodat u precies weet hoe ze werken en hoe ze worden geïmplementeerd.
* **Modulair**, waardoor u services voor alternatieven kunt verwisselen.
* **Veilig**, vanwege de combinatie van Azure-beveiliging met ingebouwde functies voor connectiviteit en apparaatbeveiliging.

## <a name="architectures-and-languages"></a>Architecturen en talen

De oorspronkelijke oplossingsversnellers werden geschreven met behulp van .NET en een model-view-controller (MVC)-architectuur. Microsoft werkt de oplossingsversnellers bij met een nieuwe architectuur op basis van microservices. In de volgende tabel wordt de huidige status van de oplossingsverbeteringen weergegeven met koppelingen naar de GitHub-opslagplaatsen:

| Oplossingsverbetering   | Architectuur  | Talen     |
| ---------------------- | ------------- | ------------- |
| Externe bewaking      | Microservices | [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java) en [.net](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet) |
| Predictief onderhoud | MVC           | [.NET](https://github.com/Azure/azure-iot-predictive-maintenance)          |
| Verbonden factory      | MVC           | [.NET](https://github.com/Azure/azure-iot-connected-factory)          |
| Apparaatsimulatie      | Microservices | [.NET](https://github.com/Azure/device-simulation-dotnet)          |

Zie [Inleiding tot de Azure IoT-referentiearchitectuur](https://docs.microsoft.com/azure/architecture/reference-architectures/iot/) voor meer informatie over de microservicearchitectuur.

## <a name="deployment-options"></a>Implementatieopties

U kunt de oplossingsverbeteringen implementeren op de site [Microsoft Azure IoT-oplossingsverbeteringen](https://www.azureiotsolutions.com/Accelerators#) of door gebruik te maken van de opdrachtregel.

U kunt de verbetering voor de externe bewakingsoplossing implementeren in de volgende configuraties:

* **Standard:** uitgebreide infrastructuurimplementatie voor het ontwikkelen van een productie-implementatie. De Azure Container Service implementeert de microservices in diverse virtuele Azure-machines. Kubernetes deelt de Docker-containers in die de afzonderlijke microservices hosten.
* **Basic:** voordelige versie voor een demonstratie of het testen van een implementatie. Alle microservices worden geïmplementeerd op een enkele virtuele Azure-machine.
* **Local:** implementatie op lokale computer voor testen en ontwikkeling. Bij deze aanpak worden de microservices geïmplementeerd op een lokale Docker-container die verbinding maakt met IoT Hub, Azure Cosmos DB en Azure-opslagservices in de cloud.

De kosten voor het uitvoeren van een oplossingsversneller zijn de gecombineerde [kosten van de onderliggende Azure-services](https://azure.microsoft.com/pricing). U ziet details van de gebruikte Azure-services wanneer u uw implementatieopties kiest.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de quickstarts om een van de oplossingsverbeteringen uit te proberen:

* [Een externe bewakingsoplossing uitproberen](quickstart-remote-monitoring-deploy.md)
* [Een oplossing voor een verbonden fabriek uitproberen](quickstart-connected-factory-deploy.md)
* [Een oplossing voor predictief onderhoud uitproberen](quickstart-predictive-maintenance-deploy.md)
* [Een oplossing voor apparaatsimulatie uitproberen](quickstart-device-simulation-deploy.md)
