---
title: Problemen oplossen met de Spraak-SDK - Spraakservice
titleSuffix: Azure Cognitive Services
description: In dit artikel vindt u informatie om problemen op te lossen die u tegenkomen wanneer u de Spraak-SDK gebruikt.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: jhakulin
ms.openlocfilehash: 421b9adf4ae5d2c641484e646bea096716d46cca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74815413"
---
# <a name="troubleshoot-the-speech-sdk"></a>Problemen met de Speech-SDK oplossen

In dit artikel vindt u informatie om problemen op te lossen die u tegenkomen wanneer u de Spraak-SDK gebruikt.

## <a name="error-websocket-upgrade-failed-with-an-authentication-error-403"></a>Fout: WebSocket-upgrade is mislukt met een verificatiefout (403)

Mogelijk hebt u het verkeerde eindpunt voor uw regio of service. Controleer de URI om te controleren of het correct is.

Er kan ook een probleem zijn met uw abonnementssleutel of autorisatietoken. Zie de volgende sectie voor meer informatie.

## <a name="error-http-403-forbidden-or-http-401-unauthorized"></a>Fout: HTTP 403 Verboden of HTTP 401 Ongeautoriseerd

Deze fout wordt vaak veroorzaakt door verificatieproblemen. Verbindingsaanvragen zonder `Ocp-Apim-Subscription-Key` geldige `Authorization` of header worden afgewezen met een status van 403 of 401.

* Als u een abonnementssleutel gebruikt voor verificatie, ziet u mogelijk de fout omdat:

    - De abonnementssleutel ontbreekt of ongeldig is
    - U hebt het gebruiksquotum van uw abonnement overschreden

* Als u een autorisatietoken gebruikt voor verificatie, ziet u mogelijk de fout omdat:

    - Het autorisatietoken is ongeldig
    - Het autorisatietoken is verlopen

### <a name="validate-your-subscription-key"></a>Uw abonnementssleutel valideren

U controleren of u een geldige abonnementssleutel hebt door een van de volgende opdrachten uit te voeren.

> [!NOTE]
> Vervang `YOUR_SUBSCRIPTION_KEY` `YOUR_REGION` en met uw eigen abonnementssleutel en bijbehorende regio.

* PowerShell

    ```Powershell
    $FetchTokenHeader = @{
      'Content-type'='application/x-www-form-urlencoded'
      'Content-Length'= '0'
      'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY'
    }
    $OAuthToken = Invoke-RestMethod -Method POST -Uri https://YOUR_REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken -Headers $FetchTokenHeader
    $OAuthToken
    ```

* cURL

    ```
    curl -v -X POST "https://YOUR_REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY" -H "Content-type: application/x-www-form-urlencoded" -H "Content-Length: 0"
    ```

Als u een geldige abonnementssleutel hebt ingevoerd, retourneert de opdracht een autorisatietoken, anders wordt een fout geretourneerd.

### <a name="validate-an-authorization-token"></a>Een autorisatietoken valideren

Als u een autorisatietoken gebruikt voor verificatie, voert u een van de volgende opdrachten uit om te controleren of het autorisatietoken nog steeds geldig is. Tokens zijn 10 minuten geldig.

> [!NOTE]
> Vervang `YOUR_AUDIO_FILE` het pad naar uw vooraf opgenomen audiobestand. Vervang `YOUR_ACCESS_TOKEN` het autorisatietoken dat in de vorige stap is geretourneerd. Vervang `YOUR_REGION` door het juiste gebied.

* PowerShell

    ```Powershell
    $SpeechServiceURI =
    'https://YOUR_REGION.stt.speech.microsoft.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US'

    # $OAuthToken is the authorization token returned by the token service.
    $RecoRequestHeader = @{
      'Authorization' = 'Bearer '+ $OAuthToken
      'Transfer-Encoding' = 'chunked'
      'Content-type' = 'audio/wav; codec=audio/pcm; samplerate=16000'
    }

    # Read audio into byte array.
    $audioBytes = [System.IO.File]::ReadAllBytes("YOUR_AUDIO_FILE")

    $RecoResponse = Invoke-RestMethod -Method POST -Uri $SpeechServiceURI -Headers $RecoRequestHeader -Body $audioBytes

    # Show the result.
    $RecoResponse
    ```

* cURL

    ```
    curl -v -X POST "https://YOUR_REGION.stt.speech.microsoft.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US" -H "Authorization: Bearer YOUR_ACCESS_TOKEN" -H "Transfer-Encoding: chunked" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE
    ```

Als u een geldig autorisatietoken hebt ingevoerd, retourneert de opdracht de transcriptie voor uw audiobestand, anders wordt er een fout geretourneerd.

---

## <a name="error-http-400-bad-request"></a>Fout: HTTP 400 Bad Request

Deze fout treedt meestal op wanneer de aanvraaginstantie ongeldige audiogegevens bevat. Alleen wav-indeling wordt ondersteund. Controleer ook de kopteksten van het verzoek om `Content-Type` `Content-Length`te controleren of u de juiste waarden opgeeft voor en .

## <a name="error-http-408-request-timeout"></a>Fout: HTTP 408 Time-out aanvragen

De fout treedt waarschijnlijk op omdat er geen audiogegevens naar de service worden verzonden. Deze fout kan ook worden veroorzaakt door netwerkproblemen.

## <a name="recognitionstatus-in-the-response-is-initialsilencetimeout"></a>"RecognitionStatus" in het antwoord is "InitialSilenceTimeout"

Dit probleem wordt meestal veroorzaakt door audiogegevens. Mogelijk ziet u deze fout omdat:

* Er is een lange strook stilte aan het begin van de audio. In dat geval stopt de service de herkenning `InitialSilenceTimeout`na enkele seconden en retourneert .

* De audio maakt gebruik van een niet-ondersteunde codec-indeling, waardoor de audiogegevens als stilte worden behandeld.

## <a name="next-steps"></a>Volgende stappen

* [Bekijk de releasenotes](releasenotes.md)
