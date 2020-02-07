---
title: Technologieën en oplossingen voor Azure IoT (Internet of Things)
description: Beschrijft de verzameling technologieën en services die u kunt gebruiken om een Azure IoT-oplossing te bouwen.
author: dominicbetts
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: overview
ms.date: 01/15/2020
ms.author: dobett
ms.openlocfilehash: 0b04b5170c13f6f6c3fd74976461f03e4367060a
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/06/2020
ms.locfileid: "77046064"
---
# <a name="azure-technologies-and-services-for-creating-iot-solutions"></a>Azure-technologieën en-services voor het maken van IoT-oplossingen

Azure IoT-technologieën en-services bieden u opties voor het maken van een breed scala aan IoT-oplossingen waarmee digitale trans formatie voor uw organisatie mogelijk wordt. U kunt bijvoorbeeld:

- Gebruik [Azure IOT Central](https://apps.azureiotcentral.com), een beheerd IOT-toepassings platform, voor het bouwen en implementeren van een veilige IOT-oplossing op bedrijfs niveau. IoT Central bevat een verzameling branchespecifieke toepassings sjablonen, zoals de detail handel en gezondheids zorg, om uw oplossings ontwikkelings proces te versnellen.
- Breid de open-source code base uit voor een Azure IoT- [oplossings versneller](https://www.azureiotsolutions.com) voor het implementeren van een veelvoorkomend IOT-scenario, zoals externe controle of voor speld onderhoud.
- Gebruik Azure IoT-platform services zoals [azure IOT hub](../iot-hub/about-iot-hub.md) en de [Sdk's van het Azure IOT-apparaat](../iot-hub/iot-hub-devguide-sdks.md) om een aangepaste IOT-oplossing te bouwen.

![Azure IoT-technologieën,-services en-oplossingen](./media/iot-services-and-technologies/iot-technologies-services.png)

## <a name="azure-iot-central"></a>Azure IoT Central

Het [IOT Central toepassings platform](https://apps.azureiotcentral.com) vermindert de belasting en de kosten voor het ontwikkelen, beheren en onderhouden van IOT-oplossingen op bedrijfs niveau. Met de aanpas bare Web-UI van IoT Central in kunt u de voor waarden van apparaten bewaken, regels maken en miljoenen apparaten en hun gegevens in hun levens cyclus beheren. Het API-Opper vlak in IoT Central biedt u programmatische toegang om uw IoT-oplossing te configureren en te gebruiken.

Azure IoT Central is een volledig beheerd toepassings platform dat u kunt gebruiken om aangepaste IoT-oplossingen te maken. IoT Central gebruikt toepassings sjablonen om oplossingen te maken. Er zijn sjablonen voor algemene oplossingen en voor specifieke branches, zoals energie, gezondheids zorg, overheid en verkoop. Met IoT Central toepassings sjablonen kunt u in slechts enkele minuten een IoT Central-toepassing implementeren die u vervolgens met behulp van Thema's, Dash boards en weer gaven aanpassen.

Kies apparaten in de [Azure Certified voor IOT-Apparaatbeheer](https://catalog.azureiotsolutions.com) om snel verbinding te maken met uw oplossing. Gebruik de webinterface van IoT Central om uw apparaten te controleren en beheren om ze gezond en verbonden te houden. Gebruik connectors en Api's om uw IoT Central-toepassing te integreren met andere zakelijke toepassingen.

Als volledig beheerd toepassings platform heeft IoT Central een eenvoudig, voorspelbaar prijs model.

## <a name="azure-iot-solution-accelerators"></a>Azure IoT-oplossingsversnellers

De [Azure IOT-oplossings versnellers](https://www.azureiotsolutions.com) zijn een verzameling aanpas bare oplossingen voor bedrijfs kwaliteit. U kunt deze oplossingen implementeren zoals ze zijn, of een aangepaste IoT-oplossing ontwikkelen met behulp van de open-source Java-of .NET-bron code.

Azure IoT-oplossings Accelerators bieden een hoog niveau van controle over uw IoT-oplossing. De oplossings Accelerators bevatten vooraf ontwikkelde oplossingen voor veelvoorkomende IoT-scenario's die u binnen enkele minuten kunt implementeren in uw Azure-abonnement. De scenario's zijn onder andere:

  - Externe bewaking
  - Verbonden factory
  - Voorspellend onderhoud
  - Apparaatsimulatie

De open source code base voor alle oplossings versnellers is beschikbaar op GitHub. Down load de code om een oplossings versneller aan te passen om te voldoen aan uw specifieke IoT-vereisten.

De oplossings Accelerators gebruiken Azure-Services zoals Azure IoT Hub en Azure Storage die u moet beheren in uw Azure-abonnement.

## <a name="custom-solutions"></a>Aangepaste oplossingen

Gebruik een of meer van de volgende Azure IoT-technologieën en-services om een volledig nieuwe IoT-oplossing te bouwen of een oplossing uit te breiden die is gemaakt met IoT Central of een oplossings versneller:

### <a name="devices"></a>Apparaten

Ontwikkel uw IoT-apparaten met behulp van een van de [Azure IOT-starter kits](https://catalog.azureiotsolutions.com/kits) of kies een apparaat dat u wilt gebruiken vanuit de [Azure Certified voor IOT-Apparaatbeheer](https://catalog.azureiotsolutions.com). Implementeer uw Inge sloten code met behulp van de open-source- [apparaat-sdk's](../iot-hub/iot-hub-devguide-sdks.md). De Sdk's van het apparaat ondersteunen meerdere besturings systemen, zoals Linux, Windows en real-time besturings systemen. Er zijn sdk's voor meerdere programmeer talen, zoals [C](https://github.com/Azure/azure-iot-sdk-c), [node. js](https://github.com/Azure/azure-iot-sdk-node), [Java](https://github.com/Azure/azure-iot-sdk-java), [.net](https://github.com/Azure/azure-iot-sdk-csharp)en [python](https://github.com/Azure/azure-iot-sdk-python).

U kunt verder gaan met het maken van de Inge sloten code voor uw apparaten met behulp van de [IoT Plug en Play preview](../iot-pnp/overview-iot-plug-and-play.md) -service. Met IoT Plug en Play kunnen ontwikkel aars van oplossingen apparaten met hun oplossingen integreren zonder Inge sloten code te hoeven schrijven. De kern van IoT Plug en Play is een schema voor het _mogelijkheidsprofiel_ dat de mogelijkheden van een apparaat beschrijft. Gebruik het functionaliteits model van het apparaat om uw Inge sloten apparaatcode te genereren en een Cloud oplossing te configureren, zoals een IoT Central-toepassing.

Met [Azure IOT Edge](../iot-edge/about-iot-edge.md) kunt u delen van uw IOT-werk belasting van uw Azure-Cloud Services naar uw apparaten overzetten. IoT Edge kan de latentie in uw oplossing verminderen, de hoeveelheid gegevens die door uw apparaten worden uitgewisseld met de Cloud verminderen en offline scenario's inschakelt. U kunt IoT Edge apparaten beheren vanuit IoT Central en enkele oplossings versnellers.

[Azure Sphere](https://docs.microsoft.com/azure-sphere/product-overview/what-is-azure-sphere) is een beveiligd toepassings platform op hoog niveau met ingebouwde communicatie-en beveiligings functies voor apparaten met een Internet verbinding. Het bevat een beveiligde micro controller-eenheid, een aangepast Linux-besturings systeem en een cloud-gebaseerde beveiligings service die continue, Hernieuw bare beveiliging biedt.

### <a name="cloud-connectivity"></a>Cloud connectiviteit

Met de [Azure IOT hub](../iot-hub/about-iot-hub.md) -service kunt u betrouw bare en veilige bidirectionele communicatie tussen miljoenen IOT-apparaten en een Cloud oplossing. [Azure IOT hub Device Provisioning Service](../iot-dps/about-iot-dps.md) is een helper-Service voor IOT hub. De service biedt Zero-Touch, just-in-time inrichting van apparaten aan de juiste IoT-hub zonder menselijke tussen komst. Met deze mogelijkheden kunnen klanten miljoenen apparaten op een veilige en schaal bare manier inrichten.

IoT Hub is een kern onderdeel van de oplossings accelerators en u kunt het gebruiken om te voldoen aan de uitdagingen voor de implementatie van IoT, zoals:

* Connectiviteit en beheer van grote volumes aan apparaten.
* Telemetrieopname van groot volume.
* Opdracht en besturing van apparaten.
* Afdwinging van apparaatbeveiliging.

### <a name="bridging-the-gap-between-the-physical-and-digital-worlds"></a>De tussen ruimte tussen de fysieke en digitale werelden

[Azure Digital apparaatdubbels](../digital-twins/about-digital-twins.md) is een IOT-service waarmee u een fysieke omgeving kunt model leren. Er wordt een ruimtelijke informatie grafiek gebruikt om de relaties tussen personen, ruimten en apparaten te model leren. Door informatie over de digitale en fysieke werelden te hebben, kunt u contextuele bewuste oplossingen maken.

IOT Central maakt gebruik van digitale apparaatdubbels om apparaten en gegevens in de echte wereld te synchroniseren met de digitale modellen waarmee gebruikers deze aangesloten apparaten kunnen bewaken en beheren.

### <a name="data-and-analytics"></a>Gegevens en analyse

IoT-apparaten genereren doorgaans grote hoeveel heden tijdreeks gegevens, zoals temperatuur metingen van Sens oren. [Azure time series Insights](../time-series-insights/time-series-insights-overview.md) kunt verbinding maken met een IOT-hub, de telemetrie-stroom van uw apparaten lezen, die gegevens opslaan en u in staat stellen om query's uit te voeren en te visualiseren.

[Azure Maps](/azure/azure-maps) is een verzameling georuimtelijke services die gebruikmaken van nieuwe toewijzings gegevens om de juiste geografische context te bieden voor web-en mobiele toepassingen. U kunt een REST API, een webgebaseerd java script-besturings element of een Android-SDK gebruiken om uw toepassingen te bouwen.

## <a name="next-steps"></a>Volgende stappen

Probeer een van de Quick starts voor een praktijk ervaring:

- [Een Azure IoT Central-toepassing maken](../iot-central/core/quick-deploy-iot-central.md)
- [Telemetrie van een apparaat naar een IoT-hub verzenden](../iot-hub/quickstart-send-telemetry-cli.md)
- [Een externe bewakingsoplossing in de cloud uitproberen](../iot-accelerators/quickstart-remote-monitoring-deploy.md)
