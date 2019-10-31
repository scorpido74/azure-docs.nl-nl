---
title: Bureau blad-app die web-Api's aanroept (die een web-API aanroepen)-micro soft Identity-platform
description: Meer informatie over het bouwen van een desktop-app die web-Api's aanroept (die een web-API aanroepen)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8160ec489f891764b102b5ba23a687b53376f738
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73175373"
---
# <a name="desktop-app-that-calls-web-apis---call-a-web-api"></a>Bureau blad-app die web-Api's aanroept-een web-API aanroepen

Nu u een token hebt, kunt u een beveiligde web-API aanroepen.

## <a name="calling-a-web-api"></a>Een web-API aanroepen

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

<!--
More includes will come later for Python and Java
-->
# <a name="pythontabpython"></a>[Python](#tab/python)

```Python
endpoint = "url to the API"
http_headers = {'Authorization': 'Bearer ' + result['access_token'],
                'Accept': 'application/json',
                'Content-Type': 'application/json'}
data = requests.get(endpoint, headers=http_headers, stream=False).json()
```

# <a name="javatabjava"></a>[Java](#tab/java)

```Java
HttpURLConnection conn = (HttpURLConnection) url.openConnection();

// Set the appropriate header fields in the request header.
conn.setRequestProperty("Authorization", "Bearer " + accessToken);
conn.setRequestProperty("Accept", "application/json");

String response = HttpClientHelper.getResponseStringFromConn(conn);

int responseCode = conn.getResponseCode();
if(responseCode != HttpURLConnection.HTTP_OK) {
    throw new IOException(response);
}

JSONObject responseObject = HttpClientHelper.processResponse(responseCode, response);
```

# <a name="macostabmacos"></a>[MacOS](#tab/macOS)

## <a name="calling-a-web-api-in-msal-for-ios-and-macos"></a>Een web-API aanroepen in MSAL voor iOS en macOS

De methoden voor het verkrijgen van tokens retour neren een `MSALResult`-object. `MSALResult` beschrijft een `accessToken`-eigenschap die kan worden gebruikt om een web-API aan te roepen. U moet een toegangs token toevoegen aan de HTTP-autorisatie-header voordat u de aanroep voor toegang tot de beveiligde web-API.

Doel-C:

```objc
NSMutableURLRequest *urlRequest = [NSMutableURLRequest new];
urlRequest.URL = [NSURL URLWithString:"https://contoso.api.com"];
urlRequest.HTTPMethod = @"GET";
urlRequest.allHTTPHeaderFields = @{ @"Authorization" : [NSString stringWithFormat:@"Bearer %@", accessToken] };

NSURLSessionDataTask *task =
[[NSURLSession sharedSession] dataTaskWithRequest:urlRequest
     completionHandler:^(NSData * _Nullable data, NSURLResponse * _Nullable response, NSError * _Nullable error) {}];
[task resume];
```

Swift

```swift
let urlRequest = NSMutableURLRequest()
urlRequest.url = URL(string: "https://contoso.api.com")!
urlRequest.httpMethod = "GET"
urlRequest.allHTTPHeaderFields = [ "Authorization" : "Bearer \(accessToken)" ]

let task = URLSession.shared.dataTask(with: urlRequest as URLRequest) { (data: Data?, response: URLResponse?, error: Error?) in }
task.resume()
```

## <a name="calling-several-apis---incremental-consent-and-conditional-access"></a>Meerdere Api's aanroepen: incrementele toestemming en voorwaardelijke toegang

Als u meerdere Api's voor dezelfde gebruiker moet aanroepen nadat u een token voor de eerste API hebt ontvangen, kunt u `AcquireTokenSilent`aanroepen en ontvangt u een token voor de andere Api's de meeste tijd op de achtergrond.

```CSharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
```

De gevallen waarin de interactie is vereist, is als volgt:

- De gebruiker heeft toestemming gegeven voor de eerste API, maar nu moet worden geinstemming voor meer bereiken (incrementele toestemming)
- De eerste API heeft geen meervoudige verificatie nodig, maar de volgende.

```CSharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

try
{
 result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
 result = await app.AcquireTokenInteractive("scopeApi2")
                  .WithClaims(ex.Claims)
                  .ExecuteAsync();
}
```
---

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Naar productie verplaatsen](scenario-desktop-production.md)
