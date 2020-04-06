---
title: Over de Speech SDK - Spraakservice
titleSuffix: Azure Cognitive Services
description: De Speech software development kit (SDK) onthult veel van de Spraakservicemogelijkheden, waardoor het gemakkelijker wordt om spraakgestuurde toepassingen te ontwikkelen.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: dapine
ms.openlocfilehash: 440944033e4b6a8fb29cf9ace6ad784b91316526
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668774"
---
# <a name="about-the-speech-sdk"></a>Info over de Speech-SDK

De Speech software development kit (SDK) onthult veel van de Spraakservicemogelijkheden, om u in staat te stellen spraakgestuurde toepassingen te ontwikkelen. De Speech SDK is beschikbaar in vele programmeertalen en op alle platforms.

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

## <a name="scenario-capabilities"></a>Scenariomogelijkheden

De Speech SDK onthult veel functies van de Spraakservice, maar niet allemaal. De mogelijkheden van de Speech SDK worden vaak gekoppeld aan scenario's. De Speech SDK is ideaal voor zowel real-time als niet-real-time scenario's, met behulp van lokale apparaten, bestanden, Azure blob-opslag en zelfs invoer- en uitvoerstromen. Wanneer een scenario niet haalbaar is met de Speech SDK, zoekt u naar een REST API-alternatief.

### <a name="speech-to-text"></a>Spraak naar tekst

[Spraak-naar-tekst](speech-to-text.md) (ook wel *spraakherkenning*genoemd) transcribeert audiostreams naar tekst die uw toepassingen, hulpprogramma's of apparaten kunnen gebruiken of weergeven. Gebruik spraak-naar-tekst met [Language Understanding (LUIS)](../luis/index.yml) om gebruikersintenties af te leiden van getranscribeerde spraak en te handelen op spraakopdrachten. Gebruik [Spraakvertaling](speech-translation.md) om spraakinvoer te vertalen naar een andere taal met één gesprek. Zie [Basisbeginselen van spraak naar tekst](speech-to-text-basics.md)voor meer informatie.

### <a name="text-to-speech"></a>Tekst naar spraak

