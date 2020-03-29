---
title: Het verificatietoken cachen
titleSuffix: Azure Cognitive Services
description: In dit artikel ziet u hoe u het verificatietoken in de cache opslaan.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: metan
ms.openlocfilehash: e652aa29b1c1935fcc4887dbe13ef9b683a8bd05
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75946164"
---
# <a name="how-to-cache-the-authentication-token"></a>Het verificatietoken in de cache opslaan

In dit artikel wordt uitgelegd hoe u het verificatietoken in de cache opslaan om de prestaties van uw toepassing te verbeteren.

## <a name="using-aspnet"></a>Met behulp van ASP.NET

Importeer het **Microsoft.IdentityModel.Clients.ActiveDirectory** NuGet-pakket, dat wordt gebruikt om een token te verkrijgen. Gebruik vervolgens de volgende code `AuthenticationResult`om een verificatiewaarden te verkrijgen die u hebt gekregen toen u [de bron Immersive Reader hebt gemaakt.](./how-to-create-immersive-reader.md)

```csharp
private async Task<AuthenticationResult> GetTokenAsync()
{
    AuthenticationContext authContext = new AuthenticationContext($"https://login.windows.net/{TENANT_ID}");
    ClientCredential clientCredential = new ClientCredential(CLIENT_ID, CLIENT_SECRET);
    AuthenticationResult authResult = await authContext.AcquireTokenAsync("https://cognitiveservices.azure.com/", clientCredential);
    return authResult;
}
```

Het `AuthenticationResult` object `AccessToken` heeft een eigenschap die het werkelijke token is dat u gebruikt bij de lancering van de Immersive Reader met behulp van de SDK. Het heeft `ExpiresOn` ook een eigenschap die aangeeft wanneer het token verloopt. Voordat u de Immersive Reader start, u controleren of het token is verlopen en een nieuw token alleen aanschaffen als het is verlopen.

## <a name="using-nodejs"></a>Node.JS gebruiken

Voeg [**request**](https://www.npmjs.com/package/request) het aanvraagnpm-pakket toe aan uw project. Gebruik de volgende code om een token te verkrijgen, met behulp van de verificatiewaarden die u hebt gekregen toen u [de bron Immersive Reader maakte.](./how-to-create-immersive-reader.md)

```javascript
router.get('/token', function(req, res) {
    request.post(
        {
            headers: { 'content-type': 'application/x-www-form-urlencoded' },
            url: `https://login.windows.net/${TENANT_ID}/oauth2/token`,
            form: {
                grant_type: 'client_credentials',
                client_id: CLIENT_ID,
                client_secret: CLIENT_SECRET,
                resource: 'https://cognitiveservices.azure.com/'
            }
        },
        function(err, resp, json) {
            const result = JSON.parse(json);
            return res.send({
                access_token: result.access_token,
                expires_on: result.expires_on
            });
        }
    );
});
```

De `expires_on` eigenschap is de datum en het tijdstip waarop het token verloopt, uitgedrukt als het aantal seconden sinds 1 januari 1970 UTC. Gebruik deze waarde om te bepalen of uw token is verlopen voordat u een nieuwe token probeert te aanschaffen.

```javascript
async function getToken() {
    if (Date.now() / 1000 > CREDENTIALS.expires_on) {
        CREDENTIALS = await refreshCredentials();
    }
    return CREDENTIALS.access_token;
}
```

## <a name="next-steps"></a>Volgende stappen

* Ontdek de [Meeslepende Reader SDK-referentie](./reference.md)