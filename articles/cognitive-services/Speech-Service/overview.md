---
title: Wat is de Speech-service?
titleSuffix: Azure Cognitive Services
description: De spraakservice is de eenwording van spraak-naar-tekst- en tekst-naar-spraak- en spraakvertaling in één Azure-abonnement. Voeg spraak toe aan uw toepassingen, hulpprogramma's en apparaten met de SpraakSDK, Spraakapparaten SDK of REST API's.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 03/12/2020
ms.author: trbye
ms.openlocfilehash: 464e21caf3105b12f0fbf44dc2aa3674a252d3c6
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81401028"
---
# <a name="what-is-the-speech-service"></a>Wat is de Speech-service?

De spraakservice is de eenwording van spraak-naar-tekst, tekst-naar-spraak en spraakvertaling in één Azure-abonnement. Het is eenvoudig om uw toepassingen, hulpprogramma's en apparaten in te schakelen met de [Speech SDK,](speech-sdk-reference.md) [Speech Devices SDK](https://aka.ms/sdsdk-quickstart)of REST [API's.](rest-apis.md)

> [!IMPORTANT]
> De spraakservice heeft Bing Speech API en Translator Speech vervangen. Zie _Handleidingen voor > migratie_ voor migratieinstructies.

Deze functies vormen de Spraakservice. Gebruik de koppelingen in deze tabel voor meer informatie over veelvoorkomende use cases voor elke functie of blader door de API-verwijzing.

