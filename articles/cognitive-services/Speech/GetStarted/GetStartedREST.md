---
title: Aan de slag met de Bing Speech Recognition API met behulp van REST | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Gebruik REST om toegang te krijgen tot de spraakherkennings-API in Microsoft Cognitive Services om gesp roken audio om te zetten in tekst.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: e962a12c6c27737f95e78e80036e51bac41147d5
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/13/2019
ms.locfileid: "70965773"
---
# <a name="quickstart-use-the-bing-speech-recognition-rest-api"></a>Quickstart: De Bing Speech herkennings REST API gebruiken

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Met de Bing Speech-Service in de cloud kunt u toepassingen ontwikkelen met behulp van de REST API om gesp roken audio naar tekst te converteren.

## <a name="prerequisites"></a>Vereisten

### <a name="subscribe-to-the-speech-api-and-get-a-free-trial-subscription-key"></a>Abonneer u op de spraak-API en ontvang een gratis proef abonnement sleutel

De Speech-API maakt deel uit van Cognitive Services (voorheen project Oxford). U kunt abonnements sleutels voor een gratis proef versie downloaden van de pagina [Cognitive Services abonnement](https://azure.microsoft.com/try/cognitive-services/) . Nadat u de spraak-API hebt geselecteerd, selecteert u **API-sleutel ophalen** om de sleutel op te halen. Er wordt een primaire en secundaire sleutel geretourneerd. Beide sleutels zijn gekoppeld aan hetzelfde quotum, dus u kunt beide sleutels gebruiken.

> [!IMPORTANT]
>* Een abonnements sleutel ophalen. Voordat u toegang kunt krijgen tot de REST API, moet u een [abonnements sleutel](https://azure.microsoft.com/try/cognitive-services/)hebben.
>
>* Gebruik uw abonnements sleutel. Vervang YOUR_SUBSCRIPTION_KEY door uw eigen abonnements sleutel in de volgende REST-voor beelden.
>
>* Raadpleeg de [verificatie](../how-to/how-to-authentication.md) pagina voor het verkrijgen van een abonnements sleutel.

### <a name="prerecorded-audio-file"></a>Opgenomen audio bestand

In dit voor beeld gebruiken we een opgenomen audio bestand om te laten zien hoe u de REST API kunt gebruiken. Neem een audio bestand van jezelf op dat een korte zinsnede zegt. Zeg bijvoorbeeld ' wat is de weers verwachting? ' of ' vind een ' te bekijken film '. De API voor spraak herkenning biedt ook ondersteuning voor externe microfoon invoer.

> [!NOTE]
> In het voor beeld moet audio worden vastgelegd als een WAV-bestand met een **PCM-kanaal (mono), 16 kHz**.

## <a name="build-a-recognition-request-and-send-it-to-the-speech-recognition-service"></a>Een herkennings aanvraag bouwen en deze naar de spraakherkennings service verzenden

De volgende stap voor spraak herkenning is het verzenden van een POST-aanvraag naar de HTTP-eind punten van de spraak met de juiste aanvraag header en hoofd tekst.

### <a name="service-uri"></a>Service-URI

De service-URI voor spraak herkenning is gedefinieerd op basis van [herkennings modi](../concepts.md#recognition-modes) en [herkennings talen](../concepts.md#recognition-languages):

```HTTP
https://speech.platform.bing.com/speech/recognition/<RECOGNITION_MODE>/cognitiveservices/v1?language=<LANGUAGE_TAG>&format=<OUTPUT_FORMAT>
```

`<RECOGNITION_MODE>`Hiermee geeft u de herkennings modus en moet u een van de `interactive`volgende `conversation`waarden hebben `dictation`:, of. Het is een vereist bronpad in de URI. Zie [herkennings modi](../concepts.md#recognition-modes)voor meer informatie.

`<LANGUAGE_TAG>`is een vereiste para meter in de query teken reeks. Hiermee wordt de doel taal voor audio conversie gedefinieerd: bijvoorbeeld `en-US` voor Engels (Verenigde Staten). Zie [herkennings talen](../concepts.md#recognition-languages)voor meer informatie.

`<OUTPUT_FORMAT>`is een optionele para meter in de query teken reeks. De toegestane waarden zijn `simple` en `detailed`. Standaard worden met de service resultaten in `simple` de indeling weer gegeven. Zie [output format](../concepts.md#output-format)(Engelstalig) voor meer informatie.

Een aantal voor beelden van service-Uri's vindt u in de volgende tabel.

| Herkennings-modus  | Taal | Uitvoerindeling | Service-URI |
|---|---|---|---|
| `interactive` | pt-BR | Standaard | https:\//speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=pt-BR |
| `conversation` | en-US | Gedetailleerd | https:\//speech.platform.bing.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed |
| `dictation` | fr-FR | Simpel | https:\//speech.platform.bing.com/speech/recognition/dictation/cognitiveservices/v1?language=fr-FR&format=simple |

> [!NOTE]
> De service-URI is alleen nodig als uw toepassing REST-Api's gebruikt voor het aanroepen van de service voor spraak herkenning. Als u een van de [client bibliotheken](GetStartedClientLibraries.md)gebruikt, hoeft u normaal gesp roken niet te weten welke URI er wordt gebruikt. De client bibliotheken kunnen verschillende service-Uri's gebruiken, die alleen van toepassing zijn op een specifieke client bibliotheek. Zie de client bibliotheek van uw keuze voor meer informatie.

### <a name="request-headers"></a>Aanvraagheaders

De volgende velden moeten worden ingesteld in de aanvraag header:

- `Ocp-Apim-Subscription-Key`: Telkens wanneer u de service aanroept, moet u uw abonnements sleutel in de `Ocp-Apim-Subscription-Key` kop door geven. Spraak service biedt ook ondersteuning voor het door geven van autorisatie tokens in plaats van abonnements sleutels. Zie [Verificatie](../How-to/how-to-authentication.md) voor meer informatie.
- `Content-type`: In `Content-type` het veld worden de indeling en de codec van de audio stroom beschreven. Op dit moment wordt alleen WAV-bestand en PCM mono 16000-code ring ondersteund. De waarde van het inhouds type voor deze indeling `audio/wav; codec=audio/pcm; samplerate=16000`is.

Het veld `Transfer-Encoding` is optioneel. Als u dit veld instelt op `chunked`, kunt u de audio in kleine segmenten Chop. Zie [gesegmenteerde overdracht](../How-to/how-to-chunked-transfer.md)voor meer informatie.

Hier volgt een voor beeld van een aanvraag header:

```HTTP
POST https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codec=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: speech.platform.bing.com
Transfer-Encoding: chunked
Expect: 100-continue
```

### <a name="send-a-request-to-the-service"></a>Een aanvraag verzenden naar de service

In het volgende voor beeld ziet u hoe u een aanvraag voor spraak herkenning verzendt naar spraak-eind punten. De `interactive` herkennings modus wordt gebruikt.

> [!NOTE]
> Vervang `YOUR_AUDIO_FILE` met het pad naar uw vooraf opgenomen audiobestand. Vervang `YOUR_SUBSCRIPTION_KEY` door uw eigen abonnements sleutel.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```Powershell

$SpeechServiceURI =
'https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed'

# $OAuthToken is the authorization token returned by the token service.
$RecoRequestHeader = @{
  'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY';
  'Transfer-Encoding' = 'chunked';
  'Content-type' = 'audio/wav; codec=audio/pcm; samplerate=16000'
}

# Read audio into byte array
$audioBytes = [System.IO.File]::ReadAllBytes("YOUR_AUDIO_FILE")

$RecoResponse = Invoke-RestMethod -Method POST -Uri $SpeechServiceURI -Headers $RecoRequestHeader -Body $audioBytes

# Show the result
$RecoResponse

```

# <a name="curltabcurl"></a>[curl](#tab/curl)

In het voor beeld wordt krul op Linux met bash gebruikt. Als deze niet beschikbaar is op uw platform, moet u mogelijk een krul installeren. Het voor beeld werkt ook op Cygwin in Windows, Git bash, zsh en andere shells.

> [!NOTE]
> Zorg ervoor `@` dat de naam van het audio bestand `YOUR_AUDIO_FILE` wordt vervangen door het pad naar uw vastgelegde audio bestand, omdat dit aangeeft dat de waarde `--data-binary` van een bestands naam is in plaats van gegevens.

```
curl -v -X POST "https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed" -H "Transfer-Encoding: chunked" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE
```

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
HttpWebRequest request = null;
request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
request.SendChunked = true;
request.Accept = @"application/json;text/xml";
request.Method = "POST";
request.ProtocolVersion = HttpVersion.Version11;
request.ContentType = @"audio/wav; codec=audio/pcm; samplerate=16000";
request.Headers["Ocp-Apim-Subscription-Key"] = "YOUR_SUBSCRIPTION_KEY";

// Send an audio file by 1024 byte chunks
using (FileStream fs = new FileStream(YOUR_AUDIO_FILE, FileMode.Open, FileAccess.Read))
{

    /*
    * Open a request stream and write 1024 byte chunks in the stream one at a time.
    */
    byte[] buffer = null;
    int bytesRead = 0;
    using (Stream requestStream = request.GetRequestStream())
    {
        /*
        * Read 1024 raw bytes from the input audio file.
        */
        buffer = new Byte[checked((uint)Math.Min(1024, (int)fs.Length))];
        while ((bytesRead = fs.Read(buffer, 0, buffer.Length)) != 0)
        {
            requestStream.Write(buffer, 0, bytesRead);
        }

        // Flush
        requestStream.Flush();
    }
}
```

---

## <a name="process-the-speech-recognition-response"></a>De reactie op spraak herkenning verwerken

Nadat de aanvraag is verwerkt, retourneert de speech-service de resultaten in een reactie als JSON-indeling.

> [!NOTE]
> Als de vorige code een fout retourneert, raadpleegt u [probleem oplossing](../troubleshooting.md) om de mogelijke oorzaak te vinden.

Het volgende code fragment toont een voor beeld van hoe u de reactie van de stroom kunt lezen.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```Powershell
# show the response in JSON format
ConvertTo-Json $RecoResponse
```

# <a name="curltabcurl"></a>[curl](#tab/curl)

In dit voor beeld retourneert krul direct het antwoord bericht in een teken reeks. Als u deze wilt weer geven in JSON-indeling, kunt u extra hulp middelen gebruiken, bijvoorbeeld JQ.

```
curl -X POST "https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed" -H "Transfer-Encoding: chunked" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE | jq
```

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
/*
* Get the response from the service.
*/
Console.WriteLine("Response:");
using (WebResponse response = request.GetResponse())
{
    Console.WriteLine(((HttpWebResponse)response).StatusCode);

    using (StreamReader sr = new StreamReader(response.GetResponseStream()))
    {
        responseString = sr.ReadToEnd();
    }

    Console.WriteLine(responseString);
    Console.ReadLine();
}
```

---

Het volgende voor beeld is een JSON-antwoord:

```json
OK
{
  "RecognitionStatus": "Success",
  "Offset": 22500000,
  "Duration": 21000000,
  "NBest": [{
    "Confidence": 0.941552162,
    "Lexical": "find a funny movie to watch",
    "ITN": "find a funny movie to watch",
    "MaskedITN": "find a funny movie to watch",
    "Display": "Find a funny movie to watch."
  }]
}
```

## <a name="limitations"></a>Beperkingen

De REST API heeft enkele beperkingen:

- Het ondersteunt alleen audio streams tot 15 seconden.
- Het biedt geen ondersteuning voor tussenliggende resultaten tijdens de herkenning. Gebruikers ontvangen alleen het uiteindelijke herkennings resultaat.

Als u deze beperkingen wilt verwijderen, gebruikt u de speech- [client bibliotheken](GetStartedClientLibraries.md). U kunt ook rechtstreeks werken met het [Speech-WebSocket-protocol](../API-Reference-REST/websocketprotocol.md).

## <a name="whats-next"></a>Volgend onderwerp

- Zie deze [voorbeeld toepassingen](../samples.md)voor meer informatie over C#het gebruik van de rest API in, Java, enzovoort.
- Zie [probleem oplossing](../troubleshooting.md)om fouten te vinden en op te lossen.
- Als u meer geavanceerde functies wilt gebruiken, raadpleegt u aan de slag met behulp van speech- [client bibliotheken](GetStartedClientLibraries.md).

### <a name="license"></a>Licentie

Alle Cognitive Services Sdk's en voor beelden zijn gelicentieerd met de MIT-licentie. Zie [licentie](https://github.com/Microsoft/Cognitive-Speech-STT-JavaScript/blob/master/LICENSE.md)voor meer informatie.
