---
title: Problemen oplossen Bing Speech | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Problemen oplossen bij het gebruik van Bing Speech.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 1213045867f84efd6d77aebff12b8c8725d82d08
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/13/2019
ms.locfileid: "70965572"
---
# <a name="troubleshooting-bing-speech"></a>Problemen met Bing Speech oplossen

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

## <a name="error-http-403-forbidden"></a>Optreedt`HTTP 403 Forbidden`

Wanneer u de API voor spraak herkenning gebruikt, `HTTP 403 Forbidden` wordt er een fout geretourneerd.

### <a name="cause"></a>Oorzaak

Deze fout wordt vaak veroorzaakt door verificatie problemen. Verbindings aanvragen zonder geldige `Ocp-Apim-Subscription-Key` of `Authorization` header worden door de service afgewezen met een `HTTP 403 Forbidden` antwoord.

Als u abonnements sleutel voor verificatie gebruikt, kan dit de reden zijn

- De abonnements sleutel ontbreekt of is ongeldig
- het gebruiks quotum van de abonnements sleutel is overschreden
- het `Ocp-Apim-Subscription-Key` veld is niet ingesteld in de aanvraag header wanneer rest API wordt aangeroepen

Als u een autorisatie token gebruikt voor verificatie, kunnen de volgende oorzaken de fout veroorzaken.

- de `Authorization` header ontbreekt in de aanvraag bij gebruik van rest
- het autorisatie token dat is opgegeven in de autorisatie-header, is ongeldig
- het autorisatie token is verlopen. Het toegangs token heeft een verloop tijd van 10 minuten

Zie de pagina [verificatie](How-to/how-to-authentication.md) voor meer informatie over verificatie.

### <a name="troubleshooting-steps"></a>Stappen voor probleemoplossing

#### <a name="verify-that-your-subscription-key-is-valid"></a>Controleer of de abonnements sleutel geldig is

U kunt de volgende opdracht voor verificatie uitvoeren. Opmerking: *YOUR_SUBSCRIPTION_KEY* vervangen door uw eigen abonnements sleutel. Als uw abonnements sleutel geldig is, ontvangt u in het antwoord het autorisatie token als een JSON Web Token (JWT). Anders wordt er een fout bericht weer geven als reactie.

> [!NOTE]
> Vervang `YOUR_SUBSCRIPTION_KEY` door uw eigen abonnements sleutel.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```Powershell
$FetchTokenHeader = @{
  'Content-type'='application/x-www-form-urlencoded';
  'Content-Length'= '0';
  'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY'
}

$OAuthToken = Invoke-RestMethod -Method POST -Uri https://api.cognitive.microsoft.com/sts/v1.0/issueToken -Headers $FetchTokenHeader

# show the token received
$OAuthToken

```

# <a name="curltabcurl"></a>[curl](#tab/curl)

In het voor beeld wordt krul op Linux met bash gebruikt. Als deze niet beschikbaar is op uw platform, moet u mogelijk een krul installeren. Het voor beeld moet ook worden gebruikt in Cygwin op Windows, Git bash, zsh en andere shells.

```
curl -v -X POST "https://api.cognitive.microsoft.com/sts/v1.0/issueToken" -H "Content-type: application/x-www-form-urlencoded" -H "Content-Length: 0" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```
---

Zorg ervoor dat u dezelfde abonnements sleutel gebruikt in uw toepassing of in de REST-aanvraag, zoals hierboven wordt gebruikt.

#### <a name="verify-the-authorization-token"></a>Verificatie token controleren

Deze stap is alleen nodig als u een autorisatie token gebruikt voor verificatie. Voer de volgende opdracht uit om te controleren of het autorisatie token nog geldig is. De opdracht maakt een POST-aanvraag naar de service en verwacht een antwoord bericht van de service. Als u nog steeds een `403 Forbidden` HTTP-fout ontvangt, controleert u of het toegangs token juist is ingesteld en niet is verlopen.

> [!IMPORTANT]
> Het token heeft een verloop tijd van 10 minuten.
> [!NOTE]
> Vervang `YOUR_AUDIO_FILE` door het pad naar uw vastgelegde audio bestand en `YOUR_ACCESS_TOKEN` met het autorisatie token dat u in de vorige stap hebt geretourneerd.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```Powershell

$SpeechServiceURI =
'https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed'

# $OAuthToken is the authrization token returned by the token service.
$RecoRequestHeader = @{
  'Authorization' = 'Bearer '+ $OAuthToken;
  'Transfer-Encoding' = 'chunked'
  'Content-type' = 'audio/wav; codec=audio/pcm; samplerate=16000'
}

# Read audio into byte array
$audioBytes = [System.IO.File]::ReadAllBytes("YOUR_AUDIO_FILE")

$RecoResponse = Invoke-RestMethod -Method POST -Uri $SpeechServiceURI -Headers $RecoRequestHeader -Body $audioBytes

# Show the result
$RecoResponse

```

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -v -X POST "https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed" -H "Transfer-Encoding: chunked" -H "Authorization: Bearer YOUR_ACCESS_TOKEN" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE
```

---

## <a name="error-http-400-bad-request"></a>Optreedt`HTTP 400 Bad Request`

Deze reden is doorgaans dat de hoofd tekst van de aanvraag ongeldige audio gegevens bevat. Momenteel wordt WAV-bestand alleen ondersteund.

## <a name="error-http-408-request-timeout"></a>Optreedt`HTTP 408 Request Timeout`

De fout is hoogstwaarschijnlijk omdat er geen audio gegevens naar de service worden verzonden en de service deze fout na een time-out retourneert. Voor REST API moeten de audio gegevens in de hoofd tekst van de aanvraag worden geplaatst.

## <a name="the-recognitionstatus-in-the-response-is-initialsilencetimeout"></a>De `RecognitionStatus` in het antwoord is`InitialSilenceTimeout`

Audio gegevens worden meestal veroorzaakt door de oorzaak van het probleem. Bijvoorbeeld:

- de audio heeft een lange stilte tijd aan het begin. De service stopt de herkenning na een aantal seconden en wordt geretourneerd `InitialSilenceTimeout`.
- de audio gebruikt de niet-ondersteunde codec-indeling, waardoor de audio gegevens als stilte worden beschouwd.
