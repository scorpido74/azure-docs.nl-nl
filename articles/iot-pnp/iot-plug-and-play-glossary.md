---
title: Verklarende woorden lijst-preview-versie van IoT Plug en Play | Microsoft Docs
description: 'Concepten: een woorden lijst met algemene termen met betrekking tot IoT Plug en Play preview.'
author: dominicbetts
ms.author: dobett
ms.date: 07/22/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: d8efec0517cd0b4bdcba643a0936b474593d58c4
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/31/2020
ms.locfileid: "87475461"
---
# <a name="glossary-of-terms-for-iot-plug-and-play-preview"></a>Verklarende woorden lijst voor de preview-versie van IoT Plug en Play

Definities van algemene termen die worden gebruikt in de IoT Plug en Play-artikelen.

## <a name="azure-iot-explorer-tool"></a>Hulp programma Azure IoT Explorer

Azure IoT Explorer is een grafisch hulp programma dat u kunt gebruiken om te communiceren met uw [IoT Plug en Play-apparaten](#iot-plug-and-play-device)en deze te testen. Nadat u het hulp programma hebt geïnstalleerd op uw lokale computer, kunt u het gebruiken voor het volgende:

- Bekijk de apparaten die zijn verbonden met uw [IOT-hub](#azure-iot-hub).
- Verbinding maken met een IoT Plug en Play-apparaat.
- De [onderdelen](#component)van het apparaat weer geven.
- De [telemetrie](#telemetry) weer geven die het apparaat verzendt.
- Werken met apparaateigenschappen [.](#properties)
- Roep de [opdrachten](#commands)van het apparaat aan.

## <a name="azure-iot-hub"></a>Azure IoT Hub

IoT Hub is een beheerde service die wordt gehost in de cloud en die fungeert als een centrale berichtenhub voor bidirectionele communicatie tussen uw IoT-toepassing en de apparaten die hiermee worden beheerd. [Iot Plug en Play-apparaten](#iot-plug-and-play-device) kunnen verbinding maken met een IOT-hub. Een IoT-oplossing maakt gebruik van een IoT-hub om het volgende in te scha kelen:

- Apparaten voor het verzenden van telemetrie naar een Cloud oplossing.
- Een Cloud oplossing voor het beheren van verbonden apparaten.

## <a name="azure-iot-device-sdk"></a>Azure IoT Device SDK

Er zijn apparaat-Sdk's voor meerdere talen die u kunt gebruiken voor het bouwen van IoT Plug en Play Device-client toepassingen.

## <a name="commands"></a>Opdracht

Opdrachten die in een [Interface](#interface) zijn gedefinieerd, vertegenwoordigen methoden die kunnen worden uitgevoerd op het [digitale dubbele](#digital-twin). Bijvoorbeeld een opdracht om een apparaat opnieuw op te starten.

## <a name="component"></a>Onderdeel

Met onderdelen kunt u een model [Interface](#interface) bouwen als een assembly van andere interfaces. Een [apparaatprofiel](#device-model) kan meerdere interfaces als onderdelen combi neren. Zo kan een model een switch-onderdeel en een thermo staat bevatten. Meerdere onderdelen in een model kunnen ook hetzelfde interface type gebruiken. Een model kan bijvoorbeeld twee Thermo staat-onderdelen bevatten.

## <a name="connection-string"></a>Verbindingsreeks

Een connection string de gegevens die nodig zijn om verbinding te maken met een eind punt, worden ingekapseld. Een connection string bevat normaal gesp roken het adres van het eind punt en de beveiligings gegevens, maar connection string indelingen variëren in verschillende services. Er zijn twee soorten connection string gekoppeld aan de IoT Hub-service:

- Met verbindings reeksen voor apparaten kunnen [iot Plug en Play-apparaten](#iot-plug-and-play-device) verbinding maken met de op het apparaat gerichte eind punten op een IOT-hub. Client code op een apparaat gebruikt de connection string om een beveiligde verbinding met een IoT-hub tot stand te brengen.
- IoT Hub verbindings reeksen kunnen back-end-oplossingen en hulpprogram ma's gebruiken om veilig verbinding te maken met de service gerichte eind punten op een IoT-hub. Deze oplossingen en hulpprogram ma's beheren de IoT-hub en de apparaten die ermee zijn verbonden.

## <a name="device-model"></a>Apparaatmodel

Een apparaatprofiel beschrijft een [IOT-Plug en Play apparaat](#iot-plug-and-play-device) en definieert de [onderdelen](#component) waaruit het apparaat is opgebouwd. Een eenvoudig apparaatprofiel heeft geen afzonderlijke onderdelen en bevat een definitie voor één interface op hoofd niveau. Een complexere apparaatprofiel bevat meerdere onderdelen. Een apparaat model komt doorgaans overeen met een fysiek apparaat, product of SKU. U gebruikt de [Digital Apparaatdubbels definition language versie 2](#digital-twins-definition-language) om een model voor een apparaat te definiëren.

## <a name="device-builder"></a>Opbouw functie voor apparaten

Een apparaat Builder gebruikt een [model](#device-model) en [interfaces](#interface) voor het uitvoeren van code voor uitvoering op een [IOT Plug en Play-apparaat](#iot-plug-and-play-device). Apparaat bouwers gebruiken meestal een van de [Azure IOT-apparaat-sdk's](#azure-iot-device-sdk) voor het implementeren van de apparaatclient, maar dit is niet vereist.

## <a name="device-modeling"></a>Apparaats modellen

Een [opbouw functie voor apparaten](#device-builder) maakt gebruik van de [taal Digital apparaatdubbels definition](#digital-twins-definition-language) om de mogelijkheden van een [IOT Plug en Play-apparaat](#iot-plug-and-play-device)te model leren. Een [oplossings functie voor oplossingen](#solution-builder) kan een IOT-oplossing van het model configureren.

## <a name="digital-twin"></a>Digitale dubbele

Een digitale dubbele is een model van een [IOT-Plug en Play apparaat](#iot-plug-and-play-device). Een digitale dubbele is gemodelleerd met behulp van de [taal Digital Apparaatdubbels definition](#digital-twins-definition-language). U kunt de Sdk's van het [Azure IOT-apparaat](#azure-iot-device-sdk) gebruiken om te communiceren met Digital apparaatdubbels tijdens de uitvoering. U kunt bijvoorbeeld een eigenschaps waarde instellen in een digitaal, twee op een apparaat en de SDK communiceert deze wijziging naar uw IoT-oplossing in de Cloud.

## <a name="digital-twin-change-events"></a>Digitale dubbele wijzigings gebeurtenissen

Wanneer een [IOT-Plug en Play apparaat](#iot-plug-and-play-device) is verbonden met een [IOT-hub](#azure-iot-hub), kan de hub de route ring gebruiken om meldingen van digitale dubbele wijzigingen te verzenden. Wanneer bijvoorbeeld een [eigenschaps](#properties) waarde op een apparaat wordt gewijzigd, kan IOT hub een melding verzenden naar een eind punt zoals een event hub.

## <a name="digital-twins-definition-language"></a>Digital Apparaatdubbels Definition Language

Een taal voor het beschrijven van modellen en interfaces voor [IoT Plug en Play-apparaten](#iot-plug-and-play-device). Gebruik de [Digital Apparaatdubbels definition language versie 2](https://github.com/Azure/opendigitaltwins-dtdl) om een [digitale twee](#digital-twin) mogelijkheden te beschrijven en de IOT-platform-en IOT-oplossingen in te scha kelen om de semantiek van de entiteit te benutten.

## <a name="digital-twin-route"></a>Digitale dubbele route

Een route die is ingesteld in een [IOT-hub](#azure-iot-hub) voor het leveren van [digitale dubbele wijzigings gebeurtenissen](#digital-twin-change-events) aan en eind punten, zoals een event hub.

## <a name="interface"></a>Interface

Een interface beschrijft gerelateerde mogelijkheden die worden geïmplementeerd door een [IOT-Plug en Play apparaat](#iot-plug-and-play-device) of [digitale dubbele](#digital-twin). U kunt interfaces opnieuw gebruiken in verschillende [modellen](#device-model). Wanneer een interface wordt gebruikt in een apparaatprofiel, definieert deze een [onderdeel](#component) van het apparaat.

## <a name="iot-hub-query-language"></a>Query taal IoT Hub

De IoT Hub query taal wordt gebruikt voor meerdere doel einden. U kunt bijvoorbeeld de taal gebruiken om te zoeken naar apparaten die zijn geregistreerd bij uw IoT-hub of om het [digitale dubbele routerings](#digital-twin-route) gedrag te verfijnen.

## <a name="iot-plug-and-play-device"></a>IoT Plug en Play-apparaat

Een IoT-Plug en Play apparaat is doorgaans een klein, zelfstandig computer apparaat waarmee gegevens worden verzameld of andere apparaten worden beheerd, en waarmee software of firmware wordt uitgevoerd waarmee een [model](#device-model)wordt geïmplementeerd.  Een IoT-Plug en Play apparaat kan bijvoorbeeld een milieubewakings apparaat zijn of een controller voor een irrigatie systeem met een slimme land bouw. U kunt een in de Cloud gehoste IoT-oplossing schrijven naar de opdracht, het besturings element en de gegevens van IoT Plug en Play-apparaten ontvangen.

## <a name="iot-plug-and-play-conventions"></a>Conventies voor IoT Plug en Play

IoT-Plug en Play [apparaten](#iot-plug-and-play-device) worden naar verwachting een set [conventies](concepts-convention.md) volgen wanneer ze gegevens uitwisselen met een oplossing.

## <a name="model-id"></a>Model-id

Wanneer een IoT Plug en Play-apparaat verbinding maakt met een IoT Hub wordt de **model-id** van het [DTDL](#digital-twins-definition-language) -model dat wordt geïmplementeerd, verzonden. Hierdoor kan de oplossing het model van het apparaat vinden.

## <a name="model-repository"></a>Model opslagplaats

Met een [model opslagplaats](concepts-model-repository.md) worden modellen en [interfaces](#interface)van [apparaten](#device-model) opgeslagen.

## <a name="model-repository-rest-api"></a>REST API van model opslagplaats

Een API voor het beheer en interactie met de model opslagplaats. U kunt bijvoorbeeld de API gebruiken om [apparaatprofielen](#device-model)toe te voegen en te zoeken.

## <a name="properties"></a>Eigenschappen

Eigenschappen zijn gegevens velden die zijn gedefinieerd in een [Interface](#interface) die een status van een digitale dubbele waarde vertegenwoordigt. U kunt eigenschappen declareren als alleen-lezen of beschrijfbaar. Alleen-lezen eigenschappen, zoals serie nummer, worden ingesteld door code die wordt uitgevoerd op de [IOT-Plug en Play apparaat](#iot-plug-and-play-device) zelf.  Beschrijf bare eigenschappen, zoals een alarm drempel, worden doorgaans ingesteld vanuit de in de cloud gebaseerde IoT-oplossing.

## <a name="shared-access-signature"></a>Shared Access Signature

Shared Access Signatures zijn een verificatie methode op basis van SHA-256 Secure hashes of Uri's. Verificatie van de hand tekening van gedeelde toegang bestaat uit twee onderdelen: een beleid voor gedeelde toegang en een hand tekening voor gedeelde toegang (ook wel een token genoemd). Een [IOT-Plug en Play apparaat](#iot-plug-and-play-device) gebruikt een Shared Access Signature om te verifiëren bij een [IOT-hub](#azure-iot-hub).

## <a name="solution-builder"></a>Opbouw functie voor oplossingen

Een oplossings functie voor oplossingen maakt de back-end van de oplossing. Een oplossings opbouw werkt meestal met Azure-resources, zoals [IOT hub](#azure-iot-hub) en [model opslagplaatsen](#model-repository).

## <a name="telemetry"></a>Telemetrie

Telemetrie-velden die in een [Interface](#interface) zijn gedefinieerd, vertegenwoordigen metingen. Deze metingen zijn doorgaans waarden zoals sensor aflezingen die door de [IoT Plug en Play-apparaat](#iot-plug-and-play-device) als een gegevens stroom worden verzonden.
