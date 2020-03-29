---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 03/29/2019
ms.author: erhopf
ms.openlocfilehash: dc5e251fee00ee22edb2261c1abd8404714834ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78668462"
---
## <a name="authentication"></a>Authentication

Voor elke aanvraag is een autorisatiekopnodig. In deze tabel wordt weergegeven welke kopteksten voor elke service worden ondersteund:

| Ondersteunde autorisatiekoppen | Spraak naar tekst | Tekst naar spraak |
|------------------------|----------------|----------------|
| Ocp-Apim-Subscription-Key | Ja | Nee |
| Vergunning: Toonder | Ja | Ja |

Wanneer u `Ocp-Apim-Subscription-Key` de koptekst gebruikt, hoeft u alleen uw abonnementssleutel op te geven. Bijvoorbeeld:

```http
'Ocp-Apim-Subscription-Key': 'YOUR_SUBSCRIPTION_KEY'
```

Wanneer u `Authorization: Bearer` de koptekst gebruikt, moet u `issueToken` een verzoek indienen bij het eindpunt. In dit verzoek wisselt u uw abonnementssleutel in voor een toegangstoken dat 10 minuten geldig is. In de volgende secties leer je hoe je een token krijgen en gebruik je een token.

### <a name="how-to-get-an-access-token"></a>Een toegangstoken krijgen

Als u een toegangstoken wilt krijgen, moet `issueToken` u een `Ocp-Apim-Subscription-Key` verzoek indienen bij het eindpunt met de abonnementssleutel en uw abonnementssleutel.

Het `issueToken` eindpunt heeft deze indeling:

```http
https://<REGION_IDENTIFIER>.api.cognitive.microsoft.com/sts/v1.0/issueToken
```

Vervang `<REGION_IDENTIFIER>` in deze tabel de id die overeenkomt met de regio van uw abonnement:

[!INCLUDE [](cognitive-services-speech-service-region-identifier.md)]

Gebruik deze voorbeelden om uw toegangstokenaanvraag te maken.

#### <a name="http-sample"></a>HTTP-voorbeeld

Dit voorbeeld is een eenvoudig HTTP-verzoek om een token te krijgen. Vervang `YOUR_SUBSCRIPTION_KEY` door uw abonnementssleutel van Spraakservice. Als uw abonnement zich niet in de `Host` regio West-VS bevindt, vervangt u de koptekst door de hostnaam van uw regio.

```http
POST /sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
```

De hoofdtekst van het antwoord bevat het toegangstoken in de JWT-indeling (JSON Web Token).

#### <a name="powershell-sample"></a>Voorbeeld van PowerShell

Dit voorbeeld is een eenvoudig PowerShell-script om een toegangstoken te krijgen. Vervang `YOUR_SUBSCRIPTION_KEY` door uw abonnementssleutel van Spraakservice. Zorg ervoor dat u het juiste eindpunt gebruikt voor de regio die overeenkomt met uw abonnement. Dit voorbeeld is momenteel ingesteld op West US.

```powershell
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

#### <a name="curl-sample"></a>cURL-voorbeeld

cURL is een command-line tool beschikbaar in Linux (en in het Windows Subsysteem voor Linux). Deze cURL-opdracht illustreert hoe u een toegangstoken krijgen. Vervang `YOUR_SUBSCRIPTION_KEY` door uw abonnementssleutel van Spraakservice. Zorg ervoor dat u het juiste eindpunt gebruikt voor de regio die overeenkomt met uw abonnement. Dit voorbeeld is momenteel ingesteld op West US.

```console
curl -v -X POST
 "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken" \
 -H "Content-type: application/x-www-form-urlencoded" \
 -H "Content-Length: 0" \
 -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

#### <a name="c-sample"></a>C#-voorbeeld

Deze C#-klasse illustreert hoe u een toegangstoken krijgen. Geef uw spraakservice-abonnementssleutel door wanneer u de klas instantiate. Als uw abonnement zich niet in de regio `FetchTokenUri` West-VS bevindt, wijzigt u de waarde van de regio voor uw abonnement.

```csharp
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

#### <a name="python-sample"></a>Python-monster

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

### <a name="how-to-use-an-access-token"></a>Een toegangstoken gebruiken

Het toegangstoken moet als koptekst `Authorization: Bearer <TOKEN>` naar de service worden verzonden. Elk toegangstoken is 10 minuten geldig. U op elk gewenst moment een nieuw token krijgen, maar om het netwerkverkeer en de latentie te minimaliseren, raden we u aan hetzelfde token gedurende negen minuten te gebruiken.

Hier is een voorbeeld http-aanvraag voor de text-to-speech REST API:

```http
POST /cognitiveservices/v1 HTTP/1.1
Authorization: Bearer YOUR_ACCESS_TOKEN
Host: westus.stt.speech.microsoft.com
Content-type: application/ssml+xml
Content-Length: 199
Connection: Keep-Alive

// Message body here...
```
