---
title: Richt lijnen voor privacy voor spraak assistenten in Windows
titleSuffix: Azure Cognitive Services
description: De instructies om spraak activering standaard in te scha kelen voor een spraak agent.
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: 436367ede4f4be323b5334a201b1c9fb8f7f28e8
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/09/2020
ms.locfileid: "82997511"
---
# <a name="privacy-guidelines-for-voice-assistants-on-windows"></a>Richt lijnen voor privacy voor spraak assistenten in Windows

Het is belang rijk dat gebruikers duidelijke informatie ontvangen over hoe hun Voice-gegevens worden verzameld en gebruikt, en belang rijk zijn dat ze controle hebben over als en hoe deze verzameling gebeurt. Deze kern facetten van privacy- *openbaar making* en *toestemming* --zijn met name belang rijk voor spraak-assistenten die zijn geïntegreerd in Windows, gezien de altijd Luister aard van het gebruik.

Ontwikkel aars die spraak assistenten op Windows maken, moeten duidelijke gebruikers interface-elementen in hun toepassingen bevatten die de luister mogelijkheden van de assistent weer spie gelen.

> [!NOTE]
> Het is niet mogelijk om de juiste informatie te verstrekken en toestemming te geven voor een assistent-toepassing, met inbegrip van na het bijwerken van de toepassing, waardoor de assistent niet meer beschikbaar is voor spraak activering totdat de problemen met de privacy zijn opgelost. 

## <a name="minimum-requirements-for-feature-inclusion"></a>Minimale vereisten voor het opnemen van functies

Windows-gebruikers kunnen de beschik baarheid van hun assistent-toepassingen **`Settings > Privacy > Voice activation`** in bekijken en beheren.

 > [!div class="mx-imgBorder"]
 > [![privacy-app-vermelding](media/voice-assistants/windows_voice_assistant/privacy-app-listing.png "Een privacyinstelling voor een Windows Voice-activerings vermelding voor een assistent-toepassing")](media/voice-assistants/windows_voice_assistant/privacy-app-listing.png#lightbox)

Om in aanmerking te komen voor opname in deze lijst, moet een toepassing het volgende doen:

1. Duidelijk zijn dat de gebruikers worden geluisterd naar een tref woord, zelfs wanneer de toepassing niet wordt uitgevoerd, en wat het tref woord is
1. Geef een beschrijving op van de manier waarop de spraak gegevens van een gebruiker worden gebruikt, met inbegrip van een koppeling naar of een verwijzing naar het relevante privacybeleid
1. Informeer gebruikers dat naast elk van de instellingen van de app, gebruikers hun privacy-keuzes kunnen weer geven en wijzigen **`Settings > Privacy > Voice activation`** in, eventueel ook een protocol koppeling naar `ms-settings:privacy-voiceactivation` voor directe toegang

Na het voldoen aan deze vereisten en het verkrijgen van goed keuring van micro soft, wordt een assistent-toepassing weer gegeven in de lijst `Windows.ApplicationModel.ConversationalAgent` met apps voor spraak activering zodra deze is geregistreerd bij de api's en kunnen gebruikers toestemming geven voor de toepassing voor trefwoord activering. Standaard zijn `Off` beide instellingen vereist en de gebruiker moet de instellingen pagina hand matig bezoeken om in te scha kelen.

> [!NOTE]
> In alle gevallen is voor spraak activering de machtiging microfoon vereist. Als een assistent-toepassing geen toegang tot de microfoon heeft, komt deze niet in aanmerking voor spraak activering en wordt deze weer gegeven in de privacy-instellingen voor spraak activering met een uitgeschakelde status.

## <a name="additional-requirements-for-inclusion-in-microphone-consent"></a>Aanvullende vereisten voor opname in de microfoon toestemming

Assistent-auteurs die het voor hun gebruikers gemakkelijker en soepeler willen maken om zich aan te melden voor spraak activering, kunnen dit doen door een paar aanvullende vereisten aan bovenstaande voor waarden te voldoen. Na het voldoen aan deze, is de standaard instelling voor de activering van een apparaat-app in `On` één keer (en slechts eenmaal) de toegang tot de toepassing wordt verleend aan de app. Hiermee wordt de nood zaak van een extra reis naar instellingen verwijderd voordat een assistent wordt geactiveerd.

De aanvullende vereisten zijn dat een assistent-toepassing het volgende moet doen:

1. **Voordat** u wordt gevraagd om de toestemming van de microfoon (bijvoorbeeld `AppCapability.RequestAccessAsync` via de API), geeft u een duidelijke indicatie van de gebruiker dat de assistent-toepassing naar de stem van de gebruiker luistert voor een tref woord, zelfs wanneer de toepassing niet wordt uitgevoerd en de toestemming van de gebruiker is
2. Alle relevante informatie over het gebruik van gegevens en privacybeleid toevoegen **vóór** het aanvragen van de toegang tot `Windows.ApplicationModel.ConversationalAgent` de microfoon of het gebruik van de api's
3. Vermijd een wille keurige instructie of een mogelijke formule (bijvoorbeeld ' Klik op Ja bij de volgende prompt ') in het proces voor het vastleggen van audio-opnamen en het aanvragen van toestemming

Zodra aan deze vereisten wordt voldaan, wordt een in aanmerking komende assistent-toepassing weer gegeven in de lijst met toepassingen die `enabled` in aanmerking komen voor spraak activering in een status zodra de toegang tot de microfoon is verleend.

> [!NOTE]
> Spraak activering boven slot komt niet in aanmerking voor automatische activering met toegang tot de microfoon en er wordt nog steeds een gebruiker verplicht om toegang te krijgen tot de privacypagina voor spraak activering voor een assistent.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over best practices voor spraak assistenten in Windows](windows-voice-assistants-best-practices.md)