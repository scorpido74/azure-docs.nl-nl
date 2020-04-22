---
title: Wat is Azure IoT Central? | Microsoft Docs
description: Azure IoT Central is een IoT-toepassingsplatform dat het creëren van IoT-oplossingen vereenvoudigt en helpt de lasten en kosten van IoT-beheerbewerkingen en -ontwikkeling te verminderen. In dit artikel vindt u een overzicht van de functies van Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 12/10/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: timlt
ms.openlocfilehash: ffaab22efb2f6969f03720abba4a7afc6387021a
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758222"
---
# <a name="what-is-azure-iot-central"></a>Wat is Azure IoT Central?

IoT Central is een IoT-applicatieplatform dat de lasten en kosten van het ontwikkelen, beheren en onderhouden van enterprise-grade IoT-oplossingen vermindert. Door te bouwen met IoT Central u tijd, geld en energie richten op het transformeren van uw bedrijf met IoT-gegevens, in plaats van alleen een complexe en voortdurend evoluerende IoT-infrastructuur te onderhouden en bij te werken.

Met de web-gebruikersinterface u de apparaatomstandigheden controleren, regels maken en miljoenen apparaten en hun gegevens gedurende hun hele levenscyclus beheren. Bovendien stelt het u in staat om te reageren op apparaatinzichten door IoT-intelligentie uit te breiden naar line-of-business toepassingen.

In dit artikel wordt beschreven, voor IoT Central:

- De typische persona's die aan een project zijn gekoppeld.
- Het maken van uw toepassing.
- Het maken van verbinding tussen uw apparaten en uw toepassing
- Het beheren van uw toepassing.
- Azure IoT Edge-mogelijkheden in IoT Central.
- Uw Azure IoT Edge-runtime-apparaten verbinden met uw toepassing.

## <a name="known-issues"></a>Bekende problemen

- Continue gegevensexport ondersteunt het Avro-formaat (incompatibiliteit) niet.
- GeoJSON wordt momenteel niet ondersteund.
- Kaarttegel wordt momenteel niet ondersteund.
- Taken ondersteunen geen complexe typen.
- Matrixschematypen worden niet ondersteund.
- Alleen de SDK van het C-apparaat en het Node.js-apparaat en de service-SDK's worden ondersteund.
- IoT Central is momenteel beschikbaar in de Verenigde Staten, Europa, Azië-Pacific, Australië, het Verenigd Koninkrijk en Japan.
- U de toepassingssjabloon **Aangepaste toepassing (legacy)** niet gebruiken in de locaties in het Verenigd Koninkrijk en Japan.
- Apparaatcapaciteitsmodellen moeten alle interfaces inline in hetzelfde bestand hebben gedefinieerd.
- Ondersteuning voor [IoT Plug and Play](../../iot-pnp/overview-iot-plug-and-play.md) is in preview en wordt alleen ondersteund in geselecteerde regio's.

## <a name="personas"></a>Persona's

De IoT Central-documentatie verwijst naar vier persona's die interactie hebben met een IoT Central-toepassing:

- Een _oplossingsbouwer_ is verantwoordelijk voor het definiëren van de typen apparaten die verbinding maken met de toepassing en het aanpassen van de toepassing voor de operator.
- Een _operator_ beheert de apparaten die met de toepassing zijn verbonden.
- Een _beheerder_ is verantwoordelijk voor administratieve taken, zoals het beheren van [gebruikersrollen en machtigingen](howto-administer.md) binnen de toepassing.
- Een _apparaatontwikkelaar_ maakt de code die wordt uitgevoerd op een apparaat of IoT Edge-module die is verbonden met uw toepassing.

## <a name="create-your-iot-central-application"></a>Uw IoT Central-toepassing maken

Als oplossingsbouwer gebruikt u IoT Central om een aangepaste, door de cloud gehoste IoT-oplossing voor uw organisatie te maken. Een aangepaste IoT-oplossing bestaat meestal uit:

- Een cloudtoepassing die telemetriegegevens van uw apparaten ontvangt en die u in staat stelt om deze apparaten te beheren.
- Meerdere apparaten waarop aangepaste code wordt uitgevoerd en die met uw cloudtoepassing zijn verbonden.

U snel een nieuwe IoT Central-toepassing implementeren en deze vervolgens aanpassen aan uw specifieke vereisten in uw browser. U beginnen met een algemene _toepassingssjabloon_ of met een van de branchegerichte toepassingssjablonen voor [Retail](../retail/overview-iot-central-retail-pnp.md), [Energie](../energy/overview-iot-central-energy.md), [Overheid](../government/overview-iot-central-government.md)of [Gezondheidszorg](../healthcare/overview-iot-central-healthcare.md).

