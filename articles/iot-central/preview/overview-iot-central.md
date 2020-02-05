---
title: Wat is Azure IoT Central? | Microsoft Docs
description: Azure IoT Central is een IoT-toepassings platform dat het maken van IoT-oplossingen vereenvoudigt en helpt de overhead en kosten van IoT-beheer bewerkingen en-ontwikkeling te verminderen. In dit artikel vindt u een overzicht van de functies van Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 12/10/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: timlt
ms.openlocfilehash: 27862ce11785f1b264d9f0238a9bae2684c87d02
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989923"
---
# <a name="what-is-azure-iot-central-preview-features"></a>Wat is Azure IoT Central (preview-functies)?

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

> [!WARNING]
> De [IoT Plug en Play](../../iot-pnp/overview-iot-plug-and-play.md) -mogelijkheden in azure IOT Central zijn momenteel beschikbaar als open bare preview. Gebruik geen IoT Plug en Play ingeschakelde IoT Central [toepassings sjabloon](../core/concepts-app-templates.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json) voor werk belastingen voor de productie. Voor productie omgevingen wordt een IoT Central-toepassing gebruikt die is gemaakt op basis van een huidige, algemeen beschik bare, [toepassings sjabloon](../core/concepts-app-templates.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json).

IoT Central is een IoT-toepassings platform waarmee de belasting en kosten voor het ontwikkelen, beheren en onderhouden van IoT-oplossingen op bedrijfs niveau worden verminderd. Als u ervoor kiest om met IoT Central te bouwen, hebt u de mogelijkheid om tijd, geld en energie te best Eden aan het trans formatie van uw bedrijf met IoT-gegevens, in plaats van alleen maar een complexe en voortdurend veranderende IoT-infra structuur te onderhouden.

Met de webgebruikersinterface kunt u de voor waarden van apparaten bewaken, regels maken en miljoenen apparaten en hun gegevens in hun levens cyclus beheren. Daarnaast kunt u hiermee op Device Insights reageren door IoT Intelligence uit te breiden in line-of-business-toepassingen.

In dit artikel vindt u een overzicht van IoT Central:

- De typische persona's die aan een project zijn gekoppeld.
- Het maken van uw toepassing.
- Het maken van verbinding tussen uw apparaten en uw toepassing
- Het beheren van uw toepassing.
- Azure IoT Edge mogelijkheden in IoT Central.
- Hoe u uw Azure IoT Edge runtime-apparaten verbindt met uw toepassing.

## <a name="known-issues"></a>Bekende problemen

> [!Note]
> Deze bekende problemen zijn alleen van toepassing op de IoT Central preview-toepassingen.

- Continue gegevens export biedt geen ondersteuning voor de Avro-indeling (incompatibiliteit).
- Geojson wordt momenteel niet ondersteund.
- De kaart tegel wordt momenteel niet ondersteund.
- Taken bieden geen ondersteuning voor complexe typen.
- Matrix schema typen worden niet ondersteund.
- Alleen de C apparaat-SDK en het node. js-apparaat en de service-Sdk's worden ondersteund.
- Het is alleen beschikbaar op de locaties Verenigde Staten en Europa.
- Voor hulp modellen voor apparaten moeten alle interfaces in hetzelfde bestand zijn gedefinieerd.

## <a name="personas"></a>Persona's

De IoT Central documentatie verwijst naar vier personen die communiceren met een IoT Central-toepassing:

- Een _oplossings bouwer_ is verantwoordelijk voor het definiëren van de typen apparaten die verbinding maken met de toepassing en het aanpassen van de toepassing voor de operator.
- Een _operator_ beheert de apparaten die met de toepassing zijn verbonden.
- Een _beheerder_ is verantwoordelijk voor beheer taken, zoals het beheren van [gebruikers rollen en machtigingen](howto-administer.md) binnen de toepassing.
- Een _ontwikkelaar_ van het apparaat maakt de code die wordt uitgevoerd op een apparaat of IOT Edge module die is verbonden met uw toepassing.

## <a name="create-your-iot-central-application"></a>Uw IoT Central-toepassing maken

Als oplossings functie kunt u IoT Central gebruiken om een aangepaste IoT-oplossing in de cloud te maken voor uw organisatie. Een aangepaste IoT-oplossing bestaat meestal uit:

- Een cloudtoepassing die telemetriegegevens van uw apparaten ontvangt en die u in staat stelt om deze apparaten te beheren.
- Meerdere apparaten waarop aangepaste code wordt uitgevoerd en die met uw cloudtoepassing zijn verbonden.

