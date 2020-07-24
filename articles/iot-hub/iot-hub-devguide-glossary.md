---
title: Woorden lijst voor Azure IoT Hub termen | Microsoft Docs
description: 'Ontwikkelaars handleiding: een verklarende woorden lijst met een aantal algemene termen die worden gebruikt in de Azure IoT Hub-artikelen.'
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/15/2019
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 2b7a81357cc6ee2608d2acd3051dcac3e5d0e07e
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87023463"
---
# <a name="glossary-of-iot-hub-terms"></a>Woorden lijst met IoT Hub voor waarden

In dit artikel vindt u een aantal algemene termen die in de IoT Hub-artikelen worden gebruikt.

## <a name="advanced-message-queueing-protocol"></a>Protocol voor geavanceerde berichten wachtrijen

[Advanced Message queueing Protocol (AMQP)](https://www.amqp.org/) is een van de berichten protocollen die [IOT hub](#iot-hub) ondersteunt voor het communiceren met apparaten. Zie [berichten verzenden en ontvangen met IOT hub](iot-hub-devguide-messaging.md)voor meer informatie over de berichten protocollen die door IOT hub worden ondersteund.

## <a name="automatic-device-management"></a>Automatisch apparaatbeheer

Automatische Apparaatbeheer in azure IoT Hub automatiseert veel van de herhaalde en complexe taken van het beheer van grote apparaat vloots in de hele levens cyclus. Met automatisch Apparaatbeheer kunt u een set apparaten op basis van hun eigenschappen richten, een gewenste configuratie definiëren en apparaten IoT Hub bijwerken wanneer deze binnen het bereik vallen.  Bestaat uit [automatische configuraties van apparaten](iot-hub-auto-device-config.md) en [IOT Edge automatische implementaties](../iot-edge/how-to-deploy-at-scale.md).

## <a name="automatic-device-configuration"></a>Automatische apparaatconfiguratie

De back-end van de oplossing kan [automatische hardwareconfiguraties](iot-hub-auto-device-config.md) gebruiken om de gewenste eigenschappen toe te wijzen aan een set [apparaatdubbels](#device-twin) en de status van het rapport met behulp van systeem metrieken en aangepaste metrische gegevens. 

## <a name="azure-classic-cli"></a>Klassieke versie van Azure CLI

De [klassieke Azure-cli](../cli-install-nodejs.md) is een platformoverschrijdende, open-source, op shell gebaseerd, opdracht hulpprogramma voor het maken en beheren van resources in Microsoft Azure. Deze versie van de CLI moet alleen voor klassieke implementaties worden gebruikt.

## <a name="azure-cli"></a>Azure CLI

De [Azure cli](https://docs.microsoft.com/cli/azure/install-az-cli2) is een platformoverschrijdende, open-source, op shell gebaseerd, opdracht hulpprogramma voor het maken en beheren van resources in Microsoft Azure.

## <a name="azure-iot-device-sdks"></a>Sdk's van Azure IoT-apparaat

Er zijn _apparaat-sdk's_ beschikbaar voor meerdere talen waarmee u [apps](#device-app) kunt maken die communiceren met een IOT-hub. De IoT Hub zelf studies laten zien hoe u deze apparaat-Sdk's kunt gebruiken. U vindt de bron code en meer informatie over de Sdk's van het apparaat in deze GitHub- [opslag plaats](https://github.com/Azure/azure-iot-sdks).

## <a name="azure-iot-explorer"></a>Azure IoT Explorer

[Azure IOT Explorer](https://github.com/Azure/azure-iot-explorer) wordt gebruikt voor het weer geven van de telemetrie die het apparaat verzendt, het werken met apparaateigenschappen en het aanroepen van opdrachten. U kunt ook de Explorer gebruiken om te communiceren met en uw apparaten te testen en om Plug en Play-apparaten te beheren.

## <a name="azure-iot-service-sdks"></a>Sdk's van Azure IoT-service

Er zijn _service-sdk's_ beschikbaar voor meerdere talen waarmee u [back-end-apps](#back-end-app) kunt maken die communiceren met een IOT-hub. De IoT Hub zelf studies laten zien hoe u deze service-Sdk's kunt gebruiken. U vindt de bron code en meer informatie over de Sdk's van de service in deze GitHub- [opslag plaats](https://github.com/Azure/azure-iot-sdks).

## <a name="azure-iot-tools"></a>Azure IoT Tools

De [Azure IOT-Hulpprogram ma's](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) is een platformoverschrijdende, open-source Visual Studio code-extensie waarmee u Azure IOT hub en-apparaten kunt beheren in VS code. Met Azure IoT-Hulpprogram Ma's kunnen IoT-ontwikkel aars IoT-project met gemak ontwikkelen in VS code.

## <a name="azure-portal"></a>Azure Portal

De [Microsoft Azure-Portal](https://portal.azure.com) is een centrale locatie waar u uw Azure-resources kunt inrichten en beheren. De inhoud wordt ingedeeld met behulp van _Blades_.

## <a name="azure-powershell"></a>Azure PowerShell

[Azure PowerShell](/powershell/azure/) is een verzameling cmdlets die u kunt gebruiken om Azure te beheren met Windows Power shell. U kunt de-cmdlets gebruiken voor het maken, testen, implementeren en beheren van oplossingen en services die via het Azure-platform worden geleverd.

## <a name="azure-resource-manager"></a>Azure Resource Manager

[Azure Resource Manager](../azure-resource-manager/management/overview.md) kunt u met de resources in uw oplossing als groep gebruiken. U kunt de resources voor uw oplossing implementeren, bijwerken of verwijderen in een enkele, gecoördineerde bewerking.

## <a name="azure-service-bus"></a>Azure Service Bus

[Service Bus](../service-bus/index.md) biedt communicatie via de Cloud met Enter prise Messa ging en door gegeven communicatie waarmee u on-premises oplossingen kunt verbinden met de Cloud. Sommige IoT Hub-zelf studies maken gebruik van Service Bus [wacht rijen](../service-bus-messaging/service-bus-messaging-overview.md).

## <a name="azure-storage"></a>Azure Storage

[Azure Storage](../storage/common/storage-introduction.md) is een oplossing voor Cloud opslag. Het bevat de Blob Storage-service die u kunt gebruiken om ongestructureerde object gegevens op te slaan. Sommige IoT Hub zelf studies maken gebruik van Blob Storage.

## <a name="back-end-app"></a>Back-end-app

In de context van [IOT hub](#iot-hub)is een back-end-app een app die verbinding maakt met een van de service gerichte eind punten op een IOT-hub. Een back-end-app kan bijvoorbeeld [apparaat-naar-Cloud-](#device-to-cloud) berichten ophalen of het [identiteits register](#identity-registry)beheren. Normaal gesp roken wordt een back-end-app uitgevoerd in de Cloud, maar in veel van de zelf studies zijn de back-end-apps console-apps die worden uitgevoerd op uw lokale ontwikkel computer.

## <a name="built-in-endpoints"></a>Ingebouwde eind punten

Elke IoT-hub bevat een ingebouwd [eind punt](iot-hub-devguide-endpoints.md) dat compatibel is met Event hub. U kunt elk mechanisme gebruiken dat werkt met Event Hubs om apparaat-naar-Cloud-berichten van dit eind punt te lezen.

## <a name="cloud-gateway"></a>Cloud gateway

Een Cloud gateway maakt connectiviteit mogelijk voor apparaten die niet rechtstreeks verbinding kunnen maken met [IOT hub](#iot-hub). Een Cloud gateway wordt in de Cloud gehost in tegens telling tot een [veld Gateway](#field-gateway) die lokaal op uw apparaten wordt uitgevoerd. Een typische use-case voor een Cloud gateway is het implementeren van protocol vertalingen voor uw apparaten.

## <a name="cloud-to-device"></a>Cloud-naar-apparaat

Verwijst naar berichten die vanuit een IoT-hub naar een verbonden apparaat worden verzonden. Deze berichten zijn vaak opdrachten waarmee het apparaat een actie kan uitvoeren. Zie [berichten verzenden en ontvangen met IOT hub](iot-hub-devguide-messaging.md)voor meer informatie.

## <a name="configuration"></a>Configuratie

In de context van [automatische apparaatconfiguratie](iot-hub-auto-device-config.md), definieert een configuratie binnen IOT hub de gewenste configuratie voor een set apparaten apparaatdubbels en biedt een set metrische gegevens om de status en de voortgang te rapporteren.

## <a name="connection-string"></a>Verbindingsreeks

U gebruikt verbindings reeksen in uw app-code om de vereiste gegevens voor het maken van verbinding met een eind punt te integreren. Een connection string bevat normaal gesp roken het adres van het eind punt en de beveiligings gegevens, maar connection string indelingen variëren in verschillende services. Er zijn twee soorten connection string gekoppeld aan de IoT Hub-service:

- Met *verbindings reeksen* voor apparaten kunnen apparaten verbinding maken met de op het apparaat gerichte eind punten op een IOT-hub.

- Met *IOT hub verbindings reeksen* kunnen back-end-apps verbinding maken met de service gerichte eind punten op een IOT-hub.

## <a name="custom-endpoints"></a>Aangepaste eind punten

U kunt aangepaste [eind punten](iot-hub-devguide-endpoints.md) maken op een IOT-hub om berichten te leveren die worden verzonden door een [routerings regel](#routing-rules). Aangepaste eind punten verbinden rechtstreeks met een event hub, een Service Bus wachtrij of een Service Bus onderwerp.

## <a name="custom-gateway"></a>Aangepaste gateway

Een gateway maakt connectiviteit mogelijk voor apparaten die niet rechtstreeks verbinding kunnen maken met [IOT hub](#iot-hub). U kunt Azure IoT Edge gebruiken om aangepaste gateways te maken die aangepaste logica implementeren voor het afhandelen van berichten, aangepaste protocol conversies en andere verwerking aan de rand.

## <a name="data-point-message"></a>Bericht van gegevens punt

Een bericht van het gegevens punt is een [apparaat-naar-Cloud](#device-to-cloud) -bericht dat [telemetriegegevens](#telemetry) bevat, zoals wind snelheid of Tempe ratuur.

## <a name="desired-configuration"></a>Gewenste configuratie

In de context van een [apparaat dubbele](iot-hub-devguide-device-twins.md), gewenste configuratie verwijst naar de volledige set eigenschappen en meta gegevens in het apparaat-dubbele dat moet worden gesynchroniseerd met het apparaat.

## <a name="desired-properties"></a>Gewenste eigenschappen

In de context van een [apparaat met dubbele](iot-hub-devguide-device-twins.md), gewenste eigenschappen is een subsectie van het apparaat dat wordt gebruikt met [gerapporteerde eigenschappen](#reported-properties) om de apparaatconfiguratie of voor waarde te synchroniseren. Gewenste eigenschappen kunnen alleen worden ingesteld door een [back-end-app](#back-end-app) en worden waargenomen door de [apparaat-app](#device-app).

## <a name="device-to-cloud"></a>Apparaat-naar-Cloud

Verwijst naar berichten die vanuit een verbonden apparaat naar [IOT hub](#iot-hub)worden verzonden. Deze berichten kunnen [gegevens](#data-point-message) of [interactieve](#interactive-message) berichten zijn. Zie [berichten verzenden en ontvangen met IOT hub](iot-hub-devguide-messaging.md)voor meer informatie.

## <a name="device"></a>Apparaat

In de context van IoT is een apparaat doorgaans een klein, zelfstandig computer apparaat dat gegevens kan verzamelen of andere apparaten kan beheren. Een apparaat kan bijvoorbeeld een milieubewakings apparaat zijn of een controller voor de water-en ventilatie systemen in een broeikas. De [catalogus](https://catalog.azureiotsolutions.com/) met apparaten bevat een lijst met apparaten die zijn gecertificeerd voor gebruik met [IOT hub](#iot-hub).

## <a name="device-app"></a>Apparaat-app

Een apparaat-app wordt op uw [apparaat](#device) uitgevoerd en verwerkt de communicatie met uw [IOT-hub](#iot-hub). Normaal gesp roken gebruikt u een van de [Azure IOT-apparaat-sdk's](#azure-iot-device-sdks) wanneer u een apparaat-app implementeert. In veel van de IoT-zelf studies gebruikt u een [gesimuleerd apparaat](#simulated-device) voor het gemak.

## <a name="device-condition"></a>Voorwaarde voor apparaat

Verwijst naar informatie over de status van het apparaat, zoals de verbindings methode die momenteel in gebruik is, zoals gerapporteerd door een [apparaat-app](#device-app). [Apparaat-apps](#device-app) kunnen ook hun mogelijkheden rapporteren. U kunt een query uitvoeren op informatie over de voor waarde en de capaciteit met behulp van Device apparaatdubbels.

## <a name="device-data"></a>Apparaatgegevens

Apparaatgegevens verwijzen naar de gegevens per apparaat die zijn opgeslagen in het IoT Hub [identiteits register](#identity-registry). Het is mogelijk om deze gegevens te importeren en exporteren.

## <a name="device-explorer"></a>Apparatenverkenner

De Verkenner is vervangen door de [Azure IOT Explorer](https://github.com/Azure/azure-iot-explorer), die wordt gebruikt voor het weer geven van de telemetrie die het apparaat verzendt, het werken met apparaateigenschappen en het aanroepen van opdrachten. U kunt ook de Explorer gebruiken om te communiceren met en uw apparaten te testen en om Plug en Play-apparaten te beheren.

## <a name="device-identity"></a>Apparaat-id

De apparaat-id is de unieke id die is toegewezen aan elk apparaat dat is geregistreerd in het [identiteits register](#identity-registry).

## <a name="device-management"></a>Apparaatbeheer

Apparaatbeheer omvat de volledige levens cyclus voor het beheer van de apparaten in uw IoT-oplossing, waaronder het plannen, inrichten, configureren, bewaken en buiten gebruik stellen.

## <a name="device-management-patterns"></a>Patronen voor apparaatbeheer

Met [IOT hub](#iot-hub) kunt u veelgebruikte patronen voor Apparaatbeheer maken, waaronder het opnieuw opstarten, het opnieuw instellen van de fabriek en het uitvoeren van firmware-updates op uw apparaten.

## <a name="device-rest-api"></a>Apparaat REST API

U kunt het [apparaat rest API](https://docs.microsoft.com/rest/api/iothub/device) van een apparaat gebruiken om apparaat-naar-Cloud-berichten te verzenden naar een IOT-hub en [Cloud-naar-apparaat-](#cloud-to-device) berichten van een IOT-hub te ontvangen. Normaal gesp roken moet u een van de [apparaat-sdk's](#azure-iot-device-sdks) van het hogere niveau gebruiken, zoals wordt weer gegeven in de IOT hub zelf studies.

## <a name="device-provisioning"></a>Apparaat inrichten

Het inrichten van apparaten is het proces van het toevoegen van de initiële gegevens van het [apparaat](#device-data) aan de winkels in uw oplossing. Als u een nieuw apparaat wilt inschakelen om verbinding te maken met uw hub, moet u een apparaat-ID en sleutels toevoegen aan het IoT Hub- [identiteits register](#identity-registry). Als onderdeel van het inrichtings proces moet u mogelijk apparaatspecifieke gegevens initialiseren in andere oplossingen Stores.

## <a name="device-twin"></a>Dubbel apparaat

Een dubbel [apparaat](iot-hub-devguide-device-twins.md) is een JSON-document waarin informatie over de status van het apparaat, zoals meta gegevens, configuraties en voor waarden, wordt opgeslagen. [IOT hub](#iot-hub) persistent voor elk apparaat dat u inricht in IOT hub. Met Device apparaatdubbels kunt u de [voor waarden](#device-condition) en configuraties van apparaten synchroniseren tussen het apparaat en de back-end van de oplossing. U kunt een query voor apparaat apparaatdubbels uitvoeren om specifieke apparaten te vinden en de status van langlopende bewerkingen te doorzoeken.

## <a name="direct-method"></a>Directe methode

Een [directe methode](iot-hub-devguide-direct-methods.md) is een manier waarop u een methode voor het uitvoeren van een apparaat kunt activeren door een API op uw IOT-hub aan te roepen.

## <a name="endpoint"></a>Eindpunt

Een IoT-hub biedt meerdere [eind punten](iot-hub-devguide-endpoints.md) die uw apps in staat stellen om verbinding te maken met de IOT-hub. Er zijn apparaat gerichte eind punten die apparaten in staat stellen om bewerkingen uit te voeren, zoals het verzenden van [apparaat-naar-Cloud](#device-to-cloud) -berichten en het ontvangen van [Cloud-naar-apparaat-](#cloud-to-device) berichten. Er zijn service-Facing Management-eind punten waarmee [back-end-apps](#back-end-app) bewerkingen kunnen uitvoeren zoals het beheer van [apparaat-id's](#device-identity) en het dubbele beheer van apparaten. Er zijn op de service gerichte [ingebouwde eind punten](#built-in-endpoints) voor het lezen van apparaat-naar-Cloud-berichten. U kunt [aangepaste eind punten](#custom-endpoints) maken om apparaat-naar-Cloud-berichten te ontvangen die worden verzonden door een [routerings regel](#routing-rules).

## <a name="event-hubs-service"></a>Event Hubs-service

[Event hubs](../event-hubs/event-hubs-what-is-event-hubs.md) is een zeer schaal bare service voor gegevens ingang die miljoenen gebeurtenissen per seconde kan opnemen. Met deze service kunt u de enorme hoeveel heden gegevens verwerken en analyseren die door uw verbonden apparaten en toepassingen worden geproduceerd. Zie [vergelijking van azure IOT hub en azure Event hubs](iot-hub-compare-event-hubs.md)voor een vergelijking met de IOT hub-service.

## <a name="event-hub-compatible-endpoint"></a>Event hub-compatibel eind punt

Voor het lezen van [apparaat-naar-Cloud](#device-to-cloud) -berichten die naar uw IOT hub worden verzonden, kunt u verbinding maken met een eind punt op uw hub en een event hub-compatibele methode gebruiken om die berichten te lezen. Event hub-compatibele methoden zijn het gebruik van de [Event hubs sdk's](../event-hubs/event-hubs-programming-guide.md) en [Azure stream Analytics](../stream-analytics/stream-analytics-introduction.md).

## <a name="field-gateway"></a>Veld Gateway

Een veld Gateway maakt connectiviteit mogelijk voor apparaten die niet rechtstreeks verbinding kunnen maken met [IOT hub](#iot-hub) en die doorgaans lokaal op uw apparaten wordt geïmplementeerd. Zie [Wat is Azure IOT hub?](about-iot-hub.md) voor meer informatie.

## <a name="free-account"></a>Gratis account

U kunt een [gratis Azure-account](https://azure.microsoft.com/pricing/free-trial/) maken voor het volt ooien van de IOT hub zelf studies en experimenteren met de IOT hub-service (en andere Azure-Services).

## <a name="gateway"></a>Gateway

Een gateway maakt connectiviteit mogelijk voor apparaten die niet rechtstreeks verbinding kunnen maken met [IOT hub](#iot-hub). Zie ook [veld Gateway](#field-gateway), [Cloud gateway](#cloud-gateway)en [aangepaste gateway](#custom-gateway).

## <a name="identity-registry"></a>Id-REGI ster

Het [id-REGI ster](iot-hub-devguide-identity-registry.md) is het ingebouwde onderdeel van een IOT-hub dat informatie bevat over de afzonderlijke apparaten die verbinding mogen maken met een IOT-hub.

## <a name="interactive-message"></a>Interactief bericht

Een interactief bericht is een [Cloud-naar-apparaat-](#cloud-to-device) bericht dat een onmiddellijke actie activeert in de back-end van de oplossing. Een apparaat kan bijvoorbeeld een waarschuwing sturen over een fout die automatisch moet worden aangemeld bij een CRM-systeem.

[!INCLUDE [azure-iot-hub-edge-glossary-includes](../../includes/azure-iot-hub-edge-glossary-includes.md)]

## <a name="iot-hub"></a>IoT Hub

IoT Hub is een volledig beheerde Azure-service die betrouw bare en veilige bidirectionele communicatie mogelijk maakt tussen miljoenen apparaten en een back-end van een oplossing. Zie [Wat is Azure IOT hub?](about-iot-hub.md) voor meer informatie. Met uw [Azure-abonnement](#subscription)kunt u IOT hubs maken om uw IOT Messa ging-werk belastingen te verwerken.

## <a name="iot-hub-metrics"></a>IoT Hub metrische gegevens

[IOT hub metrische](iot-hub-metrics.md) gegevens geven u informatie over de status van de IOT-hubs in uw [Azure-abonnement](#subscription). Met IoT Hub metrische gegevens kunt u de algemene status van de service en de aangesloten apparaten beoordelen. Met IoT Hub metrische gegevens kunt u zien wat er gebeurt met uw IoT hub en problemen met de hoofd oorzaak onderzoeken zonder dat u contact hoeft op te nemen met de ondersteuning van Azure.

## <a name="iot-hub-query-language"></a>Query taal IoT Hub

De [IOT hub query taal](iot-hub-devguide-query-language.md) is een SQL-achtige taal waarmee u een query kunt uitvoeren op uw [taak](#job) en apparaat apparaatdubbels.

## <a name="iot-hub-resource-rest-api"></a>IoT Hub resource REST API

U kunt de [IOT hub Resource rest API](https://docs.microsoft.com/rest/api/iothub/iothubresource) gebruiken voor het beheren van de IOT-hubs in uw [Azure-abonnement](#subscription) voor het uitvoeren van bewerkingen, zoals het maken, bijwerken en verwijderen van hubs.

## <a name="iot-solution-accelerators"></a>IoT-oplossingsversnellers

Met accelerators van Azure IoT-oplossing worden meerdere Azure-Services in oplossingen gecombineerd. Met deze oplossingen kunt u snel aan de slag met end-to-end-implementaties van algemene IoT-scenario's. Zie [Wat zijn Azure IOT-oplossings Accelerators?](../iot-accelerators/about-iot-accelerators.md) voor meer informatie.

## <a name="the-iot-extension-for-azure-cli"></a>De IoT-extensie voor Azure CLI 

[De IOT-extensie voor Azure cli](https://github.com/Azure/azure-iot-cli-extension) is een platform, opdracht regel programma voor meerdere platforms. Met het hulp programma kunt u uw apparaten beheren in het [identiteits register](#identity-registry), berichten en bestanden van uw apparaten verzenden en ontvangen en uw IOT hub-bewerkingen bewaken.

## <a name="job"></a>Taak

De back-end van uw oplossing kan [taken](iot-hub-devguide-jobs.md) gebruiken om activiteiten te plannen en bij te houden voor een set apparaten die zijn geregistreerd bij uw IOT-hub. Activiteiten omvatten het bijwerken van de dubbele [gewenste eigenschappen](#desired-properties)van het apparaat, het bijwerken van de dubbele [Tags](#tags)van een apparaat en het aanroepen van [directe methoden](#direct-method). [IOT hub](#iot-hub) wordt ook gebruikt voor het [importeren van en exporteren](iot-hub-devguide-identity-registry.md#import-and-export-device-identities) uit het [identiteits register](#identity-registry).

## <a name="modules"></a>Modules

Aan de kant van het apparaat kunt u met de Sdk's van het IoT Hub apparaat [modules](iot-hub-devguide-module-twins.md) maken waarbij elke ene een onafhankelijke verbinding met IOT hub opent. Met deze functie kunt u afzonderlijke naam ruimten voor verschillende onderdelen op het apparaat gebruiken.

Module-identiteit en module twee bieden dezelfde mogelijkheden als de [apparaat-id](#device-identity) en het [apparaat](#device-twin) , met een nauw keurigere granulariteit. Met deze nauw keurigheid kunnen apparaten, zoals apparaten op basis van een besturings systeem of firmware-apparaten meerdere onderdelen beheren, de configuratie en voor waarden voor elk van deze onderdelen worden geïsoleerd.

## <a name="module-identity"></a>Module-identiteit

De module-id is de unieke id die is toegewezen aan elke module die deel uitmaakt van een apparaat. Module-identiteit wordt ook geregistreerd in het [identiteits register](#identity-registry).

## <a name="module-twin"></a>Module dubbele

Net als bij een ander apparaat dan is een module gelijk aan het JSON-document waarin module status gegevens worden opgeslagen, zoals meta gegevens, configuraties en voor waarden. IoT Hub persistent een module tussen een module-identiteit die u onder een apparaat-id in uw IoT-hub inricht. Met module apparaatdubbels kunt u module omstandigheden en configuraties synchroniseren tussen de module en de back-end van de oplossing. U kunt de module apparaatdubbels opvragen om specifieke modules te vinden en de status van langlopende bewerkingen te controleren.

## <a name="mqtt"></a>MQTT

[MQTT](https://mqtt.org/) is een van de berichten protocollen die [IOT hub](#iot-hub) ondersteunt voor het communiceren met apparaten. Zie [berichten verzenden en ontvangen met IOT hub](iot-hub-devguide-messaging.md)voor meer informatie over de berichten protocollen die door IOT hub worden ondersteund.

## <a name="operations-monitoring"></a>Controle van bewerkingen

Met IoT Hub [bewerkingen](iot-hub-operations-monitoring.md) bewaken kunt u de status van de bewerkingen op uw IOT-hub in realtime bewaken. [IOT hub](#iot-hub) houdt gebeurtenissen in verschillende categorieën bewerkingen bij. U kunt ervoor kiezen gebeurtenissen te verzenden van een of meer categorieën naar een IoT Hub eind punt voor verwerking. U kunt de gegevens controleren op fouten of complexere verwerking instellen op basis van gegevens patronen.

## <a name="physical-device"></a>Fysiek apparaat

Een fysiek apparaat is een echt apparaat, zoals een Raspberry Pi, waarmee verbinding wordt gemaakt met een IoT-hub. Voor het gemak gebruiken veel van de IoT Hub zelf studies [gesimuleerde apparaten](#simulated-device) waarmee u voor beelden kunt uitvoeren op uw lokale computer.

## <a name="primary-and-secondary-keys"></a>Primaire en secundaire sleutels

Wanneer u verbinding maakt met een apparaat-Facing of een service gericht eind punt op een IoT-hub, bevat uw [Connection String](#connection-string) sleutel om u toegang te verlenen. Wanneer u een apparaat toevoegt aan het [identiteits register](#identity-registry) of een [gedeeld toegangs beleid](#shared-access-policy) toevoegt aan uw hub, wordt door de service een primaire en secundaire sleutel gegenereerd. Met twee sleutels kunt u overgaan van de ene sleutel naar de andere wanneer u een sleutel bijwerkt zonder de toegang tot de IoT-hub te verliezen.

## <a name="protocol-gateway"></a>Protocolgateway

Een protocol gateway wordt doorgaans geïmplementeerd in de Cloud en biedt protocol Vertaal Services voor apparaten die verbinding maken met [IOT hub](#iot-hub). Zie [Wat is Azure IOT hub?](about-iot-hub.md) voor meer informatie.

## <a name="quotas-and-throttling"></a>Quota en beperkingen

Er zijn verschillende [quota's](iot-hub-devguide-quotas-throttling.md) die van toepassing zijn op uw gebruik van [IOT hub](#iot-hub). veel van de quota's variëren op basis van de laag van de IOT-hub. [IOT hub](#iot-hub) [is ook van toepassing op het](iot-hub-devguide-quotas-throttling.md) gebruik van de service tijdens de uitvoering.

## <a name="reported-configuration"></a>Gerapporteerde configuratie

In de context van een [apparaat dubbele](iot-hub-devguide-device-twins.md), gerapporteerde configuratie verwijst naar de volledige set eigenschappen en meta gegevens in het apparaat dat moet worden gerapporteerd aan de back-end van de oplossing.

## <a name="reported-properties"></a>Gerapporteerde eigenschappen

In de context van een [apparaat](iot-hub-devguide-device-twins.md), gerapporteerde eigenschappen is een subsectie van het apparaat dat wordt gebruikt met de [gewenste eigenschappen](#desired-properties) voor het synchroniseren van de apparaatconfiguratie of voor waarde. Gerapporteerde eigenschappen kunnen alleen worden ingesteld door de [apparaat-app](#device-app) en kunnen worden gelezen en opgevraagd door een [back-end-app](#back-end-app).

## <a name="resource-group"></a>Resourcegroep

[Azure Resource Manager](#azure-resource-manager) maakt gebruik van resource groepen om gerelateerde resources samen te groeperen. U kunt een resource groep gebruiken om bewerkingen uit te voeren op alle resources in de groep tegelijk.

## <a name="retry-policy"></a>Beleid voor opnieuw proberen

U gebruikt een beleid voor opnieuw proberen om [tijdelijke fouten](/azure/architecture/best-practices/transient-faults) af te handelen wanneer u verbinding maakt met een Cloud service.

## <a name="routing-rules"></a>Regels voor doorsturen

U configureert [routerings regels](iot-hub-devguide-messages-read-custom.md) in uw IOT-hub om apparaat-naar-Cloud-berichten te routeren naar een [ingebouwd eind punt](#built-in-endpoints) of naar [aangepaste eind punten](#custom-endpoints) voor verwerking door de back-end van uw oplossing.

## <a name="sasl-plain"></a>SASL PLAIN

SASL PLAIN is een protocol dat het AMQP-protocol gebruikt voor het overdragen van beveiligings tokens.

## <a name="service-rest-api"></a>Service REST API

U kunt de [Service rest API](https://docs.microsoft.com/rest/api/iothub/service/configuration) van de back-end van de oplossing gebruiken om uw apparaten te beheren. Met de API kunt u dubbele eigenschappen van het [apparaat](#device-twin) ophalen en bijwerken, [direct methoden](#direct-method)aanroepen en [taken](#job)plannen. Normaal gesp roken moet u een van de high-level [service-sdk's](#azure-iot-service-sdks) gebruiken, zoals weer gegeven in de IOT hub zelf studies.

## <a name="shared-access-signature"></a>Shared Access Signature

Shared Access signatures (SAS) zijn een verificatie methode op basis van SHA-256 Secure hashes of Uri's. SAS-verificatie heeft twee onderdelen: een _gedeeld toegangs beleid_ en een _Shared Access Signature_ (vaak een token genoemd). Een apparaat gebruikt SAS om te verifiëren bij een IoT-hub. [Back-end-apps](#back-end-app) maken ook gebruik van SAS om te verifiëren bij de service gerichte eind punten op een IOT-hub. Normaal gesp roken neemt u het SAS-token op in het [Connection String](#connection-string) dat een app gebruikt om een verbinding met een IOT-hub tot stand te brengen.

## <a name="shared-access-policy"></a>Beleid voor gedeelde toegang

In een gedeeld toegangs beleid worden de machtigingen gedefinieerd die aan iedereen zijn verleend en die een geldige [primaire of secundaire sleutel](#primary-and-secondary-keys) heeft die aan dat beleid is gekoppeld. U kunt het gedeelde toegangs beleid en de sleutels voor uw hub in de [Portal](#azure-portal)beheren.

## <a name="simulated-device"></a>Gesimuleerd apparaat

Voor het gemak gebruiken veel van de IoT Hub zelf studies gesimuleerde apparaten waarmee u voor beelden kunt uitvoeren op uw lokale computer. Een [fysiek apparaat](#physical-device) daarentegen is een echt apparaat, zoals een Raspberry Pi, waarmee verbinding wordt gemaakt met een IOT-hub.

## <a name="solution"></a>Oplossing
Een _oplossing_ kan verwijzen naar een Visual Studio-oplossing die een of meer projecten bevat. Een _oplossing_ kan ook verwijzen naar een IOT-oplossing die elementen, zoals apparaten, [apps](#device-app), een IOT-hub, andere Azure-Services en [back-end-apps](#back-end-app)bevat.

## <a name="subscription"></a>Abonnement

Een Azure-abonnement is de plaats waar facturering plaatsvindt. Elke Azure-resource die u maakt of de Azure-service die u gebruikt, is gekoppeld aan één abonnement. Veel quota's zijn ook van toepassing op het niveau van een abonnement.

## <a name="system-properties"></a>Systeemeigenschappen

In de context van een [apparaat](iot-hub-devguide-device-twins.md)zijn de systeem eigenschappen alleen-lezen en bevatten informatie over het gebruik van het apparaat, zoals de tijd van de laatste activiteit en de status van de verbinding.

## <a name="tags"></a>Tags

In de context van een [apparaat dubbele](iot-hub-devguide-device-twins.md)worden de meta gegevens van het apparaat opgeslagen en opgehaald door de back-end van de oplossing in de vorm van een JSON-document. Tags zijn niet zichtbaar voor apps op een apparaat.

## <a name="telemetry"></a>Telemetrie

Apparaten verzamelen telemetriegegevens, zoals wind snelheid of Tempe ratuur, en gebruiken gegevens punt berichten om de telemetrie te verzenden naar een IoT-hub.

## <a name="token-service"></a>Token Service

U kunt een token service gebruiken om een verificatie mechanisme voor uw apparaten te implementeren. Er wordt gebruikgemaakt van een IoT Hub [beleid voor gedeelde toegang](#shared-access-policy) met **DeviceConnect** -machtigingen voor het maken van tokens met een *bereik* . Met deze tokens kunnen apparaten verbinding maken met uw IoT-hub. Een apparaat gebruikt een aangepast verificatie mechanisme om te verifiëren met de token service. Als het apparaat is geverifieerd, geeft de token service een SAS-token uit voor het apparaat dat moet worden gebruikt voor toegang tot uw IoT-hub.

## <a name="twin-queries"></a>Dubbele query's

Bij [dubbele query's voor apparaten en modules](iot-hub-devguide-query-language.md) wordt de SQL-achtige IOT hub query taal gebruikt om informatie op te halen van uw apparaat apparaatdubbels of module apparaatdubbels. U kunt dezelfde IoT Hub query taal gebruiken om informatie op te halen over een [taak](#job) die wordt uitgevoerd in uw IOT-hub.

## <a name="twin-synchronization"></a>Dubbele synchronisatie

Dubbele synchronisatie maakt gebruik van de [gewenste eigenschappen](#desired-properties) in uw apparaat apparaatdubbels of module apparaatdubbels om uw apparaten of modules te configureren en [gerapporteerde eigenschappen](#reported-properties) van ze te verkrijgen voor opslag in de dubbele.

## <a name="x509-client-certificate"></a>X. 509-client certificaat

Een apparaat kan een X. 509-certificaat gebruiken om te verifiëren met [IOT hub](#iot-hub). Het gebruik van een X. 509-certificaat is een alternatief voor het gebruik van een [SAS-token](#shared-access-signature).
