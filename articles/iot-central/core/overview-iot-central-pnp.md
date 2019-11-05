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
ms.openlocfilehash: dfb550fd0bc93a6b865495bf2c19f394fb0737ed
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/28/2019
ms.locfileid: "72987884"
---
# <a name="what-is-azure-iot-central-preview-features"></a>Wat is Azure IoT Central (preview-functies)?

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

> [!WARNING]
> De [IoT Plug en Play](../../iot-pnp/overview-iot-plug-and-play.md) -mogelijkheden in azure IOT Central zijn momenteel beschikbaar als open bare preview. Gebruik geen IoT Plug en Play ingeschakelde IoT Central toepassing voor werk belastingen voor de productie. Voor productie omgevingen wordt een IoT Central-toepassing gebruikt die is gemaakt op basis van een huidige, algemeen beschik bare, toepassings sjabloon.

Azure IoT Central is een IoT-toepassings platform waarmee de belasting en kosten voor het ontwikkelen, beheren en onderhouden van IoT-oplossingen op bedrijfs niveau worden verminderd. Als u ervoor kiest om met Azure IoT Central te bouwen, hebt u de mogelijkheid om tijd, geld en energie te best Eden aan het trans formatie van uw bedrijf met IoT-gegevens, in plaats van alleen maar een complexe en voortdurend veranderende IoT-infra structuur te onderhouden en bij te werken.

Met de webgebruikersinterface kunt u de voor waarden van apparaten bewaken, regels maken en miljoenen apparaten en hun gegevens in hun levens cyclus beheren. Daarnaast kunt u hiermee op Device Insights reageren door IoT Intelligence uit te breiden in line-of-business-toepassingen.

Dit artikel bevat een overzicht van Azure IoT Central:

- De typische persona's die aan een project zijn gekoppeld.
- Het maken van uw toepassing.
- Het maken van verbinding tussen uw apparaten en uw toepassing
- Het beheren van uw toepassing.
- Overzicht van IoT Edge mogelijkheden in IoT Central
- Hoe u uw Azure IoT Edge runtime-apparaten verbindt met uw toepassing.


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

- Een _oplossings bouwer_ is verantwoordelijk voor het definiëren van de typen apparaten die verbinding maken met de toepassing en het aanpassen van de toepassing voor de operator.
- Een _operator_ beheert de apparaten die met de toepassing zijn verbonden.
- Een _beheerder_ is verantwoordelijk voor beheer taken, zoals het beheren van [gebruikers rollen en machtigingen](howto-administer.md) binnen de toepassing.
- Een _apparaatontwikkelaar_ maakt de code die wordt uitgevoerd op een apparaat dat met uw toepassing is verbonden.
- Een _ontwikkelaar van een apparaat/module_ maakt de code/module die wordt uitgevoerd op een apparaat dat is verbonden met uw toepassing.

## <a name="create-your-azure-iot-central-application"></a>Uw Azure IoT Central-toepassing maken

Als maker gebruikt u Azure IoT Central om een aangepaste, in de cloud gehoste IoT-oplossing voor uw organisatie te maken. Een aangepaste IoT-oplossing bestaat meestal uit:

- Een cloudtoepassing die telemetriegegevens van uw apparaten ontvangt en die u in staat stelt om deze apparaten te beheren.
- Meerdere apparaten waarop aangepaste code wordt uitgevoerd en die met uw cloudtoepassing zijn verbonden.

U kunt snel een nieuwe Azure IoT Central-toepassing implementeren en deze vervolgens aanpassen aan uw specifieke vereisten in uw browser. Als ontwerper gebruikt u de webgebaseerde hulpprogram ma's voor het maken van een _apparaatprofiel_ voor de apparaten die verbinding maken met uw toepassing. Een sjabloon voor een apparaat is de blauw druk waarmee de kenmerken en het gedrag van een type apparaat worden gedefinieerd, zoals:

