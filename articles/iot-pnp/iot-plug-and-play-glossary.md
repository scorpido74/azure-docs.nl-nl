---
title: Woordenlijst van termen - IoT Plug and Play Preview | Microsoft Documenten
description: Concepten - een woordenlijst van veelvoorkomende termen met betrekking tot IoT Plug and Play Preview.
author: ChrisGMsft
ms.author: chrisgre
ms.date: 12/23/2019
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: ef5ce9cc1cda7f1ff6b1985771e20cb20123e264
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025636"
---
# <a name="glossary-of-terms-for-iot-plug-and-play-preview"></a>Woordenlijst met termen voor IoT Plug and Play Preview

Definities van algemene termen zoals gebruikt in de IoT Plug and Play artikelen.

## <a name="azure-certified-for-iot-portal"></a>Azure-gecertificeerd voor IoT-portal

U de azure [certified voor IoT-portalwebsite](https://aka.ms/ACFI) gebruiken om:

- Voltooi het [certificeringsproces](#device-certification) voor uw [IoT Plug and Play-apparaat.](#iot-plug-and-play-device)
- Modelvoor [apparaatcapaciteit zoeken](#device-capability-model).
- Publiceer een apparaatcapaciteitsmodel naar de [openbare modelopslagplaats](#public-model-repository).

## <a name="azure-cli"></a>Azure-CLI

De Azure CLI is een cross-platform, command-line tool voor het beheer van Azure resources. De Azure IoT-extensie voor de Azure CLI is een opdrachtregelhulpmiddel voor het communiceren met en testen van [IoT Plug and Play-apparaten.](#iot-plug-and-play-device) U de extensie gebruiken om:

- Maak verbinding met een IoT Plug and Play-apparaat.
- Bekijk de [telemetrie die](#telemetry) het apparaat verzendt.
- Werken met [apparaateigenschappen](#properties).
- Opdrachten voor [oproepapparaten](#commands).
- [Modelrepositories,](#model-repository) [interfaces](#interface)en [apparaatcapaciteitsmodellen](#device-capability-model)beheren .

## <a name="azure-iot-central"></a>Azure IoT Central

Azure IoT Central is een volledig beheerde software-as-a-service-oplossing die het eenvoudig maakt om uw [IoT Plug and Play-apparaten](#iot-plug-and-play-device)aan te sluiten, te controleren en te beheren. U [apparaatcapaciteitsmodellen](#device-capability-model) gebruiken om automatisch een IoT Central-toepassing te configureren om uw apparaten te controleren en te beheren.

## <a name="azure-iot-certification-service"></a>Azure IoT-certificeringsservice

De Azure IoT-certificeringsservice voert een reeks certificeringstests uit wanneer u een [IoT Plug and Play-apparaat](#iot-plug-and-play-device) indient voor certificering via de [Azure Certified for IoT-portal.](#azure-certified-for-iot-portal) Voordat u een apparaat toevoegen aan de [catalogus van Certified for IoT-apparaten,](#certified-for-iot-device-catalog)moet het apparaat zijn gecertificeerd.

## <a name="azure-iot-tools-extension"></a>Azure IoT-hulpprogramma's-extensie

Azure IoT Tools is een verzameling extensies in [Visual Studio-code](#visual-studio-code) waarmee u communiceren met IoT Hub en IoT-apparaten ontwikkelen. Voor de ontwikkeling van IoT Plug and Play-apparaten helpt het u om:

- Ontwerp [apparaat mogelijkheden modellen](#device-capability-model) en [interfaces](#interface).
- Publiceren naar [modelrepositories](#model-repository).
- Skeletcode genereren om de apparaattoepassing te implementeren.

## <a name="azure-iot-explorer-tool"></a>Azure IoT explorer, gereedschap

De Azure IoT explorer is een grafisch hulpmiddel waarmee u communiceren en uw [IoT Plug and Play-apparaten](#iot-plug-and-play-device)testen. Nadat u het gereedschap op uw lokale machine hebt geïnstalleerd, u het gebruiken om:

- Bekijk de apparaten die zijn aangesloten op uw [IoT-hub.](#azure-iot-hub)
- Maak verbinding met een IoT Plug and Play-apparaat.
- Bekijk de [telemetrie die](#telemetry) het apparaat verzendt.
- Werken met [apparaateigenschappen](#properties).
- Opdrachten voor [oproepapparaten](#commands).

## <a name="azure-iot-hub"></a>Azure IoT Hub

IoT Hub is een beheerde service die wordt gehost in de cloud en die fungeert als een centrale berichtenhub voor bidirectionele communicatie tussen uw IoT-toepassing en de apparaten die hiermee worden beheerd. [IoT Plug and Play-apparaten](#iot-plug-and-play-device) kunnen verbinding maken met een IoT-hub. Een IoT-oplossing maakt gebruik van een IoT-hub om:

- Apparaten om telemetrie naar een cloudoplossing te verzenden.
- Een cloudgebaseerde oplossing voor het beheren van verbonden apparaten.

## <a name="azure-iot-device-sdk"></a>Azure IoT-apparaat SDK

Er zijn apparaat-SDK's voor meerdere talen die u gebruiken om Clienttoepassingen voor IoT-stekker en play-apparaten te bouwen. Een van de vereisten voor [apparaatcertificering](#device-certification) is dat de clientcode van het apparaat een van de Azure IoT-apparaat-SDK's gebruikt.

## <a name="certified-for-iot-device-catalog"></a>Gecertificeerd voor De apparaatcatalogus van IoT

De [catalogus voor gecertificeerd voor IoT-apparaten](https://catalog.azureiotsolutions.com/) bevat [IoT Plug and Play-apparaten](#iot-plug-and-play-device) die de [apparaatcertificeringstests](#device-certification) hebben doorstaan. De [apparaatmogelijkheidmodellen](#device-capability-model) voor de IoT Plug and Play-apparaten in de catalogus en gepubliceerd in de openbare modelopslagplaats.

## <a name="commands"></a>Opdrachten

Opdrachten die zijn gedefinieerd in een [interface](#interface) vertegenwoordigen methoden die kunnen worden uitgevoerd op de [digitale tweeling.](#digital-twin) Bijvoorbeeld een opdracht om een apparaat opnieuw op te starten.

## <a name="common-interface"></a>Algemene interface

Van [alle IoT Plug and Play-apparaten](#iot-plug-and-play-device) wordt verwacht dat ze een aantal algemene [interfaces](#interface)implementeren. De informatie-interface van het apparaat definieert bijvoorbeeld hardware- en besturingssysteeminformatie over het apparaat. [Apparaatcertificering](#device-certification) vereist dat uw apparaat verschillende algemene interfaces implementeert. U algemene interfacedefinities ophalen uit de openbare modelopslagplaats.

## <a name="company-model-repository"></a>Repository van bedrijfsmodel

Een organisatie kan een [bedrijfsmodelrepository](#model-repository) gebruiken als een privéwinkel voor [apparaatcapaciteitsmodellen](#device-capability-model) en [-interfaces.](#interface)

## <a name="connection-string"></a>Verbindingsreeks

Een verbindingstekenreeks bevat de informatie die nodig is om verbinding te maken met een eindpunt. Een verbindingstekenreeks bevat doorgaans het adres van het eindpunt en de beveiligingsgegevens, maar verbindingstekenreeksindelingen verschillen per services. Er zijn twee typen verbindingstekenreeksen die zijn gekoppeld aan de IoT Hub-service:

- Met apparaatverbindingstekenreeksen kunnen [IoT-plug- en play-apparaten](#iot-plug-and-play-device) verbinding maken met de apparaatgerichte eindpunten op een IoT-hub. Clientcode op een apparaat gebruikt de verbindingstekenreeks om een beveiligde verbinding met een IoT-hub tot stand te brengen.
- IoT Hub-verbindingstekenreeksen maken back-endoplossingen en -hulpprogramma's mogelijk om veilig verbinding te maken met de servicegerichte eindpunten op een IoT-hub. Deze oplossingen en tools beheren de IoT-hub en de apparaten die erop zijn aangesloten.
- De repository-verbindingstekenreeksen van bedrijfsmodellen maken back-endoplossingen en hulpprogramma's mogelijk om veilig verbinding te maken met een [bedrijfsmodelrepository.](#company-model-repository) Deze oplossingen en hulpprogramma's verbruiken of beheren de [apparaatcapaciteitsmodellen](#device-capability-model) en [-interfaces](#interface) in de repository.

## <a name="device-capability-model"></a>Apparaatcapaciteitsmodel

Een apparaatmogelijkheidsmodel beschrijft een [IoT Plug and Play-apparaat](#iot-plug-and-play-device) en definieert de set [interfaces](#interface) die door het apparaat worden geïmplementeerd. Een apparaatcapaciteitsmodel komt meestal overeen met een fysiek apparaat, product of SKU. U gebruikt de [Digital Twin Definition Language](#digital-twin-definition-language) om een apparaatcapaciteitsmodel te definiëren.

## <a name="device-certification"></a>Apparaatcertificering

Apparaatcertificering is het proces van het certificeren van een [IoT Plug and Play-apparaat,](#iot-plug-and-play-device) zodat het kan worden toegevoegd aan de [catalogus van certified for IoT-apparaten](#certified-for-iot-device-catalog) en het [apparaatcapaciteitsmodel](#device-capability-model) en [de interfaces](#interface) die zijn toegevoegd aan de [openbare modelopslagplaats.](#public-model-repository)

## <a name="device-developer"></a>Ontwikkelaar apparaat

Een apparaatontwikkelaar gebruikt een [apparaatcapaciteitsmodel,](#device-capability-model) [interfaces](#interface)en een [Azure IoT-apparaatSDK](#azure-iot-device-sdk) om code te implementeren die wordt uitgevoerd op een [IoT Plug and Play-apparaat.](#iot-plug-and-play-device)

## <a name="device-modeling"></a>Apparaatmodellering

Een [apparaatontwikkelaar](#device-developer) gebruikt de [Digital Twin Definition Language](#digital-twin-definition-language) om de mogelijkheden van een [IoT Plug and Play-apparaat](#iot-plug-and-play-device)te modelleren. Het model kan worden gedeeld met behulp van een modelrepository. Een apparaatontwikkelaar kan skeletapparaatcode van het model genereren. Een [ontwikkelaar van een oplossing](#solution-developer) kan een IoT-oplossing configureren vanuit het model.

## <a name="device-provisioning-service"></a>Service voor inrichting van apparaten

[Azure IoT Central](#azure-iot-central) gebruikt de Service Voor het inrichten van apparaten om alle apparaatregistratie en -verbinding te beheren. Zie [Apparaatconnectiviteit in Azure IoT Central](../iot-central/core/concepts-get-connected.md)voor meer informatie. U de Service Apparaatinrichting ook gebruiken om apparaatregistratie en -verbinding met uw IoT Hub-gebaseerde IoT-oplossing te beheren. Zie [Apparaten inrichten met Azure IoT Hub Device Provisioning Service](../iot-dps/about-iot-dps.md)voor meer informatie.

## <a name="device-registration"></a>Apparaatregistratie

Voordat een [IoT Plug and Play-apparaat](#iot-plug-and-play-device) verbinding kan maken met een IoT-oplossing, moet het worden geregistreerd bij de oplossing. [Azure IoT Central](#azure-iot-central) gebruikt de [Apparaatinrichtingsservice](#device-provisioning-service) om apparaatregistratie te beheren. In een aangepaste IoT-oplossing u apparaten registreren met uw IoT-hub in de Azure-portal of programmatisch.

## <a name="device-first"></a>Apparaat-eerst

[Azure IoT Central](#azure-iot-central) ondersteunt een apparaat-eerste registratie- en verbindingsscenario. In dit scenario kan een [IoT Plug and Play-apparaat](#iot-plug-and-play-device) verbinding maken met een IoT Central-toepassing zonder vooraf te zijn geregistreerd. Registratie gebeurt automatisch wanneer een apparaat voor het eerst verbinding maakt met de toepassing.

## <a name="digital-twin"></a>Digitale tweeling

Een digitale tweeling is een model van een [IoT Plug and Play-apparaat.](#iot-plug-and-play-device) Een digitale tweeling wordt gemodelleerd met behulp van de [Digital Twin Definition Language](#digital-twin-definition-language). U de [Azure IoT-apparaat SDK's](#azure-iot-device-sdk) gebruiken om te communiceren met digitale tweelingen tijdens het uitvoeren van de looptijd. U bijvoorbeeld een eigenschapswaarde instellen in een digitale tweeling op een apparaat en de SDK communiceert deze wijziging naar uw IoT-oplossing in de cloud.

## <a name="digital-twin-change-events"></a>Digitale twin change events

Wanneer een [IoT Plug and Play-apparaat](#iot-plug-and-play-device) is verbonden met een [IoT-hub,](#azure-iot-hub)kan de hub zijn routeringsmogelijkheden gebruiken om meldingen van digitale tweelingwijzigingen te verzenden. Wanneer een [eigenschapswaarde](#properties) bijvoorbeeld verandert op een apparaat, kan IoT Hub een melding verzenden naar een eindpunt, zoals een wachtrij voor servicebus.

## <a name="digital-twin-definition-language"></a>Digitale Twin Definition Taal

Een taal voor het beschrijven van modellen en interfaces voor [IoT Plug and Play-apparaten.](#iot-plug-and-play-device) Gebruik de [Digital Twin Definition Language](https://aka.ms/DTDL) om de mogelijkheden van een digitale [tweeling](#digital-twin) te beschrijven en het IoT-platform en IoT-oplossingen in staat te stellen gebruik te maken van de semantiek van de entiteit.

## <a name="digital-twin-route"></a>Digitale tweelingroute

Een route die is ingesteld in een [IoT-hub](#azure-iot-hub) om [digitale twin change-gebeurtenissen](#digital-twin-change-events) naar en eindpunt te leveren, zoals een wachtrij voor servicebussen.

## <a name="interface"></a>Interface

Een interface beschrijft gerelateerde mogelijkheden die worden geïmplementeerd door een [IoT Plug and Play-apparaat](#iot-plug-and-play-device) of [digitale tweeling.](#digital-twin) U interfaces opnieuw gebruiken voor verschillende [apparaatcapaciteitsmodellen.](#device-capability-model)

## <a name="iot-hub-query-language"></a>Taal van IoT Hub-query

De IoT Hub-querytaal wordt voor meerdere doeleinden gebruikt. U de taal bijvoorbeeld gebruiken om te zoeken naar [apparaten die zijn geregistreerd](#device-registration) bij uw IoT-hub of om het digitale probleem van de [routering van twee personen](#digital-twin-route) te verfijnen.

## <a name="iot-plug-and-play-device"></a>IoT-stekker en afspelen-apparaat

Een IoT Plug and Play-apparaat is meestal een kleinschalig, zelfstandig computerapparaat dat gegevens verzamelt of andere apparaten bestuurt, en dat software of firmware uitvoert die een [apparaatcapaciteitsmodel](#device-capability-model)implementeert.  Een IoT Plug and Play-apparaat kan bijvoorbeeld een milieubewakingsapparaat zijn of een controller voor een irrigatiesysteem voor slimme landbouw. U een door de cloud gehoste IoT-oplossing schrijven om gegevens van IoT Plug and Play-apparaten te bevelen, te bedienen en te ontvangen. De [Azure Certified for IoT-apparaatcatalogus](#certified-for-iot-device-catalog) bevat beschikbare IoT-plug- en play-apparaten. Elk IoT Plug and Play-apparaat in de catalogus is gevalideerd en heeft een [apparaatcapaciteitsmodel.](#device-capability-model)

## <a name="microsoft-partner-center"></a>Microsoft Partnercentrum

[Microsoft Partner Center](https://docs.microsoft.com/partner-center/) is de plaats waar uw organisatie de end-to-end relatie met Microsoft beheert. U hebt een Microsoft Partner Center-account nodig voordat u uw [IoT Plug and Play-apparaat](#iot-plug-and-play-device) certificeren in de [Azure Certified for IoT-portal.](#azure-certified-for-iot-portal)

## <a name="model-discovery"></a>Modeldetectie

Wanneer een [IoT Plug and Play-apparaat](#iot-plug-and-play-device) verbinding maakt met een IoT-oplossing, kan de oplossing de mogelijkheden van het apparaat ontdekken door het [apparaatcapaciteitsmodel te](#device-capability-model)vinden. Een apparaat kan het capaciteitsmodel naar de oplossing sturen of de oplossing kan een apparaatcapaciteitsmodel vinden in een [modelopslagplaats.](#model-repository)

## <a name="model-repository"></a>Modelopslagplaats

Een model repository slaat [apparaatmogelijkheden modellen](#device-capability-model) en [interfaces](#interface). Er is één [openbare modelopslagplaats.](#public-model-repository) Organisaties kunnen hun eigen organisatiemodelrepositories maken.

## <a name="model-repository-rest-api"></a>REST-API voor modelopslagplaats

Een API voor het beheren en interactie met modelrepositories. U de API bijvoorbeeld gebruiken om [apparaatcapaciteitsmodellen](#device-capability-model) toe te voegen en te zoeken naar capaciteitsmodellen.

## <a name="properties"></a>Eigenschappen

Eigenschappen zijn gegevensvelden die zijn gedefinieerd in een [interface](#interface) die een bepaalde status van een digitale tweeling vertegenwoordigt. U eigenschappen declareren als alleen-lezen of beschrijfbaar. Alleen-lezen eigenschappen, zoals serienummer, worden ingesteld op code die wordt uitgevoerd op het [IoT Plug and Play-apparaat](#iot-plug-and-play-device) zelf.  Beschrijfbare eigenschappen, zoals een alarmdrempel, worden meestal ingesteld vanuit de cloudgebaseerde IoT-oplossing.

## <a name="public-model-repository"></a>Opslagplaats voor openbaar model

Er is één openbare modelopslagplaats die [apparaatcapaciteitsmodellen](#device-capability-model) en [-interfaces](#interface) voor [gecertificeerde apparaten opslaat.](#device-certification) De openbare modelrepository slaat ook [gemeenschappelijke interfacedefinities](#common-interface) op.

## <a name="registration-id"></a>Registratie-id

Een registratie-id identificeert op unieke wijze een apparaat in de [Apparaatinrichtingsservice](#device-provisioning-service). Deze ID is niet hetzelfde als de apparaat-id die een unieke id is voor een apparaat in een [IoT-hub.](#azure-iot-hub)

## <a name="scope-id"></a>Bereik-ID

In het scope-id-bereik wordt op unieke wijze een [apparaatinrichtingsservice-instantie](#device-provisioning-service) geïdentificeerd.

## <a name="shared-access-signature"></a>Handtekening voor gedeelde toegang

Gedeelde toegangshandtekeningen zijn een verificatiemechanisme op basis van SHA-256 beveiligde hashes of URI's. Verificatie van handtekening voor gedeelde toegang bestaat uit twee onderdelen: een beleid voor gedeelde toegang en een handtekening voor gedeelde toegang (vaak een token genoemd). Een [IoT Plug and Play-apparaat](#iot-plug-and-play-device) gebruikt een handtekening voor gedeelde toegang om te verifiëren met een [IoT-hub.](#azure-iot-hub)

## <a name="solution-developer"></a>Oplossingsontwikkelaar

Een ontwikkelaar van oplossingen creëert de back-end van de oplossing. Een oplossingsontwikkelaar werkt doorgaans met Azure-bronnen zoals [IoT Hub](#azure-iot-hub) en [modelrepositories](#model-repository)of werkt met [IoT Central.](#azure-iot-central)

## <a name="telemetry"></a>Telemetrie

Telemetrievelden die in een [interface](#interface) zijn gedefinieerd, vertegenwoordigen metingen. Deze metingen zijn meestal waarden zoals sensormetingen die door het [IoT Plug and Play-apparaat](#iot-plug-and-play-device) worden verzonden als een stroom van gegevens.

## <a name="visual-studio-code"></a>Visual Studio-code

Visual Studio-code is een moderne code-editor die beschikbaar is voor meerdere platforms. Met extensies, zoals die in het [Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) Tools-pakket, u de editor aanpassen om een breed scala aan ontwikkelingsscenario's te ondersteunen.
