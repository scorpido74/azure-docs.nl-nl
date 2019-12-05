---
title: Spraak assistenten-spraak service
titleSuffix: Azure Cognitive Services
description: Een overzicht van de functies, mogelijkheden en beperkingen voor spraak assistenten met behulp van de speech Software Development Kit (SDK).
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: travisw
ms.openlocfilehash: d687d1d353c1734c5d98121f658003afde2eb182
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/04/2019
ms.locfileid: "74812918"
---
# <a name="about-voice-assistants"></a>Over spraak assistenten

Met spraak assistenten die gebruikmaken van de speech-service kunnen ontwikkel aars natuurlijke, menselijke-achtige gespreks interfaces maken voor hun toepassingen en ervaringen.

De Voice Assistant-service biedt snelle, betrouw bare interactie tussen een apparaat en een assistent-implementatie die gebruikmaakt van (1) het directe lijn spraak kanaal van de bot-Framework of (2) de geïntegreerde service voor het uitvoeren van aangepaste opdrachten (preview) voor taak voltooiing.

Toepassingen maken verbinding met de Voice Assistant-service met de speech Software Development Kit (SDK).

   ![Conceptueel diagram van de service stroom van de Orchestrator-indeling voor spraak assistenten](media/voice-assistants/overview.png "De stroom van de Voice Assistant")

## <a name="choosing-an-assistant-solution"></a>Een assistent-oplossing kiezen

De eerste stap bij het maken van een spraak assistent is bepalen wat het moet doen. De speech-service biedt meerdere, complementaire oplossingen voor het bouwen van uw assistent-interacties. Of u de flexibiliteit en veelzijdigheid wilt hebben dat het directe- [lijn spraak](direct-line-speech.md) kanaal van het bot-Framework of de eenvoud van [aangepaste opdrachten (preview)](custom-commands.md) voor duidelijke scenario's is, en u kunt de juiste hulpprogram ma's downloaden om aan de slag te gaan.

| Als u wilt... | Bekijk vervolgens... | Bijvoorbeeld... |
|-------------------|------------------|----------------|
|Open-end-conversatie met robuuste integratie van vaardig heden en volledige implementatie beheer | Het [directe-lijn spraak](direct-line-speech.md) kanaal van het bot-Framework | <ul><li>"Ik moet naar Seattle gaan"</li><li>"Welk soort pizza kan ik best Ellen?"</li></ul>
|Discussies over opdrachten en besturings elementen of taak gerichte uitwisseling met vereenvoudigd ontwerpen en hosten | [Aangepaste opdrachten (preview-versie)](custom-commands.md) | <ul><li>"Het overhead licht inschakelen"</li><li>"Maak IT 5 graden warmer"</ul>

U wordt aangeraden [regel spraak direct](direct-line-speech.md) als de beste standaard keuze te gebruiken als u nog niet zeker weet wat uw assistent moet verwerken. Het biedt integratie met een uitgebreide set hulpprogram ma's en ontwerp hulpmiddelen, zoals de [Virtual assistent-oplossing en ondernemings sjabloon](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview) en de [QnA Maker-service](https://docs.microsoft.com/azure/cognitive-services/QnAMaker/Overview/overview) voor het bouwen van algemene patronen en het gebruik van uw bestaande kennis bronnen.

[Aangepaste opdrachten (preview-versie)](custom-commands.md) bieden een gestroomlijnde ontwerp-en hosting-ervaring die specifiek is afgestemd op de opdracht-en controle scenario's in natuurlijke taal.

   ![Vergelijking van assistent-oplossingen](media/voice-assistants/assistant-solution-comparison.png "Vergelijking van assistent-oplossingen")

## <a name="core-features"></a>Kern functies

Ongeacht of u [direct lijn spraak](direct-line-speech.md) of [aangepaste opdrachten (preview)](custom-commands.md) kiest om uw assistent-interacties te maken, kunt u een uitgebreide set aanpassings functies gebruiken om uw assistent aan uw merk, product en persoonlijkheid aan te passen.

| Category | Functies |
|----------|----------|
|[Aangepast tref woord](speech-devices-sdk-create-kws.md) | Gebruikers kunnen gesp rekken met assistenten starten met een aangepast tref woord zoals ' Hey Contoso '. Dit is een app met een aangepaste trefwoord engine in de Speech SDK, die kan worden geconfigureerd met een aangepast tref woord [dat u hier kunt genereren](speech-devices-sdk-create-kws.md). Spraak assistenten kunnen verificatie op service zijde gebruiken om de nauw keurigheid van de activering van het tref woord te verbeteren (alleen voor het apparaat).
|[Spraak naar tekst](speech-to-text.md) | Via spraak [-naar-tekst](speech-to-text.md) van de spraak service kunt u in spraak assistenten real-time audio omzetten in herkende tekst. Deze tekst is beschikbaar, omdat deze is getranscribeerd, naar de implementatie van uw assistent en uw client toepassing.
|[Tekst-naar-spraak](text-to-speech.md) | Tekstuele reacties van uw assistent worden gesynthesizerd met behulp van [tekst naar spraak](text-to-speech.md) vanuit de spraak service. Deze synthese wordt vervolgens beschikbaar gesteld voor uw client toepassing als een audio stroom. Micro soft biedt de mogelijkheid om uw eigen aangepaste Neural TTS-stem van hoge kwaliteit op te bouwen die een stem op uw merk levert. [Neem contact met ons](mailto:mstts@microsoft.com)op voor meer informatie.

## <a name="getting-started-with-voice-assistants"></a>Aan de slag met spraak assistenten

We bieden Quick starts die zijn ontworpen om code in minder dan 10 minuten uit te voeren. Deze tabel bevat een lijst met Quick starts voor de Voice Assistant, geordend op taal.

| Snelstart | Platform | API-referentie |
|------------|----------|---------------|
| C#, UWP | Windows | [Door](https://aka.ms/csspeech/csharpref) |
| Java | Windows, macOS, Linux | [Door](https://aka.ms/csspeech/javaref) |
| Java | Android | [Door](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>Voorbeeldcode

Voorbeeld code voor het maken van een spraak-assistent is beschikbaar op GitHub. Deze voor beelden beslaan de client toepassing voor het maken van verbinding met uw helper in verschillende populaire programmeer talen.

* [Voor beelden van Voice Assistant (SDK)](https://aka.ms/csspeech/samples)
* [Zelf studie: spraak inschakelen voor uw assistent met de spraak-SDKC#](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="tutorial"></a>Zelfstudie

Een zelf studie over hoe u [uw assistent stem kunt instellen met behulp van de Speech SDK en direct line speech Channel](tutorial-voice-enable-your-bot-speech-sdk.md).

## <a name="customization"></a>Aanpassing

Spraak assistenten die zijn gebouwd met behulp van de spraak service kunnen gebruikmaken van het volledige aanbod aan aanpassings opties voor [spraak naar tekst](speech-to-text.md), [tekst naar spraak](text-to-speech.md)en [aangepaste trefwoord selectie](speech-devices-sdk-create-kws.md).

> [!NOTE]
> Aanpassings opties variëren per taal/land instelling (Zie [ondersteunde talen](supported-languages.md)).

## <a name="reference-docs"></a>Referentiedocumenten

* [Speech-SDK](speech-sdk-reference.md)
* [Azure Bot Service](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>Volgende stappen

* [Gratis een abonnements sleutel voor een spraak service ophalen](get-started.md)
* [De Speech SDK ophalen](speech-sdk.md)
* [Meer informatie over aangepaste opdrachten (preview-versie)](custom-commands.md)
* [Meer informatie over direct line speech](direct-line-speech.md)
