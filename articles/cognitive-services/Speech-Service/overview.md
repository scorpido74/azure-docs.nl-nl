---
title: Wat is de Speech-service?
titleSuffix: Azure Cognitive Services
description: De spraakservice combineert spraak-naar-tekst, tekst-naar-spraak en spraakomzetting in één Azure-abonnement. U kunt spraak eenvoudig aan uw toepassingen, hulpprogramma's en apparaten toevoegen met de Speech SDK, Speech Devices SDK of REST API's.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 06/25/2020
ms.author: trbye
ms.openlocfilehash: 82099172a933496f015ae8fc575c1919a879e1f9
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/13/2020
ms.locfileid: "88167739"
---
# <a name="what-is-the-speech-service"></a>Wat is de Speech-service?

De spraakservice combineert spraak-naar-tekst, tekst-naar-spraak en spraakomzetting in één Azure-abonnement. U kunt spraak eenvoudig inschakelen voor uw toepassingen, hulpprogramma's en apparaten met de [Speech CLI](spx-overview.md), [Speech SDK](speech-sdk-reference.md), [Speech Devices SDK](https://aka.ms/sdsdk-quickstart), [Speech Studio](https://speech.microsoft.com/) of [REST API's](rest-apis.md).

> [!IMPORTANT]
> De spraakservice heeft de Bing Speech API en Translator Speech vervangen. Zie _Handleidingen > Migratie_ voor migratie-instructies.

De volgende functies vormen samen de spraakservice. Gebruik de koppelingen in deze tabel voor meer informatie over veelvoorkomende gebruikssituaties voor elke functie of blader door de API-naslaginformatie.

