---
title: Azure Speaker Recognition-service
titleSuffix: Azure Cognitive Services
description: Azure Cognitive Services Speaker Recognition biedt algoritmen die luid sprekers verifiëren en identificeren aan de hand van hun unieke spraak kenmerken. Speaker Recognition wordt gebruikt voor het beantwoorden van de vraag ' wie is praten? '.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/27/2020
ms.author: trbye
ms.openlocfilehash: 2d4ce6f274efbd4d8afe2ac48856b0fc312f0a09
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/01/2020
ms.locfileid: "84261784"
---
# <a name="what-is-the-azure-speaker-recognition-service"></a>Wat is de Azure Speaker Recognition-service?

De Speaker Recognition-service biedt algoritmen die luid sprekers verifiëren en identificeren aan de hand van hun unieke spraak kenmerken. Speaker Recognition wordt gebruikt voor het beantwoorden van de vraag ' wie is praten? '. U geeft audio training gegevens voor één spreker op, waarmee een inschrijvings profiel wordt gemaakt op basis van de unieke kenmerken van de stem van de spreker. U kunt vervolgens Audio-Voice-voor beelden op dit profiel controleren om te controleren of de spreker dezelfde persoon is (verificatie van de spreker), of audio spraak voorbeelden op een *groep* Inge schreven luidspreker profielen controleren om te zien of deze overeenkomen met een profiel in de groep (speaker-id). Met de Diarization van de [spreker](batch-transcription.md#speaker-separation-diarization) worden daarentegen segmenten van audio door de spreker gegroepeerd in een batch-bewerking.

## <a name="speaker-verification"></a>Sprekercontrole

Speaker Verification stroomlijnt het proces van het controleren van de identiteit van een Inge schreven spreker met wachtwoordzins of vrije-vorm spraak invoer. De IT-afdeling kan worden gebruikt om personen te verifiëren voor veilige, frictieloze klant afspraken in een breed scala aan oplossingen, van de verificatie van de klant identiteit in Call Centers om toegang te krijgen tot een beperktere faciliteit.

### <a name="how-does-speaker-verification-work"></a>Hoe werkt Speaker Verification?

![Hoe werkt de luidspreker verificatie](media/speaker-recognition/speaker-rec.png)

De luidspreker verificatie kan tekst afhankelijke of tekst onafhankelijk zijn. **Tekst afhankelijke** verificatie betekent dat de sprekers dezelfde wachtwoordzin moeten kiezen voor gebruik tijdens de inschrijvings-en verificatie fases. **Tekst onafhankelijke** verificatie betekent dat sprekers in de alledaagse taal in de registratie-en verificatie zinnen kunnen spreken.

Voor verificatie met een **tekst afhankelijke** wordt de stem van de spreker Inge schreven door een wachtwoordzin te zeggen uit een reeks vooraf gedefinieerde woord groepen. Spraak functies worden geëxtraheerd uit de audio-opname om een unieke spraak handtekening te vormen, terwijl de gekozen wachtwoordzin ook wordt herkend. De spraak handtekening en de wachtwoordzin worden samen gebruikt om de spreker te controleren. 

**Tekst onafhankelijke** verificatie heeft geen beperkingen ten aanzien van wat de spreker tijdens de registratie of in het audio voorbeeld moet worden geverifieerd, omdat hiermee alleen spraak functies worden uitgepakt voor het vergelijken van de score. 

De Api's zijn niet bedoeld om te bepalen of de audio afkomstig is van een live persoon of een imitatie/opname van een Inge schreven spreker. 

## <a name="speaker-identification"></a>Sprekeridentificatie

Speaker Identification wordt gebruikt om de identiteit van een onbekende spreker binnen een groep Inge schreven luid sprekers te bepalen. Met Speaker Identification kunt u een spraak op de afzonderlijke sprekers maken, en de waarde van scenario's met meerdere sprekers ontgrendelen, zoals:

* Ondersteunings oplossingen voor externe productiviteit van de vergadering 
* Personalisatie van apparaten op meerdere gebruikers bouwen

### <a name="how-does-speaker-identification-work"></a>Hoe werkt Speaker Identification?

De inschrijving voor Speaker-identificatie is **onafhankelijk van tekst**, wat betekent dat er geen beperkingen zijn voor wat de spreker in de audio vertelt. Net als bij Speaker Verification, in de inschrijvings fase wordt de stem van de spreker vastgelegd en Voice-functies worden uitgepakt om een unieke spraak handtekening te vormen. In de id-fase wordt het voor beeld van de invoer in vergelijking met een opgegeven lijst met geregistreerde stemmen (Maxi maal 50 in elke aanvraag).

## <a name="data-security-and-privacy"></a>Gegevens beveiliging en privacy

Inschrijvings gegevens voor de spreker worden opgeslagen in een beveiligd systeem, met inbegrip van de spraak audio voor inschrijving en de functies van de spraak handtekening. De spraak audio voor inschrijving wordt alleen gebruikt wanneer het algoritme wordt geüpgraded en de functies moeten opnieuw worden geëxtraheerd. De service behoudt de spraak opname of de uitgepakte spraak functies die tijdens de herkennings fase naar de service worden verzonden. 

U bepaalt hoe lang de gegevens bewaard moeten blijven. U kunt inschrijvings gegevens voor afzonderlijke sprekers maken, bijwerken en verwijderen via API-aanroepen. Wanneer het abonnement wordt verwijderd, worden alle gegevens over de registratie van de sprekernotities die zijn gekoppeld aan het abonnement, ook verwijderd. 

Net als bij alle Cognitive Services resources moeten ontwikkel aars die gebruikmaken van de Speaker Recognition-service op de hoogte zijn van het beleid van micro soft op klant gegevens. Zorg ervoor dat u de juiste machtigingen hebt ontvangen van de gebruikers voor Speaker Recognition. Zie de [pagina Cognitive Services](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/)   in het micro soft vertrouwens centrum voor meer informatie. 

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> * Raadpleeg de [video zelf studie](https://azure.microsoft.com/resources/videos/speaker-recognition-text-independent-verification-developer-tutorial/) voor tekst onafhankelijke luidspreker verificatie.
