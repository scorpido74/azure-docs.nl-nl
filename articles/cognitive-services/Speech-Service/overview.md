---
title: Wat is de Speech-service?
titleSuffix: Azure Cognitive Services
description: De speech-service is de combineert van spraak naar tekst, tekst naar spraak en spraak omzetting in één Azure-abonnement. Voeg spraak toe aan uw toepassingen, hulpprogram ma's en apparaten met de Speech SDK, de speech-apparaten SDK of REST Api's.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 03/12/2020
ms.author: trbye
ms.openlocfilehash: 464e21caf3105b12f0fbf44dc2aa3674a252d3c6
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81401028"
---
# <a name="what-is-the-speech-service"></a>Wat is de Speech-service?

De speech-service is het combineert van spraak naar tekst, tekst naar spraak en spraak omzetting in één Azure-abonnement. Het is eenvoudig om uw toepassingen, hulpprogram ma's en apparaten met de spraak- [SDK](speech-sdk-reference.md), [Speech-apparaten SDK](https://aka.ms/sdsdk-quickstart)of [rest api's](rest-apis.md)in te scha kelen.

> [!IMPORTANT]
> De speech-service is vervangen Bing Speech-API en Translator Speech. Raadpleeg de _hand leidingen > migratie_ voor migratie-instructies.

Deze functies vormen de spraak service. Gebruik de koppelingen in deze tabel voor meer informatie over veelvoorkomende use cases voor elke functie of blader door de API-verwijzing.

