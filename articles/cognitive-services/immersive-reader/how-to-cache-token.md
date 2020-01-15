---
title: Het verificatie token in de cache opslaan
titleSuffix: Azure Cognitive Services
description: In dit artikel wordt uitgelegd hoe u het verificatie token in de cache opslaat.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: metan
ms.openlocfilehash: e652aa29b1c1935fcc4887dbe13ef9b683a8bd05
ms.sourcegitcommit: 49e14e0d19a18b75fd83de6c16ccee2594592355
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2020
ms.locfileid: "75946164"
---
# <a name="how-to-cache-the-authentication-token"></a>Het verificatie token in de cache opslaan

In dit artikel wordt beschreven hoe u het verificatie token in de cache opslaat om de prestaties van uw toepassing te verbeteren.

## <a name="using-aspnet"></a>ASP.NET gebruiken

Importeer het NuGet-pakket **micro soft. Identity model. clients. ActiveDirectory** , dat wordt gebruikt om een token op te halen. Gebruik vervolgens de volgende code om een `AuthenticationResult`te verkrijgen, met behulp van de verificatie waarden die u hebt gekregen tijdens [het maken van de insluitende lezer-resource](./how-to-create-immersive-reader.md).

```csharp
private async Task<AuthenticationResult> GetTokenAsync()
{
    AuthenticationContext authContext = new AuthenticationContext($"https://login.windows.net/{TENANT_ID}");
    ClientCredential clientCredential = new ClientCredential(CLIENT_ID, CLIENT_SECRET);
    AuthenticationResult authResult = await authContext.AcquireTokenAsync("https://cognitiveservices.azure.com/", clientCredential);
    return authResult;
}
```

Het `AuthenticationResult`-object heeft een `AccessToken`-eigenschap die het daad werkelijke token is dat u gebruikt bij het starten van de insluitende lezer met behulp van de SDK. Het bevat ook een `ExpiresOn`-eigenschap die geeft aan wanneer het token verloopt. Voordat u de insluitende lezer start, kunt u controleren of het token is verlopen en alleen een nieuw token verkrijgen als het is verlopen.

## <a name="using-nodejs"></a>Node. JS gebruiken

Voeg het [**aanvraag**](https://www.npmjs.com/package/request) NPM-pakket toe aan uw project. Gebruik de volgende code om een token te verkrijgen, met behulp van de verificatie waarden die u hebt gekregen tijdens [het maken van de insluitende lezer-resource](./how-to-create-immersive-reader.md).

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

De eigenschap `expires_on` is de datum en tijd waarop het token verloopt, uitgedrukt als het aantal seconden sinds 1 januari 1970 UTC. Gebruik deze waarde om te bepalen of uw token is verlopen voordat er wordt geprobeerd een nieuwe te verkrijgen.

```javascript
async function getToken() {
    if (Date.now() / 1000 > CREDENTIALS.expires_on) {
        CREDENTIALS = await refreshCredentials();
    }
    return CREDENTIALS.access_token;
}
```

## <a name="next-steps"></a>Volgende stappen

* De referentie voor de [insluitende lezer-SDK](./reference.md) verkennen