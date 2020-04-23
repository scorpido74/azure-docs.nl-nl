---
title: Tokens aanschaffen om een web-API aan te roepen (daemon-app) - Microsoft-identiteitsplatform | Azure
description: Meer informatie over het bouwen van een daemon-app die web-API's aanroept (tokens aanschaffen)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: d755573b53eb63d85165fb73fe4b97298dbeff09
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81868999"
---
# <a name="daemon-app-that-calls-web-apis---acquire-a-token"></a>Daemon-app die web-API's aanroept - een token aanschaffen

Nadat u een vertrouwelijke clienttoepassing hebt gemaakt, u een `AcquireTokenForClient`token voor de app aanschaffen door te bellen, het bereik door te geven en optioneel een vernieuwing van het token te forceren.

## <a name="scopes-to-request"></a>Scopes om aan te vragen

Het bereik dat u moet aanvragen voor een clientreferentiestroom, is de naam van de resource, gevolgd door `/.default`. Met deze notatie wordt azure active directory (Azure AD) gevraagd om de *machtigingen op toepassingsniveau* te gebruiken die statisch zijn gedeclareerd tijdens de registratie van toepassingen. Deze API-machtigingen moeten ook worden verleend door een tenantbeheerder.

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

# <a name="python"></a>[Python](#tab/python)

In MSAL Python ziet het configuratiebestand eruit als dit codefragment:

```Json
{
    "scope": ["https://graph.microsoft.com/.default"],
}
```

# <a name="java"></a>[Java](#tab/java)

```Java
final static String GRAPH_DEFAULT_SCOPE = "https://graph.microsoft.com/.default";
```

---

### <a name="azure-ad-v10-resources"></a>Azure AD-bronnen (v1.0)

Het bereik dat wordt gebruikt voor clientreferenties `/.default`moet altijd de resource-id zijn, gevolgd door .

