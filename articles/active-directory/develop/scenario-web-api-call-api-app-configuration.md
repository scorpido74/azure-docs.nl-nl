---
title: Een web-API configureren die web-API's aanroept | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het maken van een web-API die web-API's aanroept (de codeconfiguratie van de app)
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
ms.openlocfilehash: 4129f1a89575c9a0e7cd6a0090168df659356c1b
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885103"
---
# <a name="a-web-api-that-calls-web-apis-code-configuration"></a>Een web-API die web-API's aanroept: codeconfiguratie

Nadat u uw web-API hebt geregistreerd, u de code voor de toepassing configureren.

De code die u gebruikt om uw web-API zo te configureren dat deze downstream web-API's aanroept, bouwt bovenop de code die wordt gebruikt om een web-API te beschermen. Zie [Beveiligde web-API: App-configuratie voor](scenario-protected-web-api-app-configuration.md)meer informatie.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

## <a name="code-subscribed-to-ontokenvalidated"></a>Code geabonneerd op OnTokenValidated

Naast de codeconfiguratie voor beveiligde web-API's, moet u zich abonneren op de validatie van het token aan toonder dat u ontvangt wanneer uw API wordt aangeroepen:

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

## <a name="on-behalf-of-flow"></a>Namens de stroom

De methode AddAccountToCacheFromJwt() moet:

- Een vertrouwelijke clienttoepassing van de Microsoft Authentication Library (MSAL) instantiëren.
- Roep `AcquireTokenOnBehalf` de methode. Deze oproep wisselt het token aan toonder dat door de client voor de web-API is overgenomen, tegen een token aan toonder voor dezelfde gebruiker, maar het heeft de API-aanroep een downstream-API.

### <a name="instantiate-a-confidential-client-application"></a>Instantiate een vertrouwelijke client applicatie

Deze stroom is alleen beschikbaar in de vertrouwelijke clientstroom, zodat de beveiligde web-API clientreferenties (clientgeheim `WithClientSecret` of `WithCertificate` certificaat) via de klasse of methode biedt aan de [klasse ConfidentialClientApplicationBuilder.](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder)

![Lijst met iConfidentialClientApplication-methoden](https://user-images.githubusercontent.com/13203188/55967244-3d8e1d00-5c7a-11e9-8285-a54b05597ec9.png)

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

Ten slotte, in plaats van het bewijzen van hun identiteit via een klant geheim of een certificaat, vertrouwelijke client toepassingen kunnen hun identiteit te bewijzen met behulp van client beweringen.
Zie [Vertrouwelijke clientbeweringen voor](msal-net-client-assertions.md)meer informatie over dit geavanceerde scenario.

### <a name="how-to-call-on-behalf-of"></a>Hoe te bellen Op-Behalf-Of

U doet de On-Behalf-Of (OBO) aanroep door de `IConfidentialClientApplication` [AcquireTokenOnBehalf-methode](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenonbehalfofparameterbuilder) op de interface aan te roepen.

De `UserAssertion` klasse is opgebouwd uit het token aan toonder dat door de web-API van zijn eigen clients wordt ontvangen. Er zijn [twee constructeurs:](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientcredential.-ctor?view=azure-dotnet)
* Een die een JSON Web Token (JWT) toonder token neemt
* Een die elke vorm van bewering van de gebruiker, een ander soort beveiligingstoken, waarvan het type wordt vervolgens opgegeven in een extra parameter met de naam`assertionType`

![Eigenschappen en methoden voor UserAssertion](https://user-images.githubusercontent.com/13203188/37082180-afc4b708-21e3-11e8-8af8-a6dcbd2dfba8.png)

In de praktijk wordt de OBO-stroom vaak gebruikt om een token voor een downstream-API te verkrijgen en op te slaan in de MSAL.NET user token cache. U doet dit zodat andere delen van de web-API ``AcquireTokenOnSilent`` later een beroep kunnen doen op de [overschrijvingen](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientapplicationbase.acquiretokensilent?view=azure-dotnet) om de downstream API's te bellen. Deze oproep heeft het effect van het vernieuwen van de tokens, indien nodig.

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

De OBO-stroom (Namens-rekening) wordt gebruikt om een token te verkrijgen om de downstream web-API aan te roepen. In deze stroom ontvangt uw web-API een token aan toonder met door de gebruiker gedelegeerde machtigingen van de clienttoepassing en wisselt dit token vervolgens om voor een ander toegangstoken om de downstream web-API aan te roepen.

De onderstaande code maakt `SecurityContextHolder` gebruik van Spring Security framework's in de web-API om het gevalideerde token aan toonder te krijgen. Vervolgens gebruikt het de MSAL Java-bibliotheek om `acquireToken` een `OnBehalfOfParameters`token voor downstream-API te verkrijgen met behulp van de aanroep met . MSAL caches het token, zodat latere `acquireTokenSilently` oproepen naar de API kunnen gebruiken om de cache token te krijgen.

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

De OBO-stroom (Namens-rekening) wordt gebruikt om een token te verkrijgen om de downstream web-API aan te roepen. In deze stroom ontvangt uw web-API een token aan toonder met door de gebruiker gedelegeerde machtigingen van de clienttoepassing en wisselt dit token vervolgens om voor een ander toegangstoken om de downstream web-API aan te roepen.

Een Python-web-API moet een aantal middleware gebruiken om het token aan toonder dat van de client is ontvangen, te valideren. De web-API kan vervolgens het toegangstoken voor downstream-API [`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) verkrijgen met behulp van MSAL Python-bibliotheek door de methode aan te roepen. Een voorbeeld dat deze stroom met MSAL Python aantoont, is nog niet beschikbaar.

---

U ook een voorbeeld zien van de implementatie van de OBO-stroom in [Node.js en Azure-functies.](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions/blob/master/MiddleTierAPI/MyHttpTrigger/index.js#L61)

## <a name="protocol"></a>Protocol

Zie Microsoft Identity Platform en [OAuth 2.0 On-Behalf-Of flow](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)voor meer informatie over het OBO-protocol.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een web-API die web-API's aanroept: een token voor de app aanschaffen](scenario-web-api-call-api-acquire-token.md)