U kunt snel een nieuwe IoT Central-toepassing implementeren en deze vervolgens aanpassen aan uw specifieke vereisten in uw browser. Als opbouw functie voor oplossingen gebruikt u de webgebaseerde hulpprogram ma's voor het maken van een _apparaatprofiel_ voor de apparaten die verbinding maken met uw toepassing. Een sjabloon voor een apparaat is de blauw druk waarmee de kenmerken en het gedrag van een type apparaat worden gedefinieerd, zoals:

- Telemetrie verzendt.
- Bedrijfseigenschappen die kunnen worden gewijzigd door een operator.
- Apparaateigenschappen die zijn ingesteld door een apparaat en die het kenmerk alleen-lezen hebben in de toepassing.
- Eigenschappen, die door een operator worden ingesteld, waarmee het gedrag van het apparaat wordt bepaald.

Deze sjabloon voor apparaten omvat:

- Een _mogelijkheidsprofiel_ met een beschrijving van de mogelijkheden die een apparaat moet implementeren, zoals de telemetrie die het verzendt en de eigenschappen die worden gerapporteerd.
- Cloud eigenschappen die niet op het apparaat zijn opgeslagen.
- Aanpassingen, Dash boards en formulieren die deel uitmaken van uw IoT Central-toepassing.

### <a name="pricing"></a>Prijzen

U kunt IoT Central-toepassing maken met een gratis proef versie van 7 dagen of een standaard abonnement gebruiken.

