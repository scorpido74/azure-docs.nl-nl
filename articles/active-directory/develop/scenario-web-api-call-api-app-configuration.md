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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 219724186e3fa69fec35e89435af495b662c871d
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74919746"
---
# <a name="web-api-that-calls-web-apis---code-configuration"></a>Web-API voor het aanroepen van web-Api's-code configuratie

Nadat u uw web-API hebt geregistreerd, kunt u de code voor de toepassing configureren.

De code voor het configureren van uw web-API zodat deze downstream Web-Api's aanroept boven op de code die wordt gebruikt om een web-API te beveiligen. Zie [Protected Web API-app Configuration (](scenario-protected-web-api-app-configuration.md)Engelstalig) voor meer informatie.

## <a name="code-subscribed-to-ontokenvalidated"></a>Code geabonneerd op OnTokenValidated

Boven op de code configuratie voor beveiligde web-Api's moet u zich abonneren op de validatie van het Bearer-token dat wordt ontvangen wanneer uw API wordt aangeroepen:

```CSharp
/// <summary>
/// Protects the web API with Microsoft Identity Platform (a.k.k AAD v2.0)
/// This supposes that the configuration files have a section named "AzureAD"
/// </summary>
/// <param name="services">Service collection to which to add authentication</param>
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
        // to MSAL.NET's cache so that it can be used from the controllers.
        options.Events = new JwtBearerEvents();

        options.Events.OnTokenValidated = async context =>
        {
            context.Success();

            // Adds the token to the cache, and also handles the incremental consent 
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

- Instantiëren van een MSAL vertrouwelijke client toepassing.
- Roep `AcquireTokenOnBehalf` aan voor het uitwisselen van het Bearer-token dat is verkregen door de client voor de Web-API, op basis van een Bearer-token voor dezelfde gebruiker, maar voor onze API om een downstream API aan te roepen.

### <a name="instantiate-a-confidential-client-application"></a>Een vertrouwelijke client toepassing instantiëren

Deze stroom is alleen beschikbaar in de vertrouwelijke client stroom, zodat de beveiligde web-API client referenties (client Secret of Certificate) naar de [ConfidentialClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder) via de methoden `WithClientSecret` of `WithCertificate` verschaft.

![image](https://user-images.githubusercontent.com/13203188/55967244-3d8e1d00-5c7a-11e9-8285-a54b05597ec9.png)

```CSharp
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

Ten slotte, in plaats van een client geheim of een certificaat, kunnen vertrouwelijke client toepassingen ook hun identiteit bewijzen met behulp van client verklaringen.
Dit geavanceerde scenario wordt beschreven in [client verklaringen](msal-net-client-assertions.md)

### <a name="how-to-call-on-behalf-of"></a>Namens aanroepen

De aanroep namens een (OBO) wordt uitgevoerd door de [AcquireTokenOnBehalf](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenonbehalfofparameterbuilder) -methode aan te roepen op de `IConfidentialClientApplication`-interface.

De `UserAssertion` is gebaseerd op het Bearer-token dat is ontvangen door de Web-API van de eigen clients. Er zijn [twee constructors](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientcredential.-ctor?view=azure-dotnet): één die een JWT Bearer-token gebruikt, en een van de verschillende soorten gebruikers verklaringen (een ander type beveiligings token, dat vervolgens wordt opgegeven in een extra para meter met de naam `assertionType`).

![image](https://user-images.githubusercontent.com/13203188/37082180-afc4b708-21e3-11e8-8af8-a6dcbd2dfba8.png)

In de praktijk wordt de OBO-stroom vaak gebruikt voor het verkrijgen van een token voor een downstream API en deze op te slaan in de cache van de MSAL.NET-gebruikers token, zodat andere onderdelen van de Web-API later kunnen worden aangeroepen op de [onderdrukkingen](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientapplicationbase.acquiretokensilent?view=azure-dotnet) van ``AcquireTokenOnSilent`` om de downstream-api's aan te roepen. Deze aanroep heeft gevolgen voor het vernieuwen van de tokens, indien nodig.

```CSharp
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

        // .Result to make sure that the cache is filled-in before the controller tries to get access tokens
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

U kunt ook een voor beeld zien van namens de stroom implementatie in [NodeJS en Azure functions](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions/blob/master/MiddleTierAPI/MyHttpTrigger/index.js#L61).

## <a name="protocol"></a>Protocol

Zie voor meer informatie over de namen van het protocol [micro soft Identity platform en OAuth 2,0-of-flow](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow).

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een token voor de app ophalen](scenario-web-api-call-api-acquire-token.md)
