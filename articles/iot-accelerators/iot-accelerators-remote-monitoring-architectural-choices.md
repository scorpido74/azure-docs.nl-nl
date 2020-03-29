---
title: Architecturale architecturale keuzes voor externe bewaking - Azure | Microsoft Documenten
description: Dit artikel beschrijft de architecturale en technische keuzes die zijn gemaakt in Remote Monitoring
author: timlaverty
manager: camerons
ms.author: timlav
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/20/2018
ms.topic: conceptual
ms.openlocfilehash: 1bd08596a30db7322a72b4269fddfe0b9df19119
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61447167"
---
# <a name="remote-monitoring-architectural-choices"></a>Architectuurkeuzes voor Externe bewaking

De Azure IoT Remote Monitoring solution accelerator is een open-source, MIT gelicentieerde, oplossingversneller. Om u te helpen uw IoT-ontwikkelingsproces te versnellen, worden veelvoorkomende IoT-scenario's weergegeven, zoals:

- Connectiviteit van apparaten
- Apparaatbeheer
- Streamverwerking

De oplossing voor externe bewaking volgt de aanbevolen [Azure IoT-referentiearchitectuur.](https://aka.ms/iotrefarchitecture)

In dit artikel worden de belangrijkste architecturale en technische keuzes beschreven die in elk van de subsystemen voor bewaking op afstand zijn gemaakt. De technische keuzes die Microsoft heeft gemaakt in de remote monitoring-oplossing zijn echter niet de enige manier om een IoT-oplossing voor externe monitoring te implementeren. U moet de technische implementatie beschouwen als een basislijn voor het bouwen van een succesvolle toepassing en u moet deze wijzigen in:

- Passend bij de beschikbare vaardigheden en ervaring in uw organisatie.
- Voldoe aan uw verticale toepassingsbehoeften.

## <a name="architectural-choices"></a>Architectuurkeuzes

De architectuur die Microsoft aanbeveelt voor een IoT-toepassing is cloud native, microservice en serverless based. U moet de verschillende subsystemen van een IoT-toepassing bouwen als discrete services die u onafhankelijk implementeren en schalen. Deze kenmerken maken een grotere schaal, meer flexibiliteit bij het bijwerken van afzonderlijke subsystemen mogelijk en bieden de flexibiliteit om voor elk subsysteem een geschikte technologie te kiezen.

U microservices implementeren met behulp van meer dan één technologie. U bijvoorbeeld een van de volgende opties kiezen om een microservice te implementeren:

- Gebruik een containertechnologie zoals Docker met serverloze technologie zoals Azure-functies.
- Host uw microservices in PaaS-services, zoals Azure App Services.

## <a name="technology-choices"></a>Technologieopties

In dit gedeelte worden de technologische keuzes beschreven die zijn gemaakt in de oplossing voor externe bewaking voor elk van de kernsubsystemen.

![Kerndiagram](./media/iot-accelerators-remote-monitoring-architectural-choices/subsystem.png)

### <a name="cloud-gateway"></a>Cloudgateway

Azure IoT Hub wordt gebruikt als cloudgateway voor externe monitoringoplossingen. [IoT Hub](https://azure.microsoft.com/services/iot-hub/) biedt veilige, bidirectionele communicatie met apparaten.

Voor IoT-apparaatconnectiviteit u het:

- De [SDK's van het IoT Hub-apparaat](../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks) om een native clienttoepassing voor uw apparaat te implementeren. De SDK's bieden wrappers rond de IoT Hub REST API en behandelen scenario's zoals nieuwe pogingen.
- De integratie met Azure IoT Edge voor het implementeren en beheren van aangepaste modules die worden uitgevoerd in containers op uw apparaten.
- De integratie met automatisch apparaatbeheer in IoT Hub om verbonden apparaten in bulk te beheren.

### <a name="stream-processing"></a>Streamverwerking

Voor streamverwerking gebruikt de oplossing Voor externe bewaking Azure Stream Analytics voor complexe regelverwerking. Als u eenvoudigere regels wilt gebruiken, is er een aangepaste microservice met ondersteuning voor eenvoudige regelverwerking, hoewel deze set-up geen deel uitmaakt van de kant-en-klare implementatie. De referentiearchitectuur beveelt Azure-functies aan voor eenvoudige regelverwerking en Azure Stream Analytics voor complexe regelverwerking.

### <a name="storage"></a>Storage

Voor opslag maakt de versneller voor externe bewaking-oplossingen gebruik van zowel Azure Time Series Insights als Azure Cosmos DB. Azure Time Series Insights slaat de berichten op die via IoT Hub van uw verbonden apparaten worden verzonden. De oplossingsversneller gebruikt Azure Cosmos DB voor alle andere opslag, zoals koude opslag, definities van regels, waarschuwingen en configuratie-instellingen.

Azure Cosmos DB is de aanbevolen warme opslagoplossing voor algemene doeleinden voor IoT-toepassingen. Oplossingen zoals Azure Time Series Insights en Azure Data Lake zijn echter geschikt voor veel use cases. Met Azure Time Series Insights u dieper inzicht krijgen in uw sensorgegevens uit de tijdreeks door trends en afwijkingen te detecteren. Met deze functie u root-cause analyses uitvoeren en kostbare downtime voorkomen.

> [!NOTE]
> Time Series Insights is momenteel niet beschikbaar in de Azure China-cloud. Nieuwe remote monitoring oplossing sversneller implementaties in de Azure China cloud gebruik Cosmos DB voor alle opslag.

### <a name="business-integration"></a>Bedrijfsintegratie

Bedrijfsintegratie in de Remote Monitoring-oplossing is beperkt tot het genereren van waarschuwingen, die in warme opslag worden geplaatst. Verbind de oplossing met Azure Logic Apps om diepere scenario's voor bedrijfsintegratie te implementeren.

### <a name="user-interface"></a>Gebruikersinterface

De web-gebruikersinterface is gebouwd met JavaScript React. React biedt een veelgebruikte industrie web UI framework en is vergelijkbaar met andere populaire frameworks zoals Angular.

### <a name="runtime-and-orchestration"></a>Runtime en orkestratie

De oplossing Voor externe bewaking maakt gebruik van Docker-containers om de subsystemen met Kubernetes uit te voeren als orchestrator voor horizontale schaal. Deze architectuur maakt individuele schaaldefinities voor elk subsysteem mogelijk. Deze architectuur brengt echter Kosten met zich mee om de virtuele machines en containers up-to-date en veilig te houden.

Alternatieven voor Docker zijn het hosten van microservices in PaaS-services, zoals Azure App Service. Alternatieven voor Kubernetes zijn orchestrators zoals Service Fabric, DC/OS of Swarm.

## <a name="next-steps"></a>Volgende stappen

* Implementeer [hier](https://www.azureiotsolutions.com/)uw oplossing voor externe bewaking.
* Ontdek GitHub-code in [C#](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) en [Java.](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java/)  
* Lees [hier](https://aka.ms/iotrefarchitecture)meer over de IoT-referentiearchitectuur.
