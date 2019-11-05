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
ms.openlocfilehash: c97f6414876441290cade68b8f9a054970586402
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73507735"
---
# <a name="about-voice-assistants"></a>Over spraak assistenten

Spraak assistenten die Azure speech Services gebruiken, bieden ontwikkel aars de mogelijkheid natuurlijke, menselijke-achtige gespreks interfaces te maken voor hun toepassingen en ervaringen.

De Voice Assistant-service biedt snelle, betrouw bare interactie tussen een apparaat en een assistent-implementatie die gebruikmaakt van (1) het directe lijn spraak kanaal van de bot-Framework of (2) de geïntegreerde service voor het uitvoeren van aangepaste opdrachten (preview) voor taak voltooiing.

Toepassingen maken verbinding met de Voice Assistant-service met de speech Software Development Kit (SDK).

   ![Conceptueel diagram van de service stroom van de Orchestrator-indeling voor spraak assistenten](media/voice-assistants/overview.png "De stroom van de Voice Assistant")

## <a name="core-features"></a>Kern functies

| Category | Functies |
|----------|----------|
|[Aangepast tref woord](speech-devices-sdk-create-kws.md) | Gebruikers kunnen gesp rekken met assistenten starten met een aangepast tref woord zoals ' Hey Contoso '. Dit is een app met een aangepaste trefwoord engine in de Speech SDK, die kan worden geconfigureerd met een aangepast tref woord [dat u hier kunt genereren](speech-devices-sdk-create-kws.md). Spraak assistenten kunnen verificatie op service zijde gebruiken om de nauw keurigheid van de activering van het tref woord te verbeteren (alleen voor het apparaat).
|[Spraak naar tekst](speech-to-text.md) | Via spraak [-naar-tekst](speech-to-text.md) van Azure speech Services kunt u in spraak assistenten realtime audio omzetten in herkende tekst. Deze tekst is beschikbaar, omdat deze is getranscribeerd, naar de implementatie van uw assistent en uw client toepassing.
|[Tekst-naar-spraak](text-to-speech.md) | Tekstuele reacties van uw assistent worden gesynthesizerd met behulp van [tekst naar spraak](text-to-speech.md) vanuit Azure speech Services. Deze synthese wordt vervolgens beschikbaar gesteld voor uw client toepassing als een audio stroom. Micro soft biedt de mogelijkheid om uw eigen aangepaste Neural TTS-stem van hoge kwaliteit op te bouwen die een stem op uw merk levert. [Neem contact met ons](mailto:mstts@microsoft.com)op voor meer informatie.

## <a name="comparing-assistant-solutions"></a>Assistent-oplossingen vergelijken

De Voice Assistant-service verbindt uw toepassing op het apparaat met uw unieke assistent-implementatie. Ontwikkel aars schrijven spraak assistenten met behulp van (1) het [directe-lijn spraak](direct-line-speech.md) kanaal van de bot-Framework of (2) de oplossing [voor aangepaste opdrachten (preview)](custom-commands.md) .

   ![Vergelijking van assistent-oplossingen](media/voice-assistants/assistant-solution-comparison.png "Vergelijking van assistent-oplossingen")

| Oplossing | Functies |
|----------|----------|
|[Aangepaste opdrachten (preview-versie)](custom-commands.md) | Aangepaste opdrachten (preview) biedt een gestroomlijnde ontwerp-en hosting oplossing voor spraak assistenten. Het is afgestemd op de behoeften van het volt ooien van taken en de opdracht-en controle scenario's.
|[Directe lijn spraak](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech) | Direct line speech maakt een soepele en naadloze verbinding mogelijk tussen (1) uw client toepassing, (2) een compatibele bot en (3) de mogelijkheden van Azure speech Services. Zie de [pagina van de bot Framework-documentatie](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)voor meer informatie over het configureren van uw bot voor het gebruik van het directe lijn spraak kanaal.

Wanneer u met een van deze oplossingen een spraak assistent hebt gemaakt, kunt u uw toepassing op het apparaat aansluiten met behulp van de `DialogServiceConnector` in de Speech SDK. Raadpleeg voor meer informatie de Quick starts en voor beelden voor elke oplossing.

## <a name="getting-started-with-voice-assistants"></a>Aan de slag met spraak assistenten

We bieden Quick starts die zijn ontworpen om code in minder dan 10 minuten uit te voeren. Deze tabel bevat een lijst met Quick starts voor de Voice Assistant, geordend op taal.

| Snelstart | Platform | API-verwijzing |
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

Spraak assistenten die zijn gemaakt met behulp van Azure speech Services kunnen gebruikmaken van het volledige aanbod aan aanpassings opties voor [spraak naar tekst](speech-to-text.md), [tekst naar spraak](text-to-speech.md)en [aangepaste trefwoord selectie](speech-devices-sdk-create-kws.md).

> [!NOTE]
> Aanpassings opties variëren per taal/land instelling (Zie [ondersteunde talen](supported-languages.md)).

## <a name="reference-docs"></a>Referentiedocumenten

* [Speech-SDK](speech-sdk-reference.md)
* [Azure Bot Service](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>Volgende stappen

* [Gratis een abonnements sleutel voor spraak Services aanschaffen](get-started.md)
* [De Speech SDK ophalen](speech-sdk.md)
* [Meer informatie over aangepaste opdrachten (preview-versie)](custom-commands.md)
* [Meer informatie over direct line speech](direct-line-speech.md)