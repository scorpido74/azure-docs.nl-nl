---
title: Spraakassistenten - Spraakservice
titleSuffix: Azure Cognitive Services
description: Een overzicht van de functies, mogelijkheden en beperkingen voor spraakassistenten met behulp van de Speech Software Development Kit (SDK).
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: travisw
ms.openlocfilehash: 9219c9a72ce9e1cfba3504b0b8e16ade77f8a5e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79369893"
---
# <a name="what-is-a-voice-assistant"></a>Wat is een spraakassistent?

Spraakassistenten die de Spraakservice gebruiken, stellen ontwikkelaars in staat om natuurlijke, mensachtige conversationele interfaces te maken voor hun toepassingen en ervaringen.

De spraakassistentservice biedt een snelle, betrouwbare interactie tussen een apparaat en een assistent-implementatie die gebruik maakt van het Direct Line Speech-kanaal van het Bot Framework of (2) de geïntegreerde aangepaste opdrachten (Preview)-service voor taakvoltooiing.

Toepassingen maken verbinding met de spraakassistentservice met de Speech Software Development Kit (SDK).

   ![Conceptueel diagram van de spraakherkenningsservicestroom van spraakassistenten](media/voice-assistants/overview.png "De stroom van de spraakassistent")

## <a name="choosing-an-assistant-solution"></a>Een assistent-oplossing kiezen

De eerste stap naar het maken van een spraakassistent is om te beslissen wat het moet doen. De Spraakservice biedt meerdere, complementaire oplossingen voor het maken van uw assistent-interacties. Of u nu de flexibiliteit en veelzijdigheid wilt die het [Direct Line Speech-kanaal](direct-line-speech.md) van het Bot Framework biedt of de eenvoud van [aangepaste opdrachten (Preview)](custom-commands.md) voor eenvoudige scenario's, als u de juiste tools selecteert, wordt u op weg.

| Als je wilt... | Overweeg dan... | Bijvoorbeeld... |
|-------------------|------------------|----------------|
|Open gesprek met robuuste vaardighedenintegratie en volledige implementatiecontrole | Het [Direct Line Speech-kanaal](direct-line-speech.md) van het Bot Framework | <ul><li>"Ik moet naar Seattle"</li><li>"Wat voor pizza kan ik bestellen?"</li></ul>
|Command and control of taakgericht gesprek met vereenvoudigde authoring en hosting | [Aangepaste opdrachten (voorbeeld)](custom-commands.md) | <ul><li>"Zet de overhead licht"</li><li>"Maak het 5 graden warmer"</ul>

We raden [Direct Line Speech](direct-line-speech.md) aan als de beste standaardkeuze als je nog niet zeker weet wat je assistent wilt hanteren. Het biedt integratie met een uitgebreide set tools en ontwerphulpmiddelen zoals de [Virtual Assistant Solution en Enterprise Template](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview) en de [QnA Maker-service](https://docs.microsoft.com/azure/cognitive-services/QnAMaker/Overview/overview) om voort te bouwen op gemeenschappelijke patronen en uw bestaande kennisbronnen te gebruiken.

[Custom Commands (Preview)](custom-commands.md) biedt een gestroomlijnde ontwerp- en hostingervaring die specifiek is afgestemd op commando- en controlescenario's voor natuurlijke taal.

   ![Vergelijking van assistent-oplossingen](media/voice-assistants/assistant-solution-comparison.png "Vergelijking van assistent-oplossingen")

## <a name="core-features"></a>Kernfuncties

Of u nu [Direct Line Speech](direct-line-speech.md) of Custom [Commands (Preview)](custom-commands.md) kiest om uw assistent-interacties te maken, u een uitgebreide set aanpassingsfuncties gebruiken om uw assistent aan te passen aan uw merk, product en persoonlijkheid.

| Categorie | Functies |
|----------|----------|
|[Aangepast trefwoord](speech-devices-sdk-create-kws.md) | Gebruikers kunnen gesprekken starten met assistenten met een aangepast zoekwoord zoals 'Hey Contoso'. Een app doet dit met een aangepaste trefwoordengine in de Speech SDK, die kan worden geconfigureerd met een aangepast zoekwoord [dat u hier genereren.](speech-devices-sdk-create-kws.md) Spraakassistenten kunnen trefwoordverificatie aan de servicezijde gebruiken om de nauwkeurigheid van de trefwoordactivering te verbeteren (ten opzichte van het apparaat alleen).
|[Toespraak tot tekst](speech-to-text.md) | Spraakassistenten zetten realtime audio om in herkende tekst met [spraak naar tekst](speech-to-text.md) van de spraakservice. Deze tekst is beschikbaar, zoals deze is getranscribeerd, voor zowel uw assistent-implementatie als uw clientapplicatie.
|[Tekst naar spraak](text-to-speech.md) | Tekstuele reacties van uw assistent worden gesynthetiseerd met [tekst-naar-spraak](text-to-speech.md) van de spraakservice. Deze synthese wordt vervolgens beschikbaar gesteld aan uw clientapplicatie als een audiostream. Microsoft biedt de mogelijkheid om uw eigen aangepaste, hoogwaardige Neurale TTS-stem te bouwen die een stem geeft aan uw merk. Neem voor meer informatie [contact met ons op.](mailto:mstts@microsoft.com)

## <a name="getting-started-with-voice-assistants"></a>Aan de slag met spraakassistenten

We bieden quickstarts die zijn ontworpen om u in minder dan 10 minuten code te laten uitvoeren. Deze tabel bevat een lijst met snel gestarte spraakassistenten, geordend op taal.

| Snelstartgids | Platform | API-verwijzing |
|------------|----------|---------------|
| C#, UWP | Windows | [Bladeren](https://aka.ms/csspeech/csharpref) |
| Java | Windows, macOS, Linux | [Bladeren](https://aka.ms/csspeech/javaref) |
| Java | Android | [Bladeren](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>Voorbeeldcode

Voorbeeldcode voor het maken van een spraakassistent is beschikbaar op GitHub. Deze voorbeelden hebben betrekking op de clienttoepassing voor het verbinden met uw assistent in verschillende populaire programmeertalen.

* [Voorbeelden van spraakassistenten (SDK)](https://aka.ms/csspeech/samples)
* [Zelfstudie: Spraakinschakelen van uw assistent met de Spraak-SDK, C #](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="tutorial"></a>Zelfstudie

Een zelfstudie over het [stem-inschakelen van uw assistent met behulp van het speech-sdk- en Direct Line-spraakkanaal](tutorial-voice-enable-your-bot-speech-sdk.md).

## <a name="customization"></a>Aanpassing

Spraakassistenten die zijn gebouwd met de spraakservice, kunnen het volledige scala aan aanpassingsopties gebruiken die beschikbaar zijn voor [spraak-naar-tekst,](speech-to-text.md) [tekst-naar-spraak](text-to-speech.md)en [aangepaste trefwoordselectie.](speech-devices-sdk-create-kws.md)

> [!NOTE]
> Aanpassingsopties verschillen per taal/landtaal (zie [Ondersteunde talen).](supported-languages.md)

## <a name="reference-docs"></a>Referentiedocumenten

* [Speech-SDK](speech-sdk-reference.md)
* [Azure Bot Service](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>Volgende stappen

* [Ontvang gratis een abonnementssleutel voor spraakservice](get-started.md)
* [Download de Spraak-SDK](speech-sdk.md)
* [Meer informatie over aangepaste opdrachten (voorbeeld)](custom-commands.md)
* [Meer informatie over direct line spraak](direct-line-speech.md)
