---
title: Spraak-naar-tekst API-verwijzing (REST) - Spraakservice
titleSuffix: Azure Cognitive Services
description: Meer informatie over het gebruik van de REST-API voor spraak naar tekst. In dit artikel leert u over autorisatieopties, queryopties, hoe u een aanvraag structureert en een antwoord ontvangt.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: dapine
ms.openlocfilehash: 759ea697e4093da5bfc1c082c886c6dfda636f42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79474795"
---
# <a name="speech-to-text-rest-api"></a>REST API's voor spraak-naar-tekst

Als alternatief voor de [Speech SDK](speech-sdk.md)u met de Spraakservice spraak naar tekst converteren met behulp van een REST API. Elk toegankelijk eindpunt is gekoppeld aan een regio. Voor uw toepassing is een abonnementssleutel vereist voor het eindpunt dat u wilt gebruiken. De REST API is zeer beperkt, en het mag alleen worden gebruikt in gevallen waar de [Speech SDK](speech-sdk.md) niet kan.

Voordat u de REST-API voor spraak naar tekst gebruikt, begrijpt u:

* Verzoeken die gebruik maken van de REST API en rechtstreeks audio verzenden, kunnen slechts tot 60 seconden audio bevatten.
* De spraak-naar-tekst REST API retourneert alleen de uiteindelijke resultaten. Gedeeltelijke resultaten worden niet verstrekt.

Als het verzenden van langere audio een vereiste is voor uw toepassing, u overwegen de [Speech SDK](speech-sdk.md) of een op bestanden gebaseerde REST-API te gebruiken, zoals [batchtranscriptie.](batch-transcription.md)

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="regions-and-endpoints"></a>Regio's en eindpunten

Het eindpunt voor de REST API heeft deze indeling:

```
https://<REGION_IDENTIFIER>.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1
```

Vervang `<REGION_IDENTIFIER>` in deze tabel de id die overeenkomt met de regio van uw abonnement:

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

> [!NOTE]
> De taalparameter moet aan de URL worden toegevoegd om te voorkomen dat u een HTTP-fout van 4xx ontvangt. De taal die is ingesteld op Amerikaans Engels met `https://westus.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US`behulp van het Eindpunt van west-VS is bijvoorbeeld: .

## <a name="query-parameters"></a>Queryparameters

Deze parameters kunnen worden opgenomen in de queryreeks van het REST-verzoek.