- Telemetrie verzendt.
- Bedrijfseigenschappen die kunnen worden gewijzigd door een operator.
- Apparaateigenschappen die zijn ingesteld door een apparaat en die het kenmerk alleen-lezen hebben in de toepassing.
- Eigenschappen, die door een operator worden ingesteld, waarmee het gedrag van het apparaat wordt bepaald.

Deze sjabloon voor apparaten omvat:

- Een _mogelijkheidsprofiel_ met een beschrijving van de mogelijkheden die een apparaat moet implementeren, zoals de telemetrie die het verzendt en de eigenschappen die worden gerapporteerd.
- Cloud eigenschappen die niet worden opgeslagen op het apparaat.
- Aanpassingen, Dash boards en formulieren die deel uitmaken van uw IoT Central-toepassing.

### <a name="create-device-templates"></a>Device-sjablonen maken

[IoT Plug en Play](../../iot-pnp/overview-iot-plug-and-play.md) maakt het IOT Central mogelijk om apparaten te integreren zonder dat u een Inge sloten apparaatcode hoeft te schrijven. De kern van IoT Plug en Play is een schema voor het mogelijkheidsprofiel dat de mogelijkheden van een apparaat beschrijft. In een IoT Central preview-toepassing gebruiken Device-sjablonen deze IoT Plug en Play-mogelijkheden voor het apparaat.

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


## <a name="what-is-azure-iot-central-with-azure-iot-edge-preview-features"></a>Wat is Azure IoT Central met Azure IoT Edge (preview-functies)

IoT Central de Port Folio uit te breiden door Azure IoT Edge-apparaten te ondersteunen. 

Bedrijven kunnen nu Cloud Intelligence rechtstreeks uitvoeren op IoT-apparaten die worden beheerd door Azure IoT Central. Deze nieuwe functie helpt bedrijven bij het verbinden en beheren van Azure IoT Edge apparaten met Azure IoT Edge runtime, het implementeren van software modules, het publiceren van inzichten en het nemen van maat regelen op basis van IoT Central. 

[Overzicht van Azure IoT Edge](../../iot-edge/about-iot-edge.md)

### <a name="overview-of-iot-edge-capabilities-in-iot-central"></a>Overzicht van IoT Edge mogelijkheden in IoT Central

De Azure IoT Edge-runtime maakt aangepaste en cloudlogica op IoT Edge-apparaten mogelijk. IoT Edge apparaat wordt aangedreven door de runtime en voert beheer-en communicatie bewerkingen uit. 

Azure IoT Edge runtime voert de volgende functies uit:

- Workloads op het apparaat installeren en bijwerken.
- De Azure IoT Edge-beveiligingsstandaarden op het apparaat onderhouden.
- Ervoor zorgen dat de IoT Edge-modules altijd worden uitgevoerd.
- De status van de module aan de cloud rapporteren voor externe bewaking.
- De communicatie tussen downstream bladknooppuntapparaten en een IoT Edge-apparaat, tussen modules op een IoT Edge-apparaat en tussen een IoT Edge-apparaat en de cloud beheren.

![Overzicht van IoT Central Azure IoT Edge](./media/overview-iot-central-pnp-edge/iotedge.png)

Azure IoT Central voert de volgende functies uit: 

- Azure IoT Edge ondersteuning voor de Device-sjabloon waarin de mogelijkheden worden beschreven die een Azure IoT Edge apparaat moet implementeren, zoals 
  1. upload functionaliteit voor het implementatie manifest, waarmee u een manifest voor een vloot apparaat kunt beheren
  2. modules die worden uitgevoerd op het Azure IoT Edge apparaat
  3. telemetrie elke module verzendt
  4. eigenschappen van elk module rapport en 
  5. opdracht elke module reageert op
  6. Relaties tot stand brengen Azure IoT Edge tussen het functionaliteits model en het functionaliteits model van het downstream-apparaat
  7. Cloud eigenschappen die niet op het Azure IoT Edge apparaat zijn opgeslagen
  8. Aanpassingen, Dash boards en formulieren die deel uitmaken van uw IoT Central-toepassing

  [Azure IoT Edge-apparaatprofiel maken](./tutorial-define-edge-device-type-pnp.md)
   
