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
ms.openlocfilehash: 05d73ae09d95879c37e86f5f3e73e35ed4607296
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82560970"
---
# <a name="what-is-azure-iot-central"></a>Wat is Azure IoT Central?

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

- Continue gegevens export biedt geen ondersteuning voor de Avro-indeling (incompatibiliteit).
- Geojson wordt momenteel niet ondersteund.
- De kaart tegel wordt momenteel niet ondersteund.
- Taken bieden geen ondersteuning voor complexe typen.
- Matrix schema typen worden niet ondersteund.
- Alleen de C apparaat-SDK en het node. js-apparaat en de service-Sdk's worden ondersteund.
- IoT Central is momenteel beschikbaar in de locaties Verenigde Staten, Europa, Azië en Stille Oceaan, Australië, Verenigd Konink rijk en Japan.
- U kunt de toepassings sjabloon van de **aangepaste toepassing (verouderd)** niet gebruiken in de locaties voor het Verenigd Konink rijk en Japan.
- Voor hulp modellen voor apparaten moeten alle interfaces in hetzelfde bestand zijn gedefinieerd.
- Ondersteuning voor [IoT Plug en Play](../../iot-pnp/overview-iot-plug-and-play.md) is in de preview-versie en wordt alleen ondersteund in geselecteerde regio's.

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

U kunt snel een nieuwe IoT Central-toepassing implementeren en deze vervolgens aanpassen aan uw specifieke vereisten in uw browser. U kunt beginnen met een _sjabloon_ voor een algemene toepassing of met een van de toepassingen sjablonen die in de branche zijn gericht op de [detail handel](../retail/overview-iot-central-retail.md), [energie](../energy/overview-iot-central-energy.md), [overheid](../government/overview-iot-central-government.md)of [gezondheids zorg](../healthcare/overview-iot-central-healthcare.md).

Als opbouw functie voor oplossingen gebruikt u de webgebaseerde hulpprogram ma's voor het maken van een _apparaatprofiel_ voor de apparaten die verbinding maken met uw toepassing. Een sjabloon voor een apparaat is de blauw druk waarmee de kenmerken en het gedrag van een type apparaat worden gedefinieerd, zoals:

- Telemetrie verzendt.
- Bedrijfseigenschappen die kunnen worden gewijzigd door een operator.
- Apparaateigenschappen die zijn ingesteld door een apparaat en die het kenmerk alleen-lezen hebben in de toepassing.
- Eigenschappen, die door een operator worden ingesteld, waarmee het gedrag van het apparaat wordt bepaald.

Deze sjabloon voor apparaten omvat:

- Een _mogelijkheidsprofiel_ met een beschrijving van de mogelijkheden die een apparaat moet implementeren, zoals de telemetrie die het verzendt en de eigenschappen die worden gerapporteerd.
- Cloud eigenschappen die niet op het apparaat zijn opgeslagen.
- Aanpassingen, Dash boards en formulieren die deel uitmaken van uw IoT Central-toepassing.

### <a name="pricing"></a>Prijzen

U kunt IoT Central-toepassing maken met een gratis proef versie van 7 dagen of een Standard-prijs plan gebruiken.