| Parameter | Beschrijving | Vereist / Optioneel |
|-----------|-------------|---------------------|
| `language` | Identificeert de gesproken taal die wordt herkend. Zie [Ondersteunde talen](language-support.md#speech-to-text). | Vereist |
| `format` | Hiermee geeft u de resultaatnotatie op. Geaccepteerde `simple` waarden `detailed`zijn en . Eenvoudige resultaten `RecognitionStatus` `DisplayText`zijn `Offset`, `Duration`, en . Gedetailleerde antwoorden bevatten meerdere resultaten met betrouwbaarheidswaarden en vier verschillende weergaven. De standaardinstelling is `simple`. | Optioneel |
| `profanity` | Hiermee geeft u op hoe met godslastering in herkenningsresultaten moet worden omgaat. Geaccepteerde `masked`waarden zijn , die godslastering vervangt `removed`door sterretjes, , die alle `raw`godslastering uit het resultaat verwijdert, of , waaronder de godslastering in het resultaat. De standaardinstelling is `masked`. | Optioneel |
| `cid` | Wanneer u de [portal Aangepaste spraak](how-to-custom-speech.md) gebruikt om aangepaste modellen te maken, u aangepaste modellen gebruiken via hun **Endpoint ID** op de pagina **Implementatie.** Gebruik de **eindpunt-id** als `cid` argument voor de parameter querytekenreeks. | Optioneel |

## <a name="request-headers"></a>Aanvraagheaders

In deze tabel worden vereiste en optionele kopteksten weergegeven voor spraak-naar-tekstaanvragen.

|Header| Beschrijving | Vereist / Optioneel |
|------|-------------|---------------------|
| `Ocp-Apim-Subscription-Key` | Uw abonnementssleutel voor spraakservice. | Deze koptekst `Authorization` of is vereist. |
| `Authorization` | Een autorisatietoken voorafgegaan door `Bearer`het woord . Zie [Verificatie](#authentication) voor meer informatie. | Deze koptekst `Ocp-Apim-Subscription-Key` of is vereist. |
| `Content-type` | Beschrijft de indeling en codec van de meegeleverde audiogegevens. Geaccepteerde `audio/wav; codecs=audio/pcm; samplerate=16000` waarden `audio/ogg; codecs=opus`zijn en . | Vereist |
| `Transfer-Encoding` | Hiermee geeft u op dat er geblokte audiogegevens worden verzonden in plaats van één bestand. Gebruik deze koptekst alleen als audiogegevens worden vermorzegeld. | Optioneel |
| `Expect` | Als u chunked `Expect: 100-continue`transfer gebruikt, verzendt u . De Spraakservice erkent het oorspronkelijke verzoek en wacht op aanvullende gegevens.| Vereist bij het verzenden van geblokte audiogegevens. |
| `Accept` | Indien verstrekt, moet `application/json`het . De Spraakservice levert resultaten in JSON. Sommige aanvraagframeworks bieden een onverenigbare standaardwaarde. Het is een goede `Accept`gewoonte om altijd . | Optioneel, maar aanbevolen. |

## <a name="audio-formats"></a>Audio-indelingen

Audio wordt verzonden in `POST` de hoofdtekst van het HTTP-verzoek. Het moet in een van de formaten in deze tabel:

| Indeling | Codec | Bitrate | Voorbeeldsnelheid  |
|--------|-------|---------|--------------|
| WAV    | PCM   | 16-bits  | 16 kHz, mono |
| Ogg    | Opus  | 16-bits  | 16 kHz, mono |

>[!NOTE]
>De bovenstaande indelingen worden ondersteund via REST API en WebSocket in de spraakservice. De [SpraakSDK](speech-sdk.md) ondersteunt momenteel de WAV-indeling met PCM-codec en [andere indelingen.](how-to-use-codec-compressed-audio-input-streams.md)

## <a name="sample-request"></a>Voorbeeldaanvraag

Het voorbeeld hieronder bevat de hostnaam en vereiste headers. Het is belangrijk op te merken dat de dienst ook audiogegevens verwacht, die niet in dit voorbeeld zijn opgenomen. Zoals eerder vermeld, wordt chunking aanbevolen, echter niet vereist.

```HTTP
POST speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codecs=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.stt.speech.microsoft.com
Transfer-Encoding: chunked
Expect: 100-continue
```

## <a name="http-status-codes"></a>HTTP-statuscode

De HTTP-statuscode voor elk antwoord geeft succes of veelvoorkomende fouten aan.

| HTTP-statuscode | Beschrijving | Mogelijke reden |
|------------------|-------------|-----------------|
| `100` | Doorgaan | Het oorspronkelijke verzoek is ingewilligd. Ga verder met het verzenden van de rest van de gegevens. (Gebruikt bij geblokte overdracht) |
| `200` | OK | Het verzoek was succesvol; de reactiebody is een JSON-object. |
| `400` | Slecht verzoek | Taalcode niet verstrekt, geen ondersteunde taal, ongeldig audiobestand, enz. |
| `401` | Niet geautoriseerd | Abonnementssleutel of autorisatietoken is ongeldig in het opgegeven gebied of ongeldig eindpunt. |
| `403` | Verboden | Ontbrekende abonnementssleutel of autorisatietoken. |

## <a name="chunked-transfer"></a>Chunked transfer

Chunked transfer`Transfer-Encoding: chunked`( ) kan helpen verminderen herkenning latentie. Hiermee kan de spraakservice beginnen met het verwerken van het audiobestand terwijl het wordt verzonden. De REST API geeft geen gedeeltelijke of tussentijdse resultaten.

In dit codevoorbeeld ziet u hoe u audio in brokken verzendt. Alleen de eerste brok mag de koptekst van het audiobestand bevatten. `request`is `HttpWebRequest` een object dat is verbonden met het juiste REST-eindpunt. `audioFile`is het pad naar een audiobestand op schijf.

```csharp
var request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
request.SendChunked = true;
request.Accept = @"application/json;text/xml";
request.Method = "POST";
request.ProtocolVersion = HttpVersion.Version11;
request.Host = host;
request.ContentType = @"audio/wav; codecs=audio/pcm; samplerate=16000";
request.Headers["Ocp-Apim-Subscription-Key"] = "YOUR_SUBSCRIPTION_KEY";
request.AllowWriteStreamBuffering = false;

using (var fs = new FileStream(audioFile, FileMode.Open, FileAccess.Read))
{
    // Open a request stream and write 1024 byte chunks in the stream one at a time.
    byte[] buffer = null;
    int bytesRead = 0;
    using (var requestStream = request.GetRequestStream())
    {
        // Read 1024 raw bytes from the input audio file.
        buffer = new Byte[checked((uint)Math.Min(1024, (int)fs.Length))];
        while ((bytesRead = fs.Read(buffer, 0, buffer.Length)) != 0)
        {
            requestStream.Write(buffer, 0, bytesRead);
        }

        requestStream.Flush();
    }
}
```

## <a name="response-parameters"></a>Responsparameters

De resultaten worden verstrekt als JSON. De `simple` indeling bevat deze velden op het hoogste niveau.

| Parameter | Beschrijving  |
|-----------|--------------|
|`RecognitionStatus`|Status, bijvoorbeeld `Success` voor succesvolle erkenning. Zie volgende tabel.|
|`DisplayText`|De erkende tekst na hoofdletters, interpunctie, omgekeerde tekstnormalisatie (omzetting van gesproken tekst in kortere vormen, zoals 200 voor "tweehonderd" of "Dr. Smith" voor "doctor smith"), en godslastering maskeren. Aanwezig alleen op succes.|
|`Offset`|De tijd (in 100-nanoseconde eenheden) waarop de erkende spraak begint in de audiostream.|
|`Duration`|De duur (in 100-nanoseconde eenheden) van de erkende spraak in de audiostream.|

Het `RecognitionStatus` veld kan de volgende waarden bevatten:

| Status | Beschrijving |
|--------|-------------|
| `Success` | De erkenning was `DisplayText` succesvol en het veld is aanwezig. |
| `NoMatch` | Spraak werd gedetecteerd in de audiostream, maar er werden geen woorden uit de doeltaal gematcht. Meestal betekent dit dat de herkenningstaal een andere taal is dan de taal die de gebruiker spreekt. |
| `InitialSilenceTimeout` | Het begin van de audiostream bevatte alleen stilte en de service time-out te wachten op spraak. |
| `BabbleTimeout` | Het begin van de audiostream bevatte alleen ruis en de service time-out te wachten op spraak. |
| `Error` | De herkenningsservice heeft een interne fout opgelopen en kon niet worden voortgezet. Probeer het opnieuw indien mogelijk. |

> [!NOTE]
> Als de audio alleen uit godslastering `profanity` bestaat en de `remove`queryparameter is ingesteld op , geeft de service geen spraakresultaat terug.

Het `detailed` formaat bevat dezelfde `simple` gegevens als `NBest`het formaat, samen met een lijst van alternatieve interpretaties van hetzelfde herkenningsresultaat. Deze resultaten zijn gerangschikt van de meeste waarschijnlijk tot minst waarschijnlijk. De eerste vermelding is hetzelfde als het hoofdherkenningsresultaat.  Bij het `detailed` gebruik `DisplayText` van `Display` de indeling, `NBest` wordt verstrekt als voor elk resultaat in de lijst.

Elk object `NBest` in de lijst bevat:

| Parameter | Beschrijving |
|-----------|-------------|
| `Confidence` | De betrouwbaarheidsscore van de inzending van 0,0 (geen vertrouwen) tot 1,0 (volledig vertrouwen) |
| `Lexical` | De lexicale vorm van de erkende tekst: de daadwerkelijke erkende woorden. |
| `ITN` | De omgekeerde-tekst-genormaliseerde ("canonieke") vorm van de erkende tekst, met telefoonnummers, nummers, afkortingen ("dokter smith" naar "dr smith"), en andere transformaties toegepast. |
| `MaskedITN` | De ITN-vorm met godslastering maskeren toegepast, indien gevraagd. |
| `Display` | De weergavevorm van de herkende tekst, met leestekens en hoofdletters toegevoegd. Deze parameter is `DisplayText` hetzelfde als wanneer `simple`de indeling is ingesteld op . |

## <a name="sample-responses"></a>Voorbeeldreacties

Een typisch `simple` antwoord voor herkenning:

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

Een typisch `detailed` antwoord voor herkenning:

```json
{
  "RecognitionStatus": "Success",
  "Offset": "1236645672289",
  "Duration": "1236645672289",
  "NBest": [
      {
        "Confidence" : "0.87",
        "Lexical" : "remind me to buy five pencils",
        "ITN" : "remind me to buy 5 pencils",
        "MaskedITN" : "remind me to buy 5 pencils",
        "Display" : "Remind me to buy 5 pencils.",
      },
      {
        "Confidence" : "0.54",
        "Lexical" : "rewind me to buy five pencils",
        "ITN" : "rewind me to buy 5 pencils",
        "MaskedITN" : "rewind me to buy 5 pencils",
        "Display" : "Rewind me to buy 5 pencils.",
      }
  ]
}
```

## <a name="next-steps"></a>Volgende stappen

- [Uw proefabonnement voor Speech ophalen](https://azure.microsoft.com/try/cognitive-services/)
- [Akoestische modellen aanpassen](how-to-customize-acoustic-models.md)
- [Taalmodellen aanpassen](how-to-customize-language-model.md)
