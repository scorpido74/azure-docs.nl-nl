---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 03/29/2019
ms.author: erhopf
ms.openlocfilehash: 020055c1629a66ec1aa82beb050501803b2a0f18
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77168320"
---
## <a name="authentication"></a>Verificatie

Elke aanvraag vereist een autorisatie-header. Deze tabel ziet u welke headers voor elke service worden ondersteund:

| Ondersteunde autorisatie-header | Spraak naar tekst | Tekst naar spraak |
|------------------------|----------------|----------------|
| OCP-Apim-Subscription-Key | Ja | Nee |
| Autorisatie: Bearer | Ja | Ja |

Wanneer u de `Ocp-Apim-Subscription-Key`-header gebruikt, hoeft u alleen uw abonnements sleutel op te geven. Bijvoorbeeld:

```
'Ocp-Apim-Subscription-Key': 'YOUR_SUBSCRIPTION_KEY'
```

Wanneer u de `Authorization: Bearer`-header gebruikt, moet u een aanvraag indienen bij het `issueToken`-eind punt. In deze aanvraag, moet u uw abonnementssleutel voor een toegangstoken dat is geldig voor 10 minuten uitwisselen. In de volgende gedeelten leert u hoe u een token krijgt en een token gebruikt.

### <a name="how-to-get-an-access-token"></a>Over het verkrijgen van een toegangstoken

Als u een toegangs token wilt ophalen, moet u een aanvraag indienen bij het `issueToken`-eind punt met behulp van de `Ocp-Apim-Subscription-Key` en uw abonnements sleutel.

Het `issueToken`-eind punt heeft de volgende indeling:

```
https://<REGION_IDENTIFIER>.api.cognitive.microsoft.com/sts/v1.0/issueToken
```

Vervang `<REGION_IDENTIFIER>` door de id die overeenkomt met de regio van uw abonnement uit deze tabel:

[!INCLUDE [](cognitive-services-speech-service-region-identifier.md)]

Gebruik deze voorbeelden om u te maken van uw aanvraag voor een toegangstoken.

#### <a name="http-sample"></a>HTTP-voorbeeld

In dit voorbeeld is een eenvoudige HTTP-aanvraag voor een token verkrijgen. Vervang `YOUR_SUBSCRIPTION_KEY` door de abonnements sleutel van uw speech-service. Als uw abonnement zich niet in de regio vs West bevindt, vervangt u de `Host` koptekst door de hostnaam van uw regio.

```http
POST /sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
```

De hoofd tekst van het antwoord bevat de toegangs token in de indeling JSON Web Token (JWT).

#### <a name="powershell-sample"></a>Voorbeeld van PowerShell

In dit voorbeeld is een eenvoudige PowerShell-script voor een toegangstoken. Vervang `YOUR_SUBSCRIPTION_KEY` door de abonnements sleutel van uw speech-service. Zorg ervoor dat u het juiste eindpunt voor de regio die overeenkomt met uw abonnement. In dit voorbeeld is momenteel ingesteld op VS-West.

```Powershell
$FetchTokenHeader = @{
  'Content-type'='application/x-www-form-urlencoded';
  'Content-Length'= '0';
  'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY'
}

$OAuthToken = Invoke-RestMethod -Method POST -Uri https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken
 -Headers $FetchTokenHeader

# show the token received
$OAuthToken

```

#### <a name="curl-sample"></a>cURL voorbeeld

cURL is een opdrachtregelprogramma beschikbaar in Linux (en in de Windows-subsysteem voor Linux). Deze cURL-opdracht laat zien hoe een toegangstoken. Vervang `YOUR_SUBSCRIPTION_KEY` door de abonnements sleutel van uw speech-service. Zorg ervoor dat u het juiste eindpunt voor de regio die overeenkomt met uw abonnement. In dit voorbeeld is momenteel ingesteld op VS-West.

```cli
curl -v -X POST
 "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken" \
 -H "Content-type: application/x-www-form-urlencoded" \
 -H "Content-Length: 0" \
 -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

#### <a name="c-sample"></a>C#-voorbeeld

Dit C# klasse ziet u hoe u een toegangstoken. Uw abonnementssleutel Speech Service doorgeven als u een exemplaar maken van de klasse. Als uw abonnement zich niet in de regio vs-West bevindt, wijzigt u de waarde van `FetchTokenUri` zodat deze overeenkomt met de regio voor uw abonnement.

```cs
public class Authentication
{
    public static readonly string FetchTokenUri =
        "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken";
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

            var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
            Console.WriteLine("Token Uri: {0}", uriBuilder.Uri.AbsoluteUri);
            return await result.Content.ReadAsStringAsync();
        }
    }
}
```

#### <a name="python-sample"></a>Python-voor beeld

```python
# Request module must be installed.
# Run pip install requests if necessary.
import requests

subscription_key = 'REPLACE_WITH_YOUR_KEY'


def get_token(subscription_key):
    fetch_token_url = 'https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken'
    headers = {
        'Ocp-Apim-Subscription-Key': subscription_key
    }
    response = requests.post(fetch_token_url, headers=headers)
    access_token = str(response.text)
    print(access_token)
```

### <a name="how-to-use-an-access-token"></a>Het gebruik van een toegangstoken

Het toegangs token moet worden verzonden naar de service als de `Authorization: Bearer <TOKEN>`-header. Elke toegangstoken is geldig voor 10 minuten. Krijgt u een nieuw token op elk gewenst moment, om te beperken van netwerkverkeer en de latentie, raden we echter aan met dezelfde token negen minuten.

Hier volgt een voorbeeld-HTTP-aanvraag naar de Text to Speech REST-API:

```http
POST /cognitiveservices/v1 HTTP/1.1
Authorization: Bearer YOUR_ACCESS_TOKEN
Host: westus.stt.speech.microsoft.com
Content-type: application/ssml+xml
Content-Length: 199
Connection: Keep-Alive

// Message body here...
```
