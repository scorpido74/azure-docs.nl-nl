---
title: Technologieën en oplossingen voor Azure IoT (Internet of Things)
description: Beschrijft de verzameling technologieën en services die u gebruiken om een Azure IoT-oplossing te bouwen.
author: dominicbetts
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: overview
ms.date: 01/15/2020
ms.author: dobett
ms.openlocfilehash: 0b04b5170c13f6f6c3fd74976461f03e4367060a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77046064"
---
# <a name="azure-technologies-and-services-for-creating-iot-solutions"></a>Azure-technologieën en -services voor het maken van IoT-oplossingen

Azure IoT-technologieën en -services bieden u opties om een breed scala aan IoT-oplossingen te maken die digitale transformatie voor uw organisatie mogelijk maken. U kunt bijvoorbeeld:

- Gebruik [Azure IoT Central](https://apps.azureiotcentral.com), een beheerd IoT-toepassingsplatform, om een veilige, enterprise-grade IoT-oplossing te bouwen en te implementeren. IoT Central beschikt over een verzameling branchespecifieke toepassingssjablonen, zoals retail en gezondheidszorg, om uw proces voor de ontwikkeling van oplossingen te versnellen.
- Breid de open-source codebasis uit voor een Azure [IoT-oplossingsversneller](https://www.azureiotsolutions.com) om een gemeenschappelijk IoT-scenario te implementeren, zoals bewaking op afstand of voorspellend onderhoud.
- Gebruik Azure IoT-platformservices zoals [Azure IoT Hub](../iot-hub/about-iot-hub.md) en de [Azure IoT-apparaat-SDK's](../iot-hub/iot-hub-devguide-sdks.md) om een aangepaste IoT-oplossing helemaal opnieuw te bouwen.

![Azure IoT-technologieën, -services en -oplossingen](./media/iot-services-and-technologies/iot-technologies-services.png)

## <a name="azure-iot-central"></a>Azure IoT Central

Het [IoT Central-applicatieplatform](https://apps.azureiotcentral.com) vermindert de lasten en kosten van het ontwikkelen, beheren en onderhouden van Enterprise-grade IoT-oplossingen. Met de aanpasbare web-gebruikersinterface van IoT Central u de omstandigheden van apparaten controleren, regels maken en miljoenen apparaten en hun gegevens gedurende hun hele levenscyclus beheren. Het API-oppervlak binnen IoT Central geeft u programmatische toegang tot het configureren en communiceren met uw IoT-oplossing.

Azure IoT Central is een volledig beheerd toepassingsplatform dat u gebruiken om aangepaste IoT-oplossingen te maken. IoT Central gebruikt toepassingssjablonen om oplossingen te creëren. Er zijn sjablonen voor generieke oplossingen en voor specifieke industrieën zoals energie, gezondheidszorg, overheid en detailhandel. Met IoT Central-toepassingssjablonen u een IoT Central-toepassing in enkele minuten implementeren die u vervolgens aanpassen met thema's, dashboards en weergaven.

Kies apparaten uit de [Azure Certified for IoT-apparaatcatalogus](https://catalog.azureiotsolutions.com) om snel verbinding te maken met uw oplossing. Gebruik de IoT Central-webgebruikersinterface om uw apparaten te controleren en te beheren om ze gezond en verbonden te houden. Gebruik connectors en API's om uw IoT Central-toepassing te integreren met andere zakelijke toepassingen.

Als volledig beheerd applicatieplatform heeft IoT Central een eenvoudig, voorspelbaar prijsmodel.

## <a name="azure-iot-solution-accelerators"></a>Azure IoT-oplossingsversnellers

De [Azure IoT-oplossingsversnellers](https://www.azureiotsolutions.com) zijn een verzameling aanpasbare oplossingen op bedrijfsniveau. U deze oplossingen zoals ze zijn implementeren of een aangepaste IoT-oplossing ontwikkelen met behulp van de open-source Java- of .NET-broncode.

Azure IoT-oplossingsversnellers bieden een hoog niveau van controle over uw IoT-oplossing. De oplossingsversnellers bevatten vooraf gebouwde oplossingen voor veelvoorkomende IoT-scenario's die u binnen enkele minuten implementeren op uw Azure-abonnement. De scenario's omvatten:

  - Externe bewaking
  - Verbonden factory
  - Voorspellend onderhoud
  - Apparaatsimulatie

De open-source code basis voor alle oplossingsversnellers is beschikbaar op GitHub. Download de code om een oplossingsversneller aan te passen aan uw specifieke IoT-vereisten.

De oplossingsversnellers maken gebruik van Azure-services zoals Azure IoT Hub en Azure Storage die u moet beheren in uw Azure-abonnement.

## <a name="custom-solutions"></a>Aangepaste oplossingen

Als u een IoT-oplossing helemaal opnieuw wilt bouwen of een oplossing wilt uitbreiden die is gemaakt met IoT Central of een oplossingsversneller, gebruikt u een of meer van de volgende Azure IoT-technologieën en -services:

### <a name="devices"></a>Apparaten

Ontwikkel uw IoT-apparaten met een van de [Azure IoT Starter Kits](https://catalog.azureiotsolutions.com/kits) of kies een apparaat dat u wilt gebruiken in de Azure Certified for [IoT-apparaatcatalogus.](https://catalog.azureiotsolutions.com) Implementeer uw ingesloten code met behulp van de open-source [apparaat SDKs](../iot-hub/iot-hub-devguide-sdks.md). De sdk's van het apparaat ondersteunen meerdere besturingssystemen, zoals Linux, Windows en realtime besturingssystemen. Er zijn SDK's voor meerdere programmeertalen, zoals [C,](https://github.com/Azure/azure-iot-sdk-c) [Node.js,](https://github.com/Azure/azure-iot-sdk-node) [Java,](https://github.com/Azure/azure-iot-sdk-java) [.NET](https://github.com/Azure/azure-iot-sdk-csharp)en [Python](https://github.com/Azure/azure-iot-sdk-python).

U de manier waarop u de ingesloten code voor uw apparaten maakt verder vereenvoudigen met behulp van de [IoT Plug and Play Preview-service.](../iot-pnp/overview-iot-plug-and-play.md) IoT Plug and Play stelt ontwikkelaars van oplossingen in staat om apparaten te integreren met hun oplossingen zonder ingesloten code te schrijven. De kern van IoT Plug and Play is een modelschema voor _apparaatmogelijkheden_ dat apparaatmogelijkheden beschrijft. Gebruik het apparaatcapaciteitsmodel om uw ingesloten apparaatcode te genereren en een cloudoplossing te configureren, zoals een IoT Central-toepassing.

[Met Azure IoT Edge](../iot-edge/about-iot-edge.md) u delen van uw IoT-workload van uw Azure-cloudservices naar uw apparaten laden. IoT Edge kan de latentie in uw oplossing verminderen, de hoeveelheid gegevens die uw apparaten uitwisselen met de cloud verminderen en off-line scenario's inschakelen. U IoT Edge-apparaten beheren via IoT Central en enkele oplossingsversnellers.

[Azure Sphere](https://docs.microsoft.com/azure-sphere/product-overview/what-is-azure-sphere) is een beveiligd, toepassingsplatform op hoog niveau met ingebouwde communicatie- en beveiligingsfuncties voor apparaten die met internet zijn verbonden. Het omvat een beveiligde microcontroller-eenheid, een op een aangepast Linux-gebaseerd besturingssysteem en een cloudgebaseerde beveiligingsservice die continue, hernieuwbare beveiliging biedt.

### <a name="cloud-connectivity"></a>Cloudconnectiviteit

De [Azure IoT Hub-service](../iot-hub/about-iot-hub.md) maakt betrouwbare en veilige bidirectionele communicatie mogelijk tussen miljoenen IoT-apparaten en een cloudgebaseerde oplossing. [Azure IoT Hub Device Provisioning Service](../iot-dps/about-iot-dps.md) is een helperservice voor IoT Hub. De service biedt zero-touch, just-in-time provisioning van apparaten naar de juiste IoT-hub zonder menselijke tussenkomst. Met deze mogelijkheden kunnen klanten miljoenen apparaten op een veilige en schaalbare manier inrichten.

IoT Hub is een kernonderdeel van de oplossingsversnellers en u deze gebruiken om iot-implementatieuitdagingen aan te gaan, zoals:

* Connectiviteit en beheer van grote volumes aan apparaten.
* Telemetrieopname van groot volume.
* Opdracht en besturing van apparaten.
* Afdwinging van apparaatbeveiliging.

### <a name="bridging-the-gap-between-the-physical-and-digital-worlds"></a>De kloof overbruggen tussen de fysieke en digitale wereld

[Azure Digital Twins](../digital-twins/about-digital-twins.md) is een IoT-service waarmee u een fysieke omgeving modelleren. Het maakt gebruik van een ruimtelijke intelligentie grafiek om de relaties tussen mensen, ruimten en apparaten te modelleren. Door data te coreiseren in de digitale en fysieke werelden u contextueel bewuste oplossingen creëren.

Iot Central gebruikt digitale tweelingen om apparaten en gegevens in de echte wereld te synchroniseren met de digitale modellen waarmee gebruikers deze verbonden apparaten kunnen monitoren en beheren.

### <a name="data-and-analytics"></a>Gegevens en analyse

IoT-apparaten genereren doorgaans grote hoeveelheden tijdreeksgegevens, zoals temperatuurmetingen van sensoren. [Azure Time Series Insights](../time-series-insights/time-series-insights-overview.md) kan verbinding maken met een IoT-hub, de telemetriestream van uw apparaten lezen, die gegevens opslaan en u in staat stellen deze op te vragen en te visualiseren.

[Azure Maps](/azure/azure-maps) is een verzameling georuimtelijke services die nieuwe toewijzingsgegevens gebruiken om nauwkeurige geografische context te bieden aan web- en mobiele toepassingen. U een REST-API, een javascript-besturingselement op het web of een Android SDK gebruiken om uw toepassingen te bouwen.

## <a name="next-steps"></a>Volgende stappen

Voor een hands-on ervaring, probeer een van de quickstarts:

- [Een Azure IoT Central-toepassing maken](../iot-central/core/quick-deploy-iot-central.md)
- [Telemetrie verzenden van een apparaat naar een IoT-hub](../iot-hub/quickstart-send-telemetry-cli.md)
- [Een externe bewakingsoplossing in de cloud proberen](../iot-accelerators/quickstart-remote-monitoring-deploy.md)