- Toepassingen die u maakt met behulp van het **gratis** abonnement, zijn zeven dagen gratis en ondersteunen Maxi maal vijf apparaten. U kunt ze op elk gewenst moment converteren om een standaard-prijs plan te gebruiken voordat ze verlopen.
- Toepassingen die u maakt met behulp van het **Standard** -abonnement, worden per apparaat gefactureerd. u kunt het prijs plan **Standard 1** of **Standard 2** kiezen waarbij de eerste twee apparaten gratis zijn. Meer informatie over de gratis en standaard prijzen abonnementen op de [pagina met prijzen voor Azure IOT Central](https://azure.microsoft.com/pricing/details/iot-central/).


### <a name="create-device-templates"></a>Device-sjablonen maken

[IoT Plug en Play](../../iot-pnp/overview-iot-plug-and-play.md) maakt het IOT Central mogelijk om apparaten te integreren zonder dat u een Inge sloten apparaatcode hoeft te schrijven. De kern van IoT Plug en Play is een schema voor het mogelijkheidsprofiel dat de mogelijkheden van een apparaat beschrijft. In een IoT Central preview-toepassing gebruiken Device-sjablonen deze IoT Plug en Play-mogelijkheden voor het apparaat.

Als opbouw functie voor oplossingen hebt u verschillende opties voor het maken van Device-sjablonen:

- Ontwerp de sjabloon voor het apparaat in IoT Central en implementeer vervolgens het hulp model van het apparaat in de code van uw apparaat.
- Importeer een mogelijkheidsprofiel uit de [Azure Certified voor IOT-Apparaatbeheer](https://aka.ms/iotdevcat) en voeg vervolgens alle Cloud eigenschappen, aanpassingen en dash boards toe die uw IOT Central toepassing nodig heeft.
- Maak een mogelijkheidsprofiel met Visual Studio code. Implementeer uw apparaatcode vanuit het model en verbind uw apparaat met uw IoT Central-toepassing. IoT Central vindt het mogelijkheidsprofiel vanuit een opslag plaats en maakt een eenvoudige sjabloon voor uw apparaat.
- Maak een mogelijkheidsprofiel met Visual Studio code. Implementeer uw apparaatcode vanuit het model. Importeer het mogelijkheidsprofiel hand matig in uw IoT Central-toepassing en voeg vervolgens alle Cloud eigenschappen, aanpassingen en dash boards toe die uw IoT Central toepassing nodig heeft.

Als oplossings bouwer kunt u IoT Central gebruiken om code te genereren voor test apparaten om uw apparaatinstellingen te valideren.

### <a name="customize-the-ui"></a>De gebruikersinterface aanpassen

Als opbouw functie voor oplossingen kunt u ook de gebruikers interface van de IoT Central-toepassing aanpassen voor de Opera tors die verantwoordelijk zijn voor het dagelijkse gebruik van de toepassing. Aanpassingen die een oplossings bouwer kan maken, zijn onder andere:

- Het definiëren van de indeling van eigenschappen en instellingen in een apparaatsjabloon.
- Het configureren van aangepaste dashboards, zodat operators nieuwe inzichten kunnen krijgen en problemen sneller kunnen oplossen.
- Het configureren van aangepaste analyses om tijdseriegegevens van uw verbonden apparaten te verkennen.

## <a name="connect-your-devices"></a>Uw apparaten verbinden

Nadat de maker heeft gedefinieerd welke typen apparaten verbinding kunnen maken met de toepassing, maakt een apparaatontwikkelaar de code die op de apparaten moet worden uitgevoerd. Als apparaatontwikkelaar gebruikt u de [Azure IoT SDK's](https://github.com/Azure/azure-iot-sdks) van Microsoft, die open-source zijn, om de code voor uw apparaat te maken. Deze Sdk's hebben uitgebreide taal-, platform-en protocol ondersteuning om te voldoen aan uw behoeften om uw apparaten te verbinden met uw IoT Central-toepassing. De Sdk's helpen u bij het implementeren van de volgende mogelijkheden voor apparaten:

- Een beveiligde verbinding maken.
- Telemetrie verzenden.
- Status melden.
- Configuratie-updates ontvangen.

Lees de blogpost [Benefits of using the Azure IoT SDKs, and pitfalls to avoid if you don't](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/) (Voordelen van het gebruik van de Azure IoT SDK's en valkuilen die u moet vermijden als u deze niet gebruikt) voor meer informatie.

### <a name="azure-iot-edge-devices"></a>Azure IoT Edge-apparaten

En apparaten die zijn gemaakt met behulp van de [Azure IOT sdk's](https://github.com/Azure/azure-iot-sdks), kunt u ook [Azure IOT edge apparaten](../../iot-edge/about-iot-edge.md) verbinden met een IOT Central-toepassing. Met Azure IoT Edge kunt u Cloud Intelligence en aangepaste logica rechtstreeks uitvoeren op IoT-apparaten die worden beheerd door IoT Central. Met de IoT Edge runtime kunt u het volgende doen:

- Workloads op het apparaat installeren en bijwerken.
- De Azure IoT Edge-beveiligingsstandaarden op het apparaat onderhouden.
- Ervoor zorgen dat de IoT Edge-modules altijd worden uitgevoerd.
- De status van de module aan de cloud rapporteren voor externe bewaking.
- De communicatie tussen downstream bladknooppuntapparaten en een IoT Edge-apparaat, tussen modules op een IoT Edge-apparaat en tussen een IoT Edge-apparaat en de cloud beheren.

Zie [Azure IOT edge apparaten en IOT Central](./concepts-architecture.md#azure-iot-edge-devices)voor meer informatie.

## <a name="manage-your-application"></a>Uw toepassing beheren

IoT Central toepassingen worden volledig gehost door micro soft, waardoor de beheer overhead van het beheer van uw toepassingen wordt verminderd.

Als operator kunt u de IoT Central-toepassing gebruiken voor het beheren van de apparaten in uw IoT Central oplossing. Opera tors doen taken zoals:

- Het controleren van de apparaten die met de toepassing zijn verbonden.
- Het oplossen en verhelpen van problemen met apparaten.
- Het inrichten van nieuwe apparaten.

Als opbouw functie voor oplossingen kunt u aangepaste regels en acties definiëren die worden gebruikt voor het streamen van gegevens vanaf verbonden apparaten. Een operator kan deze regels op apparaatniveau in- of uitschakelen om taken binnen de toepassing te beheren en automatiseren.

Beheerders beheren de toegang tot uw toepassing met [gebruikers rollen en machtigingen](howto-administer.md).

## <a name="quotas"></a>Quota

Elk Azure-abonnement heeft standaard quota's die van invloed kunnen zijn op het bereik van uw IoT-oplossing. Op dit moment beperkt IoT Central het aantal toepassingen dat u in een abonnement kunt implementeren op 10. Neem contact op met [micro soft ondersteuning](https://azure.microsoft.com/support/options/)als u deze limiet wilt verhogen.

## <a name="next-steps"></a>Volgende stappen

Nu u een overzicht van IoT Central hebt, worden de volgende stappen hierna voorgesteld:

- Meer informatie over de beschik bare [Azure-technologieën en-services voor het maken van IOT-oplossingen](../../iot-fundamentals/iot-services-and-technologies.md).
- Raak vertrouwd met de [gebruikersinterface van Azure IoT Central](overview-iot-central-tour.md).
- Ga aan de slag door [een Azure IoT Central-toepassing te maken](quick-deploy-iot-central.md).
- Meer informatie over [IoT Plug en Play](../../iot-pnp/overview-iot-plug-and-play.md)
- Meer informatie over het [maken van Azure IOT Edge-apparaatprofiel](./tutorial-define-edge-device-type.md)
