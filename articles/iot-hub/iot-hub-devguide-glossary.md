---
title: Azure IoT Hub-woordenlijst van termen | Microsoft Documenten
description: Handleiding voor ontwikkelaars - een woordenlijst waarin enkele van de algemene termen worden uitgelegd die worden gebruikt in de Azure IoT Hub-artikelen.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/15/2019
ms.openlocfilehash: 3471bd7297118dc75fb97390c4601179f61e6416
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79497542"
---
# <a name="glossary-of-iot-hub-terms"></a>Woordenlijst van IoT Hub-termen
In dit artikel worden enkele van de algemene termen weergegeven die worden gebruikt in de IoT Hub-artikelen.

## <a name="advanced-message-queueing-protocol"></a>Geavanceerd protocol voor wachtrijen in wachtrijen
[Advanced Message Queueing Protocol (AMQP)](https://www.amqp.org/) is een van de berichtenprotocollen die [IoT Hub](#iot-hub) ondersteunt voor het communiceren met apparaten. Zie [Berichten verzenden en ontvangen met IoT Hub](iot-hub-devguide-messaging.md)voor meer informatie over de berichtenprotocollen die IoT Hub ondersteunt.

## <a name="automatic-device-management"></a>Automatisch apparaatbeheer
Automatic Device Management in Azure IoT Hub automatiseert veel van de repetitieve en complexe taken van het beheer van grote apparaatvloten gedurende het gehele levenscyclus. Met Automatisch apparaatbeheer u een set apparaten targeten op basis van hun eigenschappen, een gewenste configuratie definiëren en IoT Hub-apparaten laten bijwerken wanneer ze in het bereik komen.  Bestaat uit [automatische apparaatconfiguraties](iot-hub-auto-device-config.md) en automatische implementaties van [IoT Edge.](../iot-edge/how-to-deploy-monitor.md)

## <a name="automatic-device-configuration"></a>Automatische apparaatconfiguratie
Uw back-end van uw oplossing kan [automatische apparaatconfiguraties](iot-hub-auto-device-config.md) gebruiken om de gewenste eigenschappen toe te wijzen aan een set [apparaattweelingen](#device-twin) en de status van rapport te rapporteren met behulp van systeemstatistieken en aangepaste statistieken. 

## <a name="azure-classic-cli"></a>Klassieke versie van Azure CLI
De [Azure-klassieke CLI](../cli-install-nodejs.md) is een cross-platform, open-source, shell-based, command tool voor het maken en beheren van resources in Microsoft Azure. Deze versie van de CLI mag alleen worden gebruikt voor klassieke implementaties.

## <a name="azure-cli"></a>Azure-CLI
De [Azure CLI](https://docs.microsoft.com/cli/azure/install-az-cli2) is een cross-platform, open-source, shell-based, command tool voor het maken en beheren van resources in Microsoft Azure.


## <a name="azure-iot-device-sdks"></a>Azure IoT-apparaat SDKs
Er zijn _apparaat-SDK's_ beschikbaar voor meerdere talen waarmee u [apparaat-apps](#device-app) maken die werken met een IoT-hub. De IoT Hub-zelfstudies laten zien hoe u deze apparaat-SDK's gebruiken. U vindt de broncode en verdere informatie over het apparaat SDKs in deze GitHub [repository.](https://github.com/Azure/azure-iot-sdks)

## <a name="azure-iot-service-sdks"></a>Azure IoT-service SDKs
Er zijn _service-SDK's_ beschikbaar voor meerdere talen waarmee u [back-end-apps](#back-end-app) maken die met een IoT-hub werken. De IoT Hub-zelfstudies laten zien hoe u deze service-SDK's gebruiken. U vindt de broncode en meer informatie over de service SDKs in deze GitHub [repository.](https://github.com/Azure/azure-iot-sdks)

## <a name="azure-iot-tools"></a>Azure IoT Tools
De [Azure IoT-hulpprogramma's](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) is een cross-platform, open-source Visual Studio Code-extensie waarmee u Azure IoT Hub en apparaten in VS-code beheren. Met Azure IoT-hulpprogramma's kunnen IoT-ontwikkelaars iot-project in VS-code eenvoudig ontwikkelen.

## <a name="azure-portal"></a>Azure Portal
De [Microsoft Azure-portal](https://portal.azure.com) is een centrale plek waar u uw Azure-resources inrichten en beheren. Het organiseert de inhoud ervan met behulp van _messen_.

## <a name="azure-powershell"></a>Azure PowerShell
[Azure PowerShell](/powershell/azure/overview) is een verzameling cmdlets die u gebruiken om Azure te beheren met Windows PowerShell. U de cmdlets gebruiken om oplossingen en services die via het Azure-platform worden geleverd, te maken, te testen, te implementeren en te beheren.

## <a name="azure-resource-manager"></a>Azure Resource Manager
[Met Azure Resource Manager](../azure-resource-manager/management/overview.md) u als groep werken met de resources in uw oplossing. U de bronnen voor uw oplossing implementeren, bijwerken of verwijderen in één gecoördineerde bewerking.

## <a name="azure-service-bus"></a>Azure Service Bus
[Service Bus](../service-bus/index.md) biedt cloudcommunicatie met zakelijke berichten en doorgegeven communicatie waarmee u on-premises oplossingen verbinden met de cloud. Sommige IoT Hub-zelfstudies maken gebruik maken van wachtrijen voor [servicebus.](../service-bus-messaging/service-bus-messaging-overview.md)

## <a name="azure-storage"></a>Azure Storage
[Azure Storage](../storage/common/storage-introduction.md) is een cloudopslagoplossing. Het bevat de Blob Storage-service die u gebruiken om ongestructureerde objectgegevens op te slaan. Sommige IoT Hub-zelfstudies maken gebruik van blob-opslag.

## <a name="back-end-app"></a>Back-end-app
In de context van [IoT Hub](#iot-hub)is een back-end-app een app die verbinding maakt met een van de servicegerichte eindpunten op een IoT-hub. Een back-end-app kan bijvoorbeeld [device-to-cloudberichten](#device-to-cloud) ophalen of het [identiteitsregister](#identity-registry)beheren. Normaal gesproken wordt een back-end-app uitgevoerd in de cloud, maar in veel van de zelfstudies zijn de back-end-apps console-apps die op uw lokale ontwikkelingsmachine worden uitgevoerd.

## <a name="built-in-endpoints"></a>Ingebouwde eindpunten
Elke IoT-hub bevat een ingebouwd [eindpunt](iot-hub-devguide-endpoints.md) dat compatibel is met Event Hub. U elk mechanisme dat werkt met Gebeurtenishubs gebruiken om device-to-cloudberichten vanaf dit eindpunt te lezen.

## <a name="cloud-gateway"></a>Cloudgateway
Een cloudgateway maakt connectiviteit mogelijk voor apparaten die niet rechtstreeks verbinding kunnen maken met [IoT Hub.](#iot-hub) Een cloudgateway wordt gehost in de cloud in tegenstelling tot een [veldgateway](#field-gateway) die lokaal wordt uitgevoerd op uw apparaten. Een typische use case voor een cloudgateway is het implementeren van protocolvertaling voor uw apparaten.

## <a name="cloud-to-device"></a>Cloud-to-device
Verwijst naar berichten die vanuit een IoT-hub naar een verbonden apparaat worden verzonden. Vaak zijn deze berichten opdrachten die het apparaat instrueren om een actie uit te voeren. Zie [Berichten verzenden en ontvangen met IoT Hub](iot-hub-devguide-messaging.md)voor meer informatie.

## <a name="configuration"></a>Configuratie
In de context van [automatische apparaatconfiguratie](iot-hub-auto-device-config.md)definieert een configuratie binnen IoT Hub de gewenste configuratie voor een set apparaten en biedt een reeks statistieken om status en voortgang te rapporteren.

## <a name="connection-string"></a>Verbindingsreeks
U gebruikt verbindingstekenreeksen in uw app-code om de informatie in te kapselen die nodig is om verbinding te maken met een eindpunt. Een verbindingstekenreeks bevat doorgaans het adres van het eindpunt en de beveiligingsgegevens, maar verbindingstekenreeksindelingen verschillen per services. Er zijn twee typen verbindingstekenreeksen die zijn gekoppeld aan de IoT Hub-service:
- *Met apparaatverbindingstekenreeksen* kunnen apparaten verbinding maken met de apparaatgerichte eindpunten op een IoT-hub.
- *Met de verbindingstekenreeksen van IoT Hub* kunnen back-end-apps verbinding maken met de servicegerichte eindpunten op een IoT-hub.

## <a name="custom-endpoints"></a>Aangepaste eindpunten
U aangepaste [eindpunten](iot-hub-devguide-endpoints.md) maken op een IoT-hub om berichten te leveren die zijn verzonden door een [routeringsregel.](#routing-rules) Aangepaste eindpunten maken rechtstreeks verbinding met een gebeurtenishub, een servicebuswachtrij of een servicebusonderwerp.

## <a name="custom-gateway"></a>Aangepaste gateway
Een gateway maakt connectiviteit mogelijk voor apparaten die niet rechtstreeks verbinding kunnen maken met [IoT Hub.](#iot-hub) U Azure IoT Edge gebruiken om aangepaste gateways te maken die aangepaste logica implementeren voor het verwerken van berichten, aangepaste protocolconversies en andere verwerkingen aan de rand.

## <a name="data-point-message"></a>Datapuntbericht
Een data-point bericht is een [device-to-cloud](#device-to-cloud) bericht dat [telemetriegegevens](#telemetry) bevat, zoals windsnelheid of temperatuur.

## <a name="desired-configuration"></a>Gewenste configuratie
In de context van een [apparaattweeling](iot-hub-devguide-device-twins.md)verwijst de gewenste configuratie naar de volledige set eigenschappen en metagegevens in de apparaattweeling die moet worden gesynchroniseerd met het apparaat.

## <a name="desired-properties"></a>Gewenste eigenschappen
In de context van een [apparaattweeling](iot-hub-devguide-device-twins.md)is de gewenste eigenschappen een onderafdeling van de apparaattweeling die wordt gebruikt met [gerapporteerde eigenschappen](#reported-properties) om de configuratie of conditie van het apparaat te synchroniseren. Gewenste eigenschappen kunnen alleen worden ingesteld door een [back-end app](#back-end-app) en worden waargenomen door de [apparaat-app.](#device-app)

## <a name="device-to-cloud"></a>Device-to-cloud
Verwijst naar berichten die vanaf een verbonden apparaat naar [IoT Hub worden](#iot-hub)verzonden. Deze berichten kunnen [gegevens-punt](#data-point-message) of [interactieve](#interactive-message) berichten. Zie [Berichten verzenden en ontvangen met IoT Hub](iot-hub-devguide-messaging.md)voor meer informatie.

## <a name="device"></a>Apparaat
In de context van IoT is een apparaat meestal een kleinschalig, zelfstandig computerapparaat dat gegevens kan verzamelen of andere apparaten kan bedienen. Een apparaat kan bijvoorbeeld een milieubewakingsapparaat zijn of een controller voor de water- en ventilatiesystemen in een kas. De [apparaatcatalogus](https://catalog.azureiotsolutions.com/) bevat een lijst met hardwareapparaten die gecertificeerd zijn om te werken met [IoT Hub.](#iot-hub)

## <a name="device-app"></a>Apparaat-app
Een apparaat-app wordt uitgevoerd op uw [apparaat](#device) en verzorgt de communicatie met uw [IoT-hub.](#iot-hub) Normaal gesproken gebruikt u een van de [SDK's van het Azure IoT-apparaat](#azure-iot-device-sdks) wanneer u een apparaat-app implementeert. In veel van de IoT-zelfstudies gebruikt u een [gesimuleerd apparaat](#simulated-device) voor het gemak.

## <a name="device-condition"></a>Apparaatconditie
Verwijst naar informatie over apparaatstatus, zoals de verbindingsmethode die momenteel in gebruik is, zoals gerapporteerd door een [apparaat-app](#device-app). [Apparaat-apps](#device-app) kunnen ook hun mogelijkheden rapporteren. U vragen naar conditie- en capaciteitsinformatie met behulp van apparaattweelingen.

## <a name="device-data"></a>Apparaatgegevens
Apparaatgegevens hebben betrekking op de gegevens per apparaat die zijn opgeslagen in het [identiteitsregister](#identity-registry)van de IoT Hub. Het is mogelijk om deze gegevens te importeren en te exporteren.

## <a name="device-explorer"></a>Apparatenverkenner
De [device explorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) is een tool die wordt uitgevoerd op Windows en stelt u in staat om uw apparaten te beheren in het [identiteitsregister.](#identity-registry) De tool kan ook berichten verzenden en ontvangen naar uw apparaten.

## <a name="device-identity"></a>Apparaat-id
De apparaatidentiteit is de unieke id die is toegewezen aan elk apparaat dat in het [identiteitsregister is](#identity-registry)geregistreerd.

## <a name="device-management"></a>Apparaatbeheer
Apparaatbeheer omvat de volledige levenscyclus die is gekoppeld aan het beheer van de apparaten in uw IoT-oplossing, inclusief planning, inrichting, configureren, bewaken en met pensioen gaan.

## <a name="device-management-patterns"></a>Patronen voor apparaatbeheer
[IoT-hub](#iot-hub) maakt algemene apparaatbeheerpatronen mogelijk, waaronder opnieuw opstarten, fabrieksresets uitvoeren en firmware-updates uitvoeren op uw apparaten.

## <a name="device-rest-api"></a>API voor apparaatrest
U de [Device REST API](https://docs.microsoft.com/rest/api/iothub/device) vanaf een apparaat gebruiken om device-to-cloudberichten naar een IoT-hub te verzenden en [cloud-to-device-berichten te](#cloud-to-device) ontvangen vanaf een IoT-hub. Normaal gesproken moet u een van de [sdk's](#azure-iot-device-sdks) op een hoger niveau gebruiken, zoals weergegeven in de IoT Hub-zelfstudies.

## <a name="device-provisioning"></a>Inrichting van apparaten
Apparaatinrichting is het proces van het toevoegen van de eerste [apparaatgegevens](#device-data) aan de winkels in uw oplossing. Als u een nieuw apparaat wilt inschakelen om verbinding te maken met uw hub, moet u een apparaat-id en sleutels toevoegen aan het [identiteitsregister](#identity-registry)van de IoT Hub. Als onderdeel van het inrichtingsproces moet u mogelijk apparaatspecifieke gegevens initialiseren in andere oplossingsarchieven.

## <a name="device-twin"></a>Dubbel apparaat
Een [apparaattweeling](iot-hub-devguide-device-twins.md) is JSON-document dat apparaatstatusgegevens zoals metagegevens, configuraties en voorwaarden opslaat. [IoT Hub](#iot-hub) blijft bestaan voor elk apparaat dat u inuw IoT-hub indient. Met apparaattweelingen u [apparaatomstandigheden](#device-condition) en configuraties synchroniseren tussen het apparaat en de back-end van de oplossing. U apparaattweelingen opvragen om specifieke apparaten te vinden en de status van langlopende bewerkingen op te vragen.

## <a name="direct-method"></a>Directe methode
Een [directe methode](iot-hub-devguide-direct-methods.md) is een manier voor u om een methode te activeren om uit te voeren op een apparaat door een aanroepen op een API op uw IoT-hub.

## <a name="endpoint"></a>Eindpunt
Een IoT-hub legt meerdere [eindpunten](iot-hub-devguide-endpoints.md) bloot waarmee uw apps verbinding kunnen maken met de IoT-hub. Er zijn apparaatgerichte eindpunten waarmee apparaten bewerkingen kunnen uitvoeren, zoals het verzenden van [device-to-cloud-berichten](#device-to-cloud) en het ontvangen van berichten van [cloud naar apparaat.](#cloud-to-device) Er zijn servicegerichte beheereindpunten waarmee [back-end-apps](#back-end-app) bewerkingen kunnen uitvoeren, zoals [apparaatidentiteitsbeheer](#device-identity) en apparaatdubbelbeheer. Er zijn servicegerichte [ingebouwde eindpunten](#built-in-endpoints) voor het lezen van device-to-cloud-berichten. U [aangepaste eindpunten](#custom-endpoints) maken om device-to-cloudberichten te ontvangen die worden verzonden door een [routeringsregel.](#routing-rules)

## <a name="event-hubs-service"></a>Service gebeurtenishubs
[Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) is een zeer schaalbare data-ingress-service die miljoenen gebeurtenissen per seconde kan innemen. De service stelt u in staat om de enorme hoeveelheden gegevens die door uw verbonden apparaten en toepassingen worden geproduceerd, te verwerken en te analyseren. Zie [Vergelijking van Azure IoT Hub en Azure Event Hubs](iot-hub-compare-event-hubs.md)voor een vergelijking met de IoT Hub-service.

## <a name="event-hub-compatible-endpoint"></a>Eindpunt met gebeurtenishub
Als u [device-to-cloudberichten](#device-to-cloud) wilt lezen die naar uw IoT-hub worden verzonden, u verbinding maken met een eindpunt op uw hub en elke gebeurtenishub-compatibele methode gebruiken om die berichten te lezen. Event Hub-compatibele methoden omvatten het gebruik van de [SDK's van Gebeurtenishubs](../event-hubs/event-hubs-programming-guide.md) en [Azure Stream Analytics.](../stream-analytics/stream-analytics-introduction.md)

## <a name="field-gateway"></a>Veldgateway
Een veldgateway maakt connectiviteit mogelijk voor apparaten die niet rechtstreeks verbinding kunnen maken met [IoT Hub](#iot-hub) en meestal lokaal worden geïmplementeerd met uw apparaten. Zie [Wat is Azure IoT Hub voor](about-iot-hub.md) meer informatie?

## <a name="free-account"></a>Gratis account
U een [gratis Azure-account](https://azure.microsoft.com/pricing/free-trial/) maken om de IoT Hub-zelfstudies te voltooien en te experimenteren met de IoT Hub-service (en andere Azure-services).

## <a name="gateway"></a>Gateway
Een gateway maakt connectiviteit mogelijk voor apparaten die niet rechtstreeks verbinding kunnen maken met [IoT Hub.](#iot-hub) Zie ook [Field Gateway,](#field-gateway) [Cloud Gateway](#cloud-gateway)en [Custom Gateway.](#custom-gateway)

## <a name="identity-registry"></a>Identiteitsregister
Het [identiteitsregister](iot-hub-devguide-identity-registry.md) is de ingebouwde component van een IoT-hub die informatie opslaat over de afzonderlijke apparaten die verbinding mogen maken met een IoT-hub.

## <a name="interactive-message"></a>Interactief bericht
Een interactief bericht is een [cloud-to-device-bericht](#cloud-to-device) dat een onmiddellijke actie in de back-end van de oplossing activeert. Een apparaat kan bijvoorbeeld een alarm verzenden over een fout die automatisch moet worden aangemeld bij een CRM-systeem.

[!INCLUDE [azure-iot-hub-edge-glossary-includes](../../includes/azure-iot-hub-edge-glossary-includes.md)]

## <a name="iot-hub"></a>IoT Hub
IoT Hub is een volledig beheerde Azure-service die betrouwbare en veilige bidirectionele communicatie tussen miljoenen apparaten en een back-end van een oplossing mogelijk maakt. Zie [Wat is Azure IoT Hub voor](about-iot-hub.md) meer informatie? Met uw [Azure-abonnement](#subscription)u IoT-hubs maken om uw IoT-messaging-workloads te verwerken.

## <a name="iot-hub-metrics"></a>IoT Hub-statistieken
[IoT Hub-statistieken](iot-hub-metrics.md) geven u gegevens over de status van de IoT-hubs in uw [Azure-abonnement.](#subscription) Met IoT Hub-statistieken u de algehele status van de service en de apparaten die ermee zijn verbonden, beoordelen. IoT Hub-statistieken kunnen u helpen te zien wat er aan de hand is met uw IoT-hub en problemen met de oorzaak van de oorzaak te onderzoeken zonder contact op te nemen met Azure-ondersteuning.

## <a name="iot-hub-query-language"></a>Taal van IoT Hub-query
De [IoT Hub-querytaal](iot-hub-devguide-query-language.md) is een SQL-achtige [](#job) taal waarmee u uw en apparaattweeling opvragen.

## <a name="iot-hub-resource-rest-api"></a>IoT Hub Resource REST API
U de [IoT Hub Resource REST API](https://docs.microsoft.com/rest/api/iothub/iothubresource) gebruiken om de IoT-hubs in uw [Azure-abonnement](#subscription) te beheren en bewerkingen uit te voeren, zoals hubs maken, bijwerken en verwijderen.

## <a name="iot-solution-accelerators"></a>IoT-oplossingsversnellers
Azure IoT-oplossingversnellers verpakken meerdere Azure-services tot oplossingen. Met deze oplossingen u snel aan de slag met end-to-end implementaties van veelvoorkomende IoT-scenario's. Zie [Wat zijn Azure IoT-oplossingsversnellers voor](../iot-accelerators/about-iot-accelerators.md) meer informatie?

## <a name="the-iot-extension-for-azure-cli"></a>De IoT-extensie voor Azure CLI 
[De IoT-extensie voor Azure CLI](https://github.com/Azure/azure-iot-cli-extension) is een cross-platform, command-line tool. Met de tool u uw apparaten beheren in het [identiteitsregister,](#identity-registry)berichten en bestanden verzenden en ontvangen vanaf uw apparaten en uw IoT-hubbewerkingen controleren.

## <a name="job"></a>Taak
Uw back-end van uw oplossing kan [taken](iot-hub-devguide-jobs.md) gebruiken om activiteiten te plannen en bij te houden op een reeks apparaten die zijn geregistreerd bij uw IoT-hub. Activiteiten omvatten het bijwerken van apparaat twin [gewenste eigenschappen,](#desired-properties)het bijwerken van apparaat twin [tags,](#tags)en het inroepen van [directe methoden](#direct-method). [IoT Hub](#iot-hub) gebruikt ook om te [importeren naar en exporteren](iot-hub-devguide-identity-registry.md#import-and-export-device-identities) vanuit het [identiteitsregister](#identity-registry).

## <a name="modules"></a>Modules
Aan de apparaatzijde u met de SDK's van het IoT Hub-apparaat [modules](iot-hub-devguide-module-twins.md) maken waarbij elk een onafhankelijke verbinding met IoT Hub opent. Met deze functionaliteit u afzonderlijke naamruimten gebruiken voor verschillende componenten op uw apparaat.

Module identiteit en module twin bieden dezelfde mogelijkheden als [apparaat identiteit](#device-identity) en [apparaat twin,](#device-twin) maar op een fijnere granulariteit. Deze fijnere granulariteit stelt geschikte apparaten, zoals apparaten op basis van het besturingssysteem of firmware-apparaten die meerdere componenten beheren, in staat om configuratie en voorwaarden voor elk van deze componenten te isoleren.

## <a name="module-identity"></a>Module-identiteit
De module-identiteit is de unieke id die is toegewezen aan elke module die bij een apparaat hoort. Moduleidentiteit wordt ook geregistreerd in het [identiteitsregister](#identity-registry).

## <a name="module-twin"></a>Module twin
Net als bij device twin is een moduletwin JSON-document dat informatie over de status van de module opslaat, zoals metagegevens, configuraties en voorwaarden. IoT Hub blijft bestaan uit een moduletwin voor elke module-identiteit die u indient onder een apparaatidentiteit in uw IoT-hub. Module twins stelt u in staat om moduleomstandigheden en configuraties tussen de module en de oplossing back-end te synchroniseren. U moduletweelingen opvragen om specifieke modules te vinden en de status van langlopende bewerkingen op te vragen.

## <a name="mqtt"></a>MQTT
[MQTT](https://mqtt.org/) is een van de berichtenprotocollen die [IoT Hub](#iot-hub) ondersteunt voor het communiceren met apparaten. Zie [Berichten verzenden en ontvangen met IoT Hub](iot-hub-devguide-messaging.md)voor meer informatie over de berichtenprotocollen die IoT Hub ondersteunt.

## <a name="operations-monitoring"></a>Controle van bewerkingen
Met [iot-hub-bewerkingenbewaking](iot-hub-operations-monitoring.md) u de status van bewerkingen op uw IoT-hub in realtime controleren. [IoT Hub](#iot-hub) houdt gebeurtenissen bij in verschillende categorieën bewerkingen. U ervoor kiezen om gebeurtenissen van een of meer categorieën naar een IoT Hub-eindpunt te verzenden voor verwerking. U de gegevens controleren op fouten of complexere verwerkinginstellen op basis van gegevenspatronen.

## <a name="physical-device"></a>Fysiek apparaat
Een fysiek apparaat is een echt apparaat, zoals een Raspberry Pi die verbinding maakt met een IoT-hub. Voor het gemak maken veel van de IoT Hub-zelfstudies gebruik van [gesimuleerde apparaten](#simulated-device) om voorbeelden op uw lokale machine uit te voeren.

## <a name="primary-and-secondary-keys"></a>Primaire en secundaire toetsen
Wanneer u verbinding maakt met een apparaatgericht of servicegericht eindpunt op een IoT-hub, bevat uw [verbindingstekenreeks](#connection-string) de sleutel om u toegang te verlenen. Wanneer u een apparaat toevoegt aan het [identiteitsregister](#identity-registry) of een beleid voor [gedeelde toegang](#shared-access-policy) toevoegt aan uw hub, genereert de service een primaire en secundaire sleutel. Met twee toetsen u van de ene sleutel naar de andere draaien wanneer u een sleutel bijwerkt zonder de toegang tot de IoT-hub te verliezen.

## <a name="protocol-gateway"></a>Protocolgateway
Een protocolgateway wordt doorgaans geïmplementeerd in de cloud en biedt protocolvertaalservices voor apparaten die verbinding maken met [IoT Hub.](#iot-hub) Zie [Wat is Azure IoT Hub voor](about-iot-hub.md) meer informatie?

## <a name="quotas-and-throttling"></a>Quota en beperkingen
Er zijn verschillende [quota](iot-hub-devguide-quotas-throttling.md) die van toepassing zijn op uw gebruik van [IoT Hub,](#iot-hub)veel van de quota variëren op basis van de laag van de IoT-hub. [IoT Hub](#iot-hub) past ook [gashendels](iot-hub-devguide-quotas-throttling.md) toe op uw gebruik van de service tijdens het uitvoeren van de service.

## <a name="reported-configuration"></a>Gerapporteerde configuratie
In de context van een [apparaattweeling](iot-hub-devguide-device-twins.md)verwijst de gerapporteerde configuratie naar de volledige set eigenschappen en metagegevens in de apparaattweeling die aan de back-end van de oplossing moet worden gerapporteerd.

## <a name="reported-properties"></a>Gerapporteerde eigenschappen
In de context van een [apparaattweeling](iot-hub-devguide-device-twins.md)is gerapporteerde eigenschappen een onderafdeling van de apparaattweeling die wordt gebruikt met [de gewenste eigenschappen](#desired-properties) om de configuratie of conditie van het apparaat te synchroniseren. Gerapporteerde eigenschappen kunnen alleen worden ingesteld door de [apparaat-app](#device-app) en kunnen worden gelezen en opgevraagd door een [back-end-app.](#back-end-app)

## <a name="resource-group"></a>Resourcegroep
[Azure Resource Manager](#azure-resource-manager) gebruikt resourcegroepen om gerelateerde resources samen te groeperen. U een resourcegroep gebruiken om bewerkingen op alle resources in de groep tegelijkertijd uit te voeren.

## <a name="retry-policy"></a>Beleid voor opnieuw proberen
U gebruikt een beleid voor het opnieuw proberen om [tijdelijke fouten](/azure/architecture/best-practices/transient-faults) te verwerken wanneer u verbinding maakt met een cloudservice.

## <a name="routing-rules"></a>Regels voor doorsturen
U [configureert routeringsregels](iot-hub-devguide-messages-read-custom.md) in uw IoT-hub om device-to-cloudberichten te routeren naar een [ingebouwd eindpunt](#built-in-endpoints) of naar [aangepaste eindpunten](#custom-endpoints) voor verwerking door de back-end van uw oplossing.

## <a name="sasl-plain"></a>SASL PLAIN
SASL PLAIN is een protocol dat het AMQP-protocol gebruikt om beveiligingstokens over te dragen.

## <a name="service-rest-api"></a>Service REST API
U de [Service REST API](https://docs.microsoft.com/rest/api/iothub/service/configuration) van de back-end van de oplossing gebruiken om uw apparaten te beheren. Met de API u de dubbele eigenschappen [van het apparaat](#device-twin) ophalen en bijwerken, directe [methoden](#direct-method)aanroepen en [taken plannen.](#job) Normaal gesproken moet u een van de [sdk's](#azure-iot-service-sdks) op een hoger niveau gebruiken, zoals weergegeven in de IoT Hub-zelfstudies.

## <a name="shared-access-signature"></a>Handtekening voor gedeelde toegang
Shared Access Signatures (SAS) zijn een verificatiemechanisme gebaseerd op SHA-256 beveiligde hashes of URI's. SAS-verificatie bestaat uit twee componenten: een _beleid voor gedeelde toegang_ en een gedeelde _toegangshandtekening_ (vaak een token genoemd). Een apparaat gebruikt SAS om te verifiëren met een IoT-hub. [Back-end-apps](#back-end-app) gebruiken SAS ook om te verifiëren met de servicegerichte eindpunten op een IoT-hub. Meestal neemt u het SAS-token op in de [verbindingstekenreeks](#connection-string) die een app gebruikt om een verbinding met een IoT-hub tot stand te brengen.

## <a name="shared-access-policy"></a>Beleid voor gedeelde toegang
Een beleid voor gedeelde toegang definieert de machtigingen die worden verleend aan iedereen die een geldige [primaire of secundaire sleutel](#primary-and-secondary-keys) heeft die aan dat beleid is gekoppeld. U het beleid voor gedeelde toegang en de sleutels voor uw hub beheren in de [portal.](#azure-portal)

## <a name="simulated-device"></a>Gesimuleerd apparaat
Voor het gemak maken veel van de IoT Hub-zelfstudies gebruik van gesimuleerde apparaten om voorbeelden op uw lokale machine uit te voeren. Een fysiek [apparaat](#physical-device) is daarentegen een echt apparaat, zoals een Raspberry Pi die verbinding maakt met een IoT-hub.

## <a name="solution"></a>Oplossing
Een _oplossing_ kan verwijzen naar een Visual Studio-oplossing die een of meer projecten omvat. Een _oplossing_ kan ook verwijzen naar een IoT-oplossing die elementen zoals apparaten, [apparaat-apps,](#device-app)een IoT-hub, andere [Azure-services en back-end-apps](#back-end-app)bevat.

## <a name="subscription"></a>Abonnement
Een Azure-abonnement is de plaats waar facturering plaatsvindt. Elke Azure-bron die u maakt of Azure-service die u gebruikt, is gekoppeld aan één abonnement. Veel quota gelden ook op het niveau van een abonnement.

## <a name="system-properties"></a>Systeemeigenschappen
In de context van een [apparaattweeling](iot-hub-devguide-device-twins.md)zijn systeemeigenschappen alleen-lezen en bevatten ze informatie over het apparaatgebruik, zoals de laatste activiteitstijd en verbindingsstatus.

## <a name="tags"></a>Tags
In de context van een [apparaattweeling](iot-hub-devguide-device-twins.md)zijn tags apparaatmetagegevens die door de back-end van de oplossing zijn opgeslagen en opgehaald in de vorm van een JSON-document. Tags zijn niet zichtbaar voor apps op een apparaat.

## <a name="telemetry"></a>Telemetrie
Apparaten verzamelen telemetriegegevens, zoals windsnelheid of temperatuur, en gebruiken gegevenspuntberichten om de telemetrie naar een IoT-hub te verzenden.

## <a name="token-service"></a>Tokenservice
U een tokenservice gebruiken om een verificatiemechanisme voor uw apparaten te implementeren. Het maakt gebruik van een IoT Hub [gedeelde toegangsbeleid](#shared-access-policy) met **DeviceConnect-machtigingen** om *tokens met apparaatbereik* te maken. Met deze tokens kan een apparaat verbinding maken met uw IoT-hub. Een apparaat gebruikt een aangepast verificatiemechanisme om te verifiëren met de tokenservice. Als het apparaat zich verifieert, geeft de tokenservice een SAS-token uit voor het apparaat om toegang te krijgen tot uw IoT-hub.

## <a name="twin-queries"></a>Dubbele query's
[Apparaat- en moduledubbele query's](iot-hub-devguide-query-language.md) gebruiken de SQL-achtige IoT Hub-querytaal om informatie op te halen van uw apparaattweeling of moduletweeling. U dezelfde IoT Hub-querytaal [](#job) gebruiken om informatie over het uitvoeren in uw IoT-hub op te halen.

## <a name="twin-synchronization"></a>Dubbele synchronisatie
Dubbele synchronisatie maakt gebruik van de [gewenste eigenschappen](#desired-properties) in uw apparaat tweelingen of module tweelingen om uw apparaten of modules te configureren en te halen [gerapporteerde eigenschappen](#reported-properties) van hen op te slaan in de tweeling.

## <a name="x509-client-certificate"></a>X.509 clientcertificaat
Een apparaat kan een X.509-certificaat gebruiken om te verifiëren met [IoT Hub.](#iot-hub) Het gebruik van een X.509-certificaat is een alternatief voor het gebruik van een [SAS-token.](#shared-access-signature)
