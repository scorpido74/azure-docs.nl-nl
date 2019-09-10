---
title: Wat is Azure IoT Central? | Microsoft Docs
description: Azure IoT Central is een end-to-end SaaS-oplossing die u kunt gebruiken om uw aangepaste IoT-oplossing te bouwen en te beheren. In dit artikel vindt u een overzicht van de functies van Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 07/06/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: timlt
ms.openlocfilehash: bfdad0d81599035e7d8c270ec4e8ee8d6a45125e
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/10/2019
ms.locfileid: "70858917"
---
# <a name="what-is-azure-iot-central-preview-features"></a>Wat is Azure IoT Central (preview-functies)?

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

> [!WARNING]
> De [IoT Plug en Play](../iot-pnp/overview-iot-plug-and-play.md) -mogelijkheden in azure IOT Central zijn momenteel beschikbaar als open bare preview. Gebruik geen IoT Plug en Play ingeschakelde IoT Central toepassing voor werk belastingen voor de productie. Voor productie omgevingen wordt een IoT Central-toepassing gebruikt die is gemaakt op basis van een huidige, algemeen beschik bare, toepassings sjabloon.

Azure IoT Central is een volledig beheerde IoT-software-as-a-service-oplossing waarmee u eenvoudig producten kunt maken die de fysieke en digitale werelden verbinden. U kunt uw productvisie voor verbonden apparaten als volgt tot leven wekken door:

- Nieuwe inzichten af te leiden uit verbonden apparaten, om uw klanten betere producten en ervaringen te kunnen bieden.
- Nieuwe zakelijke kansen voor uw organisatie te creëren.

Azure IoT Central, vergeleken met een typisch IoT-project:

- Vermindert de beheer Last.
- Hiermee worden de operationele kosten en overhead verminderd.
- Maakt het eenvoudig om uw toepassing aan te passen tijdens het werken met:
  - Toonaangevende technologieën zoals [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) en [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/).
  - Beveiligingsfuncties op bedrijfsniveau, zoals end-to-endversleuteling.

De volgende video biedt een overzicht van Azure IoT Central:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Microsoft-IoT-Central-intro-walkthrough/Player]

Dit artikel bevat een overzicht van Azure IoT Central:

- De typische persona's die aan een project zijn gekoppeld.
- Het maken van uw toepassing.
- Het maken van verbinding tussen uw apparaten en uw toepassing
- Het beheren van uw toepassing.

## <a name="known-issues"></a>Bekende problemen

> [!Note]
> Deze bekende problemen zijn alleen van toepassing op de IoT Central preview-toepassing.

- Regels bieden geen ondersteuning voor alle acties (alleen e-mail).
- Voor complexe typen: regels, analyses en apparaatgroepen worden niet ondersteund.
- Continue gegevens export biedt geen ondersteuning voor de Avro-indeling (incompatibiliteit).
- Gesimuleerde apparaten bieden geen ondersteuning voor alle complexe typen.
- Geojson wordt momenteel niet ondersteund.
- De kaart tegel wordt momenteel niet ondersteund.
- Taken bieden geen ondersteuning voor complexe typen.
- Matrix schema typen worden niet ondersteund.
- Het exporteren van de toepassings sjabloon en het kopiëren van de toepassing worden niet ondersteund.
- Alleen de C apparaat-SDK en het node. js-apparaat en de service-Sdk's worden ondersteund.
- Het is alleen beschikbaar in de regio's Noord-Europa en Centraal vs.
- Voor hulp modellen voor apparaten moeten alle interfaces in hetzelfde bestand zijn gedefinieerd.

## <a name="personas"></a>Persona's

De documentatie van Azure IoT Central verwijst naar vier personen die communiceren met een Azure IoT Central-toepassing:

- Een _maker_ is verantwoordelijk voor het definiëren van de typen apparaten die verbinding maken met de toepassing en voor het aanpassen van de toepassing voor de operator.
- Een _operator_ beheert de apparaten die met de toepassing zijn verbonden.
- Een _beheerder_ is verantwoordelijk voor beheertaken zoals het beheer van gebruikers en rollen binnen de toepassing.
- Een _apparaatontwikkelaar_ maakt de code die wordt uitgevoerd op een apparaat dat met uw toepassing is verbonden.

## <a name="create-your-azure-iot-central-application"></a>Uw Azure IoT Central-toepassing maken

Als maker gebruikt u Azure IoT Central om een aangepaste, in de cloud gehoste IoT-oplossing voor uw organisatie te maken. Een aangepaste IoT-oplossing bestaat meestal uit:

- Een cloudtoepassing die telemetriegegevens van uw apparaten ontvangt en die u in staat stelt om deze apparaten te beheren.
- Meerdere apparaten waarop aangepaste code wordt uitgevoerd en die met uw cloudtoepassing zijn verbonden.

U kunt snel een nieuwe Azure IoT Central-toepassing implementeren en deze vervolgens aanpassen aan uw specifieke vereisten in uw browser. Als ontwerper gebruikt u de webgebaseerde hulpprogram ma's voor het maken van een _apparaatprofiel_ voor de apparaten die verbinding maken met uw toepassing. Een sjabloon voor een apparaat is de blauw druk waarmee de kenmerken en het gedrag van een type apparaat worden gedefinieerd, zoals:

- Telemetrie verzendt.
- Bedrijfseigenschappen die kunnen worden gewijzigd door een operator.
- Apparaateigenschappen die zijn ingesteld door een apparaat en die het kenmerk alleen-lezen hebben in de toepassing.
- Eigenschappen die zijn ingesteld door een operator die het gedrag van het apparaat bepalen.

Deze sjabloon voor apparaten omvat:

- Een _mogelijkheidsprofiel_ met een beschrijving van de mogelijkheden die een apparaat moet implementeren, zoals de telemetrie die het verzendt en de eigenschappen die worden gerapporteerd.
- Cloud eigenschappen die niet worden opgeslagen op het apparaat.
- Aanpassingen, Dash boards en formulieren die deel uitmaken van uw IoT Central-toepassing.

### <a name="create-device-templates"></a>Device-sjablonen maken

[IoT Plug en Play](../iot-pnp/overview-iot-plug-and-play.md) maakt het IOT Central mogelijk om apparaten te integreren zonder dat u een Inge sloten apparaatcode hoeft te schrijven. De kern van IoT Plug en Play is een schema voor het capaciteits model van het apparaat dat de mogelijkheden van apparaten beschrijft. In een IoT Central preview-toepassing gebruiken Device-sjablonen deze IoT Plug en Play-mogelijkheden voor het apparaat.

Als opbouw functie hebt u verschillende opties voor het maken van Apparaatinstellingen:

- Ontwerp de sjabloon voor het apparaat in IoT Central en implementeer vervolgens het hulp model van het apparaat in de code van uw apparaat.
- Importeer een mogelijkheidsprofiel uit de [Azure Certified voor IOT-Apparaatbeheer](https://aka.ms/iotdevcat) en voeg vervolgens alle Cloud eigenschappen, aanpassingen en dash boards toe die uw IOT Central toepassing nodig heeft.
- Maak een mogelijkheidsprofiel met Visual Studio code. Implementeer uw apparaatcode vanuit het model en verbind uw apparaat met uw IoT Central-toepassing. IoT Central vindt het mogelijkheidsprofiel vanuit een opslag plaats en maakt een eenvoudige sjabloon voor uw apparaat.
- Maak een mogelijkheidsprofiel met Visual Studio code. Implementeer uw apparaatcode vanuit het model. Importeer het mogelijkheidsprofiel hand matig in uw IoT Central-toepassing en voeg vervolgens alle Cloud eigenschappen, aanpassingen en dash boards toe die uw IoT Central toepassing nodig heeft.

Als ontwerper kunt u IoT Central gebruiken om code te genereren voor test apparaten om uw apparaatinstellingen te valideren.

### <a name="customize-the-ui"></a>De gebruikersinterface aanpassen

Als maker kunt u de gebruikersinterface van de Azure IoT Central-toepassing ook aanpassen voor de operators die verantwoordelijk zijn voor het dagelijks gebruik van de toepassing. Voorbeelden van aanpassingen die een maker kan maken, zijn:

- Het definiëren van de indeling van eigenschappen en instellingen in een apparaatsjabloon.
- Het configureren van aangepaste dashboards, zodat operators nieuwe inzichten kunnen krijgen en problemen sneller kunnen oplossen.
- Het configureren van aangepaste analyses om tijdseriegegevens van uw verbonden apparaten te verkennen.

## <a name="connect-your-devices"></a>Uw apparaten verbinden

Nadat de maker heeft gedefinieerd welke typen apparaten verbinding kunnen maken met de toepassing, maakt een apparaatontwikkelaar de code die op de apparaten moet worden uitgevoerd. Als apparaatontwikkelaar gebruikt u de [Azure IoT SDK's](https://github.com/Azure/azure-iot-sdks) van Microsoft, die open-source zijn, om de code voor uw apparaat te maken. Deze SDK's hebben uitgebreide taal-, platform- en protocolondersteuning, zodat ze kunnen voldoen aan uw behoeften bij het verbinden van uw apparaten met uw Azure IoT Central-toepassing. De Sdk's helpen u bij het implementeren van de volgende mogelijkheden voor apparaten:

- Een beveiligde verbinding maken.
- Telemetrie verzenden.
- Status melden.
- Configuratie-updates ontvangen.

Lees de blogpost [Benefits of using the Azure IoT SDKs, and pitfalls to avoid if you don't](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/) (Voordelen van het gebruik van de Azure IoT SDK's en valkuilen die u moet vermijden als u deze niet gebruikt) voor meer informatie.

## <a name="manage-your-application"></a>Uw toepassing beheren

Azure IoT Central-toepassingen worden volledig gehost door Microsoft, waardoor de overheadkosten voor het beheer van uw toepassingen worden verlaagd.

Als operator gebruikt u de Azure IoT Central-toepassing om de apparaten in uw Azure IoT Central-oplossing te beheren. Opera tors doen taken zoals:

- Het controleren van de apparaten die met de toepassing zijn verbonden.
- Het oplossen en verhelpen van problemen met apparaten.
- Het inrichten van nieuwe apparaten.

Als opbouw functie kunt u aangepaste regels en acties definiëren die worden uitgevoerd via het streamen van gegevens vanaf verbonden apparaten. Een operator kan deze regels op apparaatniveau in- of uitschakelen om taken binnen de toepassing te beheren en automatiseren.

Beheerders beheren de toegang tot uw toepassing met [gebruikers rollen en machtigingen](howto-administer-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

## <a name="next-steps"></a>Volgende stappen

Nu u een overzicht van Azure IoT Central hebt, zijn dit mogelijke volgende stappen:

- Leer wat de verschillen zijn tussen de oplossingsversnellers [Azure IoT Central en Azure IoT](overview-iot-options.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).
- Raak vertrouwd met de [gebruikersinterface van Azure IoT Central](overview-iot-central-tour-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).
- Ga aan de slag door [een Azure IoT Central-toepassing te maken](quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).
- Volg een reeks zelfstudies die u leren hoe u:
  - [Als maker een apparaatsjabloon maakt](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
  - [Als maker regels toevoegt om uw oplossing te automatiseren](tutorial-configure-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
  - [Als operator uw apparaten bewaakt](tutorial-monitor-devices-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
  - [Als operator kunt u een apparaat aan uw oplossing toevoegen](tutorial-add-device-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
- Meer informatie over [IoT Plug en Play](../iot-pnp/overview-iot-plug-and-play.md)
