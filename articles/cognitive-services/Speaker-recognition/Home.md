---
title: Wat is de Sprekerherkenning-API?
titleSuffix: Azure Cognitive Services
description: Luidspreker verificatie en luidspreker identificatie met de Sprekerherkenning-API in Cognitive Services.
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
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "73464913"
---
# <a name="speaker-recognition-api---preview"></a>Sprekerherkenning-API-preview

Speaker Recognition Api's zijn Cloud-Api's die de geavanceerde AI-algoritmen bieden voor de verificatie van de spreker en de identificatie van de spreker. Speaker Recognition is onderverdeeld in twee categorieën: luidspreker verificatie en identificatie van de spreker.

## <a name="speaker-verification"></a>Sprekercontrole

Voice heeft unieke kenmerken die kunnen worden gekoppeld aan een individu.  Toepassingen kunnen Voice gebruiken als een extra factor voor verificatie, in scenario's zoals call centers en webservices.

Speaker Verification-Api's fungeren als een intelligent hulp programma om gebruikers te helpen bij het controleren van de spraak-en spraak wachtwoordzin.

### <a name="enrollment"></a>Inschrijving

Inschrijving voor luidspreker verificatie is tekst afhankelijk, wat inhoudt dat de luid sprekers een specifieke wachtwoordzin moeten kiezen die tijdens de inschrijvings-en verificatie fase moet worden gebruikt.

In de inschrijvings fase van de spreker wordt de stem van de spreker vastgelegd met een specifieke woord groep. Spraak functies worden geëxtraheerd om een unieke spraak handtekening te vormen terwijl de gekozen woord groep wordt herkend. De gegevens voor de registratie van de spreker worden samen gebruikt om de spreker te controleren. De gegevens voor de registratie van de speaker worden opgeslagen in een beveiligd systeem. De klant bepaalt hoelang deze moet worden bewaard. Klanten kunnen inschrijvings gegevens voor individuele sprekers maken, bijwerken en verwijderen via API-aanroepen.  Wanneer het abonnement wordt verwijderd, worden alle gegevens over de registratie van de sprekernotities die zijn gekoppeld aan het abonnement, ook verwijderd.

Klanten moeten ervoor zorgen dat ze de juiste machtigingen hebben ontvangen van de gebruikers voor een luidspreker verificatie.

### <a name="verification"></a>Verificatie

In de verificatie fase moet de klant de luidspreker verificatie-API aanroepen met de ID die aan de persoon is gekoppeld om te worden geverifieerd.  De service extraheert spraak functies en de wachtwoordzin van de invoer spraak opname. Vervolgens worden de functies vergeleken met de corresponderende elementen van de gegevens van de speaker registratie voor de spreker die de klant wil controleren en vindt deze een overeenkomst.  Het antwoord retourneert ' Accept ' of ' afwijzen ' met verschillende betrouwbaarheids niveaus.  De klant bepaalt vervolgens hoe de resultaten moeten worden gebruikt om te bepalen of deze persoon de Inge schreven spreker is.

Het betrouwbaarheids niveau voor drempel waarden moet worden ingesteld op basis van het scenario en andere verificatie factoren die worden gebruikt. U wordt aangeraden met het betrouwbaarheids niveau te experimenteren en de juiste instelling voor elke toepassing te overwegen. De Api's zijn niet bedoeld om te bepalen of de audio afkomstig is van een live persoon of een imitatie of een registratie van een Inge schreven spreker.

De service behoudt de spraak opname of de uitgepakte spraak functies die tijdens de verificatie fase naar de service worden verzonden.

Raadpleeg de API [Spreker - Verificatie](https://westus.dev.cognitive.microsoft.com/docs/services/563309b6778daf02acc0a508/operations/563309b7778daf06340c9652) voor meer informatie over sprekercontrole.

## <a name="speaker-identification"></a>Sprekeridentificatie

Toepassingen kunnen spraak gebruiken om "wie wordt gepraat" te identificeren op basis van een groep Inge schreven luid sprekers. Speaker Identification-Api's kunnen worden gebruikt in scenario's als productiviteit voor vergaderingen, personalisatie en Call Center transcriptie.

### <a name="enrollment"></a>Inschrijving

Registratie voor sprekeridentificatie is tekstonafhankelijk, wat betekent dat er geen beperkingen zijn voor hetgeen de spreker in de audio zegt. Er is geen wachtwoordzin vereist.

In de inschrijvings fase wordt de stem van de spreker vastgelegd en worden de spraak functies geëxtraheerd om een unieke spraak handtekening te vormen. De spraak geluiden en-functies die worden geëxtraheerd, worden opgeslagen in een beveiligd systeem. De klant bepaalt hoelang deze wordt bewaard. Klanten kunnen deze gegevens over de registratie van de spreker voor individuele sprekers maken, bijwerken en verwijderen via API-aanroepen. Wanneer het abonnement wordt verwijderd, worden alle gegevens over de registratie van de sprekernotities die zijn gekoppeld aan het abonnement, ook verwijderd.

Klanten moeten ervoor zorgen dat ze de juiste machtigingen hebben ontvangen van de gebruikers voor het identificeren van de speaker.

### <a name="identification"></a>Identificatie

In de identificatie fase haalt de luidspreker Identification-Service spraak functies uit de invoer spraak opname. Vervolgens worden de functies vergeleken met de inschrijvings gegevens van de opgegeven lijst met luid sprekers. Als er een overeenkomst wordt gevonden met een Inge schreven spreker, retourneert het antwoord de ID van de spreker met een betrouwbaarheids niveau.  Anders retourneert het antwoord "afwijzen" wanneer geen enkele spreker overeenkomt met een Inge schreven spreker.

Het betrouwbaarheids niveau van de drempel waarde moet worden ingesteld op basis van het scenario. U wordt aangeraden met het betrouwbaarheids niveau te experimenteren en de juiste instelling voor elke toepassing te overwegen. De Api's zijn niet bedoeld om te bepalen of de audio afkomstig is van een live persoon of een imitatie of een registratie van een Inge schreven spreker.

De service behoudt de spraak opname of de uitgepakte spraak functies die naar de service worden verzonden voor de identificatie fase.

Raadpleeg de API  [Spreker - Identificatie](https://westus.dev.cognitive.microsoft.com/docs/services/563309b6778daf02acc0a508/operations/5645c068e597ed22ec38f42e) voor meer informatie over sprekeridentificatie.
