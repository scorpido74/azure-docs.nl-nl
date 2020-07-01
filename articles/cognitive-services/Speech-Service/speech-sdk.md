---
title: Over de Speech SDK-Speech-Service
titleSuffix: Azure Cognitive Services
description: De speech Software Development Kit (SDK) toont veel van de mogelijkheden van de spraak service, waardoor het eenvoudiger wordt om toepassingen met spraak functionaliteit te ontwikkelen.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: 5dc0a7fc9797948e834b8b3cb802bb92fce0eb59
ms.sourcegitcommit: 32592ba24c93aa9249f9bd1193ff157235f66d7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/01/2020
ms.locfileid: "85610758"
---
# <a name="about-the-speech-sdk"></a>Info over de Speech-SDK

De speech Software Development Kit (SDK) toont veel van de mogelijkheden van de spraak service, om u te ondersteunen bij het ontwikkelen van toepassingen met spraak herkenning. De Speech SDK is beschikbaar in veel programmeer talen en op alle platforms.

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

## <a name="scenario-capabilities"></a>Scenario mogelijkheden

De Speech-SDK maakt veel functies van de speech-service beschikbaar, maar niet alle. De mogelijkheden van de Speech SDK zijn vaak gekoppeld aan scenario's. De Speech SDK is ideaal voor realtime-en niet-real-time scenario's, met behulp van lokale apparaten, bestanden, Azure Blob-opslag en zelfs invoer-en uitvoer stromen. Wanneer een scenario niet kan worden behaald met de Speech SDK, zoekt u naar een REST API alternatief.

### <a name="speech-to-text"></a>Spraak naar tekst

Met [spraak naar tekst](speech-to-text.md) (ook wel *spraak herkenning*genoemd) worden audio stromen getranscribeerd naar tekst die uw toepassingen, hulpprogram ma's of apparaten kunnen gebruiken of weer geven. Gebruik spraak-naar-tekst met [Language Understanding (Luis)](../luis/index.yml) om gebruikers intentie af te leiden van transcribed speech en Act on Voice Commands. Gebruik [spraak omzetting](speech-translation.md) om spraak invoer te vertalen naar een andere taal met één aanroep. Zie [basis beginselen van spraak naar tekst](speech-to-text-basics.md)voor meer informatie.

### <a name="text-to-speech"></a>Tekst naar spraak

