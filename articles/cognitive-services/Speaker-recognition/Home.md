---
title: Wat is de Sprekerherkenning-API?
titleSuffix: Azure Cognitive Services
description: Luidsprekerverificatie en luidsprekeridentificatie met de Speaker Recognition API in Cognitive Services.
services: cognitive-services
author: dwlin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speaker-recognition
ms.topic: overview
ms.date: 10/01/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: f354793e3779f4d9f7be9bae8a21545a15ed1c4c
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73464913"
---
# <a name="speaker-recognition-api---preview"></a>Api voor luidsprekerherkenning - Voorbeeld

Api's voor luidsprekerherkenning zijn cloudgebaseerde API's die de geavanceerde AI-algoritmen bieden voor luidsprekerverificatie en luidsprekeridentificatie. Luidsprekerherkenning is onderverdeeld in twee categorieën: luidsprekerverificatie en luidsprekeridentificatie.

## <a name="speaker-verification"></a>Sprekercontrole

Voice heeft unieke kenmerken die kunnen worden geassocieerd met een individu.  Toepassingen kunnen spraak gebruiken als een extra factor voor verificatie, in scenario's zoals callcenters en webservices.

Api's voor verificatie van sprekers dienen als een intelligent hulpmiddel om gebruikers te verifiëren met behulp van zowel hun stem- als spraakwachtwoordzinnen.

### <a name="enrollment"></a>Registratie

Inschrijving voor luidsprekerverificatie is tekstafhankelijk, wat betekent dat sprekers een specifieke wachtwoordzin moeten kiezen om te gebruiken tijdens zowel inschrijvings- als verificatiefasen.

In de inschrijvingsfase van de spreker wordt de stem van de spreker opgenomen met een specifieke zin. Stemfuncties worden geëxtraheerd om een unieke stemhandtekening te vormen terwijl de gekozen zin wordt herkend. Samen zouden deze informatie over de inschrijving van de luidspreker worden gebruikt om de luidspreker te verifiëren. De informatie over de inschrijving van de luidspreker wordt opgeslagen in een beveiligd systeem. De klant bepaalt hoe lang het moet worden bewaard. Klanten kunnen inschrijvingsgegevens voor afzonderlijke luidsprekers maken, bijwerken en verwijderen via API-aanroepen.  Wanneer het abonnement wordt verwijderd, worden ook alle informatieover inschrijvingsgegevens van de spreker die aan het abonnement zijn gekoppeld, verwijderd.

Klanten moeten ervoor zorgen dat ze de juiste machtigingen van de gebruikers hebben ontvangen voor verificatie van de spreker.

### <a name="verification"></a>Verificatie

In de verificatiefase moet de klant de API voor luidsprekerverificatie aanroepen met de id die is gekoppeld aan de persoon die moet worden geverifieerd.  De service haalt spraakfuncties en de wachtwoordzin uit de opname van invoerspraak. Vervolgens vergelijkt de functies met de bijbehorende elementen van de luidsprekerinschrijvingsgegevens voor de spreker die de klant probeert te verifiëren en bepaalt elke overeenkomst.  Het antwoord retourneert "accepteren" of "weigeren" met verschillende vertrouwensniveaus.  De klant bepaalt vervolgens hoe de resultaten moeten worden gebruikt om te bepalen of deze persoon de ingeschreven spreker is.

Het betrouwbaarheidsniveau van de drempel moet worden vastgesteld op basis van het scenario en andere verificatiefactoren die worden gebruikt. We raden u aan te experimenteren met het betrouwbaarheidsniveau en de juiste instelling voor elke toepassing te overwegen. De API's zijn niet bedoeld om te bepalen of de audio afkomstig is van een live persoon of een imitatie of een opname van een ingeschreven luidspreker.

De service behoudt niet de spraakopname of de geëxtraheerde spraakfuncties die tijdens de verificatiefase naar de service worden verzonden.

Raadpleeg de API [Spreker - Verificatie](https://westus.dev.cognitive.microsoft.com/docs/services/563309b6778daf02acc0a508/operations/563309b7778daf06340c9652) voor meer informatie over sprekercontrole.

## <a name="speaker-identification"></a>Sprekeridentificatie

Toepassingen kunnen spraak gebruiken om te identificeren "wie spreekt" gezien een groep ingeschreven sprekers. Api's voor luidsprekeridentificatie kunnen worden gebruikt in scenario's zoals productiviteit van vergaderingen, personalisatie en transcriptie van het callcenter.

### <a name="enrollment"></a>Registratie

Registratie voor sprekeridentificatie is tekstonafhankelijk, wat betekent dat er geen beperkingen zijn voor hetgeen de spreker in de audio zegt. Er is geen wachtwoordzin vereist.

In de inschrijvingsfase wordt de stem van de spreker opgenomen en worden stemfuncties geëxtraheerd om een unieke stemhandtekening te vormen. De spraakaudio en de uitgebreide functies worden opgeslagen in een beveiligd systeem. De klant bepaalt hoe lang het wordt bewaard. Klanten kunnen deze informatie voor de inschrijving van luidsprekers voor afzonderlijke luidsprekers maken, bijwerken en verwijderen via API-aanroepen. Wanneer het abonnement wordt verwijderd, worden ook alle informatieover inschrijvingsgegevens van de spreker die aan het abonnement zijn gekoppeld, verwijderd.

Klanten moeten ervoor zorgen dat ze de juiste machtigingen van de gebruikers hebben ontvangen voor luidsprekeridentificatie.

### <a name="identification"></a>Identificatie

In de identificatiefase haalt de luidsprekeridentificatieservice spraakfuncties uit de invoerspraakopname. Vervolgens worden de functies vergeleken met de inschrijvingsgegevens van de opgegeven lijst met luidsprekers. Wanneer een overeenkomst wordt gevonden met een ingeschreven luidspreker, wordt de id van de luidspreker met een betrouwbaarheidsniveau geretourneerd.  Anders retourneert het antwoord "weigeren" wanneer geen enkele spreker overeenkomt met een ingeschreven spreker.

Het basisbetrouwbaarheidsniveau moet worden vastgesteld op basis van het scenario. We raden u aan te experimenteren met het betrouwbaarheidsniveau en de juiste instelling voor elke toepassing te overwegen. De API's zijn niet bedoeld om te bepalen of de audio afkomstig is van een live persoon of een imitatie of een opname van een ingeschreven luidspreker.

De service behoudt niet de spraakopname of de geëxtraheerde spraakfuncties die naar de service worden verzonden voor de identificatiefase.

Raadpleeg de API  [Spreker - Identificatie](https://westus.dev.cognitive.microsoft.com/docs/services/563309b6778daf02acc0a508/operations/5645c068e597ed22ec38f42e) voor meer informatie over sprekeridentificatie.