- Azure IoT Edge apparaten op schaal inrichten met behulp van Azure IoT Device Provisioning Service
- Trigger regels en acties uitvoeren op Azure IoT Edge apparaten
- Dash boards en analyses bouwen 
- Continue gegevens export van telemetrie stromen van Azure IoT Edge-apparaten

### <a name="azure-iot-edge-device-types-in-iot-central"></a>Azure IoT Edge typen apparaten in IoT Central

Azure IoT Central classificeert Azure IoT Edge apparaattypen als volgt:

- Azure IoT Edge apparaat als een Leaf-apparaat. Azure IoT Edge apparaat kan downstream-apparaten hebben, maar downstream-apparaten zijn niet ingericht in IoT Central
- Azure IoT Edge apparaat als een gateway apparaat met downstream-apparaten. Zowel het gateway apparaat als de downstream-apparaten zijn ingericht in IoT Central

![Overzicht van IoT Central Azure IoT Edge](./media/overview-iot-central-pnp-edge/gatewayedge.png)

### <a name="azure-iot-edge-patterns-supported-in-iot-central"></a>Azure IoT Edge patronen ondersteund in IoT Central

- Azure IoT Edge als een Leaf-apparaat ![Azure IoT Edge als een Leaf-apparaat](./media/overview-iot-central-pnp-edge/edgeasleafdevice.png) Azure IoT Edge apparaat wordt ingericht in IoT Central en alle downstream-apparaten en de telemetrie van het apparaat worden weer gegeven als afkomstig van Azure IoT Edgeapparaat. Downstream-apparaten als een verbinding met het Azure IoT Edge apparaat niet wordt ingericht in IoT Central. 

- Azure IoT Edge gateway apparaat dat is verbonden met downstream-apparaten met identiteits ![Azure IoT Edge met downstream-apparaat-id](./media/overview-iot-central-pnp-edge/edgewithdownstreamdeviceidentity.png) Azure IoT Edge apparaat wordt ingericht in IoT Central, samen met de downstream-apparaten die zijn verbonden met de Azure IoT Edge apparaatconfiguratie. Runtime-ondersteuning voor het inrichten van downstream-apparaten via gateway is gepland voor de toekomst. IoT Central wordt de Cloud eerst ingericht voor het inrichten van de downstream-apparaten en worden de referenties hand matig beheerd op het downstream-apparaat. Het apparaat moet eerst worden ingericht van downstream-apparaten die zijn gepland voor toekomstige semesters. 

- Azure IoT Edge gateway apparaat dat is verbonden met downstream-apparaten met een identiteit die wordt verstrekt door de Edge gateway ![Azure IoT Edge met het downstream-apparaat zonder identiteit](./media/overview-iot-central-pnp-edge/edgewithoutdownstreamdeviceidentity.png) Azure IoT Edge apparaat wordt ingericht in IoT Central en de downstream apparaten die zijn verbonden met het Azure IoT Edge apparaat. Runtime-ondersteuning van gateway die identiteit levert aan downstream-apparaten en het inrichten van downstream-apparaten is gepland voor de toekomst. U kunt uw eigen module voor identiteits vertalingen maken en IoT Central dit patroon wordt ondersteund. 

## <a name="next-steps"></a>Volgende stappen

Nu u een overzicht van Azure IoT Central hebt, zijn dit mogelijke volgende stappen:

- Leer wat de verschillen zijn tussen de oplossingsversnellers [Azure IoT Central en Azure IoT](overview-iot-options.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).
- Raak vertrouwd met de [gebruikersinterface van Azure IoT Central](overview-iot-central-tour-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).
- Ga aan de slag door [een Azure IoT Central-toepassing te maken](quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).
- Meer informatie over [IoT Plug en Play](../../iot-pnp/overview-iot-plug-and-play.md)
- Meer informatie over het [maken van Azure IOT Edge-apparaatprofiel](./tutorial-define-edge-device-type-pnp.md)
