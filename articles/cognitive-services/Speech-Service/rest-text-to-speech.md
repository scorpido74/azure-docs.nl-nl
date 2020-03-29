---
title: Tekst-naar-spraak API-verwijzing (REST) - Spraakservice
titleSuffix: Azure Cognitive Services
description: Meer informatie over het gebruik van de REST-API voor tekst tot spraak. In dit artikel leert u over autorisatieopties, queryopties, hoe u een aanvraag structureert en een antwoord ontvangt.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: dapine
ms.openlocfilehash: 17b5e21291078f424ee775f21add181859dbbed5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131639"
---
# <a name="text-to-speech-rest-api"></a>REST API voor tekst-naar-spraak

Met de spraakservice u [tekst omzetten in gesynthetiseerde spraak](#convert-text-to-speech) en een lijst met ondersteunde stemmen voor een regio [krijgen](#get-a-list-of-voices) met behulp van een set REST-API's. Elk beschikbaar eindpunt is gekoppeld aan een regio. Er is een abonnementssleutel vereist voor het eindpunt/de regio die u wilt gebruiken.

De text-to-speech REST API ondersteunt neurale en standaard tekst-naar-spraak stemmen, die elk een specifieke taal en dialect ondersteunt, geïdentificeerd door locale.

* Zie [taalondersteuning](language-support.md#text-to-speech)voor een volledige lijst met stemmen.
* Zie [regio's](regions.md#text-to-speech)voor informatie over de regionale beschikbaarheid.

> [!IMPORTANT]
> Kosten variëren voor standaard, aangepaste en neurale stemmen. Zie [Prijzen voor](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)meer informatie.

Voordat u deze API gebruikt, begrijpt u:

* De rest-inhouds-API voor tekst-naar-spraak vereist een autorisatiekoptekst. Dit betekent dat u een token-uitwisseling moet voltooien om toegang te krijgen tot de service. Zie [Verificatie](#authentication) voor meer informatie.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="get-a-list-of-voices"></a>Een lijst met stemmen

Met `voices/list` het eindpunt u een volledige lijst met stemmen krijgen voor een specifiek regio/eindpunt.

### <a name="regions-and-endpoints"></a>Regio's en eindpunten

| Regio | Eindpunt |
|--------|----------|
| Australië - oost | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Brazilië - zuid | `https://brazilsouth.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Canada - midden | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| VS - centraal | `https://centralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Azië - oost | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| VS - oost | `https://eastus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| VS - oost 2 | `https://eastus2.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Frankrijk - centraal | `https://francecentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| India - centraal | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Japan - oost | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Korea - centraal | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| VS - noord-centraal | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Europa - noord | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| VS - zuid-centraal | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Azië - zuidoost | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Verenigd Koninkrijk Zuid | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Europa -west | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| VS - west | `https://westus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| VS - west 2 | `https://westus2.tts.speech.microsoft.com/cognitiveservices/voices/list` |

### <a name="request-headers"></a>Aanvraagheaders

In deze tabel worden vereiste en optionele kopteksten weergegeven voor tekst-naar-spraakaanvragen.

| Header | Beschrijving | Vereist / Optioneel |
|--------|-------------|---------------------|
| `Authorization` | Een autorisatietoken voorafgegaan door `Bearer`het woord . Zie [Verificatie](#authentication) voor meer informatie. | Vereist |

### <a name="request-body"></a>Aanvraagbody

Een instantie is niet `GET` vereist voor aanvragen voor dit eindpunt.

### <a name="sample-request"></a>Voorbeeldaanvraag

Voor deze aanvraag is alleen een autorisatiekop nodig.

```http
GET /cognitiveservices/voices/list HTTP/1.1

Host: westus.tts.speech.microsoft.com
Authorization: Bearer [Base64 access_token]
```

### <a name="sample-response"></a>Voorbeeldantwoord

Dit antwoord is afgekapt om de structuur van een reactie te illustreren.

> [!NOTE]
> De beschikbaarheid van spraak verschilt per regio/eindpunt.

```json
[
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (ar-EG, Hoda)",
        "ShortName": "ar-EG-Hoda",
        "Gender": "Female",
        "Locale": "ar-EG",
        "SampleRateHertz": "16000",
        "VoiceType": "Standard"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (ar-SA, Naayf)",
        "ShortName": "ar-SA-Naayf",
        "Gender": "Male",
        "Locale": "ar-SA",
        "SampleRateHertz": "16000",
        "VoiceType": "Standard"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (bg-BG, Ivan)",
        "ShortName": "bg-BG-Ivan",
        "Gender": "Male",
        "Locale": "bg-BG",
        "SampleRateHertz": "16000",
        "VoiceType": "Standard"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (ca-ES, HerenaRUS)",
        "ShortName": "ca-ES-HerenaRUS",
        "Gender": "Female",
        "Locale": "ca-ES",
        "SampleRateHertz": "16000",
        "VoiceType": "Standard"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (zh-CN, XiaoxiaoNeural)",
        "ShortName": "zh-CN-XiaoxiaoNeural",
        "Gender": "Female",
        "Locale": "zh-CN",
        "SampleRateHertz": "24000",
        "VoiceType": "Neural"
    },

    ...
]
```

### <a name="http-status-codes"></a>HTTP-statuscode

De HTTP-statuscode voor elk antwoord geeft succes of veelvoorkomende fouten aan.

| HTTP-statuscode | Beschrijving | Mogelijke reden |
|------------------|-------------|-----------------|
| 200 | OK | Het verzoek is geslaagd. |
| 400 | Onjuiste aanvraag | Een vereiste parameter ontbreekt, leeg of null. Of de waarde die wordt doorgegeven aan een vereiste of optionele parameter is ongeldig. Een veelvoorkomend probleem is een koptekst die te lang is. |
| 401 | Niet geautoriseerd | Het verzoek is niet geautoriseerd. Controleer of uw abonnementssleutel of token geldig is en in de juiste regio. |
| 429 | Te veel aanvragen | U hebt het quotum of het aantal toegestane aanvragen voor uw abonnement overschreden. |
| 502 | Slechte gateway    | Probleem aan de netwerk- of serverzijde. Kan ook wijzen op ongeldige headers. |


## <a name="convert-text-to-speech"></a>Tekst naar spraak converteren

Met `v1` het eindpunt u tekst-naar-spraak converteren met behulp van [SSML (Speech Synthesis Markup Language).](speech-synthesis-markup.md)

### <a name="regions-and-endpoints"></a>Regio's en eindpunten

Deze regio's worden ondersteund voor tekst-naar-spraak met behulp van de REST API. Zorg ervoor dat u het eindpunt selecteert dat overeenkomt met uw abonnementsregio.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]

### <a name="request-headers"></a>Aanvraagheaders

In deze tabel worden vereiste en optionele kopteksten weergegeven voor tekst-naar-spraakaanvragen.

| Header | Beschrijving | Vereist / Optioneel |
|--------|-------------|---------------------|
| `Authorization` | Een autorisatietoken voorafgegaan door `Bearer`het woord . Zie [Verificatie](#authentication) voor meer informatie. | Vereist |
| `Content-Type` | Hiermee geeft u het inhoudstype voor de opgegeven tekst op. Geaccepteerde `application/ssml+xml`waarde: . | Vereist |
| `X-Microsoft-OutputFormat` | Hiermee geeft u de audio-uitvoerindeling op. Zie [audio-uitgangen](#audio-outputs)voor een volledige lijst met geaccepteerde waarden. | Vereist |
| `User-Agent` | De toepassingsnaam. De opgegeven waarde moet lager zijn dan 255 tekens. | Vereist |

### <a name="audio-outputs"></a>Audio-uitgangen

Dit is een lijst met ondersteunde audio-indelingen `X-Microsoft-OutputFormat` die in elk verzoek als koptekst worden verzonden. Elk bevat een bitrate en codering type. De Spraakservice ondersteunt audio-uitgangen van 24 kHz, 16 kHz en 8 kHz.

|||
|-|-|
| `raw-16khz-16bit-mono-pcm` | `raw-8khz-8bit-mono-mulaw` |
| `riff-8khz-8bit-mono-alaw` | `riff-8khz-8bit-mono-mulaw` |
| `riff-16khz-16bit-mono-pcm` | `audio-16khz-128kbitrate-mono-mp3` |
| `audio-16khz-64kbitrate-mono-mp3` | `audio-16khz-32kbitrate-mono-mp3` |
| `raw-24khz-16bit-mono-pcm` | `riff-24khz-16bit-mono-pcm` |
| `audio-24khz-160kbitrate-mono-mp3` | `audio-24khz-96kbitrate-mono-mp3` |
| `audio-24khz-48kbitrate-mono-mp3` | |

> [!NOTE]
> Als de geselecteerde stem- en uitvoerindeling verschillende bitsnelheden hebben, wordt de audio indien nodig opnieuw gesampled. Echter, 24 kHz stemmen `audio-16khz-16kbps-mono-siren` `riff-16khz-16kbps-mono-siren` ondersteunen geen en output formaten.

### <a name="request-body"></a>Aanvraagbody

De instantie `POST` van elk verzoek wordt verzonden als [Speech Synthesis Markup Language (SSML)](speech-synthesis-markup.md). Met SSML u de stem en taal kiezen van de gesynthetiseerde spraak die wordt geretourneerd door de tekst-naar-spraakservice. Zie [taalondersteuning](language-support.md#text-to-speech)voor een volledige lijst met ondersteunde stemmen.

> [!NOTE]
> Als u een aangepaste stem gebruikt, kan de hoofdtekst van een aanvraag worden verzonden als platte tekst (ASCII of UTF-8).

### <a name="sample-request"></a>Voorbeeldaanvraag

Met deze HTTP-aanvraag wordt SSML gebruikt om de stem en taal op te geven. Als de lichaamslengte lang is en de resulterende audio meer dan 10 minuten bedraagt , wordt deze afgekapt tot 10 minuten. Met andere woorden, de audiolengte mag niet langer zijn dan 10 minuten.

```http
POST /cognitiveservices/v1 HTTP/1.1

X-Microsoft-OutputFormat: raw-16khz-16bit-mono-pcm
Content-Type: application/ssml+xml
Host: westus.tts.speech.microsoft.com
Content-Length: 225
Authorization: Bearer [Base64 access_token]

<speak version='1.0' xml:lang='en-US'><voice xml:lang='en-US' xml:gender='Female'
    name='en-US-JessaRUS'>
        Microsoft Speech Service Text-to-Speech API
</voice></speak>
```

Bekijk onze quickstarts voor taalspecifieke voorbeelden:

* [.NET Core, C #](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-csharp&tabs=dotnetcore)
* [Python](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-python)
* [Node.js](quickstart-nodejs-text-to-speech.md)

### <a name="http-status-codes"></a>HTTP-statuscode

De HTTP-statuscode voor elk antwoord geeft succes of veelvoorkomende fouten aan.

| HTTP-statuscode | Beschrijving | Mogelijke reden |
|------------------|-------------|-----------------|
| 200 | OK | Het verzoek was succesvol; de responsbody is een audiobestand. |
| 400 | Onjuiste aanvraag | Een vereiste parameter ontbreekt, leeg of null. Of de waarde die wordt doorgegeven aan een vereiste of optionele parameter is ongeldig. Een veelvoorkomend probleem is een koptekst die te lang is. |
| 401 | Niet geautoriseerd | Het verzoek is niet geautoriseerd. Controleer of uw abonnementssleutel of token geldig is en in de juiste regio. |
| 413 | Entiteit aanvragen die te groot is | De SSML-invoer is langer dan 1024 tekens. |
| 415 | Niet-ondersteund mediatype | Het is mogelijk dat `Content-Type` het verkeerde was verstrekt. `Content-Type`moeten worden `application/ssml+xml`ingesteld op . |
| 429 | Te veel aanvragen | U hebt het quotum of het aantal toegestane aanvragen voor uw abonnement overschreden. |
| 502 | Slechte gateway    | Probleem aan de netwerk- of serverzijde. Kan ook wijzen op ongeldige headers. |

Als de HTTP-status is, `200 OK`bevat de hoofdtekst van het antwoord een audiobestand in de gewenste indeling. Dit bestand kan worden afgespeeld terwijl het wordt overgedragen, opgeslagen in een buffer of wordt opgeslagen in een bestand.

## <a name="next-steps"></a>Volgende stappen

- [Uw proefabonnement voor Speech ophalen](https://azure.microsoft.com/try/cognitive-services)
- [Asynchrone synthese voor lange audio](quickstarts/text-to-speech/async-synthesis-long-form-audio.md)
- [Aan de slag met Custom Voice](how-to-custom-voice.md)
