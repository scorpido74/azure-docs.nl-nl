---
title: IoT Plug en Play preview model-detectie implementeren | Microsoft Docs
description: Als oplossings ontwikkelaar leert u hoe u IoT Plug en Play model detectie kunt implementeren in uw oplossing.
author: ChrisGMsft
ms.author: chrisgre
ms.date: 07/17/2019
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: e4ab1d45e27762ef05ab7ec74c98ab0b0b934cbf
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69880551"
---
# <a name="implement-iot-plug-and-play-preview-model-discovery-in-an-iot-solution"></a>IoT Plug en Play preview-model detectie implementeren in een IoT-oplossing

In dit artikel wordt beschreven hoe u als oplossings ontwikkelaar IoT Plug en Play preview-model detectie kunt implementeren in een IoT-oplossing.  IoT Plug en Play model detectie is de manier waarop IoT Plug en Play apparaten hun ondersteunde bekwaamheids modellen en-interfaces identificeren en hoe een IoT-oplossing deze modellen en interfaces ophaalt.

Er zijn twee algemene categorieën van IoT-oplossingen: speciaal ontwikkelde oplossingen die werken met een bekende set van IoT Plug en Play-apparaten, en modellen gestuurde oplossingen die werken met een IoT Plug en Play-apparaat.

In dit concept artikel wordt beschreven hoe u model detectie in beide typen oplossingen implementeert.

## <a name="model-discovery"></a>Model detectie

Wanneer een IoT-Plug en Play apparaat voor het eerst verbinding maakt met uw IoT-hub, verzendt het een telemetrie-bericht over model gegevens. Dit bericht bevat de Id's van de interfaces die het apparaat implementeert. Uw oplossing werkt alleen met het apparaat als deze Id's worden omgezet en de definities voor elke interface worden opgehaald.

Hier volgen de stappen die worden uitgevoerd door een IoT-Plug en Play apparaat wanneer het gebruikmaakt van de Device Provisioning Service (DPS) om verbinding te maken met een hub:

1. Wanneer het apparaat is ingeschakeld, wordt er verbinding gemaakt met het globale eind punt voor de DPS en wordt een verificatie op basis van een van de toegestane methoden gebruikt.
1. DPS verifieert vervolgens het apparaat en zoekt de regel op waarmee wordt aangegeven aan welke IoT-hub het apparaat moet worden toegewezen. DPS registreert vervolgens het apparaat met die hub.
1. DPS retourneert een IoT Hub connection string op het apparaat.
1. Het apparaat verzendt vervolgens een bericht met de detectie van een telemetrie naar uw IoT Hub. Het bericht van de detectie-telemetrie bevat de Id's van de interfaces die het apparaat implementeert.
1. Het IoT Plug en Play-apparaat is nu klaar voor gebruik met een oplossing die gebruikmaakt van uw IoT-hub.

Als het apparaat rechtstreeks verbinding maakt met uw IoT-hub, wordt er verbinding gemaakt met behulp van een connection string dat is inge sloten in de apparaatcode. Het apparaat verzendt vervolgens een bericht met de detectie van een telemetrie naar uw IoT Hub.

Raadpleeg de [ModelInformation](concepts-common-interfaces.md) -interface voor meer informatie over het bericht over het telemetrie-model informatie.

### <a name="purpose-built-iot-solutions"></a>Doel bewuste IoT-oplossingen

Een met doel opgebouwde IoT-oplossing werkt met een bekende set IoT Plug en Play-mogelijkheden en-interfaces voor apparaten.

U hebt het mogelijkheden model en de interfaces voor de apparaten die van tevoren verbinding maken met uw oplossing. Gebruik de volgende stappen om uw oplossing voor te bereiden:

1. Sla de JSON-bestanden van de interface op in azure op een locatie waar uw oplossing ze kan lezen.
1. Schrijf logica in uw IoT-oplossing op basis van de verwachte IoT Plug en Play-mogelijkheden en-interface.
1. Abonneer u op meldingen van de IoT-hub die door uw oplossing wordt gebruikt.

Wanneer u een melding ontvangt voor een nieuwe apparaat, voert u de volgende stappen uit:

1. Lees het bericht van de Discovery-telemetrie om de Id's op te halen van het functionaliteits model en de interfaces die door het apparaat worden geïmplementeerd.
1. Vergelijk de ID van het bekwaamheids model met de Id's van de mogelijkheden modellen die u van tevoren hebt opgeslagen.
1. Nu weet u welk type apparaat is verbonden. Gebruik de logica die u eerder hebt geschreven om gebruikers in staat te stellen op de juiste manier te communiceren met het apparaat.

### <a name="model-driven-solutions"></a>Model gerichte oplossingen

Een op modellen gebaseerde IoT-oplossing kan worden gebruikt met een IoT Plug en Play-apparaat. Het bouwen van een model met IoT-oplossing is complexer, maar het voor deel is dat uw oplossing werkt met apparaten die in de toekomst worden toegevoegd.

Als u een model-gebaseerde IoT-oplossing wilt bouwen, moet u logica bouwen op basis van de IoT-Plug en Play interface primitieven: telemetrie, eigenschappen en opdrachten. De logica van uw IoT-oplossing duidt op een apparaat door meerdere telemetrie, eigenschappen en opdracht mogelijkheden te combi neren.

Uw oplossing moet ook worden geabonneerd op meldingen van de IoT-hub die wordt gebruikt.

Als uw oplossing een melding voor een nieuwe apparaat krijgt, voert u de volgende stappen uit:

1. Lees het bericht van de Discovery-telemetrie om de Id's op te halen van het functionaliteits model en de interfaces die door het apparaat worden geïmplementeerd.
1. Lees voor elke ID het volledige JSON-bestand om de mogelijkheden van het apparaat te vinden.
1. Controleer of elke interface aanwezig is in de caches die u hebt gemaakt voor het opslaan van de JSON-bestanden die eerder zijn opgehaald door uw oplossing.
1. Controleer vervolgens of er een interface met die ID aanwezig is in de globale model opslagplaats. Zie [globale model opslagplaats](howto-manage-models.md)voor meer informatie.
1. Als de interface niet aanwezig is in de globale model opslagplaats, zoekt u deze in een persoonlijke model opslagplaats die bekend is bij uw oplossing. U hebt een connection string nodig om toegang te krijgen tot een persoonlijke model opslagplaats. Zie voor meer informatie [opslag plaats voor persoonlijke modellen](howto-manage-models.md).
1. Als u niet alle interfaces in de globale model opslagplaats kunt vinden, of in een opslag plaats van een persoonlijk model, controleert u of het apparaat de interface definitie kan bieden. Een apparaat kan de standaard [ModelDefinition](concepts-common-interfaces.md) -interface implementeren voor het publiceren van informatie over het ophalen van interface bestanden met een opdracht.
1. Als u JSON-bestanden hebt gevonden voor elke interface die door het apparaat is geïmplementeerd, kunt u de mogelijkheden van het apparaat opsommen. Gebruik de logica die u eerder hebt geschreven om gebruikers in staat te stellen te communiceren met het apparaat.
1. U kunt op elk gewenst moment de Digital apparaatdubbels-API aanroepen om de ID en interface-Id's van het bekwaamheids model voor het apparaat op te halen.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd over model detectie een IoT-oplossing, kunt u meer te weten komen over het [Azure IOT-platform](overview-iot-plug-and-play.md) om gebruik te maken van andere mogelijkheden voor uw oplossing.
