---
title: IoT Plug and Play Preview-voorbeelddetectie implementeren | Microsoft Documenten
description: Lees als ontwikkelaar van oplossingen hoe u IoT Plug and Play-modeldetectie in uw oplossing implementeren.
author: ChrisGMsft
ms.author: chrisgre
ms.date: 12/26/2019
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 66da0321930ac38217a336380c9889963a433e67
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75531357"
---
# <a name="implement-iot-plug-and-play-preview-model-discovery-in-an-iot-solution"></a>IoT Plug and Play Preview-modeldetectie implementeren in een IoT-oplossing

In dit artikel wordt beschreven hoe u als ontwikkelaar van oplossingen IoT Plug and Play Preview-modeldetectie implementeren in een IoT-oplossing.  IoT Plug and Play-modeldetectie is hoe IoT Plug and Play-apparaten hun ondersteunde mogelijkheden en interfaces identificeren en hoe een IoT-oplossing deze capaciteitsmodellen en -interfaces ophaalt.

Er zijn twee brede categorieën IoT-oplossingen: speciaal gebouwde oplossingen die werken met een bekende set IoT Plug and Play-apparaten en modelgestuurde oplossingen die werken met elk IoT Plug and Play-apparaat.

In dit conceptartikel wordt beschreven hoe modeldetectie in beide soorten oplossingen kan worden geïmplementeerd.

## <a name="model-discovery"></a>Modeldetectie

Wanneer een IoT Plug and Play-apparaat voor het eerst verbinding maakt met uw IoT-hub, stuurt het een telemetriebericht voor modelinformatie. Dit bericht bevat de id's van de interfaces die het apparaat implementeert. Als uw oplossing met het apparaat kan werken, moet deze id's worden opgelost en de definities voor elke interface worden opgehaald.

Dit zijn de stappen die een IoT Plug and Play-apparaat neemt wanneer het de Device Provisioning Service (DPS) gebruikt om verbinding te maken met een hub:

1. Wanneer het apparaat is ingeschakeld, maakt het verbinding met het globale eindpunt voor DPS en verifieert het met behulp van een van de toegestane methoden.
1. DPS verifieert vervolgens het apparaat en zoekt de regel op die aangeeft aan welke IoT-hub het apparaat moet toewijzen. DPS registreert het apparaat vervolgens met die hub.
1. DPS retourneert een IoT Hub-verbindingstekenreeks naar het apparaat.
1. Het apparaat stuurt vervolgens een detectietelemetriebericht naar uw IoT-hub. Het detectietelemetriebericht bevat de id's van de interfaces die het apparaat implementeert.
1. Het IoT Plug and Play-apparaat is nu klaar om te werken met een oplossing die uw IoT-hub gebruikt.

Als het apparaat rechtstreeks verbinding maakt met uw IoT-hub, wordt het verbonden via een verbindingstekenreeks die is ingesloten in de apparaatcode. Het apparaat stuurt vervolgens een detectietelemetriebericht naar uw IoT-hub.

Zie de [Interface ModelInformatie](concepts-common-interfaces.md) voor meer informatie over het telemetriebericht voor modelinformatie.

### <a name="purpose-built-iot-solutions"></a>Speciaal gebouwde IoT-oplossingen

Een speciaal gebouwde IoT-oplossing werkt met een bekende set iot-plug-en-play-apparaatmogelijkheden en -interfaces.

U beschikt over het capaciteitsmodel en de interfaces voor de apparaten die van tevoren verbinding maken met uw oplossing. Gebruik de volgende stappen om uw oplossing voor te bereiden:

1. Sla de INTERFACE JSON-bestanden op in Azure op een locatie waar uw oplossing ze kan lezen.
1. Schrijf logica in uw IoT-oplossing op basis van de verwachte IoT Plug and Play-mogelijkheden en interface.
1. Abonneer u op meldingen van de IoT-hub die uw oplossing gebruikt.

Wanneer u een melding ontvangt voor een nieuwe apparaatverbinding, voert u de volgende stappen uit:

1. Lees het bericht detectietelemetrie om de id's van het capaciteitsmodel en de interfaces die door het apparaat zijn geïmplementeerd, op te halen.
1. Vergelijk de ID van het capaciteitsmodel met de id's van de capaciteitsmodellen die u van tevoren hebt opgeslagen.
1. Nu weet u welk type apparaat is aangesloten. Gebruik de logica die u eerder hebt geschreven om gebruikers in staat te stellen op de juiste manier met het apparaat te communiceren.

### <a name="model-driven-solutions"></a>Modelgestuurde oplossingen

Een modelgestuurde IoT-oplossing kan werken met elk IoT Plug and Play-apparaat. Het bouwen van een modelgestuurde IoT-oplossing is complexer, maar het voordeel is dat uw oplossing werkt met alle apparaten die in de toekomst worden toegevoegd.

Als u een modelgestuurde IoT-oplossing wilt bouwen, moet u logica bouwen tegen de primitieve nit-interface van IoT Plug and Play: telemetrie, eigenschappen en opdrachten. De logica van uw IoT-oplossing vertegenwoordigt een apparaat door meerdere telemetrie-, eigenschap- en opdrachtmogelijkheden te combineren.

Uw oplossing moet zich ook abonneren op meldingen van de IoT-hub die wordt gebruikt.

Wanneer uw oplossing een melding ontvangt voor een nieuwe apparaatverbinding, voert u de volgende stappen uit:

1. Lees het bericht detectietelemetrie om de id's van het capaciteitsmodel en de interfaces die door het apparaat zijn geïmplementeerd, op te halen.
1. Lees voor elke ID het volledige JSON-bestand om de mogelijkheden van het apparaat te vinden.
1. Controleer of elke interface aanwezig is in caches die u hebt gebouwd voor het opslaan van de JSON-bestanden die eerder door uw oplossing zijn opgehaald.
1. Controleer vervolgens of er een interface met die ID aanwezig is in de openbare modelopslagplaats. Zie [Openbare modelopslagplaats](howto-manage-models.md)voor meer informatie .
1. Als de interface niet aanwezig is in de openbare modelrepository, probeer het dan te zoeken in bedrijfsmodelrepositories die bekend zijn bij uw oplossing. U hebt een verbindingstekenreeks nodig om toegang te krijgen tot een bedrijfsmodelopslagplaats. Zie [Bedrijfsmodelrepository](howto-manage-models.md)voor meer informatie.
1. Als u niet alle interfaces vinden in de openbare modelopslagplaats of in een bedrijfsmodelopslagplaats, u controleren of het apparaat de interfacedefinitie kan bieden. Een apparaat kan de standaard [ModelDefinition-interface](concepts-common-interfaces.md) implementeren om informatie te publiceren over het ophalen van interfacebestanden met een opdracht.
1. Als u JSON-bestanden hebt gevonden voor elke interface die door het apparaat wordt geïmplementeerd, u de mogelijkheden van het apparaat opsommen. Gebruik de logica die u eerder hebt geschreven om gebruikers in staat te stellen met het apparaat te communiceren.
1. Op elk gewenst moment u de API voor digitale tweelingen bellen om de inhoudsmodel-ID en interface-id's voor het apparaat op te halen.

## <a name="next-steps"></a>Volgende stappen

Nu u meer te weten bent gekomen over modeldetectie van een IoT-oplossing, leest u meer over het [Azure IoT-platform](overview-iot-plug-and-play.md) om andere mogelijkheden voor uw oplossing te benutten.