| Service | Functie | Beschrijving | SDK | REST |
|---------|---------|-------------|-----|------|
| [Spraak-naar-tekst](speech-to-text.md) | Spraak-naar-tekst in realtime | Bij spraak-naar-tekst worden audiostromen of lokale bestanden in realtime naar tekst getranscribeerd of vertaald, zodat uw toepassingen, hulpprogramma's of apparaten deze kunnen gebruiken of weergeven. Gebruik spraak-naar-tekst met [Language Understanding (LUIS)](https://docs.microsoft.com/azure/cognitive-services/luis/) om gebruikersintentie af te leiden uit getranscribeerde spraak en om te reageren op spraakopdrachten. | [Ja](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Ja](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Spraak-naar-tekst batchgewijs verwerken](batch-transcription.md) | Met Spraak-naar-tekst batchgewijs verwerken kunt u asynchrone spraak-naar-tekst-transcriptie doorvoeren van grote volumes spraakgegevens die zijn opgeslagen in Azure Blob Storage. Met Spraak-naar-tekst batchgewijs verwerken kunt u niet alleen spraak omzetten naar tekst, maar ook sprekersindexering en sentimentanalyses uitvoeren. | Nee | [Ja](https://westus.cris.ai/swagger/ui/index) |
| | [Gesprek via meerdere apparaten](multi-device-conversation.md) | Meerdere apparaten of clients in een gesprek verbinden om spraak- of tekstberichten te verzenden, met eenvoudige ondersteuning voor transcriptie en vertaling| Ja | Nee |
| | [Gesprekstranscriptie](conversation-transcription-service.md) | Hiermee schakelt u realtime spraakherkenning, sprekeridentificatie en sprekersindexering in. Het is ideaal voor het transcriberen van persoonlijke vergaderingen met de mogelijkheid om de sprekers te onderscheiden. | Ja | Nee |
| | [Aangepaste spraakmodellen maken](#customize-your-speech-experience) | Als u spraak-naar-tekst gebruikt voor herkenning en transcriptie in een unieke omgeving, kunt u aangepaste akoestische, taal- en uitspraakmodellen maken en trainen om te kunnen omgaan met omgevingslawaai of branchespecifieke woordenlijsten. | Nee | [Ja](https://westus.cris.ai/swagger/ui/index) |
| [Tekst naar spraak](text-to-speech.md) | Tekst naar spraak | Bij tekst-naar-spraak wordt invoertekst omgezet in menselijke spraak die is samengesteld met behulp van [SSML (Speech Synthesis Markup Language)](speech-synthesis-markup.md). Kies uit standaardstemmen en neurale stemmen (zie [Taalondersteuning](language-support.md)). | [Ja](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Ja](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Aangepaste stemmen maken](#customize-your-speech-experience) | Maak aangepaste spraakstijlen die uniek zijn voor uw merk of product. | Nee | [Ja](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Speech Translation](speech-translation.md) | Spraakomzetting | Spraakomzetting maakt realtime omzetting van spraak in meerdere talen mogelijk voor uw toepassingen, hulpprogramma's en apparaten. Gebruik deze service voor het omzetten van spraak-naar-spraak en spraak-naar-tekst. | [Ja](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | Nee |
| [Spraakassistenten](voice-assistants.md) | Spraakassistenten | Spraakassistenten die gebruikmaken van de spraakservice stellen ontwikkelaars in staat om natuurlijke, menselijke gespreksinterfaces te maken voor hun toepassingen en gebruikstoepassingen. De spraakassistentservice biedt een snelle, betrouwbare interactie tussen een apparaat en een assistentimplementatie die gebruikmaakt van het Direct Line Speech-kanaal van het Bot Framework of de geïntegreerde service voor het uitvoeren van aangepaste opdrachten (preview) voor het uitvoeren van taken. | [Ja](voice-assistants.md) | Nee |
| [Speaker Recognition](speaker-recognition-overview.md) | Sprekercontrole en -identificatie | De Speaker Recognition-service biedt algoritmen die sprekers controleren en identificeren aan de hand van hun unieke stemkenmerken. Speaker Recognition wordt gebruikt voor het beantwoorden van de vraag 'Wie spreekt er?'. | Ja | [Ja](https://docs.microsoft.com/rest/api/speakerrecognition/) |


[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

## <a name="try-the-speech-service"></a>De spraakservice proberen

We bieden quickstarts in de populairste programmeertalen waarin u in minder dan tien minuten code kunt uitvoeren. Deze tabel bevat de populairste quickstarts voor elke functie. Gebruik de linkernavigatiebalk om aanvullende talen en platformen te verkennen.

| Spraak-naar-tekst (SDK) | Tekst-naar-spraak (SDK) | Omzetting (SDK) |
|----------------------|----------------------|-------------------|
| [Spraak herkennen vanuit een audiobestand](quickstarts/speech-to-text-from-file.md) | [Spraaksynthese in een audiobestand](quickstarts/text-to-speech-audio-file.md) | [Spraak omzetten in tekst](quickstarts/translate-speech-to-text.md) |
| [Spraak herkennen met een microfoon](quickstarts/speech-to-text-from-microphone.md) | [Spraaksynthese naar een luidspreker](quickstarts/text-to-speech.md) | [Spraak omzetten naar meerdere doeltalen](quickstarts/translate-speech-to-text-multiple-languages.md) |
| [Spraak herkennen die is opgeslagen in blobopslag](quickstarts/from-blob.md) | [Asynchrone synthese voor audio in lange vorm](quickstarts/text-to-speech/async-synthesis-long-form-audio.md) | [Spraak naar spraak omzetten](quickstarts/translate-speech-to-speech.md) |

> [!NOTE]
> Spraak-naar-tekst en tekst-naar-spraak hebben ook REST-eindpunten en bijbehorende quickstarts.

Nadat u de spraakservice hebt gebruikt, kunt u de zelfstudies proberen waarin u leert om verschillende scenario's op te lossen.

- [Zelfstudie: Intenties van gesproken inhoud herkennen met de Speech SDK en LUIS, C#](how-to-recognize-intents-from-speech-csharp.md)
- [Zelfstudie: Spraak inschakelen voor uw bot met de Speech SDK, C#](tutorial-voice-enable-your-bot-speech-sdk.md)
- [Zelfstudie: Een Flask-app maken om tekst te vertalen, sentiment te analyseren en vertaalde tekst in spraak om te zetten](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2fazure%2fcognitive-services%2fspeech-service%2ftoc.json&bc=%2fazure%2fcognitive-services%2fspeech-service%2fbreadcrumb%2ftoc.json&toc=%2Fen-us%2Fazure%2Fcognitive-services%2Fspeech-service%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)

## <a name="get-sample-code"></a>Voorbeeldcode ophalen

Voorbeeldcode is beschikbaar op GitHub voor de spraakservice. Deze voorbeelden hebben betrekking op veelvoorkomende scenario's, zoals het lezen van audio van een bestand of stream, continue en eenmalige herkenning en het werken met aangepaste modellen. Gebruik de volgende koppelingen om SDK- en REST-voorbeelden te bekijken:

- [Voorbeelden van spraak-naar-tekst, tekst-naar-spraak en spraakomzetting (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Voorbeelden van batchtranscriptie (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
- [Voorbeelden van tekst-naar-spraak (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)
- [Voorbeelden van spraakassistenten (SDK)](https://aka.ms/csspeech/samples)

## <a name="customize-your-speech-experience"></a>Uw spraaktoepassing aanpassen

De spraakservice kan goed worden gebruikt met ingebouwde modellen, maar mogelijk wilt u de toepassing verder aanpassen en afstemmen op uw product of omgeving. Aanpassingsopties variëren van het afstemmen van akoestische modellen tot unieke spraakstijlen voor uw merk.

| Speech Service | Platform | Beschrijving |
| -------------- | -------- | ----------- |
| Spraak naar tekst | [Aangepaste spraak](https://aka.ms/customspeech) | Pas de modellen voor spraakherkenning aan uw behoeften en beschikbare gegevens aan. Neem belemmeringen voor spraakherkenning weg, zoals spreekstijl, vocabulaire en achtergrondgeluiden. |
| Tekst naar spraak | [Aangepaste stem](https://aka.ms/customvoice) | Bouw een herkenbare, eenduidige stem voor uw tekst-naar-spraak-apps met uw beschikbare gesproken gegevens. U kunt de stemuitvoer verder verfijnen door een aantal stemparameters aan te passen. |

## <a name="reference-docs"></a>Naslagdocumentatie

- [Speech-SDK](speech-sdk-reference.md)
- [Speech Devices SDK](speech-devices-sdk.md)
- [REST API: Spraak-naar-tekst](rest-speech-to-text.md)
- [REST API: Tekst-naar-spraak](rest-text-to-speech.md)
- [REST API: Batchtranscriptie en aanpassing](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Verkrijg gratis een spraakserviceabonnementssleutel](get-started.md)
