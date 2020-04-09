---
title: Een web-API aanroepen vanuit een mobiele app | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het bouwen van een mobiele app die web-API's aanroept. (Roep een web-API aan.)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 28f57c5657ce2f8537a654a7f67ed4481fab2c91
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80882689"
---
# <a name="call-a-web-api-from-a-mobile-app"></a>Een web-API aanroepen vanuit een mobiele app

Nadat uw app zich heeft aanmeldt bij een gebruiker en tokens heeft ontvangen, geeft Microsoft Authentication Library (MSAL) informatie over de gebruiker, de omgeving van de gebruiker en de uitgegeven tokens bloot. Uw app kan deze waarden gebruiken om een web-API aan te roepen of een welkomstbericht aan de gebruiker weer te geven.

In dit artikel zullen we eerst kijken naar het MSAL-resultaat. Vervolgens bekijken we hoe we een toegangstoken kunnen gebruiken van `AuthenticationResult` of `result` een beveiligde web-API kunnen aanroepen.

## <a name="msal-result"></a>MSAL resultaat
MSAL geeft de volgende waarden: 

- `AccessToken`roept beschermde web-API's in een HTTP-verzoek om drager.
- `IdToken`bevat nuttige informatie over de aangemelde gebruiker. Deze informatie omvat de naam van de gebruiker, de huistenant en een unieke id voor opslag.
- `ExpiresOn`is de vervaldatum van het token. MSAL verwerkt de automatische vernieuwing van een app.
- `TenantId`is de id van de tenant waar de gebruiker zich heeft aangemeld. Voor gastgebruikers in Azure Active Directory (Azure AD) B2B identificeert deze waarde de tenant waar de gebruiker zich heeft aangemeld. De waarde identificeert niet de huistenant van de gebruiker.  
- `Scopes`geeft de scopes aan die met uw token zijn toegekend. De toegekende scopes kunnen een subset zijn van de scopes die u hebt aangevraagd.

MSAL biedt ook een `Account` abstractie voor een waarde. Een `Account` waarde vertegenwoordigt het account van de huidige gebruiker:

- `HomeAccountIdentifier`identificeert de huistenant van de gebruiker.
- `UserName`is de voorkeursgebruikersnaam van de gebruiker. Deze waarde kan leeg zijn voor Azure AD B2C-gebruikers.
- `AccountIdentifier`identificeert de aangemelde gebruiker. In de meeste gevallen is deze `HomeAccountIdentifier` waarde hetzelfde als de waarde, tenzij de gebruiker een gast is in een andere tenant.

## <a name="call-an-api"></a>Een API aanroepen

Nadat u het toegangstoken hebt, u een web-API aanroepen. Uw app gebruikt het token om een HTTP-aanvraag te maken en voert het verzoek uit.

### <a name="android"></a>Android

```Java
        RequestQueue queue = Volley.newRequestQueue(this);
        JSONObject parameters = new JSONObject();

        try {
            parameters.put("key", "value");
        } catch (Exception e) {
            // Error when constructing.
        }
        JsonObjectRequest request = new JsonObjectRequest(Request.Method.GET, MSGRAPH_URL,
                parameters,new Response.Listener<JSONObject>() {
            @Override
            public void onResponse(JSONObject response) {
                // Successfully called Graph. Process data and send to UI.
            }
        }, new Response.ErrorListener() {
            @Override
            public void onErrorResponse(VolleyError error) {
                // Error.
            }
        }) {
            @Override
            public Map<String, String> getHeaders() throws AuthFailureError {
                Map<String, String> headers = new HashMap<>();
                
                // Put access token in HTTP request.
                headers.put("Authorization", "Bearer " + authResult.getAccessToken());
                return headers;
            }
        };

        request.setRetryPolicy(new DefaultRetryPolicy(
                3000,
                DefaultRetryPolicy.DEFAULT_MAX_RETRIES,
                DefaultRetryPolicy.DEFAULT_BACKOFF_MULT));
        queue.add(request);
```

### <a name="msal-for-ios-and-macos"></a>MSAL voor iOS en macOS

De methoden om tokens `MSALResult` te verkrijgen geven een object terug. `MSALResult`onthult `accessToken` een eigenschap. U kunt `accessToken` een web-API aanroepen. Voeg deze eigenschap toe aan de HTTP-autorisatiekop voordat u aanroept om toegang te krijgen tot de beveiligde web-API.

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

```swift
let urlRequest = NSMutableURLRequest()
urlRequest.url = URL(string: "https://contoso.api.com")!
urlRequest.httpMethod = "GET"
urlRequest.allHTTPHeaderFields = [ "Authorization" : "Bearer \(accessToken)" ]
     
let task = URLSession.shared.dataTask(with: urlRequest as URLRequest) { (data: Data?, response: URLResponse?, error: Error?) in }
task.resume()
```

### <a name="xamarin"></a>Xamarin

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

## <a name="make-several-api-requests"></a>Meerdere API-aanvragen indienen

Als u meerdere keren dezelfde API moet aanroepen of als u meerdere API's moet aanroepen, moet u bij het bouwen van uw app rekening houden met de volgende onderwerpen:

- **Incrementele toestemming**: Met het Microsoft-identiteitsplatform kunnen apps toestemming van gebruikers krijgen wanneer machtigingen vereist zijn in plaats van allemaal aan het begin. Elke keer dat uw app klaar is om een API aan te roepen, moet deze alleen de scopes aanvragen die deze nodig heeft.

- **Voorwaardelijke toegang:** Wanneer u meerdere API-aanvragen instelt, moet u in bepaalde scenario's mogelijk voldoen aan aanvullende vereisten voor voorwaardelijke toegang. Vereisten kunnen op deze manier toenemen als het eerste verzoek geen beleid voor voorwaardelijke toegang heeft en uw app probeert in stilte toegang te krijgen tot een nieuwe API die voorwaardelijke toegang vereist. Om dit probleem aan te pakken, moet u fouten van stille verzoeken opvangen en bereid zijn om een interactief verzoek in te dienen.  Zie [Richtlijnen voor voorwaardelijke toegang voor](../azuread-dev/conditional-access-dev-guide.md)meer informatie .

## <a name="call-several-apis-by-using-incremental-consent-and-conditional-access"></a>Meerdere API's aanroepen met incrementele toestemming en voorwaardelijke toegang

Als u meerdere API's voor dezelfde gebruiker moet bellen, u, nadat u een token voor `AcquireTokenSilent` de gebruiker hebt aangeschaft, voorkomen dat u de gebruiker herhaaldelijk om referenties vraagt door vervolgens te bellen om een token te krijgen:

```csharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
```

Interactie is vereist wanneer:

- De gebruiker heeft ingestemd met de eerste API, maar moet nu toestemming geven voor meer scopes. In dit geval gebruikt u incrementele toestemming.
- De eerste API vereist geen meervoudige verificatie, maar de volgende API wel.

```csharp
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

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Verplaatsen naar productie](scenario-mobile-production.md)
