---
title: Beveiligde web-API-app-code configuratie
titleSuffix: Microsoft identity platform
description: Meer informatie over het bouwen van een beveiligde web-API en het configureren van de code van uw toepassing.
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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb562caf2dfc83841762748f37d1a7ee325de10b
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/09/2019
ms.locfileid: "73882629"
---
# <a name="protected-web-api-code-configuration"></a>Beveiligde web-API: code configuratie

Als u de code voor de beveiligde web-API wilt configureren, moet u weten wat Api's als beveiligd definieert, hoe u een Bearer-token configureert en hoe u het token valideert.

## <a name="what-defines-aspnetaspnet-core-apis-as-protected"></a>Wat definieert ASP.NET/ASP.NET core-Api's als beveiligd?

Net als web-apps zijn de ASP.NET/ASP.NET core-web-Api's beveiligd omdat hun controller acties worden voorafgegaan door het kenmerk `[Authorize]`. Daarom kunnen de controller acties alleen worden aangeroepen als de API wordt aangeroepen met een identiteit die is geautoriseerd.

Houd rekening met de volgende vragen:

- Hoe kent de Web-API de identiteit van de app waarmee deze wordt aangeroepen? (Alleen een app kan een web-API aanroepen.)
- Wat is de identiteit van de gebruiker als de app namens een gebruiker de Web-API wordt aangeroepen?

## <a name="bearer-token"></a>Bearer-token

De informatie over de identiteit van de app en over de gebruiker (tenzij de web-app service-naar-service aanroepen van een daemon-app accepteert), wordt vastgehouden in het Bearer-token dat is ingesteld in de header wanneer de app wordt aangeroepen.

Hier volgt een C# code voorbeeld waarin een client wordt weer gegeven die de API aanroept nadat deze een token heeft verkregen met micro soft Authentication Library voor .net (MSAL.net):

```CSharp
var scopes = new[] {$"api://.../access_as_user}";
var result = await app.AcquireToken(scopes)
                      .ExecuteAsync();

httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
```

> [!IMPORTANT]
> Het Bearer-token is door een client toepassing aangevraagd bij het micro soft Identity platform-eind punt *voor de Web-API*. De Web-API is de enige toepassing waarmee het token moet worden geverifieerd en de claims worden weer gegeven. Client-apps moeten nooit proberen de claims in tokens te controleren. (De Web-API kan in de toekomst vereist zijn dat het token wordt versleuteld. Deze vereiste zou voor komen dat de toegang voor client-apps waarmee toegangs tokens kunnen worden weer gegeven.)

## <a name="jwtbearer-configuration"></a>JwtBearer-configuratie

In deze sectie wordt beschreven hoe u een Bearer-token configureert.

### <a name="config-file"></a>Configuratie bestand

```Json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    /*
      You need specify the TenantId only if you want to accept access tokens from a single tenant
     (line-of-business app).
      Otherwise, you can leave them set to common.
      This can be:
      - A GUID (Tenant ID = Directory ID)
      - 'common' (any organization and personal accounts)
      - 'organizations' (any organization)
      - 'consumers' (Microsoft personal accounts)
    */
    "TenantId": "common"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="code-initialization"></a>Code-initialisatie

Wanneer een app wordt aangeroepen voor een controller actie die een `[Authorize]` kenmerk bevat, kijkt ASP.NET/ASP.NET core naar het Bearer-token in de autorisatie-header van de aanroep aanvraag en wordt het toegangs token geëxtraheerd. Het token wordt vervolgens doorgestuurd naar de JwtBearer-middleware, waarmee micro soft Identity model-extensies voor .NET worden aangeroepen.

In ASP.NET Core wordt deze middleware geïnitialiseerd in het Startup.cs-bestand:

```CSharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
```

De middleware wordt toegevoegd aan de Web-API met behulp van deze instructie:

```CSharp
 services.AddAzureAdBearer(options => Configuration.Bind("AzureAd", options));
