---
title: Een web-API configureren die web-Api's aanroept | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het bouwen van een web-API die web-Api's aanroept (de code configuratie van de app)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/05/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: e9faea3462ae953e474b5053b651808b03f07c23
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/25/2020
ms.locfileid: "88855454"
---
# <a name="a-web-api-that-calls-web-apis-code-configuration"></a>Een web-API die web-Api's aanroept: code configuratie

Nadat u uw web-API hebt geregistreerd, kunt u de code voor de toepassing configureren.

De code die u gebruikt om uw web-API te configureren, zodat downstream Web-Api's worden aangeroepen boven op de code die wordt gebruikt om een web-API te beveiligen. Zie [Protected Web API: app Configuration](scenario-protected-web-api-app-configuration.md)(Engelstalig) voor meer informatie.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

## <a name="client-secrets-or-client-certificates"></a>Client geheimen of client certificaten

Gezien dat uw web-API nu een stroomafwaartse Web-API aanroept, moet u een client geheim of client certificaat opgeven in de *appsettings.jsin* het bestand.

```JSON
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    "TenantId": "common"
  
   // To call an API
   "ClientSecret": "[Copy the client secret added to the app from the Azure portal]",
   "ClientCertificates": [
  ]
 }
}
```

In plaats van een client geheim kunt u een client certificaat opgeven. Het volgende code fragment toont het gebruik van een certificaat dat is opgeslagen in Azure Key Vault.

```JSON
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    "TenantId": "common"
  
   // To call an API
   "ClientCertificates": [
      {
        "SourceType": "KeyVault",
        "KeyVaultUrl": "https://msidentitywebsamples.vault.azure.net",
        "KeyVaultCertificateName": "MicrosoftIdentitySamplesCert"
      }
  ]
 }
}
```

Micro soft. Identity. Web biedt verschillende manieren om certificaten te beschrijven, hetzij per configuratie of per code. Zie voor meer informatie [micro soft. Identity. Web wiki-certificaten gebruiken](https://github.com/AzureAD/microsoft-identity-web/wiki/Using-certificates) op github.

## <a name="startupcs"></a>Startup.cs

Gebruik micro soft. Identity. web als u wilt dat uw web-API downstream Web-Api's aanroept, voeg de `.EnableTokenAcquisitionToCallDownstreamApi()` regel na `.AddMicrosoftIdentityWebApi(Configuration)` en kies vervolgens een implementatie van de token cache, bijvoorbeeld `.AddInMemoryTokenCaches()` in *Startup.cs*:

```csharp
using Microsoft.Identity.Web;

public class Startup
{
  ...
  public void ConfigureServices(IServiceCollection services)
  {
   // ...
    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
            .AddMicrosoftIdentityWebApi(Configuration, "AzureAd")
                .EnableTokenAcquisitionToCallDownstreamApi()
                .AddInMemoryTokenCaches();
  // ...
  }
  // ...
}
```

Net als bij Web apps, kunt u verschillende implementaties van de token-cache kiezen. Zie voor meer informatie [micro soft Identity Web wiki-token cache serialisatie](https://aka.ms/ms-id-web/token-cache-serialization) op github.

# <a name="java"></a>[Java](#tab/java)

De stroom namens-of (OBO) wordt gebruikt om een token op te halen voor het aanroepen van de stroomafwaartse Web-API. In deze stroom ontvangt uw web-API een Bearer-token met door de gebruiker gedelegeerde machtigingen van de client toepassing. vervolgens wordt dit token door gegeven aan een ander toegangs token om de stroomafwaartse Web-API aan te roepen.

De onderstaande code maakt gebruik van Lente Security Framework `SecurityContextHolder` in de Web-API om het gevalideerde Bearer-token op te halen. Vervolgens wordt de MSAL Java-bibliotheek gebruikt voor het verkrijgen van een token voor de downstream API met behulp van de `acquireToken` aanroep van `OnBehalfOfParameters` . Met MSAL wordt het token in de cache opgeslagen, zodat de volgende aanroepen naar de API kunnen `acquireTokenSilently` worden gebruikt om het token in de cache op te halen.

```Java
@Component
class MsalAuthHelper {

    @Value("${security.oauth2.client.authority}")
    private String authority;

    @Value("${security.oauth2.client.client-id}")
    private String clientId;

    @Value("${security.oauth2.client.client-secret}")
    private String secret;

    @Autowired
    CacheManager cacheManager;

    private String getAuthToken(){
        String res = null;
        Authentication authentication = SecurityContextHolder.getContext().getAuthentication();

        if(authentication != null){
            res = ((OAuth2AuthenticationDetails) authentication.getDetails()).getTokenValue();
        }
        return res;
    }

    String getOboToken(String scope) throws MalformedURLException {
        String authToken = getAuthToken();

        ConfidentialClientApplication application =
                ConfidentialClientApplication.builder(clientId, ClientCredentialFactory.create(secret))
                        .authority(authority).build();

        String cacheKey = Hashing.sha256()
                .hashString(authToken, StandardCharsets.UTF_8).toString();

        String cachedTokens = cacheManager.getCache("tokens").get(cacheKey, String.class);
        if(cachedTokens != null){
            application.tokenCache().deserialize(cachedTokens);
        }

        IAuthenticationResult auth;
        SilentParameters silentParameters =
                SilentParameters.builder(Collections.singleton(scope))
                        .build();
        auth = application.acquireTokenSilently(silentParameters).join();

        if (auth == null){
            OnBehalfOfParameters parameters =
                    OnBehalfOfParameters.builder(Collections.singleton(scope),
                            new UserAssertion(authToken))
                            .build();

            auth = application.acquireToken(parameters).join();
        }

        cacheManager.getCache("tokens").put(cacheKey, application.tokenCache().serialize());

        return auth.accessToken();
    }
}
```

# <a name="python"></a>[Python](#tab/python)

De stroom namens-of (OBO) wordt gebruikt om een token op te halen voor het aanroepen van de stroomafwaartse Web-API. In deze stroom ontvangt uw web-API een Bearer-token met door de gebruiker gedelegeerde machtigingen van de client toepassing. vervolgens wordt dit token door gegeven aan een ander toegangs token om de stroomafwaartse Web-API aan te roepen.

Een python-Web-API moet een middleware gebruiken om het Bearer-token te valideren dat van de client is ontvangen. De Web-API kan vervolgens het toegangs token voor downstream API verkrijgen met behulp van de MSAL python-bibliotheek door de methode aan te roepen [`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) . Voor een voor beeld van het gebruik van deze API raadpleegt u de [test code voor micro soft-Authentication-library-for-python op github](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.2.0/tests/test_e2e.py#L429-L472). Zie ook de bespreking van [probleem 53](https://github.com/AzureAD/microsoft-authentication-library-for-python/issues/53) in diezelfde opslag plaats voor een benadering die de nood zaak van een toepassing voor een middelste laag niet meer hoeft te omzeilen.

---

U kunt ook een voor beeld van een OBO-stroom implementatie bekijken in [Node.js en Azure functions](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions/blob/master/MiddleTierAPI/MyHttpTrigger/index.js#L61).

## <a name="protocol"></a>Protocol

Zie voor meer informatie over het OBO-protocol [micro soft Identity platform en OAuth 2,0-of-flow](./v2-oauth2-on-behalf-of-flow.md).

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een web-API die web-Api's aanroept: een Token ophalen voor de app](scenario-web-api-call-api-acquire-token.md)