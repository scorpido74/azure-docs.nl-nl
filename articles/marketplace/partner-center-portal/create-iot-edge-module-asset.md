---
title: Uw technische activa van uw IoT Edge-module voorbereiden - Azure Marketplace
description: Lees meer over de technische en configuratievereisten waaraan de technische en configuratievereisten van uw IoT-module Edge-module moeten voldoen voordat u ze publiceren naar Azure Marketplace.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/03/2020
ms.openlocfilehash: 2c0cd47acbd4639ff5eff2af78dcebdfc26270a7
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81730703"
---
# <a name="prepare-your-iot-edge-module-technical-assets"></a>Uw technische activa van uw IoT Edge-module voorbereiden

> [!IMPORTANT]
> We verplaatsen het beheer van uw IoT Edge-moduleaanbiedingen van Cloud Partner Portal naar Partner Center. Volg de instructies in De technische [activa van uw IoT Edge-module](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-create-technical-assets) voor Cloud Partner Portal om uw aanbiedingen te beheren totdat uw aanbiedingen zijn gemigreerd.

In dit artikel worden de vereisten beschreven waaraan de technische elementen van uw Internet of Things (IoT)Edge-module moeten voldoen voordat ze in Azure Marketplace worden gepubliceerd.

## <a name="get-started"></a>Aan de slag

Een IoT Edge-module is een Docker-compatibele container die wordt uitgevoerd op een IoT Edge-apparaat.