- Toepassingen die u maakt met behulp van het *gratis* abonnement, zijn zeven dagen gratis en ondersteunen Maxi maal vijf apparaten. U kunt ze op elk gewenst moment converteren om een standaard-prijs plan te gebruiken voordat ze verlopen.
- Toepassingen die u maakt met behulp van een *standaard* abonnement, worden per apparaat gefactureerd, u kunt het prijs plan **Standard 1** of **Standard 2** kiezen waarbij de eerste twee apparaten gratis zijn. Meer informatie over de gratis en standaard prijzen abonnementen op de [pagina met prijzen voor Azure IOT Central](https://azure.microsoft.com/pricing/details/iot-central/).

### <a name="create-device-templates"></a>Device-sjablonen maken

Met [IoT Plug en Play (preview)](../../iot-pnp/overview-iot-plug-and-play.md) kunnen apparaten worden geïntegreerd in IOT Central zonder dat u de code van een Inge sloten apparaat hoeft te schrijven. De kern van IoT Plug en Play (preview) is een schema voor het capaciteits model van het apparaat dat de mogelijkheden van apparaten beschrijft. In een IoT Central-toepassing gebruiken Apparaatinstellingen deze IoT Plug en Play (preview)-mogelijkheden voor apparaten.

Als opbouw functie voor oplossingen hebt u verschillende opties voor het maken van Device-sjablonen:

- Importeer een mogelijkheidsprofiel uit de [Azure Certified voor IOT-Apparaatbeheer](https://aka.ms/iotdevcat) en voeg vervolgens alle Cloud eigenschappen, aanpassingen en dash boards toe die uw IOT Central toepassing nodig heeft.
- Ontwerp de sjabloon voor het apparaat in IoT Central en implementeer vervolgens het hulp model van het apparaat in de code van uw apparaat.
- Maak een mogelijkheidsprofiel met Visual Studio code en publiceer het model naar een opslag plaats. Implementeer uw apparaatcode vanuit het model en verbind uw apparaat met uw IoT Central-toepassing. IoT Central vindt het hulp model voor het apparaat op basis van de opslag plaats en maakt een eenvoudige apparaatprofiel voor u.
- Maak een mogelijkheidsprofiel met Visual Studio code. Implementeer uw apparaatcode vanuit het model. Importeer het mogelijkheidsprofiel hand matig in uw IoT Central-toepassing en voeg vervolgens alle Cloud eigenschappen, aanpassingen en dash boards toe die uw IoT Central toepassing nodig heeft.

Als oplossings bouwer kunt u IoT Central gebruiken om code te genereren voor test apparaten om uw apparaatinstellingen te valideren.

### <a name="customize-the-ui"></a>De gebruikersinterface aanpassen

Als opbouw functie voor oplossingen kunt u ook de gebruikers interface van de IoT Central-toepassing aanpassen voor de Opera tors die verantwoordelijk zijn voor het dagelijkse gebruik van de toepassing. Aanpassingen die een oplossings bouwer kan maken, zijn onder andere:

- Het definiëren van de indeling van eigenschappen en instellingen in een apparaatsjabloon.
- Het configureren van aangepaste dashboards, zodat operators nieuwe inzichten kunnen krijgen en problemen sneller kunnen oplossen.
- Het configureren van aangepaste analyses om tijdseriegegevens van uw verbonden apparaten te verkennen.

## <a name="pricing"></a>Prijzen

U kunt IoT Central-toepassing maken met een gratis proef versie van 7 dagen of een Standard-prijs plan gebruiken.

- Toepassingen die u maakt met behulp van het *gratis* abonnement, zijn zeven dagen gratis en ondersteunen Maxi maal vijf apparaten. U kunt ze op elk gewenst moment converteren om een standaard-prijs plan te gebruiken voordat ze verlopen.
- Toepassingen die u maakt met behulp van het *Standard* -abonnement, worden per apparaat gefactureerd. u kunt het prijs plan **Standard 1** of **Standard 2** kiezen waarbij de eerste twee apparaten gratis zijn. Meer informatie over [IOT Central prijzen](https://aka.ms/iotcentral-pricing).

## <a name="connect-your-devices"></a>Uw apparaten verbinden

Azure IoT Central maakt gebruik van de [azure IOT hub Device Provisioning Service (DPS)](../../iot-dps/about-iot-dps.md) om alle apparaatregistratie en-verbinding te beheren.

Met DPS kunt u het volgende doen:

- IoT Central voor het ondersteunen van onboarding en het verbinden van apparaten op schaal.
- U kunt de referenties van het apparaat genereren en de apparaten offline configureren zonder de apparaten te registreren via IoT Central-gebruikers interface.
- Apparaten om verbinding te maken met hand tekeningen voor gedeelde toegang.
- Apparaten om verbinding te maken met behulp van de industrie standaard X. 509-certificaten.
- U kunt uw eigen apparaat-Id's gebruiken om apparaten te registreren in IoT Central. Het gebruik van uw eigen apparaat-Id's vereenvoudigt de integratie met bestaande back-office-systemen.
- Een enkele, consistente manier om apparaten te verbinden met IoT Central.

Zie [Get connected to Azure IOT Central](./concepts-get-connected.md)voor meer informatie.

### <a name="azure-iot-edge-devices"></a>Azure IoT Edge-apparaten

En apparaten die zijn gemaakt met behulp van de [Azure IOT sdk's](https://github.com/Azure/azure-iot-sdks), kunt u ook [Azure IOT edge apparaten](../../iot-edge/about-iot-edge.md) verbinden met een IOT Central-toepassing. Met Azure IoT Edge kunt u Cloud Intelligence en aangepaste logica rechtstreeks uitvoeren op IoT-apparaten die worden beheerd door IoT Central. Met de IoT Edge runtime kunt u het volgende doen:

- Workloads op het apparaat installeren en bijwerken.
- De Azure IoT Edge-beveiligingsstandaarden op het apparaat onderhouden.
- Ervoor zorgen dat de IoT Edge-modules altijd worden uitgevoerd.
- De status van de module aan de cloud rapporteren voor externe bewaking.
- De communicatie tussen downstream bladknooppuntapparaten en een IoT Edge-apparaat, tussen modules op een IoT Edge-apparaat en tussen een IoT Edge-apparaat en de cloud beheren.

Zie [Azure IOT edge apparaten en IOT Central](concepts-architecture.md#azure-iot-edge-devices)voor meer informatie.

## <a name="stay-connected"></a>Blijf verbonden

IoT Central toepassingen worden volledig gehost door micro soft, waardoor de beheer overhead van het beheer van uw toepassingen wordt verminderd.

Als operator kunt u de IoT Central-toepassing gebruiken voor het beheren van de apparaten in uw IoT Central oplossing. Opera tors doen taken zoals:

- Het controleren van de apparaten die met de toepassing zijn verbonden.
- Het oplossen en verhelpen van problemen met apparaten.
- Het inrichten van nieuwe apparaten.

Als opbouw functie voor oplossingen kunt u aangepaste regels en acties definiëren die worden gebruikt voor het streamen van gegevens vanaf verbonden apparaten. Een operator kan deze regels op apparaatniveau in- of uitschakelen om taken binnen de toepassing te beheren en automatiseren.

Beheerders beheren de toegang tot uw toepassing met [gebruikers rollen en machtigingen](howto-administer.md).

Met een IoT-oplossing die is ontworpen om op schaal te worden gebruikt, is een gestructureerde benadering van het beheer van apparaten belang rijk. Het is niet voldoende alleen om uw apparaten met de cloud te verbinden. u moet uw apparaten aangesloten blijven en in orde zijn. Een operator kan de volgende IoT Central mogelijkheden gebruiken voor het beheren van uw apparaten gedurende de levens cyclus van de toepassing:

### <a name="dashboards"></a>Dashboards 

Ingebouwde [Dash boards](./howto-set-up-template.md#generate-default-views) bieden een aanpas bare gebruikers interface voor het bewaken van apparaatstatus en telemetrie. Begin met een vooraf gebouwd dash board in een [toepassings sjabloon](howto-use-app-templates.md) of maak uw eigen Dash boards die zijn afgestemd op de behoeften van uw Opera tors. U kunt Dash boards delen met alle gebruikers in uw toepassing of ze privé laten.

### <a name="rules-and-actions"></a>Regels en acties

Bouw [aangepaste regels](tutorial-create-telemetry-rules.md) op basis van de apparaatstatus en telemetrie om apparaten te identificeren die aandacht vereisen. Configureer acties om de juiste mensen op de hoogte te stellen en ervoor te zorgen dat corrigerende maat regelen tijdig worden genomen.

### <a name="jobs"></a>Taken

Met [taken](howto-run-a-job.md) kunt u afzonderlijke of bulk updates Toep assen op apparaten door eigenschappen in te stellen of opdrachten aan te roepen. 

### <a name="user-roles-and-permissions"></a>Gebruikers rollen en machtigingen

[Met rollen en machtigingen](howto-manage-users-roles.md) kan een beheerder elke gebruikers ervaring aanpassen. Een beheerder gebruikt de Web-UI om rollen te maken en machtigingen toe te wijzen.

## <a name="transform-your-iot-data"></a>Uw IoT-gegevens transformeren

Als toepassings platform biedt IoT Central u de mogelijkheid om uw IoT-gegevens te transformeren in de zakelijke inzichten waarmee actie kan worden ondernomen. [Regels](./tutorial-create-telemetry-rules.md), [gegevens export](./howto-export-data.md)en de [open bare rest API](https://docs.microsoft.com/learn/modules/manage-iot-central-apps-with-rest-api/) zijn voor beelden van hoe u IOT Central kunt integreren met line-of-business-toepassingen:

![Hoe IoT Central uw IoT-gegevens kunt transformeren](media/overview-iot-central/transform.png)

### <a name="monitor-device-health-and-operations-using-rules"></a>De status en bewerkingen van apparaten bewaken met behulp van regels

Wanneer uw apparaten zijn verbonden en gegevens verzenden, kunnen regels identificeren van apparaten die problemen ondervinden of fout berichten verzenden, zodat u deze kunt oplossen met minimale downtime. Bouw regels in uw IoT Central-toepassing om telemetrie van uw apparaten te controleren en een operator te waarschuwen wanneer een metriek een drempel waarde overschrijdt of een apparaat een specifiek bericht verzendt. E-mail acties en webhooks voor uw regels melden de juiste personen en de juiste downstream-systemen.

### <a name="run-custom-analytics-and-processing-on-your-exported-data"></a>Aangepaste analyse en verwerking uitvoeren op uw geëxporteerde gegevens

U kunt zakelijke inzichten genereren, zoals het vaststellen van de prestaties van machine efficiëntie of het voors pellen van toekomstig energie gebruik op een fabriek, door aangepaste analyse pijplijnen te bouwen om de telemetrie van uw apparaten te verwerken en de resultaten op te slaan. Gegevens exports configureren in uw IoT Central-toepassing voor het exporteren van telemetrie, wijzigingen in Apparaatinstellingen en wijzigingen in de apparaatinstellingen in andere services waar u de gegevens kunt analyseren, opslaan en visualiseren met uw favoriete hulpprogram ma's.

### <a name="build-custom-iot-solutions-and-integrations-with-the-rest-apis"></a>Aangepaste IoT-oplossingen en integraties bouwen met de REST-Api's

Bouw IoT-oplossingen zoals:

- Mobiele-Companion-apps waarmee apparaten op afstand kunnen worden ingesteld en beheerd.
- Aangepaste integraties die bestaande line-of-business-toepassingen kunnen gebruiken om te communiceren met uw IoT-apparaten en-gegevens.
- Toepassingen voor Apparaatbeheer voor het maken van apparaten, onboarding, beheer en gegevens toegang.

## <a name="quotas"></a>Quota

Elk Azure-abonnement heeft standaard quota's die van invloed kunnen zijn op het bereik van uw IoT-oplossing. Op dit moment beperkt IoT Central het aantal toepassingen dat u in een abonnement kunt implementeren op 10. Neem contact op met [micro soft ondersteuning](https://azure.microsoft.com/support/options/)als u deze limiet wilt verhogen.

## <a name="next-steps"></a>Volgende stappen

Nu u een overzicht van IoT Central hebt, vindt u hier een aantal voorgestelde volgende stappen:

- Meer informatie over de beschik bare [Azure-technologieën en-services voor het maken van IOT-oplossingen](../../iot-fundamentals/iot-services-and-technologies.md).
- Raak vertrouwd met de [gebruikersinterface van Azure IoT Central](overview-iot-central-tour.md).
- Ga aan de slag door [een Azure IoT Central-toepassing te maken](quick-deploy-iot-central.md).
- Meer informatie over [IoT Plug en Play (preview)](../../iot-pnp/overview-iot-plug-and-play.md).
- Meer informatie over het [verbinden van een Azure IOT edge apparaat](./tutorial-add-edge-as-leaf-device.md).
- Meer informatie over [Azure IOT-technologieën en-services](../../iot-fundamentals/iot-services-and-technologies.md).

Als u een ontwikkelaar van een apparaat bent en u wilt in bepaalde code gaan, is de voorgestelde volgende stap het [maken en koppelen van een client toepassing aan uw Azure IOT Central-toepassing](./tutorial-connect-device-nodejs.md).
