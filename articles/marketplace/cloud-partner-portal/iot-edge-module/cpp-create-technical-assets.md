---
title: Technische activa van Azure IoT Edge module maken | Azure Marketplace
description: Maak de technische assets voor een IoT Edge module.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: pabutler
ms.openlocfilehash: 57bc2f789836a7d3453004cdacc59029c4b24129
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827624"
---
# <a name="prepare-your-iot-edge-module-technical-assets"></a>De technische activa van uw IoT Edge module voorbereiden

In dit artikel worden de vereisten beschreven waaraan de technische activa van uw IoT Edge-module moeten voldoen voordat ze kunnen worden gepubliceerd op Azure Marketplace.

## <a name="understanding-iot-edge-modules-and-getting-started"></a>Informatie over IoT Edge modules en aan de slag

Een IoT Edge module is een docker-compatibele container die wordt uitgevoerd op een IoT Edge apparaat.

- Zie [Azure IOT Edge modules begrijpen](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules)voor meer informatie over IOT Edge-modules.
- Zie [vereisten en hulpprogram ma's voor het ontwikkelen van IOT Edge modules](https://docs.microsoft.com/azure/iot-edge/module-development)om aan de slag te gaan met de ontwikkeling van uw IOT Edge-module.

## <a name="technical-requirements"></a>Technische vereisten

U moet aan de volgende technische vereisten voldoen om uw IoT Edge-module te kunnen certificeren en publiceren op de Azure Marketplace.

### <a name="platform-support"></a>Platformondersteuning

De module IoT Edge moet een van de volgende platform opties ondersteunen.

#### <a name="tier-1-platforms-supported-by-iot-edge"></a>Platforms van laag 1 die door IoT Edge worden ondersteund

Biedt ondersteuning voor alle platforms van laag 1 die door IoT Edge worden ondersteund (zoals vastgelegd in [Azure IOT EDGE-ondersteuning](https://docs.microsoft.com/azure/iot-edge/support)). We raden u aan deze optie te kiezen omdat deze een betere klant ervaring biedt. Modules die aan deze criteria voldoen, worden weer gepresenteerd. Een module die gebruikmaakt van deze platform optie moet:

- Geef een `latest`-tag en een versie label (bijvoorbeeld `1.0.1`) op die manifest Tags zijn die zijn gemaakt met het [manifest hulp programma](https://github.com/estesp/manifest-tool)github.
- Gebruik het [tabblad Marketplace](./cpp-marketplace-tab.md) om een koppeling toe te voegen aan [compatibele IOT Edge gecertificeerde apparaten](https://aka.ms/iot-edge-certified). Deze koppeling wordt omgezet in `https://aka.ms/iot-edge-certified`, een website waar klanten kunnen bladeren of zoeken naar gecertificeerde apparaten. Deze website wordt ook wel de [Azure IOT Edge Certified](https://catalog.azureiotsolutions.com/) Device Catalog genoemd.

#### <a name="a-subset-of-tier-1-platforms-supported-by-iot-edge"></a>Een subset van laag 1-platforms die door IoT Edge worden ondersteund
  
Ondersteuning voor een subset (ten minste één) van laag 1-platformen die door IoT Edge worden ondersteund (zoals vastgelegd in [Azure IOT EDGE ondersteuning](https://docs.microsoft.com/azure/iot-edge/support)). Een module die gebruikmaakt van deze platform optie moet:

- Geef een `latest`-tag en een versie label (bijvoorbeeld `1.0.1`) op die manifest Tags zijn die zijn gemaakt met het [manifest hulp programma](https://github.com/estesp/manifest-tool) github als meer dan één platform wordt ondersteund. Manifest Tags zijn alleen optioneel wanneer één platform wordt ondersteund.
- Gebruik het [tabblad Marketplace](./cpp-marketplace-tab.md) om een koppeling naar ten minste één IOT edge apparaat op te geven in de [Azure IOT Edge Certified](https://catalog.azureiotsolutions.com/) Device Catalog.

### <a name="device-dimensions"></a>Afmetingen van apparaat

IoT Edge module dimensies (CPU/RAM/opslag/GPU/etc.) op doel IoT Edge apparaten moeten voldoen aan de volgende vereisten:

- De module moet **samen werken met ten minste één IOT Edge gecertificeerd** apparaat in de [Azure IOT Edge Certified](https://catalog.azureiotsolutions.com/) Device Catalog.
- De **minimale hardwarevereisten** moeten worden gedocumenteerd als de laatste alinea in de beschrijving van de aanbieding (op het [tabblad Marketplace](./cpp-marketplace-tab.md)). U kunt eventueel ook de aanbevolen hardwarevereisten weer geven als ze aanzienlijk verschillen. Voeg bijvoorbeeld de volgende sectie toe aan het einde van de beschrijving van uw aanbieding:

  ```html
    <p><u>Minimum hardware requirements:</u> Linux x64 and arm32  OS, 1GB of RAM, 500 Mb of storage</p>
  ```

### <a name="configuration"></a>Configuratie

Het bevat ook standaard configuratie-instellingen om de implementatie zo snel mogelijk op een IoT Edge apparaat in te stellen. De container kan ook de SDK van IoT Edge-module bevatten om communicatie met de edgeHub en IoT Hub mogelijk te maken.

#### <a name="default-configuration"></a>Standaard configuratie

IoT Edge modules moeten kunnen starten met de standaard instellingen op het [tabblad SKU van de Cloud Partner Portal](./cpp-skus-tab.md). De volgende standaard instellingen zijn beschikbaar:

- Standaard **routes**
- Standaard **dubbele gewenste eigenschappen**
- Standaard **omgevings variabelen**
- Standaard **createOptions**

In een scenario waarbij een para meter die vereist is voor een standaard waarde niet zinvol is (bijvoorbeeld het IP-adres van de server van een klant), voegt u een para meter toe als de standaard waarde. Deze waarde ligt tussen vier Kante haken en in hoofd letters. Voor dit voor beeld stelt u de volgende standaard omgevings variabele in:

```
    ServerIPAddress = <MY_SERVER_IP_ADDRESS>
```

#### <a name="configuration-documentation"></a>Configuratie documentatie

Alle configuratie-instellingen van een IoT Edge module moeten duidelijk zijn gedocumenteerd (het gebruik van de routes, dubbele gewenste eigenschappen, omgevings variabelen, createOptions, enzovoort). Geef een koppeling naar uw documentatie of de documentatie moet deel uitmaken van de beschrijving van uw aanbieding/SKU.

### <a name="tags-and-versioning"></a>Tags en versie beheer

Klanten moeten eenvoudig een module kunnen implementeren en automatisch updates ophalen van Marketplace (in een scenario voor ontwikkel aars). Ze moeten ook een exacte versie kunnen gebruiken en blok keren die ze hebben getest (in een productie scenario).

IoT Edge modules moeten voldoen aan de volgende vereisten om te voldoen aan de verwachtingen van deze klant en op Marketplace worden gepubliceerd:

- Neem een manifest `latest` tag op, die de meest recente versie op alle ondersteunde platforms wijst.
- Versie Tags moeten de indeling X. Y. Z hebben, waarbij X, Y en Z gehele getallen zijn.
- Neem een "version"-tag op, zoals `1.0.1`, die verwijst naar een specifieke versie op alle ondersteunde platforms.
- U kunt de Tags version, zoals `1.0.1`, niet bijwerken omdat ze onveranderbaar moeten zijn.

>[!Note]
>Versie beheer kan optioneel de tags ' roulerende versie ' bevatten, zoals `2.0` en `1.0`. Dit biedt ondersteuning voor het tegelijkertijd onderhouden van meerdere primaire versies.

### <a name="telemetry"></a>Telemetrie

Modules die gebruikmaken van de IoT-module-SDK, moeten de unieke module-id instellen op `PublisherId.OfferId.SkuId` voor telemetrische doel einden. Met een unieke id kan Azure Marketplace het aantal module-exemplaren identificeren dat wordt uitgevoerd.

 Gebruik de volgende methoden van de IoT-module Sdk's om de `ProductInfo` in te stellen op deze id:

- [C\#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.productinfo?view=azure-dotnet#Microsoft_Azure_Devices_Client_DeviceClient_ProductInfo) 
- [C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Python](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.productinfo?view=azure-java-stable)

Voor modules die geen gebruikmaken van de IoT-module-SDK, zijn er minder nauw keurige inzichten beschikbaar via de Cloud Partner-portal, zoals het aantal down loads.

### <a name="security"></a>Beveiliging

IoT Edge modules moeten zo weinig mogelijk toegang tot de host vragen. [Beschermde modules](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities) moeten worden vermeden.

### <a name="module-iot-sdk"></a>Module IoT SDK

Met inbegrip van de IoT-module SDK is er geen vereiste voor certificering. Met inbegrip van de IoT-module-SDK kunt u echter betere gebruikers ervaring bieden. Bijvoorbeeld voor het ondersteunen van route ring of het verzenden van berichten naar de Cloud.

De IoT-module SDK is vereist voor het ophalen van telemetriegegevens van het aantal module-exemplaren dat wordt uitgevoerd.


## <a name="recertification-process"></a>Opnieuw certificerings proces

<!-- Add legal time windows-->
Partners ontvangen een melding wanneer er een belang rijke wijziging is die van invloed is op de modules, zoals:

- Ondersteunings matrix voor het besturings systeem/de boog op laag 1 wordt ondersteund door IoT Edge
- IoT-module-SDK
- IoT Edge runtime
- De certificerings richtlijnen voor de module IoT Edge

Partners moeten hun modules bijwerken en opnieuw certificeren met het Cloud Partner-portal-hulp programma.

## <a name="host-your-iot-edge-module-in-an-azure-container-registry"></a>Uw IoT Edge-module hosten in een Azure Container Registry

Als u uw IoT Edge-module naar de Cloud Partner-portal wilt uploaden, moet u deze eerst hosten in een [Azure container Registry](https://azure.microsoft.com/services/container-registry/) (ACR). De module moet alle labels bevatten die u wilt publiceren, met inbegrip van de afbeeldings Tags waarnaar wordt verwezen door een manifest-tag.


## <a name="next-steps"></a>Volgende stappen

- [Uw IoT Edge-module aanbieding maken](./cpp-create-offer.md)
