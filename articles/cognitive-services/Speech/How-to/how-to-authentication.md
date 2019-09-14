---
title: Verifiëren bij Bing Speech | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Verificatie aanvragen voor het gebruik van de Bing Speech-API
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: d1e708ff29293b87935d0d191ba44ad4a11917a0
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/13/2019
ms.locfileid: "70965663"
---
# <a name="authenticate-to-the-speech-api"></a>Verifiëren bij de spraak-API

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Bing Speech ondersteunt verificatie met behulp van:

- Een abonnementssleutel.
- Een autorisatie token.

## <a name="use-a-subscription-key"></a>Een abonnements sleutel gebruiken

Als u spraak service wilt gebruiken, moet u zich eerst abonneren op de spraak-API die deel uitmaakt van Cognitive Services (voorheen project Oxford). U kunt abonnements sleutels voor een gratis proef versie downloaden van de pagina [Cognitive Services abonnement](https://azure.microsoft.com/try/cognitive-services/) . Nadat u de spraak-API hebt geselecteerd, selecteert u **API-sleutel ophalen** om de sleutel op te halen. Er wordt een primaire en secundaire sleutel geretourneerd. Beide sleutels zijn gekoppeld aan hetzelfde quotum, dus u kunt beide sleutels gebruiken.

Meld u aan voor een [Azure-account](https://azure.microsoft.com/free/)voor lange termijn gebruik of een verhoogd quotum.

Als u de spraak rest API wilt gebruiken, moet u de abonnements sleutel in het `Ocp-Apim-Subscription-Key` veld in de aanvraag header door geven.

Name| Indeling| Description
----|-------|------------
OCP-Apim-Subscription-Key | ASCII | YOUR_SUBSCRIPTION_KEY

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

> [!IMPORTANT]
> Als u [client bibliotheken](../GetStarted/GetStartedClientLibraries.md) in uw toepassing gebruikt, controleert u of u het autorisatie token kunt ophalen met uw abonnements sleutel, zoals wordt beschreven in de volgende sectie. De-client bibliotheken gebruiken de abonnements sleutel om een autorisatie token op te halen en vervolgens het token voor verificatie te gebruiken.

## <a name="use-an-authorization-token"></a>Een autorisatie token gebruiken

U kunt ook een autorisatie token gebruiken voor verificatie als bewijs van verificatie/autorisatie. Als u dit token wilt ophalen, moet u eerst een abonnements sleutel van de spraak-API verkrijgen, zoals beschreven in de [vorige sectie](#use-a-subscription-key).

### <a name="get-an-authorization-token"></a>Een autorisatie Token ophalen

Nadat u een geldige abonnements sleutel hebt, verzendt u een POST-aanvraag naar de token service van Cognitive Services. In het antwoord ontvangt u het autorisatie token als een JSON Web Token (JWT).

> [!NOTE]
> Het token verloopt over tien minuten. Zie de volgende sectie voor het vernieuwen van het token.

De URI van de token service bevindt zich hier:

```
https://api.cognitive.microsoft.com/sts/v1.0/issueToken
```

In het volgende code voorbeeld ziet u hoe u een toegangs token kunt ophalen. Vervangen `YOUR_SUBSCRIPTION_KEY` door uw eigen abonnements sleutel:

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

In het voor beeld wordt krul op Linux met bash gebruikt. Als deze niet beschikbaar is op uw platform, moet u mogelijk een krul installeren. Het voor beeld werkt ook op Cygwin in Windows, Git bash, zsh en andere shells.

```
curl -v -X POST "https://api.cognitive.microsoft.com/sts/v1.0/issueToken" -H "Content-type: application/x-www-form-urlencoded" -H "Content-Length: 0" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
    /*
     * This class demonstrates how to get a valid access token.
     */
    public class Authentication
    {
        public static readonly string FetchTokenUri = "https://api.cognitive.microsoft.com/sts/v1.0";
        private string subscriptionKey;
        private string token;

        public Authentication(string subscriptionKey)
        {
            this.subscriptionKey = subscriptionKey;
            this.token = FetchTokenAsync(FetchTokenUri, subscriptionKey).Result;
        }

        public string GetAccessToken()
        {
            return this.token;
        }

        private async Task<string> FetchTokenAsync(string fetchUri, string subscriptionKey)
        {
            using (var client = new HttpClient())
            {
                client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
                UriBuilder uriBuilder = new UriBuilder(fetchUri);
                uriBuilder.Path += "/issueToken";

                var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
                Console.WriteLine("Token Uri: {0}", uriBuilder.Uri.AbsoluteUri);
                return await result.Content.ReadAsStringAsync();
            }
        }
    }
```

---

Hier volgt een voor beeld van een POST-aanvraag:

```HTTP
POST https://api.cognitive.microsoft.com/sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
Connection: Keep-Alive
```

Als u geen autorisatie token kunt ophalen uit de token service, controleert u of uw abonnements sleutel nog geldig is. Als u een gratis proef versie gebruikt, gaat u naar de pagina [Cognitive Services abonnement](https://azure.microsoft.com/try/cognitive-services/) , klikt u op aanmelden om u aan te melden met het account dat u hebt gebruikt voor het Toep assen van de gratis proef versie. Controleer of de abonnements sleutel is verlopen of groter is dan het quotum.

### <a name="use-an-authorization-token-in-a-request"></a>Een autorisatie token gebruiken in een aanvraag

Telkens wanneer u de spraak-API aanroept, moet u het autorisatie token in de `Authorization` header door geven. De `Authorization` header moet een JWT-toegangs token bevatten.

In het volgende voor beeld ziet u hoe u een autorisatie token gebruikt wanneer u de spraak REST API aanroept.

> [!NOTE]
> Vervang `YOUR_AUDIO_FILE` met het pad naar uw vooraf opgenomen audiobestand. Vervang `YOUR_ACCESS_TOKEN` door het autorisatie token dat u in de vorige stap hebt gekregen [een autorisatie Token ophalen](#get-an-authorization-token).

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

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
HttpWebRequest request = null;
request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
request.SendChunked = true;
request.Accept = @"application/json;text/xml";
request.Method = "POST";
request.ProtocolVersion = HttpVersion.Version11;
request.Host = @"speech.platform.bing.com";
request.ContentType = @"audio/wav; codec=audio/pcm; samplerate=16000";
request.Headers["Authorization"] = "Bearer " + token;

// Send an audio file by 1024 byte chunks
using (fs = new FileStream(audioFile, FileMode.Open, FileAccess.Read))
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

### <a name="renew-an-authorization-token"></a>Een autorisatie token vernieuwen

Het autorisatie token verloopt na een bepaalde tijds periode (momenteel 10 minuten). U moet het autorisatie token vernieuwen voordat het verloopt.

De volgende code is een voorbeeld implementatie voor C# het vernieuwen van het autorisatie token:

```cs
    /*
     * This class demonstrates how to get a valid O-auth token.
     */
    public class Authentication
    {
        public static readonly string FetchTokenUri = "https://api.cognitive.microsoft.com/sts/v1.0";
        private string subscriptionKey;
        private string token;
        private Timer accessTokenRenewer;

        //Access token expires every 10 minutes. Renew it every 9 minutes.
        private const int RefreshTokenDuration = 9;

        public Authentication(string subscriptionKey)
        {
            this.subscriptionKey = subscriptionKey;
            this.token = FetchToken(FetchTokenUri, subscriptionKey).Result;

            // renew the token on set duration.
            accessTokenRenewer = new Timer(new TimerCallback(OnTokenExpiredCallback),
                                           this,
                                           TimeSpan.FromMinutes(RefreshTokenDuration),
                                           TimeSpan.FromMilliseconds(-1));
        }

        public string GetAccessToken()
        {
            return this.token;
        }

        private void RenewAccessToken()
        {
            this.token = FetchToken(FetchTokenUri, this.subscriptionKey).Result;
            Console.WriteLine("Renewed token.");
        }

        private void OnTokenExpiredCallback(object stateInfo)
        {
            try
            {
                RenewAccessToken();
            }
            catch (Exception ex)
            {
                Console.WriteLine(string.Format("Failed renewing access token. Details: {0}", ex.Message));
            }
            finally
            {
                try
                {
                    accessTokenRenewer.Change(TimeSpan.FromMinutes(RefreshTokenDuration), TimeSpan.FromMilliseconds(-1));
                }
                catch (Exception ex)
                {
                    Console.WriteLine(string.Format("Failed to reschedule the timer to renew access token. Details: {0}", ex.Message));
                }
            }
        }

        private async Task<string> FetchToken(string fetchUri, string subscriptionKey)
        {
            using (var client = new HttpClient())
            {
                client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
                UriBuilder uriBuilder = new UriBuilder(fetchUri);
                uriBuilder.Path += "/issueToken";

                var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
                Console.WriteLine("Token Uri: {0}", uriBuilder.Uri.AbsoluteUri);
                return await result.Content.ReadAsStringAsync();
            }
        }
    }
```