> [!IMPORTANT]
> Wanneer MSAL een toegangstoken aanvraagt voor een bron die een versie 1.0-toegangstoken accepteert, ontleedt Azure AD het gewenste publiek uit het gevraagde bereik door alles voor de laatste slash te nemen en deze te gebruiken als resource-id.
> Dus als de bron, net als Azure SQL Database **(https:\//database.windows.net),** een `https://database.windows.net/`doelgroep verwacht die eindigt met `https://database.windows.net//.default`een slash (voor Azure SQL Database), moet u een bereik van . (Let op de dubbele slash.) Zie ook MSAL.NET probleem [#747: De trailing slash van de resourceurl wordt weggelaten, waardoor sql auth-fouten zijn mislukt.](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747)

## <a name="acquiretokenforclient-api"></a>OvernameTokenForClient API

Als u een token voor de `AcquireTokenForClient` app wilt aanschaffen, gebruikt u of het equivalent ervan, afhankelijk van het platform.

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
using Microsoft.Identity.Client;

// With client credentials flows, the scope is always of the shape "resource/.default" because the
// application permissions need to be set statically (in the portal or by PowerShell), and then granted by
// a tenant administrator.
string[] scopes = new string[] { "https://graph.microsoft.com/.default" };

AuthenticationResult result = null;
try
{
 result = await app.AcquireTokenForClient(scopes)
                  .ExecuteAsync();
}
catch (MsalUiRequiredException ex)
{
    // The application doesn't have sufficient permissions.
    // - Did you declare enough app permissions during app creation?
    // - Did the tenant admin grant permissions to the application?
}
catch (MsalServiceException ex) when (ex.Message.Contains("AADSTS70011"))
{
    // Invalid scope. The scope has to be in the form "https://resourceurl/.default"
    // Mitigation: Change the scope to be as expected.
}
```

# <a name="python"></a>[Python](#tab/python)

```Python
# The pattern to acquire a token looks like this.
result = None

# First, the code looks up a token from the cache.
# Because we're looking for a token for the current app, not for a user,
# use None for the account parameter.
result = app.acquire_token_silent(config["scope"], account=None)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    result = app.acquire_token_for_client(scopes=config["scope"])

if "access_token" in result:
    # Call a protected API with the access token.
    print(result["token_type"])
else:
    print(result.get("error"))
    print(result.get("error_description"))
    print(result.get("correlation_id"))  # You might need this when reporting a bug.
```

# <a name="java"></a>[Java](#tab/java)

Deze code wordt gewonnen uit de [MSAL Java dev monsters.](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/confidential-client/)

```Java
private static IAuthenticationResult acquireToken() throws Exception {

     // Load token cache from file and initialize token cache aspect. The token cache will have
     // dummy data, so the acquireTokenSilently call will fail.
     TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

     IClientCredential credential = ClientCredentialFactory.createFromSecret(CLIENT_SECRET);
     ConfidentialClientApplication cca =
             ConfidentialClientApplication
                     .builder(CLIENT_ID, credential)
                     .authority(AUTHORITY)
                     .setTokenCacheAccessAspect(tokenCacheAspect)
                     .build();

     IAuthenticationResult result;
     try {
         SilentParameters silentParameters =
                 SilentParameters
                         .builder(SCOPE)
                         .build();

         // try to acquire token silently. This call will fail since the token cache does not
         // have a token for the application you are requesting an access token for
         result = cca.acquireTokenSilently(silentParameters).join();
     } catch (Exception ex) {
         if (ex.getCause() instanceof MsalException) {

             ClientCredentialParameters parameters =
                     ClientCredentialParameters
                             .builder(SCOPE)
                             .build();

             // Try to acquire a token. If successful, you should see
             // the token information printed out to console
             result = cca.acquireToken(parameters).join();
         } else {
             // Handle other exceptions accordingly
             throw ex;
         }
     }
     return result;
 }
```

---

### <a name="protocol"></a>Protocol

Als u nog geen bibliotheek voor de door u gekozen taal hebt, u het protocol rechtstreeks gebruiken:

#### <a name="first-case-access-the-token-request-by-using-a-shared-secret"></a>Eerste geval: Toegang tot het tokenverzoek met behulp van een gedeeld geheim

```HTTP
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity.
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

#### <a name="second-case-access-the-token-request-by-using-a-certificate"></a>Tweede aanvraag: Toegang tot de tokenaanvraag met behulp van een certificaat

```HTTP
POST /{tenant}/oauth2/v2.0/token HTTP/1.1               // Line breaks for clarity.
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&grant_type=client_credentials
```

Zie voor meer informatie de protocoldocumentatie: [Microsoft-identiteitsplatform en de stroom van de gegevens van de OAuth 2.0-clientreferenties](v2-oauth2-client-creds-grant-flow.md).

## <a name="application-token-cache"></a>Cache voor toepassingstoken

In MSAL.NET `AcquireTokenForClient` gebruikt u de cache van het toepassingstoken. (Alle andere AcquireToken*XX-methoden* gebruiken de cache van het gebruikerstoken.) Bel niet `AcquireTokenSilent` voordat u `AcquireTokenForClient`belt, omdat `AcquireTokenSilent` u de cache van het *gebruikerstoken* gebruikt. `AcquireTokenForClient`controleert de cache van het *toepassingstoken* zelf en werkt deze bij.

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="did-you-use-the-resourcedefault-scope"></a>Heeft u de resource/.default scope gebruikt?

Als u een foutbericht ontvangt waarin staat dat u een ongeldige scope hebt gebruikt, hebt u het `resource/.default` bereik waarschijnlijk niet gebruikt.

### <a name="did-you-forget-to-provide-admin-consent-daemon-apps-need-it"></a>Bent u vergeten toestemming van de beheerder te geven? Daemon apps hebben het nodig!

Als u onvoldoende bevoegdheden krijgt **om de bewerkingsfout te voltooien** wanneer u de API aanroept, moet de tenantbeheerder machtigingen verlenen aan de toepassing. Zie stap 6 van Registreer de client-app hierboven.
U ziet meestal een fout die lijkt op deze fout:

```json
Failed to call the web API: Forbidden
Content: {
  "error": {
    "code": "Authorization_RequestDenied",
    "message": "Insufficient privileges to complete the operation.",
    "innerError": {
      "request-id": "<guid>",
      "date": "<date>"
    }
  }
}
```

## <a name="next-steps"></a>Volgende stappen

# <a name="net"></a>[.NET](#tab/dotnet)

> [!div class="nextstepaction"]
> [Daemon-app - een web-API aanroepen](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-call-api?tabs=dotnet)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Daemon-app - een web-API aanroepen](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-call-api?tabs=python)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Daemon-app - een web-API aanroepen](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-call-api?tabs=java)

---
