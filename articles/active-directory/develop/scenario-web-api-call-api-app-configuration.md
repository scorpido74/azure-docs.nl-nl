---
title: Een web-API configureren die web-Api's aanroept | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het bouwen van een web-API die web-Api's aanroept (de code configuratie van de app)
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
ms.date: 07/16/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: dd38cb58e6e6db9767be9adb8f299107601de580
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701770"
---
# <a name="a-web-api-that-calls-web-apis-code-configuration"></a>Een web-API die web-Api's aanroept: code configuratie

Nadat u uw web-API hebt geregistreerd, kunt u de code voor de toepassing configureren.

De code die u gebruikt om uw web-API te configureren, zodat downstream Web-Api's worden aangeroepen boven op de code die wordt gebruikt om een web-API te beveiligen. Zie [Protected Web API: app Configuration](scenario-protected-web-api-app-configuration.md)(Engelstalig) voor meer informatie.

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

Deze stroom is alleen beschikbaar in de vertrouwelijke client stroom, zodat de beveiligde web-API client referenties (client Secret of Certificate) aan de [klasse ConfidentialClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder) verstrekt via de methode `WithClientSecret` of `WithCertificate`.

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

U maakt de aanroepen van de OBO-aanroep door de [AcquireTokenOnBehalf-methode](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenonbehalfofparameterbuilder) aan te roepen op de `IConfidentialClientApplication`-interface.

De klasse `UserAssertion` is gebaseerd op het Bearer-token dat door de Web-API van de eigen clients wordt ontvangen. Er zijn [twee constructors](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientcredential.-ctor?view=azure-dotnet):
* Een JSON Web Token (JWT) Bearer-token
* Een voor elk soort gebruikers Assertie, een ander soort beveiligings token, waarvan het type wordt opgegeven in een extra para meter met de naam `assertionType`

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

U kunt ook een voor beeld van een OBO-stroom implementatie bekijken in [node. js en Azure functions](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions/blob/master/MiddleTierAPI/MyHttpTrigger/index.js#L61).

## <a name="protocol"></a>Protocol

Zie voor meer informatie over het OBO-protocol [micro soft Identity platform en OAuth 2,0-of-flow](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow).

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een web-API die web-Api's aanroept: een Token ophalen voor de app](scenario-web-api-call-api-acquire-token.md)