| Service | Functie | Beschrijving | SDK | REST |
|---------|---------|-------------|-----|------|
| [Spraak naar tekst](speech-to-text.md) | Realtime spraak-naar-tekst | Spraak-naar-tekst transcribeert of vertaalt audiostreams of lokale bestanden naar tekst in realtime die uw toepassingen, hulpprogramma's of apparaten kunnen gebruiken of weergeven. Gebruik spraak-naar-tekst met [Language Understanding (LUIS)](https://docs.microsoft.com/azure/cognitive-services/luis/) om gebruikersintenties af te leiden van getranscribeerde spraak en te handelen op spraakopdrachten. | [Ja](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Ja](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Spraak-naar-tekst batch](batch-transcription.md) | Batch Speech-to-text maakt asynchrone spraak-naar-teksttranscriptie mogelijk van grote hoeveelheden spraakaudiogegevens die zijn opgeslagen in Azure Blob Storage. Naast het converteren van spraakaudio naar tekst, maakt Batch Speech-to-text ook diarisatie en sentiment-analyse mogelijk. | Nee | [Ja](https://westus.cris.ai/swagger/ui/index) |
| | [Gesprek met meerdere apparaten](multi-device-conversation.md) | Verbind meerdere apparaten of clients in een gesprek om spraak- of tekstberichten te verzenden, met eenvoudige ondersteuning voor transcriptie en vertaling| Ja | Nee |
| | [Gesprekstranscriptie](conversation-transcription-service.md) | Maakt real-time spraakherkenning, luidsprekeridentificatie en diarisatie mogelijk. Het is perfect voor het transcriberen van persoonlijke vergaderingen met de mogelijkheid om sprekers te onderscheiden. | Ja | Nee |
| | [Aangepaste spraakmodellen maken](#customize-your-speech-experience) | Als u spraak-naar-tekst gebruikt voor herkenning en transcriptie in een unieke omgeving, u aangepaste akoestische, taal- en uitspraakmodellen maken en trainen om omgevingsgeluid of branchespecifieke woordenschat aan te pakken. | Nee | [Ja](https://westus.cris.ai/swagger/ui/index) |
| [Text-to-speech](text-to-speech.md) | Tekst naar spraak | Tekst-naar-spraak zet invoertekst om in menselijke gesynthetiseerde spraak met behulp van [Speech Synthesis Markup Language (SSML).](speech-synthesis-markup.md) Kies uit standaardstemmen en neurale stemmen (zie [Taalondersteuning).](language-support.md) | [Ja](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Ja](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Aangepaste stemmen maken](#customize-your-speech-experience) | Maak aangepaste spraaklettertypen die uniek zijn voor uw merk of product. | Nee | [Ja](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Spraakvertaling](speech-translation.md) | Spraakomzetting | Spraakvertaling maakt real-time, meertalige vertaling van spraak naar uw toepassingen, hulpprogramma's en apparaten mogelijk. Gebruik deze service voor spraak-naar-spraak en spraak-naar-tekst vertaling. | [Ja](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | Nee |
| [Spraakassistenten](voice-assistants.md) | Spraakassistenten | Spraakassistenten die de Spraakservice gebruiken, stellen ontwikkelaars in staat om natuurlijke, mensachtige conversationele interfaces te maken voor hun toepassingen en ervaringen. De spraakassistentservice biedt een snelle, betrouwbare interactie tussen een apparaat en een assistent-implementatie die gebruikmaakt van het Direct Line Speech-kanaal van het Bot Framework of de geïntegreerde aangepaste opdrachtenservice (Preview) voor het voltooien van taken. | [Ja](voice-assistants.md) | Nee |


[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

## <a name="try-the-speech-service"></a>Probeer de spraakservice

We bieden quickstarts in de meest populaire programmeertalen, elk ontworpen om u code in minder dan 10 minuten te laten uitvoeren. Deze tabel bevat de meest populaire quickstarts voor elke functie. Gebruik de navigatie aan de linkerkant om extra talen en platforms te verkennen.

| Spraak-naar-tekst (SDK) | SDK van tekst naar spraak | Vertaling (SDK) |
|----------------------|----------------------|-------------------|
| [Spraak herkennen vanuit een audiobestand](quickstarts/speech-to-text-from-file.md) | [Spraaksynthese in een audiobestand](quickstarts/text-to-speech-audio-file.md) | [Spraak naar tekst vertalen](quickstarts/translate-speech-to-text.md) |
| [Spraak herkennen met een microfoon](quickstarts/speech-to-text-from-microphone.md) | [Spraaksynthese naar een luidspreker](quickstarts/text-to-speech.md) | [Spraak omzetten naar meerdere doeltalen](quickstarts/translate-speech-to-text-multiple-languages.md) |
| [Spraak herkennen die is opgeslagen in blobopslag](quickstarts/from-blob.md) | [Asynchrone synthese voor audio in lange vorm](quickstarts/text-to-speech/async-synthesis-long-form-audio.md) | [Spraak naar spraak omzetten](quickstarts/translate-speech-to-speech.md) |

> [!NOTE]
> Spraak-naar-tekst en tekst-naar-spraak hebben ook REST-eindpunten en bijbehorende quickstarts.

Nadat u de spraakservice hebt kunnen gebruiken, probeert u onze zelfstudie die u leert hoe u intenties van spraak herkennen met behulp van de Speech SDK en LUIS.

- [Zelfstudie: Intents van spraak herkennen met de Speech SDK en LUIS, C #](how-to-recognize-intents-from-speech-csharp.md)
- [Zelfstudie: Spraakschakel uw bot in met de SpraakSDK, C #](tutorial-voice-enable-your-bot-speech-sdk.md)
- [Zelfstudie: Een Flask-app bouwen om tekst te vertalen, sentiment te analyseren en vertaalde tekst te synthetiseren naar spraak, REST](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2fazure%2fcognitive-services%2fspeech-service%2ftoc.json&bc=%2fazure%2fcognitive-services%2fspeech-service%2fbreadcrumb%2ftoc.json&toc=%2Fen-us%2Fazure%2Fcognitive-services%2Fspeech-service%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)

## <a name="get-sample-code"></a>Voorbeeldcode ophalen

Voorbeeldcode is beschikbaar op GitHub voor de spraakservice. Deze voorbeelden hebben betrekking op veelvoorkomende scenario's zoals het lezen van audio uit een bestand of stream, continue en single-shot herkenning en het werken met aangepaste modellen. Gebruik deze koppelingen om SDK- en REST-voorbeelden weer te geven:

- [Voorbeelden van spraak-naar-tekst, tekst-naar-spraak en spraakvertaling (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Batch transcriptie monsters (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
- [Tekst-naar-spraakvoorbeelden (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)
- [Voorbeelden van spraakassistenten (SDK)](https://aka.ms/csspeech/samples)

## <a name="customize-your-speech-experience"></a>Uw spraakervaring aanpassen

De Spraakservice werkt goed met ingebouwde modellen, maar misschien wilt u de ervaring verder aanpassen en afstemmen op uw product of omgeving. Aanpassingsopties variëren van akoestische modeltuning tot unieke spraaklettertypen voor uw merk.

| Speech Service | Platform | Beschrijving |
| -------------- | -------- | ----------- |
| Spraak naar tekst | [Aangepaste spraak](https://aka.ms/customspeech) | Pas spraakherkenningsmodellen aan uw behoeften en beschikbare gegevens aan. Overwin spraakherkenningsbarrières zoals spreekstijl, woordenschat en achtergrondgeluid. |
| Tekst naar spraak | [Aangepaste stem](https://aka.ms/customvoice) | Bouw een herkenbare, eenduidige stem voor uw tekst-naar-spraak-apps met uw beschikbare gesproken gegevens. U de stemuitvoer verder verfijnen door een reeks stemparameters aan te passen. |

## <a name="reference-docs"></a>Referentiedocumenten

- [Speech-SDK](speech-sdk-reference.md)
- [Speech Devices SDK](speech-devices-sdk.md)
- [REST-API: spraak-naar-tekst](rest-speech-to-text.md)
- [REST API: Tekst-naar-spraak](rest-text-to-speech.md)
- [REST API: Batch transcriptie en aanpassing](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Ontvang gratis een abonnementssleutel voor spraakservice](get-started.md)