| Service | Functie | Beschrijving | SDK | REST |
|---------|---------|-------------|-----|------|
| [Spraak naar tekst](speech-to-text.md) | Realtime spraak-naar-tekst | Met spraak-naar-tekst worden audio-streams of lokale bestanden omgezet naar tekst in realtime die uw toepassingen, hulpprogram ma's of apparaten kunnen gebruiken of weer geven. Gebruik spraak-naar-tekst met [Language Understanding (Luis)](https://docs.microsoft.com/azure/cognitive-services/luis/) om gebruikers intentie af te leiden van transcribed speech en Act on Voice Commands. | [Ja](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Ja](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Batch spraak naar tekst](batch-transcription.md) | Met batch-spraak naar-tekst kunt u asynchrone spraak-naar-tekst transcriptie van grote volumes met spraak gegevens die zijn opgeslagen in Azure Blob Storage. Met batch spraak naar tekst kunt u niet alleen spraak-audio omzetten naar tekst, maar ook diarization-en sentiment-analyses. | Nee | [Ja](https://westus.cris.ai/swagger/ui/index) |
| | [Conversatie met meerdere apparaten](multi-device-conversation.md) | Meerdere apparaten of clients in een conversatie verbinden om spraak-of tekst berichten te verzenden, met eenvoudige ondersteuning voor transcriptie en vertaling| Ja | Nee |
| | [Gesprek transcriptie](conversation-transcription-service.md) | Hiermee schakelt u realtime spraak herkenning, identificatie van de spreker en diarization in. Het is ideaal voor het overzetten van persoonlijke vergaderingen met de mogelijkheid om de luid sprekers te onderscheiden. | Ja | Nee |
| | [Custom Speech modellen maken](#customize-your-speech-experience) | Als u spraak-naar-tekst gebruikt voor herkenning en transcriptie in een unieke omgeving, kunt u aangepaste akoestische, taal en uitspraak modellen maken en trainen om omgevings lawaai of branchespecifieke woorden lijsten te verhelpen. | Nee | [Ja](https://westus.cris.ai/swagger/ui/index) |
| [Tekst-naar-spraak](text-to-speech.md) | Tekst naar spraak | Tekst-naar-spraak zet invoer tekst om in Human-achtige, geprogrammeerde spraak met behulp van [SSML (Speech synthese Markup Language)](speech-synthesis-markup.md). Kies uit standaard stemmen en Neural stemmen (Zie [taal ondersteuning](language-support.md)). | [Ja](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Ja](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Aangepaste stemmen maken](#customize-your-speech-experience) | Aangepaste spraak lettertypen maken die uniek zijn voor uw merk of product. | Nee | [Ja](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Spraakomzetting](speech-translation.md) | Spraakomzetting | Met spraak omzetting kan spraak op meerdere talen worden omgezet in uw toepassingen, hulpprogram ma's en apparaten. Gebruik deze service voor conversie van spraak naar spraak en spraak naar tekst. | [Ja](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | Nee |
| [Spraakassistenten](voice-assistants.md) | Spraakassistenten | Met de spraak service kunnen ontwikkel aars natuurlijke, menselijke-achtige gespreks interfaces maken voor hun toepassingen en ervaringen. De Voice Assistant-service biedt een snelle, betrouw bare interactie tussen een apparaat en een assistent-implementatie die gebruikmaakt van het directe lijn spraak kanaal van het bot-Framework of de geïntegreerde service voor het uitvoeren van aangepaste opdrachten (preview) voor taak voltooiing. | [Ja](voice-assistants.md) | Nee |


[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

## <a name="try-the-speech-service"></a>Probeer de speech-service

We bieden Quick starts in de populairste programmeer talen, die allemaal ontworpen zijn voor het uitvoeren van code in minder dan 10 minuten. Deze tabel bevat de populairste Quick starts voor elke functie. Gebruik de linkernavigatiebalk om extra talen en platformen te verkennen.

| Spraak naar tekst (SDK) | Tekst-naar-spraak (SDK) | Vertaling (SDK) |
|----------------------|----------------------|-------------------|
| [Spraak herkennen vanuit een audiobestand](quickstarts/speech-to-text-from-file.md) | [Spraaksynthese in een audiobestand](quickstarts/text-to-speech-audio-file.md) | [Spraak omzetten naar tekst](quickstarts/translate-speech-to-text.md) |
| [Spraak herkennen met een microfoon](quickstarts/speech-to-text-from-microphone.md) | [Spraaksynthese naar een luidspreker](quickstarts/text-to-speech.md) | [Spraak omzetten naar meerdere doeltalen](quickstarts/translate-speech-to-text-multiple-languages.md) |
| [Spraak herkennen die is opgeslagen in blobopslag](quickstarts/from-blob.md) | [Asynchrone synthese voor audio in lange vorm](quickstarts/text-to-speech/async-synthesis-long-form-audio.md) | [Spraak naar spraak omzetten](quickstarts/translate-speech-to-speech.md) |

> [!NOTE]
> Spraak-naar-tekst en tekst-naar-spraak hebben ook REST-eind punten en bijbehorende Quick starts.

Nadat u de spraak service hebt bekeken, kunt u de zelf studie uitproberen die u leert hoe u de intenties kunt herkennen vanuit spraak met behulp van de Speech SDK en LUIS.

- [Zelf studie: intenties herkennen vanuit spraak met de Speech SDK en LUIS, C #](how-to-recognize-intents-from-speech-csharp.md)
- [Zelf studie: stem uw bot in met de Speech SDK, C #](tutorial-voice-enable-your-bot-speech-sdk.md)
- [Zelf studie: een kolf-app bouwen om tekst te vertalen, sentiment te analyseren en vertaalde tekst naar spraak te, rest](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2fazure%2fcognitive-services%2fspeech-service%2ftoc.json&bc=%2fazure%2fcognitive-services%2fspeech-service%2fbreadcrumb%2ftoc.json&toc=%2Fen-us%2Fazure%2Fcognitive-services%2Fspeech-service%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)

## <a name="get-sample-code"></a>Voorbeeldcode ophalen

Voorbeeld code is beschikbaar op GitHub voor de speech-service. Deze voor beelden hebben betrekking op veelvoorkomende scenario's, zoals het lezen van audio van een bestand of stream, continue en eenmalige herkenning en het werken met aangepaste modellen. Gebruik deze koppelingen om SDK-en REST-voor beelden weer te geven:

- [Voor beelden van spraak naar tekst, tekst naar spraak en spraak omzetting (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Voor beelden van batch transcriptie (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
- [Voor beelden van tekst naar spraak (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)
- [Voor beelden van Voice Assistant (SDK)](https://aka.ms/csspeech/samples)

## <a name="customize-your-speech-experience"></a>Uw spraak ervaring aanpassen

De spraak service is goed geschikt voor ingebouwde modellen, maar u kunt de ervaring voor uw product of omgeving verder aanpassen en afstemmen. Aanpassings opties variëren van het verfijnen van akoestische modellen tot unieke spraak lettertypen voor uw merk.

| Speech Service | Platform | Beschrijving |
| -------------- | -------- | ----------- |
| Spraak naar tekst | [Custom Speech](https://aka.ms/customspeech) | Pas de modellen voor spraak herkenning aan uw behoeften en beschik bare gegevens aan. U kunt de belemmeringen voor spraak herkenning, zoals spreek stijl, vocabulaire en achtergrond ruis, overwinnen. |
| Tekst naar spraak | [Aangepaste spraak](https://aka.ms/customvoice) | Bouw een herkenbare, eenduidige stem voor uw tekst-naar-spraak-apps met uw beschikbare gesproken gegevens. U kunt de spraak uitvoer verder verfijnen door een set spraak parameters aan te passen. |

## <a name="reference-docs"></a>Referentie documenten

- [Speech-SDK](speech-sdk-reference.md)
- [Speech Devices SDK](speech-devices-sdk.md)
- [REST API: spraak naar tekst](rest-speech-to-text.md)
- [REST API: tekst-naar-spraak](rest-text-to-speech.md)
- [REST API: batch transcriptie en-aanpassing](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Gratis een abonnements sleutel voor een spraak service ophalen](get-started.md)