```

 Op dit moment maken de ASP.NET Core sjablonen Azure Active Directory (Azure AD) Web-Api's die zich aanmelden bij gebruikers in uw organisatie of een organisatie, niet met persoonlijke accounts. Maar u kunt ze eenvoudig wijzigen voor het gebruik van het micro soft Identity platform-eind punt door deze code toe te voegen aan het Startup.cs-bestand:

```CSharp
services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, options =>
{
    // This is a Microsoft identity platform web API.
    options.Authority += "/v2.0";

    // The web API accepts as audiences both the Client ID (options.Audience) and api://{ClientID}.
    options.TokenValidationParameters.ValidAudiences = new []
    {
     options.Audience,
     $"api://{options.Audience}"
    };

    // Instead of using the default validation (validating against a single tenant,
    // as we do in line-of-business apps),
    // we inject our own multitenant validation logic (which even accepts both v1 and v2 tokens).
    options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.GetIssuerValidator(options.Authority).Validate;;
});
```

Dit code fragment wordt geëxtraheerd uit de stapsgewijze zelf studie voor de ASP.NET Core Web-API in [micro soft. Identity. Web/WebApiServiceCollectionExtensions. cs # L50-L63](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/154282843da2fc2958fad151e2a11e521e358d42/Microsoft.Identity.Web/WebApiServiceCollectionExtensions.cs#L50-L63). De methode `AddProtectedWebApi`, die veel meer doet, wordt aangeroepen vanuit de Startup.cs

## <a name="token-validation"></a>Token validatie

De JwtBearer-middleware, zoals de OpenID Connect Connect middleware in web apps, wordt door `TokenValidationParameters` omgeleid om het token te valideren. Het token wordt gedecodeerd (indien nodig), de claims worden geëxtraheerd en de hand tekening wordt gecontroleerd. De middleware valideert vervolgens het token door te controleren op deze gegevens:

- Het is bedoeld voor de Web-API (publiek).
- Het is uitgegeven voor een app die de Web-API (sub) mag aanroepen.
- Het is uitgegeven door een Trusted Security Token Service (STS) (verlener).
- De levens duur ligt binnen het bereik (verval datum).
- Er is geen onrecht matig geknoeid met (hand tekening).

Er kunnen ook speciale validaties zijn. Het is bijvoorbeeld mogelijk om te controleren of de ondertekeningssleutels (indien Inge sloten in een token) worden vertrouwd en dat het token niet opnieuw wordt afgespeeld. Ten slotte vereisen sommige protocollen specifieke validaties.

### <a name="validators"></a>Controles

De validatie stappen worden vastgelegd in validators, die allemaal zijn opgenomen in de [micro soft Identity model Extensions voor .net](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) open-source-bibliotheek, in één bron bestand: [micro soft. Identity model. tokens/validators. cs](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/Microsoft.IdentityModel.Tokens/Validators.cs).

De validatie functies worden in deze tabel beschreven:

| Validator | Beschrijving |
|---------|---------|
| `ValidateAudience` | Zorgt ervoor dat het token voor de toepassing is die het token valideert (voor mij). |
| `ValidateIssuer` | Hiermee zorgt u ervoor dat het token is uitgegeven door een vertrouwde STS (van iemand die ik vertrouwt). |
| `ValidateIssuerSigningKey` | Zorgt ervoor dat de toepassing die het token valideert, de sleutel vertrouwt die is gebruikt voor het ondertekenen van het token. (Speciaal geval waarbij de sleutel is inge sloten in het token. Doorgaans niet vereist.) |
| `ValidateLifetime` | Hiermee zorgt u ervoor dat het token nog (of al) geldig is. Met de validatie functie wordt gecontroleerd of de levens duur van het token (`notbefore` en `expires` claims) binnen het bereik valt. |
| `ValidateSignature` | Hiermee wordt gegarandeerd dat er niet met het token is geknoeid. |
| `ValidateTokenReplay` | Hiermee zorgt u ervoor dat het token niet opnieuw is afgespeeld. (Speciaal geval voor sommige eenmalige-protocol gebruiken.) |

De validatie functies zijn alle gekoppeld aan eigenschappen van de klasse `TokenValidationParameters`, die zelf zijn geïnitialiseerd vanuit de ASP.NET/ASP.NET-basis configuratie. In de meeste gevallen hoeft u de para meters niet te wijzigen. Er is één uitzonde ring, voor apps die geen afzonderlijke tenants zijn. (Dat wil zeggen: Web-apps die gebruikers accepteren van een organisatie of van persoonlijke micro soft-accounts.) In dit geval moet de uitgever worden gevalideerd.

## <a name="token-validation-in-azure-functions"></a>Token validatie in Azure Functions

Het is ook mogelijk om binnenkomende toegangs tokens in azure functions te valideren. In [DotNet](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions), [NodeJS](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions)en [python](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions)vindt u voor beelden van het valideren van tokens in azure functions.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Bereiken en app-rollen controleren in uw code](scenario-protected-web-api-verification-scope-app-roles.md)
