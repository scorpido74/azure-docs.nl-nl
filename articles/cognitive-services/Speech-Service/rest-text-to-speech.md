---
title: Naslag informatie voor de tekst-naar-spraak-API (REST)-Speech Service
titleSuffix: Azure Cognitive Services
description: Meer informatie over het gebruik van de tekst-naar-spraak-REST API. In dit artikel leert u over de opties voor autorisatie, opties voor query's, het structureren van een aanvraag en antwoord heeft ontvangen.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: erhopf
ms.openlocfilehash: ab0891653f449b13f50dc43b196cf16a2f71370e
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975819"
---
# <a name="text-to-speech-rest-api"></a>REST API voor tekst-naar-spraak

Met de spraak service kunt u [tekst omzetten in gesynthesizerde spraak](#convert-text-to-speech) en [een lijst met ondersteunde stemmen](#get-a-list-of-voices) voor een regio ophalen met behulp van een set rest-api's. Elk beschikbaar eind punt is gekoppeld aan een regio. Er is een abonnements sleutel vereist voor het eind punt/de regio die u wilt gebruiken.

De tekst-naar-spraak-REST API ondersteunt Neural-en standaard tekst-naar-spraak-stemmen, die elk een specifieke taal en dialect ondersteunen, geïdentificeerd door land instellingen.

* Zie [taal ondersteuning](language-support.md#text-to-speech)voor een volledige lijst met stemmen.
* Zie [regio's](regions.md#text-to-speech)voor meer informatie over regionale Beschik baarheid.

> [!IMPORTANT]
> De kosten variëren per standaard, aangepast en Neural stemmen. Zie [prijzen](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)voor meer informatie.

Voordat u deze API gebruikt, moet u het volgende weten:

* De Text to Speech REST-API vereist autorisatie-header. Dit betekent dat u voltooien van de uitwisseling van een token moet voor toegang tot de service. Zie [Verificatie](#authentication) voor meer informatie.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="get-a-list-of-voices"></a>Een lijst met stemmen ophalen

Met het `voices/list`-eind punt kunt u een volledige lijst met stemmen voor een specifieke regio/eind punt ophalen.

### <a name="regions-and-endpoints"></a>Regio's en -eindpunten

| Regio | Eindpunt |
|--------|----------|
| Australië Oost | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Brazilië - Zuid | `https://brazilsouth.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Canada-Midden | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| VS - centraal | `https://centralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Azië - oost | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| VS - oost | `https://eastus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| VS - oost 2 | `https://eastus2.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Frankrijk - centraal | `https://francecentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| India - centraal | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Japan - Oost | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Korea - centraal | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| VS - noord-centraal | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Europa - noord | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| VS - zuid-centraal | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Azië - zuidoost | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| VK - zuid | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Europa - west | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| VS - west | `https://westus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| VS - west 2 | `https://westus2.tts.speech.microsoft.com/cognitiveservices/voices/list` |

### <a name="request-headers"></a>Aanvraagheaders

In deze tabel vindt u de vereiste en optionele kopteksten voor tekst-naar-spraak-aanvragen.

| Header | Beschrijving | Vereiste / optioneel |
|--------|-------------|---------------------|
| `Authorization` | Een verificatietoken voorafgegaan door het woord `Bearer`. Zie [Verificatie](#authentication) voor meer informatie. | Verplicht |

### <a name="request-body"></a>Aanvraagbody

Er is geen hoofd tekst vereist voor `GET` aanvragen voor dit eind punt.

### <a name="sample-request"></a>Voorbeeld van een aanvraag

Voor deze aanvraag is alleen een autorisatie-header vereist.

```http
GET /cognitiveservices/voices/list HTTP/1.1

Host: westus.tts.speech.microsoft.com
Authorization: Bearer [Base64 access_token]
```

### <a name="sample-response"></a>Voorbeeldantwoord

Dit antwoord is afgekapt om de structuur van een antwoord te illustreren.

> [!NOTE]
> De beschik baarheid van de Voice varieert per regio/eind punt.

```json
[
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (ar-EG, Hoda)",
        "ShortName": "ar-EG-Hoda",
        "Gender": "Female",
        "Locale": "ar-EG"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (ar-SA, Naayf)",
        "ShortName": "ar-SA-Naayf",
        "Gender": "Male",
        "Locale": "ar-SA"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (bg-BG, Ivan)",
        "ShortName": "bg-BG-Ivan",
        "Gender": "Male",
        "Locale": "bg-BG"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (ca-ES, HerenaRUS)",
        "ShortName": "ca-ES-HerenaRUS",
        "Gender": "Female",
        "Locale": "ca-ES"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (cs-CZ, Jakub)",
        "ShortName": "cs-CZ-Jakub",
        "Gender": "Male",
        "Locale": "cs-CZ"
    },

    ...

]
```

### <a name="http-status-codes"></a>HTTP-statuscodes

De HTTP-statuscode voor elke reactie geeft aan dat het slagen of veelvoorkomende fouten.

| HTTP-statuscode | Beschrijving | Mogelijke oorzaak |
|------------------|-------------|-----------------|
| 200 | OK | De aanvraag is voltooid. |
| 400 | Onjuiste aanvraag | Er ontbreekt een vereiste parameter ontbreekt, is leeg of null zijn. Of de waarde die wordt doorgegeven aan een vereiste of optionele parameter is ongeldig. Een veelvoorkomend probleem is een header die te lang is. |
| 401 | Niet geautoriseerd | De aanvraag is niet gemachtigd. Controleer of dat uw abonnementssleutel of token geldig is en in de juiste regio. |
| 429 | Te veel aanvragen | U hebt het quotum of het aantal aanvragen dat is toegestaan voor uw abonnement overschreden. |
| 502 | Ongeldige gateway | Netwerk- of serverzijde probleem. Kan ook duiden op ongeldige kopteksten. |


## <a name="convert-text-to-speech"></a>Tekst naar spraak converteren

Met het `v1`-eind punt kunt u tekst naar spraak converteren met behulp van [SSML (Speech synthese Markup Language)](speech-synthesis-markup.md).

### <a name="regions-and-endpoints"></a>Regio's en -eindpunten

Deze regio's worden ondersteund voor tekst naar spraak met behulp van de REST-API. Zorg ervoor dat u het eindpunt dat overeenkomt met de regio van uw abonnement selecteert.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]

### <a name="request-headers"></a>Aanvraagheaders

In deze tabel vindt u de vereiste en optionele kopteksten voor tekst-naar-spraak-aanvragen.

| Header | Beschrijving | Vereiste / optioneel |
|--------|-------------|---------------------|
| `Authorization` | Een verificatietoken voorafgegaan door het woord `Bearer`. Zie [Verificatie](#authentication) voor meer informatie. | Verplicht |
| `Content-Type` | Hiermee geeft u het type inhoud voor de opgegeven tekst. Waarde geaccepteerd: `application/ssml+xml`. | Verplicht |
| `X-Microsoft-OutputFormat` | Hiermee geeft u de indeling van de audio-uitvoer. Zie voor een volledige lijst met geaccepteerde waarden [audio-uitvoer](#audio-outputs). | Verplicht |
| `User-Agent` | De naam van de toepassing. De gegeven waarde moet kleiner zijn dan 255 tekens. | Verplicht |

### <a name="audio-outputs"></a>Audio-uitvoer

Dit is een lijst met ondersteunde audio-indelingen die worden verzonden in elke aanvraag als de `X-Microsoft-OutputFormat` header. Elk omvat een bitrate en type codering. De spraak service ondersteunt 24 kHz, 16 kHz en 8 kHz audio-uitvoer.

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
> Als uw geselecteerde spraak- en de indeling van uitvoer hebt verschillende bitsnelheden, de audio nieuw voorbeeld wordt gemaakt zo nodig. 24 kHz stemmen bieden echter geen ondersteuning voor `audio-16khz-16kbps-mono-siren` en `riff-16khz-16kbps-mono-siren` uitvoer indelingen.

### <a name="request-body"></a>Aanvraagbody

De hoofd tekst van elke `POST` aanvraag wordt verzonden als [SSML (Speech synthese Markup Language)](speech-synthesis-markup.md). Met SSML kunt u de stem en taal kiezen van de gesynthesizerde spraak die wordt geretourneerd door de service tekst naar spraak. Zie [taal ondersteuning](language-support.md#text-to-speech)voor een volledige lijst met ondersteunde stemmen.

> [!NOTE]
> Als u een aangepaste stem gebruikt, kan de hoofd tekst van een aanvraag worden verzonden als tekst zonder opmaak (ASCII of UTF-8).

### <a name="sample-request"></a>Voorbeeld van een aanvraag

Deze HTTP-aanvraag maakt gebruik van SSML om op te geven van de spraak- en taal. De hoofdtekst van het niet langer zijn dan 1000 tekens.

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

Bekijk onze Quick starts voor taalspecifieke voor beelden:

* [.NET Core, C#](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-csharp&tabs=dotnetcore)
* [Python](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-python)
* [Node.js](quickstart-nodejs-text-to-speech.md)

### <a name="http-status-codes"></a>HTTP-statuscodes

De HTTP-statuscode voor elke reactie geeft aan dat het slagen of veelvoorkomende fouten.

| HTTP-statuscode | Beschrijving | Mogelijke oorzaak |
|------------------|-------------|-----------------|
| 200 | OK | De aanvraag is uitgevoerd. de antwoordtekst is een geluidsbestand. |
| 400 | Onjuiste aanvraag | Er ontbreekt een vereiste parameter ontbreekt, is leeg of null zijn. Of de waarde die wordt doorgegeven aan een vereiste of optionele parameter is ongeldig. Een veelvoorkomend probleem is een header die te lang is. |
| 401 | Niet geautoriseerd | De aanvraag is niet gemachtigd. Controleer of dat uw abonnementssleutel of token geldig is en in de juiste regio. |
| 413 | Aanvraagentiteit te groot | De invoer SSML is langer dan 1024 tekens. |
| 415 | Niet-ondersteund media type | Het is mogelijk dat het verkeerde `Content-Type` is gegeven. `Content-Type` moet worden ingesteld op `application/ssml+xml`. |
| 429 | Te veel aanvragen | U hebt het quotum of het aantal aanvragen dat is toegestaan voor uw abonnement overschreden. |
| 502 | Ongeldige gateway | Netwerk- of serverzijde probleem. Kan ook duiden op ongeldige kopteksten. |

Als de HTTP-status `200 OK`, de hoofdtekst van het antwoord bevat een audio-bestand in de gewenste indeling. Dit bestand kan worden afgespeeld, zoals deze is overgedragen, in een buffer opgeslagen of opgeslagen in een bestand.

## <a name="next-steps"></a>Volgende stappen

- [Uw proefabonnement voor Speech ophalen](https://azure.microsoft.com/try/cognitive-services/)
- [Akoestische modellen aanpassen](how-to-customize-acoustic-models.md)
- [Taalmodellen aanpassen](how-to-customize-language-model.md)
