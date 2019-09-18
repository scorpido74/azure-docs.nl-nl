---
title: Daemon-app die web-Api's aanroept (tokens ophalen voor de app)-micro soft Identity-platform
description: Meer informatie over het bouwen van een daemon-app die web-Api's aanroept (tokens ophalen)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/15/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef28520edd8500be0da52996e6484a0407fb03c8
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71056444"
---
# <a name="daemon-app-that-calls-web-apis---acquire-a-token"></a>Daemon-app die web-Api's aanroept-een Token ophalen

Zodra de vertrouwelijke client toepassing is gebouwd, kunt u een token voor de app verkrijgen door het ``AcquireTokenForClient``bereik aan te roepen, door te geven en het token te forceren of niet te vernieuwen.

## <a name="scopes-to-request"></a>Te aanvragen scopes

Het bereik dat moet worden aangevraagd voor een client referentie stroom is de naam van de resource `/.default`gevolgd door. Deze notatie vertelt Azure AD voor het gebruik van de **machtigingen op toepassings niveau** die statisch zijn gedeclareerd tijdens de registratie van de toepassing. Net zoals eerder gezien, moeten deze API-machtigingen worden verleend door een Tenant beheerder

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

```CSharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

In MSAL. Python, het configuratie bestand zou eruit zien als het volgende code fragment:

```Json
{
    "scope": ["https://graph.microsoft.com/.default"],
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

```Java
final static String GRAPH_DEFAULT_SCOPE = "https://graph.microsoft.com/.default";
```

---

### <a name="case-of-azure-ad-v10-resources"></a>Het geval van Azure AD (v 1.0)-resources

Het bereik dat wordt gebruikt voor client referenties moet altijd resourceId + "/.default" zijn

> [!IMPORTANT]
> Voor MSAL waarbij een toegangs token wordt gevraagd voor een resource die een v 1.0-toegangs token accepteert, parseert Azure AD de gewenste doel groep uit het aangevraagde bereik door alles vóór de laatste slash te nemen en deze als resource-id te gebruiken.
> Als, zoals Azure SQL ( **https://database.windows.net** ) voor de resource een doel groep verwacht die eindigt met een slash (voor Azure SQL: `https://database.windows.net/` ), moet u daarom een bereik van `https://database.windows.net//.default` aanvragen (Let op de dubbele slash). Zie ook MSAL.NET issue [#747](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747): De afsluitende slash van de bron-URL wordt wegge laten, waardoor SQL-verificatie is mislukt.

## <a name="acquiretokenforclient-api"></a>AcquireTokenForClient API

Voor het verkrijgen van een token voor de app gebruikt `AcquireTokenForClient` u of het equivalent, afhankelijk van de platformen.

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

```CSharp
using Microsoft.Identity.Client;

// With client credentials flows the scopes is ALWAYS of the shape "resource/.default", as the
// application permissions need to be set statically (in the portal or by PowerShell), and then granted by
// a tenant administrator
string[] scopes = new string[] { "https://graph.microsoft.com/.default" };

AuthenticationResult result = null;
try
{
 result = await app.AcquireTokenForClient(scopes)
                  .ExecuteAsync();
}
catch (MsalUiRequiredException ex)
{
    // The application does not have sufficient permissions
    // - did you declare enough app permissions in during the app creation?
    // - did the tenant admin needs to grant permissions to the application.
}
catch (MsalServiceException ex) when (ex.Message.Contains("AADSTS70011"))
{
    // Invalid scope. The scope has to be of the form "https://resourceurl/.default"
    // Mitigation: change the scope to be as expected !
}
```

# <a name="pythontabpython"></a>[Python](#tab/python)

```Python
# The pattern to acquire a token looks like this.
result = None

# Firstly, looks up a token from cache
# Since we are looking for token for the current app, NOT for an end user,
# notice we give account parameter as None.
result = app.acquire_token_silent(config["scope"], account=None)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    result = app.acquire_token_for_client(scopes=config["scope"])

if "access_token" in result:
    # Call a protected API with the access token
    print(result["token_type"])
    print(result["expires_in"])  # You don't normally need to care about this.
                                 # It will be good for at least 5 minutes.
else:
    print(result.get("error"))
    print(result.get("error_description"))
    print(result.get("correlation_id"))  # You may need this when reporting a bug
```

# <a name="javatabjava"></a>[Java](#tab/java)

```Java
ClientCredentialParameters clientCredentialParam = ClientCredentialParameters.builder(
        Collections.singleton(GRAPH_DEFAULT_SCOPE))
        .build();

CompletableFuture<IAuthenticationResult> future = app.acquireToken(clientCredentialParam);

BiConsumer<IAuthenticationResult, Throwable> processAuthResult = (res, ex) -> {
    if (ex != null) {
        System.out.println("Oops! We have an exception - " + ex.getMessage());
    }
    System.out.println("Returned ok - " + res);
    System.out.println("ID Token - " + res.idToken());

    /* call a protected API with res.accessToken() */
};

future.whenCompleteAsync(processAuthResult);
future.join();
```

---

### <a name="protocol"></a>Protocol

Als u nog geen bibliotheek hebt voor de taal van uw keuze, kunt u het protocol het beste rechtstreeks gebruiken:

#### <a name="first-case-access-token-request-with-a-shared-secret"></a>Eerste geval: Toegangs token aanvraag met een gedeeld geheim

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

#### <a name="second-case-access-token-request-with-a-certificate"></a>Tweede geval: Toegangs token aanvraag met een certificaat

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1               // Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&grant_type=client_credentials
```

Zie de protocol documentatie voor meer informatie: [Micro soft Identity platform en de OAuth 2,0-client referenties stroom](v2-oauth2-client-creds-grant-flow.md).

## <a name="application-token-cache"></a>Toepassings token cache

In MSAL.net `AcquireTokenForClient` maakt gebruik van de **toepassings token cache** (alle andere AcquireTokenXX-methoden gebruiken de token cache van de gebruiker `AcquireTokenSilent` ) worden `AcquireTokenForClient` niet `AcquireTokenSilent` aangeroepen voordat aanroepen als gebruikt de token cache van de **gebruiker** . `AcquireTokenForClient`Hiermee wordt de cache van het **toepassings** token zelf gecontroleerd en bijgewerkt.

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="did-you-use-the-resourcedefault-scope"></a>Hebt u het resource/.-standaard bereik gebruikt?

Als er een fout bericht wordt weer gegeven met de melding dat u een ongeldig bereik hebt gebruikt, hebt `resource/.default` u waarschijnlijk geen gebruik gemaakt van het bereik.

### <a name="did-you-forget-to-provide-admin-consent-daemon-apps-need-it"></a>Bent u verg eten om beheerders toestemming te geven? Daemon-apps zijn vereist!

Als er een fout optreedt bij het aanroepen van de API **onvoldoende bevoegdheden om de bewerking te volt ooien**, moet de Tenant beheerder machtigingen verlenen voor de toepassing. Zie stap 6 van de bovenstaande client-app registreren.
Normaal gesp roken ziet u een fout, zoals de volgende fout beschrijving:

```JSon
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

> [!div class="nextstepaction"]
> [Daemon-app: een web-API aanroepen](scenario-daemon-call-api.md)