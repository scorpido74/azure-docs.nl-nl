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
ms.date: 07/16/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 38e319efb100d326d55f6f821e7c903306a7c7d0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "80991004"
---
# <a name="a-web-api-that-calls-web-apis-code-configuration"></a>Een web-API die web-Api's aanroept: code configuratie

Nadat u uw web-API hebt geregistreerd, kunt u de code voor de toepassing configureren.

De code die u gebruikt om uw web-API te configureren, zodat downstream Web-Api's worden aangeroepen boven op de code die wordt gebruikt om een web-API te beveiligen. Zie [Protected Web API: app Configuration](scenario-protected-web-api-app-configuration.md)(Engelstalig) voor meer informatie.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

## <a name="code-subscribed-to-ontokenvalidated"></a>Code geabonneerd op OnTokenValidated

Boven op de code configuratie voor beveiligde web-Api's moet u zich abonneren op de validatie van het Bearer-token dat u ontvangt wanneer uw API wordt aangeroepen:

```csharp
/// <summary>
/// Protects the web API with the Microsoft identity platform, or Azure Active Directory (Azure AD) developer platform
/// This supposes that the configuration files have a section named "AzureAD"
/// </summary>
/// <param name="services">The service collection to which to add authentication</param>
/// <param name="configuration">Configuration</param>
/// <returns></returns>
public static IServiceCollection AddProtectedApiCallsWebApis(this IServiceCollection services,
                                                             IConfiguration configuration,
                                                             IEnumerable<string> scopes)
{
    services.AddTokenAcquisition();
    services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        // When an access token for our own web API is validated, we add it
        // to the MSAL.NET cache so that it can be used from the controllers.
        options.Events = new JwtBearerEvents();

        options.Events.OnTokenValidated = async context =>
        {
            context.Success();

            // Adds the token to the cache and handles the incremental consent
            // and claim challenges
            AddAccountToCacheFromJwt(context, scopes);
            await Task.FromResult(0);
        };
    });
    return services;
}
```

## <a name="on-behalf-of-flow"></a>Namens-stroom

De methode AddAccountToCacheFromJwt () moet:

- Exemplaar een vertrouwelijke client toepassing van micro soft Authentication Library (MSAL).
- Roep de `AcquireTokenOnBehalf` methode aan. Deze aanroep is een uitwisseling van het Bearer-token dat is verkregen door de client voor de Web-API op basis van een Bearer-token voor dezelfde gebruiker, maar heeft de API-aanroep een downstream API.

### <a name="instantiate-a-confidential-client-application"></a>Een vertrouwelijke client toepassing instantiëren

Deze stroom is alleen beschikbaar in de vertrouwelijke client stroom, zodat de beveiligde web-API client referenties (client Secret of Certificate) aan de [ConfidentialClientApplicationBuilder-klasse](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder) verstrekt via de `WithClientSecret` or- `WithCertificate` methode.

![Lijst met IConfidentialClientApplication-methoden](https://user-images.githubusercontent.com/13203188/55967244-3d8e1d00-5c7a-11e9-8285-a54b05597ec9.png)

```csharp
IConfidentialClientApplication app;

#if !VariationWithCertificateCredentials
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
           .WithClientSecret(config.ClientSecret)
           .Build();
#else
// Building the client credentials from a certificate
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
    .WithCertificate(certificate)
    .Build();
#endif
```

Ten slotte, in plaats van de identiteit te bewijzen via een client geheim of een certificaat, kunnen vertrouwelijke client toepassingen hun identiteit bewijzen door gebruik te maken van client verklaringen.
Zie voor meer informatie over dit geavanceerde scenario [vertrouwelijke client verklaringen](msal-net-client-assertions.md).

### <a name="how-to-call-on-behalf-of"></a>Namens aanroepen

U kunt de OBO-aanroep (on-naam) uitvoeren door de [methode AcquireTokenOnBehalf](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenonbehalfofparameterbuilder) op de interface aan te roepen `IConfidentialClientApplication` .

De `UserAssertion` klasse is gebaseerd op het Bearer-token dat door de Web-API van de eigen clients wordt ontvangen. Er zijn [twee constructors](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientcredential.-ctor?view=azure-dotnet):
* Een JSON Web Token (JWT) Bearer-token
* Een van de verschillende soorten gebruikers verklaringen, een ander soort beveiligings token, waarvan het type wordt opgegeven in een extra para meter met de naam`assertionType`

![UserAssertion-eigenschappen en-methoden](https://user-images.githubusercontent.com/13203188/37082180-afc4b708-21e3-11e8-8af8-a6dcbd2dfba8.png)

In de praktijk wordt de OBO-stroom vaak gebruikt voor het verkrijgen van een token voor een downstream API en deze op te slaan in de cache van de MSAL.NET-gebruikers token. U doet dit zodat andere onderdelen van de Web-API later kunnen worden aangeroepen op de [onderdrukkingen](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientapplicationbase.acquiretokensilent?view=azure-dotnet) van ``AcquireTokenOnSilent`` om de downstream-api's aan te roepen. Deze aanroep heeft gevolgen voor het vernieuwen van de tokens, indien nodig.

```csharp
private void AddAccountToCacheFromJwt(IEnumerable<string> scopes, JwtSecurityToken jwtToken, ClaimsPrincipal principal, HttpContext httpContext)
{
    try
    {
        UserAssertion userAssertion;
        IEnumerable<string> requestedScopes;
        if (jwtToken != null)
        {
            userAssertion = new UserAssertion(jwtToken.RawData, "urn:ietf:params:oauth:grant-type:jwt-bearer");
            requestedScopes = scopes ?? jwtToken.Audiences.Select(a => $"{a}/.default");
        }
        else
        {
            throw new ArgumentOutOfRangeException("tokenValidationContext.SecurityToken should be a JWT Token");
        }

        // Create the application
        var application = BuildConfidentialClientApplication(httpContext, principal);

        // .Result to make sure that the cache is filled in before the controller tries to get access tokens
        var result = application.AcquireTokenOnBehalfOf(requestedScopes.Except(scopesRequestedByMsalNet),
                                                        userAssertion)
                                .ExecuteAsync()
                                .GetAwaiter().GetResult();
     }
     catch (MsalException ex)
     {
         Debug.WriteLine(ex.Message);
         throw;
     }
}
```
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

Zie voor meer informatie over het OBO-protocol [micro soft Identity platform en OAuth 2,0-of-flow](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow).

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een web-API die web-Api's aanroept: een Token ophalen voor de app](scenario-web-api-call-api-acquire-token.md)