Als oplossingsbouwer gebruikt u de webgebaseerde tools om een _apparaatsjabloon_ te maken voor de apparaten die verbinding maken met uw toepassing. Een apparaatsjabloon is de blauwdruk die de kenmerken en het gedrag van een type apparaat definieert, zoals:

- Telemetrie die het verzendt.
- Bedrijfseigenschappen die kunnen worden gewijzigd door een operator.
- Apparaateigenschappen die zijn ingesteld door een apparaat en die het kenmerk alleen-lezen hebben in de toepassing.
- Eigenschappen die een operator instelt, die het gedrag van het apparaat bepalen.

Deze apparaatsjabloon bevat:

- Een _apparaatcapaciteitsmodel_ dat de mogelijkheden beschrijft die een apparaat moet implementeren, zoals de telemetrie die het verzendt en de eigenschappen die het rapporteert.
- Cloudeigenschappen die niet op het apparaat zijn opgeslagen.
- Aanpassingen, dashboards en formulieren die deel uitmaken van uw IoT Central-toepassing.

### <a name="pricing"></a>Prijzen

U IoT Central-toepassing maken met een gratis proefperiode van 7 dagen of een standaard prijsplan gebruiken.

- Toepassingen die u maakt met behulp van het *gratis* abonnement zijn zeven dagen gratis en ondersteunen maximaal vijf apparaten. U ze converteren naar een standaard prijsplan op elk gewenst moment voordat ze verlopen.
- Toepassingen die u maakt met behulp van een *standaardplan* worden gefactureerd op basis van een apparaat, u kiezen voor **een Standaard 1-** of **Standard 2-prijsplan** waarbij de eerste twee apparaten gratis zijn. Meer informatie over de gratis en standaard prijsplannen vindt u op de [prijspagina van Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

### <a name="create-device-templates"></a>Apparaatsjablonen maken

[IoT Plug and Play (preview)](../../iot-pnp/overview-iot-plug-and-play.md) stelt IoT Central in staat om apparaten te integreren zonder dat u een ingesloten apparaatcode schrijft. De kern van IoT Plug and Play (preview) is een modelschema voor apparaatmogelijkheden dat apparaatmogelijkheden beschrijft. In een IoT Central-toepassing gebruiken apparaatsjablonen deze IoT Plug and Play -apparaatcapaciteitsmodellen .In an IoT Central application, device templates use these IoT Plug and Play (preview) device capability models.

Als oplossingsbouwer hebt u verschillende opties voor het maken van apparaatsjablonen:

- Importeer een apparaatcapaciteitsmodel uit de [Azure Certified for IoT-apparaatcatalogus](https://aka.ms/iotdevcat) en voeg vervolgens cloudeigenschappen, aanpassingen en dashboards toe die uw IoT Central-toepassingsbehoeften nodig hebben.
- Ontwerp de apparaatsjabloon in IoT Central en implementeer vervolgens het apparaatcapaciteitsmodel in uw apparaatcode.
- Maak een apparaatcapaciteitsmodel met Visual Studio-code en publiceer het model naar een opslagplaats. Implementeer uw apparaatcode van het model en verbind uw apparaat met uw IoT Central-toepassing. IoT Central vindt het apparaatcapaciteitsmodel uit de repository en maakt een eenvoudige apparaatsjabloon voor u.
- Maak een apparaatcapaciteitsmodel met Visual Studio-code. Implementeer uw apparaatcode van het model. Importeer het apparaatcapaciteitsmodel handmatig in uw IoT Central-toepassing en voeg vervolgens alle cloudeigenschappen, aanpassingen en dashboards toe die uw IoT Central-toepassingsbehoeften nodig hebben.

Als oplossingsbouwer u IoT Central gebruiken om code te genereren voor testapparaten om uw apparaatsjablonen te valideren.

### <a name="customize-the-ui"></a>De gebruikersinterface aanpassen

Als oplossingsbouwer u ook de Gebruikersinterface van de IoT Central-toepassing aanpassen voor de operators die verantwoordelijk zijn voor het dagelijks gebruik van de toepassing. Aanpassingen die een oplossingsbouwer kan maken, zijn onder andere:

- Het definiëren van de indeling van eigenschappen en instellingen in een apparaatsjabloon.
- Het configureren van aangepaste dashboards, zodat operators nieuwe inzichten kunnen krijgen en problemen sneller kunnen oplossen.
- Het configureren van aangepaste analyses om tijdseriegegevens van uw verbonden apparaten te verkennen.

## <a name="pricing"></a>Prijzen

U IoT Central-toepassing maken met een gratis proefperiode van 7 dagen of een standaard prijsplan gebruiken.

- Toepassingen die u maakt met behulp van het *gratis* abonnement zijn zeven dagen gratis en ondersteunen maximaal vijf apparaten. U ze converteren naar een standaard prijsplan op elk gewenst moment voordat ze verlopen.
- Toepassingen die u maakt met behulp van het *standaardplan* worden gefactureerd op basis van een apparaat, u kiezen voor **standaard 1** of **Standaard 2-prijsplan** waarbij de eerste twee apparaten gratis zijn. Meer informatie over [IoT Central-prijzen](https://aka.ms/iotcentral-pricing).

## <a name="connect-your-devices"></a>Uw apparaten verbinden

Azure IoT Central gebruikt de [DPS (Azure IoT Hub Device Provisioning service)](../../iot-dps/about-iot-dps.md) om alle apparaatregistratie en -verbinding te beheren.

Het gebruik van DPS maakt:

- IoT Central ondersteunt onboarding en het verbinden van apparaten op schaal.
- U genereert apparaatreferenties en configureert de apparaten offline zonder de apparaten te registreren via de IoT Central UI.
- Apparaten om verbinding te maken met behulp van handtekeningen voor gedeelde toegang.
- Apparaten om verbinding te maken met behulp van industriestandaard X.509-certificaten.
- U gebruikt uw eigen apparaat-id's om apparaten in IoT Central te registreren. Het gebruik van uw eigen apparaat-id vereenvoudigt de integratie met bestaande back-officesystemen.
- Eén consistente manier om apparaten aan te sluiten op IoT Central.

Zie [Verbinding maken met Azure IoT Central](./concepts-get-connected.md)voor meer informatie.

### <a name="azure-iot-edge-devices"></a>Azure IoT Edge-apparaten

Naast apparaten die zijn gemaakt met de [Azure IoT SDKs,](https://github.com/Azure/azure-iot-sdks)u azure [IoT Edge-apparaten](../../iot-edge/about-iot-edge.md) ook verbinden met een IoT Central-toepassing. Met Azure IoT Edge u cloudintelligence en aangepaste logica rechtstreeks uitvoeren op IoT-apparaten die worden beheerd door IoT Central. Met de IoT Edge-runtime u:

- Workloads op het apparaat installeren en bijwerken.
- De Azure IoT Edge-beveiligingsstandaarden op het apparaat onderhouden.
- Ervoor zorgen dat de IoT Edge-modules altijd worden uitgevoerd.
- De status van de module aan de cloud rapporteren voor externe bewaking.
- De communicatie tussen downstream bladknooppuntapparaten en een IoT Edge-apparaat, tussen modules op een IoT Edge-apparaat en tussen een IoT Edge-apparaat en de cloud beheren.

Zie [Azure IoT Edge-apparaten en IoT Central](concepts-architecture.md#azure-iot-edge-devices)voor meer informatie.

## <a name="stay-connected"></a>Blijf verbonden

IoT Central-toepassingen worden volledig gehost door Microsoft, wat de beheeroverhead van het beheer van uw toepassingen vermindert.

Als operator gebruikt u de IoT Central-toepassing om de apparaten in uw IoT Central-oplossing te beheren. Operators voeren taken uit zoals:

- Het controleren van de apparaten die met de toepassing zijn verbonden.
- Het oplossen en verhelpen van problemen met apparaten.
- Het inrichten van nieuwe apparaten.

Als oplossingsbouwer u aangepaste regels en acties definiëren die via gegevensstreaming vanaf verbonden apparaten werken. Een operator kan deze regels op apparaatniveau in- of uitschakelen om taken binnen de toepassing te beheren en automatiseren.

Beheerders beheren de toegang tot uw toepassing met [gebruikersrollen en machtigingen.](howto-administer.md)

Met elke IoT-oplossing die is ontworpen om op schaal te werken, is een gestructureerde benadering van apparaatbeheer belangrijk. Het is niet genoeg om alleen uw apparaten met de cloud te verbinden, u moet uw apparaten verbonden en gezond houden. Een operator kan de volgende IoT Central-mogelijkheden gebruiken om uw apparaten gedurende de hele levenscyclus van de toepassing te beheren:

### <a name="dashboards"></a>Dashboards 

Ingebouwde [dashboards](./howto-set-up-template.md#generate-default-views) bieden een aanpasbare gebruikersinterface om de status van het apparaat en telemetrie te controleren. Begin met een vooraf ingebouwd dashboard in een [toepassingssjabloon](howto-use-app-templates.md) of maak uw eigen dashboards die zijn afgestemd op de behoeften van uw operators. U dashboards delen met alle gebruikers in uw toepassing of ze privé houden.

### <a name="rules-and-actions"></a>Regels en acties 

Stel [aangepaste regels](tutorial-create-telemetry-rules.md) op basis van apparaatstatus en telemetrie om apparaten te identificeren die aandacht nodig hebben. Stel acties in om de juiste mensen op de hoogte te stellen en ervoor te zorgen dat corrigerende maatregelen tijdig worden genomen.

### <a name="jobs"></a>Taken 

[Met taken](howto-run-a-job.md) u enkele of bulkupdates toepassen op apparaten door eigenschappen of aanroepende opdrachten in te stellen. 

### <a name="user-roles-and-permissions"></a>Gebruikersrollen en machtigingen

[Met rollen en machtigingen](howto-manage-users-roles.md) kan een beheerder de ervaring van elke gebruiker aanpassen. Een beheerder gebruikt de webgebruikersinterface om rollen te maken en machtigingen toe te wijzen.

## <a name="transform-your-iot-data"></a>Uw IoT-gegevens transformeren

Als applicatieplatform u met IoT Central uw IoT-gegevens omzetten in de bedrijfsinzichten die bruikbare resultaten opleveren. [Regels,](./tutorial-create-telemetry-rules.md) [gegevensexport](./howto-export-data.md)en de [openbare REST API](https://docs.microsoft.com/learn/modules/manage-iot-central-apps-with-rest-api/) zijn voorbeelden van hoe u IoT Central integreren met line-of-business-toepassingen:

![Hoe IoT Central uw IoT-gegevens kan transformeren](media/overview-iot-central/transform.png)

### <a name="monitor-device-health-and-operations-using-rules"></a>De status en bewerkingen van apparaten controleren met behulp van regels

Wanneer uw apparaten zijn verbonden en gegevens verzenden, kunnen regels identificeren dat apparaten problemen ondervinden of foutmeldingen verzenden, zodat u ze met minimale downtime oplossen. Bouw regels in uw IoT Central-toepassing om telemetrie vanaf uw apparaten te controleren en een operator te waarschuwen wanneer een statistiek een drempel overschrijdt of een apparaat een specifiek bericht verzendt. E-mailacties en webhooks voor uw regels stellen de juiste mensen en de juiste downstreamsystemen op de hoogte.

### <a name="run-custom-analytics-and-processing-on-your-exported-data"></a>Aangepaste analyses en verwerkingen uitvoeren op uw geëxporteerde gegevens

U bedrijfsinzichten genereren, zoals het bepalen van trends op het gebied van machine-efficiëntie of het voorspellen van toekomstig energieverbruik op een fabrieksvloer, door aangepaste analysepijplijnen te bouwen om telemetrie van uw apparaten te verwerken en de resultaten op te slaan. Configureer gegevensexport in uw IoT Central-toepassing om telemetrie, wijzigingen in apparaateigendom en wijzigingen in apparaatsjabloonen te exporteren naar andere services waar u de gegevens analyseren, opslaan en visualiseren met uw voorkeurshulpprogramma's.

### <a name="build-custom-iot-solutions-and-integrations-with-the-rest-apis"></a>Bouw op maat gemaakte IoT-oplossingen en integraties met de REST API's

IoT-oplossingen bouwen zoals:

- Mobiele companion-apps die apparaten op afstand kunnen instellen en bedienen.
- Aangepaste integraties waarmee bestaande line-of-business-toepassingen kunnen communiceren met uw IoT-apparaten en -gegevens.
- Toepassingen voor apparaatbeheer voor apparaatmodellering, onboarding, beheer en toegang tot gegevens.

## <a name="quotas"></a>Quota

Elk Azure-abonnement heeft standaardquota die van invloed kunnen zijn op het bereik van uw IoT-oplossing. Momenteel beperkt IoT Central het aantal toepassingen dat u in een abonnement implementeren tot 10. Als u deze limiet wilt verhogen, neemt u contact op met [microsoft-ondersteuning](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>Volgende stappen

Nu u een overzicht hebt van IoT Central, volgen hier enkele voorgestelde volgende stappen:

- Inzicht in de beschikbare [Azure-technologieën en -services voor het maken van IoT-oplossingen.](../../iot-fundamentals/iot-services-and-technologies.md)
- Raak vertrouwd met de [gebruikersinterface van Azure IoT Central](overview-iot-central-tour.md).
- Ga aan de slag door [een Azure IoT Central-toepassing te maken](quick-deploy-iot-central.md).
- Meer informatie over [IoT Plug and Play (preview)](../../iot-pnp/overview-iot-plug-and-play.md).
- Meer informatie over het [verbinden van een Azure IoT Edge-apparaat](./tutorial-add-edge-as-leaf-device.md).
- Meer informatie over [Azure IoT-technologieën en -services](../../iot-fundamentals/iot-services-and-technologies.md).

Als u een apparaatontwikkelaar bent en in een code wilt duiken, is de voorgestelde volgende stap het [maken en verbinden van een clienttoepassing met uw Azure IoT Central-toepassing.](./tutorial-connect-device-nodejs.md)
