---
title: Verklarende woorden lijst-preview-versie van IoT Plug en Play | Microsoft Docs
description: 'Concepten: een woorden lijst met algemene termen met betrekking tot IoT Plug en Play preview.'
author: ChrisGMsft
ms.author: chrisgre
ms.date: 10/10/2019
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 22b2cb8caa815b2ce8343a060a5f12133b763b83
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/09/2019
ms.locfileid: "73890343"
---
# <a name="glossary-of-terms-for-iot-plug-and-play-preview"></a>Verklarende woorden lijst voor de preview-versie van IoT Plug en Play

Definities van algemene termen die worden gebruikt in de IoT Plug en Play-artikelen.

## <a name="azure-certified-for-iot-portal"></a>Azure Certified voor IoT-Portal

U kunt de [Azure Certified voor IOT-Portal](https://aka.ms/ACFI) website gebruiken voor het volgende:

- Voltooi het [certificerings proces](#device-certification) voor uw [IOT Plug en Play-apparaat](#iot-plug-and-play-device).
- [Hulp modellen voor apparaten](#device-capability-model)zoeken.
- Een mogelijkheidsprofiel naar de [open bare model opslagplaats](#public-model-repository)publiceren.

## <a name="azure-cli"></a>Azure-CLI

De Azure CLI is een platform, opdracht regel programma voor het beheer van Azure-resources. De Azure IoT-extensie voor Azure CLI is een opdracht regel programma voor interactie met en het testen van [IoT Plug en Play-apparaten](#iot-plug-and-play-device). U kunt de extensie gebruiken voor het volgende:

- Verbinding maken met een IoT Plug en Play-apparaat.
- De [telemetrie](#telemetry) weer geven die het apparaat verzendt.
- Werken met apparaateigenschappen [.](#properties)
- Roep de [opdrachten](#commands)van het apparaat aan.
- [Model opslagplaatsen](#model-repository), [interfaces](#interface)en [Capability-modellen](#device-capability-model)beheren.

## <a name="azure-iot-central"></a>Azure IoT Central

Azure IoT Central is een volledig beheerde software-as-a-service-oplossing waarmee u uw [IoT Plug en Play-apparaten](#iot-plug-and-play-device)eenvoudig kunt verbinden, bewaken en beheren. U kunt [apparaatfuncties](#device-capability-model) gebruiken om automatisch een IOT Central toepassing te configureren om uw apparaten te bewaken en te beheren.

## <a name="azure-iot-certification-service"></a>Azure IoT-certificerings service

De Azure IoT-certificerings service voert een reeks certificerings tests uit wanneer u een [IoT Plug en Play-apparaat](#iot-plug-and-play-device) verzendt voor certificering via de [Azure Certified voor IOT-Portal](#azure-certified-for-iot-portal). Voordat u een apparaat kunt toevoegen aan de [certificerings catalogus voor IOT-apparaten](#certified-for-iot-device-catalog), moet het apparaat worden gecertificeerd.

## <a name="azure-iot-tools-extension"></a>Uitbrei ding Azure IoT-Hulpprogram Ma's

Azure IoT-Hulpprogram Ma's is een verzameling van uitbrei dingen in [Visual Studio code](#visual-studio-code) die u helpen bij het werken met IOT hub en IOT-apparaten te ontwikkelen. Voor IoT Plug en Play Device Development kunt u het volgende doen:

- [Hulp modellen](#device-capability-model) en [interfaces](#interface)voor het ontwikkelen van apparaten.
- Publiceren naar [model opslagplaatsen](#model-repository).
- Genereer een skelet code om de apparaat-app te implementeren.

## <a name="azure-iot-explorer-tool"></a>Hulp programma Azure IoT Explorer

Azure IoT Explorer is een grafisch hulp programma dat u kunt gebruiken om te communiceren met uw [IoT Plug en Play-apparaten](#iot-plug-and-play-device)en deze te testen. Nadat u het hulp programma hebt geïnstalleerd op uw lokale computer, kunt u het gebruiken voor het volgende:

- Bekijk de apparaten die zijn verbonden met uw [IOT-hub](#azure-iot-hub).
- Verbinding maken met een IoT Plug en Play-apparaat.
- De [telemetrie](#telemetry) weer geven die het apparaat verzendt.
- Werken met apparaateigenschappen [.](#properties)
- Roep de [opdrachten](#commands)van het apparaat aan.

## <a name="azure-iot-hub"></a>Azure IoT Hub

IoT Hub is een beheerde service die wordt gehost in de cloud en die fungeert als een centrale berichtenhub voor bidirectionele communicatie tussen uw IoT-toepassing en de apparaten die hiermee worden beheerd. [Iot Plug en Play-apparaten](#iot-plug-and-play-device) kunnen verbinding maken met een IOT-hub. Een IoT-oplossing maakt gebruik van een IoT-hub om het volgende in te scha kelen:

- Apparaten voor het verzenden van telemetrie naar een Cloud oplossing.
- Een Cloud oplossing voor het beheren van verbonden apparaten.

## <a name="azure-iot-device-sdk"></a>Azure IoT Device SDK

Er zijn apparaat-Sdk's voor meerdere talen die u kunt gebruiken voor het bouwen van IoT Plug en Play Device-client toepassingen. Een van de vereisten voor het [certificeren van apparaten](#device-certification) is dat de client code van het apparaat gebruikmaakt van een van de Azure IOT-apparaat-sdk's.

## <a name="certified-for-iot-device-catalog"></a>Gecertificeerd voor IoT-Apparaatbeheer

Het [Certified for IOT-apparaat Catalog](https://catalog.azureiotsolutions.com/) geeft [IOT Plug en Play-apparaten](#iot-plug-and-play-device) weer die de certificatie tests van het [apparaat](#device-certification) hebben door lopen. De [mogelijkheden](#device-capability-model) van het apparaat voor de IOT Plug en Play-apparaten in de catalogus en worden gepubliceerd in de open bare model opslagplaats.

## <a name="commands"></a>Opdrachten

Opdrachten die in een [Interface](#interface) zijn gedefinieerd, vertegenwoordigen methoden die kunnen worden uitgevoerd op het [digitale dubbele](#digital-twin). Bijvoorbeeld een opdracht om een apparaat opnieuw op te starten.

## <a name="common-interface"></a>Algemene interface

Alle [IoT Plug en Play-apparaten](#iot-plug-and-play-device) worden verwacht een aantal algemene [interfaces](#interface)te implementeren. De apparaatgegevens interface definieert bijvoorbeeld informatie over de hardware en het besturings systeem van het apparaat. Voor de [certificering van apparaten](#device-certification) moet uw apparaat verschillende algemene interfaces implementeren. U kunt algemene interface definities ophalen uit de open bare model opslagplaats.

## <a name="company-model-repository"></a>Bedrijfs model opslagplaats

Een organisatie kan gebruikmaken van een bedrijfs [model opslagplaats](#model-repository) als een privé-archief voor de [hulp modellen](#device-capability-model) en [interfaces](#interface)voor apparaten.

## <a name="connection-string"></a>Verbindingsreeks

Een connection string de gegevens die nodig zijn om verbinding te maken met een eind punt, worden ingekapseld. Een connection string bevat normaal gesp roken het adres van het eind punt en de beveiligings gegevens, maar connection string indelingen variëren in verschillende services. Er zijn twee soorten connection string gekoppeld aan de IoT Hub-service:

- Met verbindings reeksen voor apparaten kunnen [iot Plug en Play-apparaten](#iot-plug-and-play-device) verbinding maken met de op het apparaat gerichte eind punten op een IOT-hub. Client code op een apparaat gebruikt de connection string om een beveiligde verbinding met een IoT-hub tot stand te brengen.
- IoT Hub verbindings reeksen kunnen back-end-oplossingen en hulpprogram ma's gebruiken om veilig verbinding te maken met de service gerichte eind punten op een IoT-hub. Deze oplossingen en hulpprogram ma's beheren de IoT-hub en de apparaten die ermee zijn verbonden.
- De verbindings reeksen voor bedrijfs model opslagplaatsen bieden back-end-oplossingen en hulpprogram ma's om veilig verbinding te maken met een [bedrijfs model opslagplaats](#company-model-repository). Deze oplossingen en hulpprogram ma's gebruiken of beheren de hulp modellen en [interfaces](#interface) van het [apparaat](#device-capability-model) in de opslag plaats.

## <a name="device-capability-model"></a>Mogelijkheidsprofiel

Een mogelijkheidsprofiel beschrijft een IOT- [Plug en Play apparaat](#iot-plug-and-play-device) en definieert de set [interfaces](#interface) die door het apparaat worden geïmplementeerd. Een mogelijkheidsprofiel komt doorgaans overeen met een fysiek apparaat, product of SKU. U gebruikt de [Digital-dubbele definitie taal](#digital-twin-definition-language) om een mogelijkheidsprofiel te definiëren.

## <a name="device-certification"></a>Apparaat certificering

Apparaat-certificering is het proces van het certificeren van een [IoT Plug en Play-apparaat](#iot-plug-and-play-device) , zodat het kan worden toegevoegd aan de certificerings instantie [voor IOT-apparaten](#certified-for-iot-device-catalog) en het [functionaliteits model](#device-capability-model) van het apparaat en [interfaces](#interface) die zijn toegevoegd aan het [open bare model opslag plaats](#public-model-repository).

## <a name="device-developer"></a>Ontwikkelaar apparaat

Een ontwikkelaar van een apparaat maakt gebruik van een [mogelijkheidsprofiel](#device-capability-model), [interfaces](#interface)en een [Azure IOT Device SDK](#azure-iot-device-sdk) om code te implementeren om uit te voeren op een [IOT Plug en Play-apparaat](#iot-plug-and-play-device).

## <a name="device-modeling"></a>Apparaats modellen

[Ontwikkel aars](#device-developer) van een apparaat gebruiken de [Digital-dubbele definitie taal](#digital-twin-definition-language) om de mogelijkheden van een [IOT Plug en Play-apparaat](#iot-plug-and-play-device)te model leren. Het model kan worden gedeeld met behulp van een model opslagplaats. Een ontwikkelaar van een apparaat kan een skelet apparaatcode uit het model genereren. Een [oplossings ontwikkelaar](#solution-developer) kan een IOT-oplossing van het model configureren.

## <a name="device-provisioning-service"></a>Device Provisioning Service

[Azure IOT Central](#azure-iot-central) maakt gebruik van de Device Provisioning Service voor het beheer van alle apparaatregistratie en de verbinding. Zie [connectiviteit van apparaten in Azure IOT Central](../iot-central/preview/overview-iot-central-get-connected.md)voor meer informatie. U kunt ook de Device Provisioning Service gebruiken voor het beheren van apparaatregistratie en verbinding met uw op IoT Hub gebaseerde IoT-oplossing. Zie [apparaten inrichten met Azure IOT hub Device Provisioning Service](../iot-dps/about-iot-dps.md)voor meer informatie.

## <a name="device-registration"></a>Apparaatregistratie

Voordat een [IOT-Plug en Play apparaat](#iot-plug-and-play-device) verbinding kan maken met een IOT-oplossing, moet het zijn geregistreerd bij de oplossing. [Azure IOT Central](#azure-iot-central) maakt gebruik van de [Device Provisioning Service](#device-provisioning-service) om apparaatregistratie te beheren. In een aangepaste IoT-oplossing kunt u apparaten registreren bij uw IoT-hub in de Azure Portal of via een programma.

## <a name="device-first"></a>Apparaat-eerst

[Azure IOT Central](#azure-iot-central) ondersteunt een scenario voor het eerst registreren en verbinden van apparaten. In dit scenario kan een [IOT-Plug en Play apparaat](#iot-plug-and-play-device) verbinding maken met een IOT Central toepassing zonder vooraf te worden geregistreerd. Registratie gebeurt automatisch wanneer een apparaat voor het eerst verbinding maakt met de toepassing.

## <a name="digital-twin"></a>Digitale dubbele

Een digitale dubbele is een model van een [IOT-Plug en Play apparaat](#iot-plug-and-play-device). Een digitaal twee is gemodelleerd met behulp van de [Digital-taal voor dubbele definitie](#digital-twin-definition-language). U kunt de Sdk's van het [Azure IOT-apparaat](#azure-iot-device-sdk) gebruiken om te communiceren met Digital apparaatdubbels tijdens de uitvoering. U kunt bijvoorbeeld een eigenschaps waarde instellen in een digitaal, twee op een apparaat en de SDK communiceert deze wijziging naar uw IoT-oplossing in de Cloud.

## <a name="digital-twin-change-events"></a>Digitale dubbele wijzigings gebeurtenissen

Wanneer een [IOT-Plug en Play apparaat](#iot-plug-and-play-device) is verbonden met een [IOT-hub](#azure-iot-hub), kan de hub de route ring gebruiken om meldingen van digitale dubbele wijzigingen te verzenden. Wanneer bijvoorbeeld een [eigenschaps](#properties) waarde op een apparaat wordt gewijzigd, kan IOT hub een melding verzenden naar een eind punt, zoals een service bus wachtrij.

## <a name="digital-twin-definition-language"></a>Digital-dubbele definitie taal

Een taal voor het beschrijven van modellen en interfaces voor [IoT Plug en Play-apparaten](#iot-plug-and-play-device). Gebruik de [Digital-dubbele definitie taal](https://aka.ms/DTDL) om een [digitale twee](#digital-twin) mogelijkheden te beschrijven en het IoT-platform en IOT-oplossingen in te scha kelen om de semantiek van de entiteit te benutten.

## <a name="digital-twin-route"></a>Digitale dubbele route

Een route die is ingesteld in een [IOT-hub](#azure-iot-hub) voor het leveren van [digitale dubbele wijzigings gebeurtenissen](#digital-twin-change-events) aan en eind punt, zoals een service bus wachtrij.

## <a name="interface"></a>Interface

Een interface beschrijft gerelateerde mogelijkheden die worden geïmplementeerd door een [IOT-Plug en Play apparaat](#iot-plug-and-play-device) of [digitale dubbele](#digital-twin). U kunt interfaces opnieuw gebruiken in verschillende [apparaatfuncties](#device-capability-model).

## <a name="iot-hub-query-language"></a>Query taal IoT Hub

De IoT Hub query taal wordt gebruikt voor meerdere doel einden. U kunt bijvoorbeeld de taal gebruiken om te zoeken naar apparaten die zijn [geregistreerd](#device-registration) bij uw IOT-hub of om het [digitale dubbele routerings](#digital-twin-route) gedrag te verfijnen.

## <a name="iot-plug-and-play-device"></a>IoT Plug en Play-apparaat

Een IoT-Plug en Play apparaat is doorgaans een klein, zelfstandig computer apparaat waarmee gegevens worden verzameld of andere apparaten worden beheerd, en waarmee software of firmware wordt uitgevoerd waarmee een [mogelijkheidsprofiel](#device-capability-model)wordt geïmplementeerd.  Een IoT-Plug en Play apparaat kan bijvoorbeeld een milieubewakings apparaat zijn of een controller voor een irrigatie systeem met een slimme land bouw. U kunt een in de Cloud gehoste IoT-oplossing schrijven naar de opdracht, het besturings element en de gegevens van IoT Plug en Play-apparaten ontvangen. Het [Azure Certified for IOT-apparaat Catalog](#certified-for-iot-device-catalog) bevat een lijst met beschik bare IoT-Plug en Play apparaten. Elk IoT Plug en Play-apparaat in de catalogus is gevalideerd en heeft een [functionaliteits model voor apparaten](#device-capability-model).

## <a name="microsoft-partner-center"></a>Micro soft partner centrum

[Micro soft Partner Center](https://docs.microsoft.com/partner-center/) is de plek waar uw organisatie zijn end-to-end-relatie beheert met micro soft. U hebt een micro soft Partner Center-account nodig voordat u uw [IoT Plug en Play-apparaat](#iot-plug-and-play-device) kunt certificeren in de [Azure Certified voor IOT-Portal](#azure-certified-for-iot-portal).

## <a name="model-discovery"></a>Modeldetectie

Wanneer een [iot Plug en Play-apparaat](#iot-plug-and-play-device) verbinding maakt met een IOT-oplossing, kan de oplossing de mogelijkheden van het apparaat detecteren door het [functionaliteits model](#device-capability-model)van het apparaat te zoeken. Een apparaat kan zijn functionaliteits model naar de oplossing verzenden of de oplossing kan een mogelijkheidsprofiel in een [model opslagplaats](#model-repository)vinden.

## <a name="model-repository"></a>Model opslagplaats

Een model opslagplaats slaat [modellen](#device-capability-model) en [interfaces](#interface)voor het apparaat op. Er is één [opslag plaats voor open bare modellen](#public-model-repository). Organisaties kunnen hun eigen opslag plaatsen voor organisatie modellen maken.

## <a name="model-repository-rest-api"></a>REST API van model opslagplaats

Een API voor het beheren en communiceren met model opslagplaatsen. U kunt bijvoorbeeld de API gebruiken om [hulp modellen voor apparaten](#device-capability-model) toe te voegen en te zoeken naar bekwaamheids modellen.

## <a name="properties"></a>Eigenschappen

Eigenschappen zijn gegevens velden die zijn gedefinieerd in een [Interface](#interface) die een status van een digitale dubbele waarde vertegenwoordigt. U kunt eigenschappen declareren als alleen-lezen of beschrijfbaar. Alleen-lezen eigenschappen, zoals serie nummer, worden ingesteld door code die wordt uitgevoerd op de [IOT-Plug en Play apparaat](#iot-plug-and-play-device) zelf.  Beschrijf bare eigenschappen, zoals een alarm drempel, worden doorgaans ingesteld vanuit de in de cloud gebaseerde IoT-oplossing.

## <a name="public-model-repository"></a>Open bare model opslagplaats

Er is één open bare model opslagplaats waarmee [modellen](#device-capability-model) en [interfaces](#interface) voor apparaten worden opgeslagen voor [gecertificeerde apparaten](#device-certification). De open bare model opslagplaats bevat ook [algemene interface](#common-interface) definities.

## <a name="registration-id"></a>Registratie-ID

Een registratie-ID is een unieke aanduiding van een apparaat in de [Device Provisioning Service](#device-provisioning-service). Deze ID is niet hetzelfde als de apparaat-ID die een unieke id is voor een apparaat in een [IOT-hub](#azure-iot-hub).

## <a name="scope-id"></a>Bereik-ID

Het bereik van de scope-ID is een unieke aanduiding van een [Device Provisioning Service](#device-provisioning-service) -exemplaar.

## <a name="shared-access-signature"></a>Shared Access Signature

Shared Access Signatures zijn een verificatie methode op basis van SHA-256 Secure hashes of Uri's. Verificatie van de hand tekening van gedeelde toegang bestaat uit twee onderdelen: een beleid voor gedeelde toegang en een hand tekening voor gedeelde toegang (ook wel een token genoemd). Een [IOT-Plug en Play apparaat](#iot-plug-and-play-device) gebruikt een Shared Access Signature om te verifiëren bij een [IOT-hub](#azure-iot-hub).

## <a name="solution-developer"></a>Oplossings ontwikkelaar

Een oplossings ontwikkelaar maakt de back-end van de oplossing. Een oplossings ontwikkelaar werkt meestal met Azure-resources, zoals [IOT hub](#azure-iot-hub) en [model opslagplaatsen](#model-repository), of werkt met [IOT Central](#azure-iot-central).

## <a name="telemetry"></a>Telemetrie

Telemetrie-velden die in een [Interface](#interface) zijn gedefinieerd, vertegenwoordigen metingen. Deze metingen zijn doorgaans waarden zoals sensor aflezingen die door de [IoT Plug en Play-apparaat](#iot-plug-and-play-device) als een gegevens stroom worden verzonden.

## <a name="visual-studio-code"></a>Visual Studio code

Visual Studio code is een moderne code-editor die beschikbaar is voor meerdere platforms. Uitbrei dingen, zoals die in het [Azure IOT tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) Pack, bieden u de mogelijkheid om de editor aan te passen ter ondersteuning van een breed scala aan ontwikkelings scenario's.