[Tekst-naar-spraak](text-to-speech.md) (ook wel *spraak-synthese*genoemd) converteert tekst naar humane-achtige gesynthesizerde spraak. De invoer tekst is letterlijke teken reeksen of het gebruik van de [SSML (Speech synthese Markup Language)](speech-synthesis-markup.md). Zie voor meer informatie over de standaard-of Neural stemmen [tekst-naar-spraak-taal en spraak ondersteuning](language-support.md#text-to-speech).

### <a name="voice-assistants"></a>Spraakassistenten

[Met de spraak-](voice-assistants.md) SDK kunnen ontwikkel aars natuurlijke, menselijke-achtige gespreks interfaces maken voor hun toepassingen en ervaringen. De Voice Assistant-service biedt snelle, betrouw bare interactie tussen een apparaat en een assistent. De implementatie maakt gebruik van het direct lijn speech Channel van het bot-Framework of de geïntegreerde service voor het volt ooien van taken (preview). Daarnaast kunnen spraak assistenten aangepaste stemmen gebruiken die zijn gemaakt in de [aangepaste Voice Portal](https://aka.ms/customvoice) om een unieke spraak-uitvoer ervaring toe te voegen.

#### <a name="keyword-spotting"></a>Tref woord herkennen

Het concept van [trefwoord herkennen](speech-devices-sdk-create-kws.md) wordt ondersteund in de Speech SDK. Trefwoord herkennen is de handeling van het identificeren van een sleutel woord in spraak, gevolgd door een actie na het horen van het sleutel woord. Bijvoorbeeld: "Hey Cortana" zou de Cortana-assistent activeren.

### <a name="meeting-scenarios"></a>Scenario's voor vergaderingen

De Speech SDK is perfect voor het overzetten van Vergader scenario's, hetzij van een apparaat of een conversatie met meerdere apparaten.

#### <a name="conversation-transcription"></a>Gesprektranscriptie

[Conversatie transcriptie](conversation-transcription.md) maakt spraak herkenning in realtime (en asynchroon) mogelijk, waarbij elke spreker (ook wel bekend als *diarization*) wordt toegeschreven. Het is ideaal voor het overzetten van persoonlijke vergaderingen met de mogelijkheid om de luid sprekers te onderscheiden.

#### <a name="multi-device-conversation"></a>Conversatie met meerdere apparaten

Met een [gesprek met meerdere](multi-device-conversation.md)apparaten kunt u meerdere apparaten of clients in een gesprek verbinden om berichten op basis van spraak of tekst te verzenden, met eenvoudige ondersteuning voor transcriptie en vertaling.

### <a name="custom--agent-scenarios"></a>Aangepaste/Agent scenario's

De Speech-SDK kan worden gebruikt voor het transcriberen van oproep centrum scenario's, waar telefoon gegevens worden gegenereerd.

#### <a name="call-center-transcription"></a>Callcentertranscriptie

[Call Center transcriptie](call-center-transcription.md) is een veelvoorkomend scenario voor spraak naar tekst voor het transcriberen van grote hoeveel heden telefoon gegevens die afkomstig kunnen zijn van verschillende systemen, zoals Interactive Voice Response (IVR). De nieuwste spraakherkennings modellen van de speech-service Excel bij het transcriberen van deze telefoon gegevens, zelfs in gevallen waarin de gegevens moeilijk te begrijpen zijn.

### <a name="codec-compressed-audio-input"></a>Door codec gecomprimeerde audio-invoer

Diverse spraak-SDK-programmeer talen ondersteunen codec gecomprimeerde audio-invoer stromen. Zie <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-use-codec-compressed-audio-input-streams" target="_blank">gecomprimeerde audio-invoer indelingen <span class="docon docon-navigate-external x-hidden-focus"></span> gebruiken </a>voor meer informatie.

## <a name="rest-api"></a>REST-API

Hoewel de spraak-SDK een groot aantal functies van de spraak service omvat, kunt u voor sommige scenario's de REST API gebruiken.

### <a name="batch-transcription"></a>Batchtranscriptie

[Batch transcriptie](batch-transcription.md) maakt asynchrone spraak-naar-tekst-transcriptie met grote hoeveel heden gegevens mogelijk. Batch-transcriptie is alleen mogelijk vanuit het REST API. Met batch spraak naar tekst kunt u niet alleen spraak-audio omzetten naar tekst, maar ook diarization-en sentiment-analyses.

## <a name="customization"></a>Aanpassing

De speech-service biedt uitstekende functionaliteit met de standaard modellen voor spraak naar tekst, tekst naar spraak en spraak omzetting. Soms wilt u mogelijk de basislijn prestaties verhogen zodat deze nog beter werken met uw unieke gebruiks case. De speech-service beschikt over een aantal hulpprogram ma's waarmee u geen code kunt aanpassen en waarmee u een competitief voor deel kunt maken met aangepaste modellen op basis van uw eigen gegevens. Deze modellen zijn alleen beschikbaar voor u en uw organisatie.

### <a name="custom-speech-to-text"></a>Custom Speech-naar-tekst

Wanneer u spraak-naar-tekst gebruikt voor herkenning en transcriptie in een unieke omgeving, kunt u aangepaste akoestische, taal en uitspraak modellen maken en trainen om omgevings lawaai of branchespecifieke woorden lijsten te verhelpen. Het maken en beheren van Custom Speech modellen zonder code is beschikbaar via de [Custom speech Portal](https://aka.ms/customspeech). Zodra het Custom Speech model is gepubliceerd, kan het worden gebruikt door de spraak-SDK.

### <a name="custom-text-to-speech"></a>Aangepaste tekst-naar-spraak

Aangepaste tekst-naar-spraak, ook wel bekend als aangepaste spraak, is een set online hulpprogram ma's waarmee u een herken bare, een-van-een-stem kunt maken voor uw merk. Het maken en beheren van aangepaste spraak modellen zonder code is beschikbaar via de [aangepaste Voice Portal](https://aka.ms/customvoice). Zodra het aangepaste spraak model is gepubliceerd, kan het worden gebruikt door de spraak-SDK.

## <a name="get-the-speech-sdk"></a>De Speech SDK ophalen

# <a name="windows"></a>[Windows](#tab/windows)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-windows.md)]

# <a name="linux"></a>[Linux](#tab/linux)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-linux.md)]

# <a name="ios"></a>[iOS](#tab/ios)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-ios.md)]

# <a name="macos"></a>[macOS](#tab/macos)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-macos.md)]

# <a name="android"></a>[Android](#tab/android)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-android.md)]

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

[!INCLUDE [Get the Node.js Speech SDK](includes/get-speech-sdk-nodejs.md)]

# <a name="browser"></a>[Browser](#tab/browser)

[!INCLUDE [Get the Browser Speech SDK](includes/get-speech-sdk-browser.md)]

---

[!INCLUDE [License notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

[!INCLUDE [Sample source code](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>Volgende stappen

* [Uw proefabonnement voor Speech ophalen](https://azure.microsoft.com/try/cognitive-services/)
* [Zie spraak herkennen in C #](quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