- Zie [Azure IoT Edge-modules begrijpen](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules)voor meer informatie over IoT Edge-modules.
- Zie [Uw eigen IoT Edge-modules ontwikkelen](https://docs.microsoft.com/azure/iot-edge/module-development)om aan de slag te gaan met de ontwikkeling van uw IoT Edge-module.

## <a name="technical-requirements"></a>Technische vereisten

Uw IoT Edge-module moet voldoen aan de volgende technische vereisten om te worden gecertificeerd en gepubliceerd in Azure Marketplace.

### <a name="platform-support"></a>Platformondersteuning

Uw IoT Edge-module moet een van de volgende platformopties ondersteunen:

#### <a name="tier-1-platforms-supported-by-iot-edge"></a>Tier 1-platforms ondersteund door IoT Edge

Uw module moet alle Tier 1-platforms ondersteunen die worden ondersteund door IoT Edge (zoals opgenomen in [Azure IoT Edge-ondersteuning).](https://docs.microsoft.com/azure/iot-edge/support) We raden deze optie aan omdat het een betere klantervaring biedt. Modules die aan deze criteria voldoen, worden tentoongesteld. Een module die deze platformoptie gebruikt, moet:

- Geef een nieuwste tag en een versietag (bijvoorbeeld 1.0.1) die manifesttags zijn die zijn gebouwd met de [GitHub Manifest-tool.](https://github.com/estesp/manifest-tool)

- Gebruik het tabblad Aanbiedingsvermelding in [Partnercentrum](https://partner.microsoft.com/dashboard/commercial-marketplace) om een koppeling toe te voegen onder de sectie **Nuttige koppelingen** naar de [Azure IoT Edge Certified-apparaatcatalogus](https://catalog.azureiotsolutions.com/alldevices?filters={%2218%22:[%221%22]}/).

#### <a name="a-subset-of-tier-1-platforms-supported-by-iot-edge"></a>Een subset van Tier 1-platforms die worden ondersteund door IoT Edge

Uw module moet een subset (ten minste één) tier 1-platforms ondersteunen die worden ondersteund door IoT Edge (zoals opgenomen in [Azure IoT Edge-ondersteuning).](https://docs.microsoft.com/azure/iot-edge/support) Een module die deze platformoptie gebruikt, moet:

- Geef een nieuwste tag en een versietag (bijvoorbeeld 1.0.1) die manifesttags zijn die zijn gebouwd met de [GitHub-manifesttool](https://github.com/estesp/manifest-tool) als er meer dan één platform wordt ondersteund. Manifesttags zijn alleen optioneel wanneer één platform wordt ondersteund.
- Gebruik het tabblad Aanbiedingsvermelding in [Partnercentrum](https://partner.microsoft.com/dashboard/commercial-marketplace) om een koppeling onder de sectie **Nuttige koppelingen** toe te voegen aan ten minste één IoT Edge-apparaat uit de [Azure IoT Edge Certified-apparaatcatalogus.](https://catalog.azureiotsolutions.com/)

:::image type="content" source="media/iot-edge-module-technical-assets-offer-listing.png" alt-text="Dit is een afbeelding van de sectie Aanbiedingsaanbieding binnen partnercentrum":::

### <a name="device-dimensions"></a>Apparaatafmetingen

De afmetingen van de IoT Edge-module (zoals CPU, RAM, opslag en GPU) op gerichte IoT Edge-apparaten moeten aan de volgende vereisten voldoen:

- De module moet werken met ten minste één IoT Edge-apparaat uit de [Azure IoT Edge Certified-apparaatcatalogus.](https://catalog.azureiotsolutions.com/)

- De minimale hardwarevereisten moeten worden gedocumenteerd als de laatste alinea in de beschrijving van de aanbieding (onder het tabblad Aanbiedingsvermelding in [Partnercentrum](https://partner.microsoft.com/dashboard/commercial-marketplace)). Optioneel u ook de aanbevolen hardwarevereisten weergeven als deze aanzienlijk verschillen. Voeg bijvoorbeeld de volgende sectie toe aan het einde van de aanbiedingsbeschrijving:

Kopieer deze HTML-tekst of gebruik de bijbehorende rich text-functies in het bewerkingsvenster.

```html
<p><u>Minimum hardware requirements:</u> Linux x64 and arm32 OS, 1GB of RAM, 500 Mb of storage</p>
```

### <a name="configuration"></a>Configuratie

Uw module moet standaardconfiguratie-instellingen bevatten om de implementatie naar een IoT Edge-apparaat zo eenvoudig mogelijk te maken. Deze informatie kan worden verstrekt op de pagina **Technische configuratie** voor het plan in [partnercentrum.](https://partner.microsoft.com/dashboard/commercial-marketplace) De container kan ook de IoT Edge Module SDK bevatten om communicatie met de edge Hub en IoT Hub mogelijk te maken.

#### <a name="default-configuration"></a>Standaardconfiguratie

IoT Edge-modules moeten kunnen beginnen met de standaardinstellingen op de pagina **Technische configuratie** voor het plan in [partnercentrum.](https://partner.microsoft.com/dashboard/commercial-marketplace) De volgende standaardinstellingen zijn beschikbaar:

- **Standaardroutes**
- Standaardmodule **dubbele gewenste eigenschappen**
- **Standaardomgevingsvariabelen**
- Opties **voor het maken van standaardcontainers**

In een scenario waarin een parameter die vereist is voor een standaardwaarde geen zin heeft (bijvoorbeeld het IP-adres van de server van een klant), voegt u een parameter toe als standaardwaarde. Deze waarde is hoofdletters en tussen haakjes. In dit voorbeeld stelt u de volgende standaardomgevingsvariabele in:

```
ServerIPAddress = <MY_SERVER_IP_ADDRESS>
```

#### <a name="configuration-documentation"></a>Configuratiedocumentatie

Alle configuratie-instellingen van een IoT Edge-module moeten duidelijk worden gedocumenteerd. U moet bijvoorbeeld documenteren hoe u de routes, de gewenste eigenschappen, omgevingsvariabelen, createOptions enzovoort gebruikt. U moet een link naar uw documentatie opgeven of deze onderdeel maken van uw aanbieding of een beschrijving van uw abonnement. U deze informatie verstrekken op de pagina **Aanbiedingsaanbieding** en **Aanbieding plannen** in [partnercentrum.](https://partner.microsoft.com/dashboard/commercial-marketplace)

#### <a name="tags-and-versioning"></a>Tags en versiebeheer

Klanten moeten eenvoudig een module kunnen implementeren en automatisch updates van de marketplace kunnen ontvangen (in een ontwikkelaarsscenario). Ze moeten ook in staat zijn om een exacte versie die ze hebben getest (in een productiescenario) te gebruiken en te bevriezen.

Om aan deze verwachtingen van klanten te voldoen en op de markt te worden gepubliceerd, moeten IoT Edge-modules voldoen aan de volgende vereisten

- Voeg een duidelijke nieuwste tag toe die verwijst naar de nieuwste versie op alle ondersteunde platforms.
- Maak versietags in het formulier X.Y.Z, waarbij X, Y en Z gehele getallen zijn.
- Voeg een tag met "versie" toe, zoals 1.0.1, die verwijst naar een specifieke versie op alle ondersteunde platforms.
- Werk geen 'versie'-tags bij, zoals 1.0.1, omdat ze niet mogen worden gewijzigd.

> [!NOTE]
> Optioneel kan versiebeheer tags voor 'rollende versies' bevatten, zoals 2.0 en 1.0. Dit ondersteunt het tegelijkertijd onderhouden van meerdere grote versies.

### <a name="telemetry"></a>Telemetrie

Modules die de IoT Module SDK gebruiken, moeten de unieke module-id instellen op PublisherId.OfferId.SkuId voor telemetriedoeleinden. Met een unieke id kan Azure Marketplace het aantal module-exemplaren identificeren dat wordt uitgevoerd.

Gebruik een van de volgende methoden uit de SDK's van de IoT-module om de ProductInfo op deze id in te stellen:

- [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.productinfo?view=azure-dotnet#Microsoft_Azure_Devices_Client_DeviceClient_ProductInfo)
- [C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Python](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.productinfo?view=azure-java-stable)

Voor modules die geen gebruik maken van de IoT Module SDK, zijn minder nauwkeurige inzichten beschikbaar via Partner Center, zoals het aantal downloads.

### <a name="security"></a>Beveiliging

IoT Edge-modules moeten [bevoorrechte modules](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities)vermijden. In plaats daarvan vragen om de minst bevoorrechte toegang tot de host mogelijk.

### <a name="module-iot-sdk"></a>Module IoT SDK

Het opnemen van de IoT Module SDK is geen voorwaarde voor certificering. Het opnemen van de IoT Module SDK kan echter een betere gebruikerservaring bieden. Bijvoorbeeld om routering te ondersteunen of berichten naar de cloud te verzenden.

De IoT Module SDK is vereist om telemetriegegevens te krijgen over het aantal module-exemplaren dat wordt uitgevoerd.

## <a name="recertification-process"></a>Hercertificeringsproces

Partners worden op de hoogte gesteld wanneer er een wijziging is die van invloed is op hun modules, zoals:

- Tier 1 OS/arch-ondersteuningsmatrix ondersteund door IoT Edge
- SDK voor IoT-modules
- IoT Edge-runtime
- Richtlijnen voor certificering van IoT Edge-modules

Partners moeten hun aanbiedingen bijwerken en opnieuw certificeren door ze opnieuw te publiceren in [het Partnercenter.](https://partner.microsoft.com/dashboard/commercial-marketplace)

Uw aanbieding wordt ook opnieuw gecertificeerd als u deze bijwerkt, zoals het toevoegen van nieuwe afbeeldingstags.

## <a name="host-module-in-azure-container-registry"></a>Hostmodule in Azure Container Registry

Als u uw IoT Edge-module wilt uploaden naar Azure Marketplace, moet u deze eerst hosten in een [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) (ACR). De module moet alle tags bevatten die u wilt publiceren, inclusief de afbeeldingstags waarnaar wordt verwezen door een manifesttag. Zie de zelfstudie [Een Azure-containerregister maken en een containerafbeelding indrukken](https://docs.microsoft.com/azure/container-instances/container-instances-tutorial-prepare-acr)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- [Aanbieding voor IoT Edge-module maken](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-iot-edge-module-creation)