[Tekst-naar-spraak](text-to-speech.md) (ook bekend als *spraaksynthese)* zet tekst om in mens-achtige gesynthetiseerde spraak. De invoertekst is tekenreeksliteralals of gebruikt de [SSML (Speech Synthesis Markup Language).](speech-synthesis-markup.md) Zie [Tekst-naar-spraaktaal en spraakondersteuning](language-support.md#text-to-speech)voor meer informatie over standaard- of neurale stemmen.

### <a name="voice-assistants"></a>Spraakassistenten

Spraakassistenten die de Speech SDK gebruiken, stellen ontwikkelaars in staat om natuurlijke, mensachtige conversationele interfaces te maken voor hun toepassingen en ervaringen. De spraakassistentservice zorgt voor een snelle, betrouwbare interactie tussen een apparaat en een assistent. De implementatie maakt gebruik van het Direct Line Speech-kanaal van het Bot Framework of de geïntegreerde aangepaste opdrachtenservice (Preview) voor taakvoltooiing. Daarnaast kunnen spraakassistenten worden gemaakt met behulp van de [Custom Voice Portal](https://aka.ms/customvoice) om een unieke spraakervaring te creëren.

#### <a name="keyword-spotting"></a>Trefwoordspotten

Het concept van [keyword spotting](speech-devices-sdk-create-kws.md) wordt ondersteund in de Speech SDK. Trefwoord spotten is de handeling van het identificeren van een trefwoord in spraak, gevolgd door een actie bij het horen van het trefwoord. 'Hey Cortana' activeert bijvoorbeeld de Cortana-assistent.

### <a name="meeting-scenarios"></a>Vergaderscenario's

De Speech SDK is perfect voor het transcriberen van vergaderscenario's, of het nu gaat om één apparaat of een gesprek met meerdere apparaten.

#### <a name="conversation-transcription"></a>Gesprektranscriptie

[Conversation Transcription](conversation-transcription.md) maakt real-time (en asynchrone) spraakherkenning, luidsprekeridentificatie en zinstoeschrijving aan elke spreker mogelijk (ook bekend als *diarisatie).* Het is perfect voor het transcriberen van persoonlijke vergaderingen met de mogelijkheid om sprekers te onderscheiden.

#### <a name="multi-device-conversation"></a>Gesprek met meerdere apparaten

Met [Multi-device Conversation](multi-device-conversation.md)sluit u meerdere apparaten of clients in een gesprek aan om spraak- of tekstberichten te verzenden, met eenvoudige ondersteuning voor transcriptie en vertaling.

### <a name="custom--agent-scenarios"></a>Aangepaste / agentscenario's

De SpraakSDK kan worden gebruikt voor het transcriberen van callcenterscenario's, waarbij telefoniegegevens worden gegenereerd.

#### <a name="call-center-transcription"></a>Callcentertranscriptie

[Call Center Transcriptie](call-center-transcription.md) is een gemeenschappelijk scenario voor spraak-naar-tekst voor het transcriberen van grote hoeveelheden telefoniegegevens die afkomstig kunnen zijn van verschillende systemen, zoals Interactive Voice Response (IVR). De nieuwste spraakherkenningsmodellen van de Spraakdienst blinken uit in het transcriberen van deze telefoniegegevens, zelfs in gevallen waarin de gegevens voor een mens moeilijk te begrijpen zijn.

### <a name="codec-compressed-audio-input"></a>Codec gecomprimeerde audio-invoer

Verschillende programmeertalen Van De Sdk van de Toespraak steunen codec samengeperste audioinputstromen. Zie <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-use-codec-compressed-audio-input-streams" target="_blank">gecomprimeerde <span class="docon docon-navigate-external x-hidden-focus"> </span>audio-invoerindelingen gebruiken voor </a>meer informatie.

## <a name="rest-api"></a>REST-API

Hoewel de SpraakSDK veel functiemogelijkheden van de Spraakservice omvat, wilt u voor sommige scenario's de REST-API gebruiken.

### <a name="batch-transcription"></a>Batchtranscriptie

[Batch transcriptie](batch-transcription.md) maakt asynchrone spraak-naar-tekst transcriptie van grote hoeveelheden gegevens. Batch transcriptie is alleen mogelijk vanuit de REST API. Naast het converteren van spraakaudio naar tekst, maakt batch spraak-naar-tekst ook diarisatie en sentiment-analyse mogelijk.

## <a name="customization"></a>Aanpassing

De Spraakservice biedt geweldige functionaliteit met zijn standaardmodellen voor spraak-naar-tekst, tekst-naar-spraak en spraakvertaling. Soms wilt u de basislijnprestaties verhogen om nog beter te werken met uw unieke use case. De Speech Service heeft een verscheidenheid aan no-code aanpassingstools die het gemakkelijk maken, en u een concurrentievoordeel te creëren met aangepaste modellen op basis van uw eigen gegevens. Deze modellen zijn alleen beschikbaar voor u en uw organisatie.

### <a name="custom-speech-to-text"></a>Aangepaste spraak-naar-tekst

Wanneer u spraak-naar-tekst gebruikt voor herkenning en transcriptie in een unieke omgeving, u aangepaste akoestische, taal- en uitspraakmodellen maken en trainen om omgevingsgeluid of branchespecifieke woordenschat aan te pakken. Het maken en beheren van no-code Custom Speech-modellen is beschikbaar via de [Custom Speech Portal.](https://aka.ms/customspeech) Zodra het Custom Speech-model is gepubliceerd, kan het worden verbruikt door de Speech SDK.

### <a name="custom-text-to-speech"></a>Aangepaste tekst-naar-spraak

Aangepaste tekst-naar-spraak, ook wel bekend als Custom Voice, is een set van online tools waarmee u een herkenbare, unieke stem voor uw merk maken. Het maken en beheren van no-code Custom Voice-modellen is beschikbaar via de [Custom Voice Portal.](https://aka.ms/customvoice) Zodra het Custom Voice-model is gepubliceerd, kan het worden verbruikt door de Speech SDK.

## <a name="get-the-speech-sdk"></a>Download de Spraak-SDK

# <a name="windows"></a>[Windows](#tab/windows)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-windows.md)]

# <a name="linux"></a>[Linux](#tab/linux)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-linux.md)]

# <a name="ios"></a>[iOS](#tab/ios)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-ios.md)]

# <a name="macos"></a>[Macos](#tab/macos)

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
* [Bekijk hoe u spraak in C herkent #](quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
