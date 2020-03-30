---
title: Technische elementen van Azure IoT Edge-modules maken | Azure Marketplace
description: Maak de technische elementen voor een IoT Edge-module.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: dsindona
ms.openlocfilehash: 304ea31f2a6f9820226a6ad0db8bfc803e6fdb94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285211"
---
# <a name="prepare-your-iot-edge-module-technical-assets"></a>Uw technische activa van uw IoT Edge-module voorbereiden

In dit artikel worden de vereisten beschreven waaraan de technische elementen van uw IoT Edge-module moeten voldoen voordat ze worden gepubliceerd op Azure Marketplace.

## <a name="understanding-iot-edge-modules-and-getting-started"></a>IoT Edge-modules begrijpen en aan de slag gaan

Een IoT Edge-module is een Docker-compatibele container die is gemaakt om op een IoT Edge-apparaat te draaien.

- Zie [Azure IoT Edge-modules begrijpen](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules)voor meer informatie over IoT Edge-modules.
- Zie vereisten en tools voor het [ontwikkelen van IoT Edge-modules](https://docs.microsoft.com/azure/iot-edge/module-development)om aan de slag te gaan met de ontwikkeling van uw IoT Edge-module.

## <a name="technical-requirements"></a>Technische vereisten

Aan de volgende technische vereisten moet worden voldaan om uw IoT Edge-module te kunnen certificeren en publiceren op de Azure Marketplace.

### <a name="platform-support"></a>Platformondersteuning

Uw IoT Edge-module moet een van de volgende platformopties ondersteunen.

#### <a name="tier-1-platforms-supported-by-iot-edge"></a>Tier 1-platforms ondersteund door IoT Edge

Ondersteuning voor alle Tier 1-platforms die worden ondersteund door IoT Edge (zoals opgenomen in [Azure IoT Edge-ondersteuning).](https://docs.microsoft.com/azure/iot-edge/support) We raden deze optie aan omdat het een betere klantervaring biedt. Modules die aan deze criteria voldoen, worden tentoongesteld. Een module die deze platformoptie gebruikt, moet:

- Geef `latest` een tag en een versietag `1.0.1`(bijvoorbeeld) die manifesttags zijn die zijn gebouwd met het GitHub-manifest-tool. [manifest-tool](https://github.com/estesp/manifest-tool)
- Gebruik [het tabblad Marketplace](./cpp-marketplace-tab.md) om een koppeling toe te voegen aan [compatibele IoT Edge-gecertificeerde apparaten.](https://aka.ms/iot-edge-certified) Deze link wordt `https://aka.ms/iot-edge-certified`opgelost naar een website waar klanten kunnen bladeren of zoeken naar gecertificeerde apparaten. Deze website wordt ook wel de [Azure IoT Edge](https://catalog.azureiotsolutions.com/) Certified-apparaatcatalogus genoemd.

#### <a name="a-subset-of-tier-1-platforms-supported-by-iot-edge"></a>Een subset van Tier 1-platforms die worden ondersteund door IoT Edge
  
Ondersteuning voor een subset (ten minste één) tier 1-platforms die worden ondersteund door IoT Edge (zoals opgenomen in [Azure IoT Edge-ondersteuning).](https://docs.microsoft.com/azure/iot-edge/support) Een module die deze platformoptie gebruikt, moet:

- Geef `latest` een tag en een versietag `1.0.1`(bijvoorbeeld) die manifesttags zijn die zijn gebouwd met de GitHub-manifesttool als er meer dan één platform wordt ondersteund. [manifest-tool](https://github.com/estesp/manifest-tool) Manifesttags zijn alleen optioneel wanneer één platform wordt ondersteund.
- Gebruik het [tabblad Marketplace](./cpp-marketplace-tab.md) om een koppeling te maken naar ten minste één IoT Edge-apparaat in de [Azure IoT Edge](https://catalog.azureiotsolutions.com/) Certified-apparaatcatalogus.

### <a name="device-dimensions"></a>Apparaatafmetingen

De afmetingen van de IoT Edge-module (CPU/RAM/Storage/GPU/etc.) op gerichte IoT Edge-apparaten moeten aan de volgende eisen voldoen:

- De module moet **werken met ten minste één IoT Edge-gecertificeerd** apparaat in de Azure [IoT Edge](https://catalog.azureiotsolutions.com/) Certified-apparaatcatalogus.
- De **minimale hardwarevereisten** moeten worden gedocumenteerd als de laatste alinea in de beschrijving van de aanbieding (onder het [tabblad Marketplace).](./cpp-marketplace-tab.md) Optioneel u ook de aanbevolen hardwarevereisten weergeven als deze aanzienlijk verschillen. Voeg bijvoorbeeld de volgende sectie toe aan het einde van de aanbiedingsbeschrijving:

  ```html
    <p><u>Minimum hardware requirements:</u> Linux x64 and arm32  OS, 1GB of RAM, 500 Mb of storage</p>
  ```

### <a name="configuration"></a>Configuratie

Het bevat ook standaardconfiguratie-instellingen om de implementatie naar een IoT Edge-apparaat zo rechttoe rechtaan mogelijk te maken. De container kan ook de IoT Edge Module SDK bevatten om communicatie met de edgeHub en IoT Hub mogelijk te maken.

#### <a name="default-configuration"></a>Standaardconfiguratie

IoT Edge-modules moeten kunnen beginnen met de standaardinstellingen op het [tabblad SKU van de Cloud Partner-portal.](./cpp-skus-tab.md) De volgende standaardinstellingen zijn beschikbaar:

- **Standaardroutes**
- Standaard **dubbele gewenste eigenschappen**
- **Standaardomgevingsvariabelen**
- Standaardopties **voor maken**

In een scenario waarin een parameter die vereist is voor een standaardwaarde niet zinvol is (bijvoorbeeld het IP-adres van de server van een klant), voegt u een parameter toe als standaardwaarde. Deze waarde is tussen haakjes en in hoofdletters ingesloten. In dit voorbeeld stelt u de volgende standaardomgevingsvariabele in:

```
    ServerIPAddress = <MY_SERVER_IP_ADDRESS>
```

#### <a name="configuration-documentation"></a>Configuratiedocumentatie

Alle configuratie-instellingen van een IoT Edge-module moeten duidelijk worden gedocumenteerd (hoe de routes te gebruiken, twee gewenste eigenschappen, omgevingsvariabelen, createOptions, enzovoort.) Geef een link naar uw documentatie of de documentatie moet deel uitmaken van uw aanbieding/sku-beschrijving.

### <a name="tags-and-versioning"></a>Tags en versiebeheer

Klanten moeten eenvoudig een module kunnen implementeren en automatisch updates van de marketplace kunnen ontvangen (in een ontwikkelaarsscenario.) Ze moeten ook in staat zijn om een exacte versie die ze hebben getest (in een productiescenario)kunnen gebruiken en bevriezen.

Om aan de verwachtingen van deze klanten te voldoen en op de markt te worden gepubliceerd, moeten IoT Edge-modules aan de volgende vereisten voldoen:

- Voeg een `latest` manifesttag toe, die de nieuwste versie op alle ondersteunde platforms richt.
- Versietags moeten van het formulier X.Y.Z zijn, waarbij X, Y en Z gehele getallen zijn.
- Voeg een tag 'versie' toe, zoals `1.0.1`, die verwijst naar een specifieke versie op alle ondersteunde platforms.
- Werk geen 'versie'-tags `1.0.1`bij, zoals , omdat ze onveranderlijk moeten zijn.

>[!Note]
>Optioneel kan versiebeheer tags voor 'rollende `2.0` versies' bevatten, zoals en `1.0`. Dit ondersteunt het tegelijkertijd onderhouden van meerdere grote versies.

### <a name="telemetry"></a>Telemetrie

Modules die de IoT Module SDK gebruiken, moeten de unieke module-id instellen `PublisherId.OfferId.SkuId` voor telemetriedoeleinden. Met een unieke id kan de Azure Marketplace het aantal module-exemplaren identificeren dat wordt uitgevoerd.

 Gebruik de volgende methoden uit de SDK's `ProductInfo` van de IoT-module om de op deze id in te stellen:

- [C\#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.productinfo?view=azure-dotnet#Microsoft_Azure_Devices_Client_DeviceClient_ProductInfo) 
- [C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Python](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.productinfo?view=azure-java-stable)

Voor modules die geen gebruik maken van de IoT Module SDK, zijn minder nauwkeurige inzichten beschikbaar via de Cloud Partner Portal, zoals het aantal downloads.

### <a name="security"></a>Beveiliging

IoT Edge-modules moeten vragen om de minst bevoorrechte toegang tot de host mogelijk te maken. [Bevoorrechte modules](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities) moeten worden vermeden.

### <a name="module-iot-sdk"></a>Module IoT SDK

Het opnemen van de IoT Module SDK is geen voorwaarde voor certificering. Het opnemen van de IoT Module SDK kan echter een betere gebruikerservaring bieden. Bijvoorbeeld om routering te ondersteunen of berichten naar de cloud te verzenden.

De IoT Module SDK is vereist om telemetriegegevens over het aantal module-exemplaren dat wordt uitgevoerd, op te halen.


## <a name="recertification-process"></a>Hercertificeringsproces

<!-- Add legal time windows-->
Partners krijgen een melding wanneer er een baanbrekende wijziging is die van invloed is op hun modules, zoals:

- Tier 1-ondersteuningsmatrix voor besturingssysteem/boog ondersteund door IoT Edge
- SDK voor IoT-modules
- Runtime iot-rand
- De certificeringsrichtlijnen voor IoT Edge-modules

Partners moeten hun modules bijwerken en opnieuw certificeren met behulp van de Cloud Partner Portal-tool.

## <a name="host-your-iot-edge-module-in-an-azure-container-registry"></a>Uw IoT Edge-module hosten in een Azure-containerregister

Als u uw IoT Edge-module wilt uploaden naar de Cloud Partner Portal, moet u deze eerst hosten in een [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) (ACR). De module moet alle tags bevatten die u wilt publiceren, inclusief de afbeeldingstags waarnaar wordt verwezen door een manifesttag.


## <a name="next-steps"></a>Volgende stappen

- [Uw IoT Edge-moduleaanbieding maken](./cpp-create-offer.md)
