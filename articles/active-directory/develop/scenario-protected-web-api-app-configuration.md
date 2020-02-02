---
title: Beveiligde web API-apps configureren | Azure
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
ms.openlocfilehash: 975117ad5c58bed77002a33f0dc5370d0f1c17e2
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/01/2020
ms.locfileid: "76931465"
---
# <a name="protected-web-api-code-configuration"></a>Beveiligde web-API: code configuratie

Als u de code voor de beveiligde web-API wilt configureren, moet u het volgende weten:

- Waarvan Api's als beveiligd worden gedefinieerd.
- Een Bearer-token configureren.
- Het token valideren.

## <a name="what-defines-aspnet-and-aspnet-core-apis-as-protected"></a>Wat definieert ASP.NET-en ASP.NET Core-Api's als beveiligd?

Net als web-apps zijn de ASP.NET-en ASP.NET Core Web-Api's beschermd, omdat hun controller acties worden voorafgegaan door het kenmerk **[autoriseren]** . De controller acties kunnen alleen worden aangeroepen als de API wordt aangeroepen met een geautoriseerde identiteit.

Houd rekening met de volgende vragen:

- Alleen een app kan een web-API aanroepen. Hoe kent de API de identiteit van de app waarmee deze wordt aangeroepen?
- Als de app namens een gebruiker de API aanroept, wat is de identiteit van de gebruiker?

## <a name="bearer-token"></a>Bearer-token

Het Bearer-token dat is ingesteld in de header wanneer de app wordt aangeroepen, bevat informatie over de app-identiteit. Het bevat ook informatie over de gebruiker, tenzij de web-app service-to-service-aanroepen accepteert vanuit een daemon-app.

Hier volgt een C# code voorbeeld waarin een client wordt weer gegeven die de API aanroept nadat deze een token heeft verkregen met micro soft Authentication Library voor .net (MSAL.net):

```csharp
var scopes = new[] {$"api://.../access_as_user"};
var result = await app.AcquireToken(scopes)
                      .ExecuteAsync();

httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
```

> [!IMPORTANT]
> Een client toepassing vraagt het Bearer-token aan bij het micro soft Identity platform-eind punt *voor de Web-API*. De Web-API is de enige toepassing waarmee het token moet worden geverifieerd en de claims worden weer gegeven. Client-apps moeten nooit proberen de claims in tokens te controleren.
>
> In de toekomst is het mogelijk dat het token moet worden versleuteld met de Web-API. Deze vereiste zou de toegang voor client-apps die toegangs tokens kunnen bekijken, voor komen.

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

Wanneer een app wordt aangeroepen voor een controller actie met een kenmerk **[autoriseren]** , ASP.NET en ASP.net core het toegangs token uit het Bearer-token van de autorisatie-header ophalen. Het toegangs token wordt vervolgens doorgestuurd naar de JwtBearer-middleware, die micro soft Identity model-extensies aanroept voor .NET.

In ASP.NET Core wordt deze middleware geïnitialiseerd in het Startup.cs-bestand.

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
```

De middleware wordt toegevoegd aan de Web-API met behulp van deze instructie:

```csharp
 services.AddAzureAdBearer(options => Configuration.Bind("AzureAd", options));
```

 Op dit moment maken de ASP.NET Core sjablonen Azure Active Directory (Azure AD) Web-Api's waarmee gebruikers zich kunnen aanmelden binnen uw organisatie of een organisatie. Gebruikers kunnen zich niet aanmelden met persoonlijke accounts. Maar u kunt de sjablonen wijzigen om het micro soft Identity platform-eind punt te gebruiken door deze code toe te voegen aan Startup.cs:

```csharp
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

Het vorige code fragment wordt geëxtraheerd uit de stapsgewijze zelf studie voor de ASP.NET Core Web-API in [micro soft. Identity. Web/WebApiServiceCollectionExtensions. cs # L50-L63](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/154282843da2fc2958fad151e2a11e521e358d42/Microsoft.Identity.Web/WebApiServiceCollectionExtensions.cs#L50-L63). De methode **AddProtectedWebApi** , waarmee meer dan het fragment wordt weer gegeven, wordt aangeroepen vanuit startup.cs.

## <a name="token-validation"></a>Token validatie

In het voor gaande fragment valideert de JwtBearer-middleware, zoals de OpenID Connect Connect-verbinding in web apps, het token op basis van de waarde van `TokenValidationParameters`. Het token wordt naar behoefte ontsleuteld, de claims worden geëxtraheerd en de hand tekening wordt gecontroleerd. De middleware valideert vervolgens het token door te controleren op deze gegevens:

- Doel groep: het token is gericht op de Web-API.
- Sub: het certificaat is uitgegeven voor een app die de Web-API mag aanroepen.
- Uitgever: het is uitgegeven door een Trusted Security Token Service (STS).
- Verloop datum: de levens duur ligt binnen het bereik.
- Hand tekening: er is niet mee geknoeid.

Er kunnen ook speciale validaties zijn. Het is bijvoorbeeld mogelijk om te controleren of de ondertekeningssleutels, wanneer deze zijn Inge sloten in een token, worden vertrouwd en dat het token niet opnieuw wordt afgespeeld. Ten slotte vereisen sommige protocollen specifieke validaties.

### <a name="validators"></a>Controles

De validatie stappen worden vastgelegd in validators, die worden weer gegeven in de [micro soft Identity model Extensions voor .net](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) open-source-bibliotheek. De validatie functies worden gedefinieerd in het bron bestand van de bibliotheek [micro soft. Identity model. tokens/validators. cs](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/Microsoft.IdentityModel.Tokens/Validators.cs).

In deze tabel worden de validatie functies beschreven:

| Validator | Beschrijving |
|---------|---------|
| **ValidateAudience** | Zorgt ervoor dat het token voor de toepassing is die het token voor u valideert. |
| **ValidateIssuer** | Hiermee zorgt u ervoor dat het token is uitgegeven door een vertrouwde STS, wat inhoudt dat u vertrouwt. |
| **ValidateIssuerSigningKey** | Zorgt ervoor dat de toepassing die het token valideert, de sleutel vertrouwt die is gebruikt voor het ondertekenen van het token. Er is een speciaal geval waarin de sleutel is inge sloten in het token. Maar deze situatie doet zich meestal niet voor. |
| **ValidateLifetime** | Zorgt ervoor dat het token nog of al geldig is. Met de validatie functie wordt gecontroleerd of de levens duur van het token zich in het bereik bevindt dat is opgegeven door de **notBefore** en **verlopen** claims. |
| **ValidateSignature** | Hiermee wordt gegarandeerd dat er niet met het token is geknoeid. |
| **ValidateTokenReplay** | Hiermee zorgt u ervoor dat het token niet opnieuw is afgespeeld. Er is een speciaal geval voor sommige eenmalige-protocollen. |

De validators zijn gekoppeld aan eigenschappen van de klasse **TokenValidationParameters** . De eigenschappen worden geïnitialiseerd vanuit de configuratie ASP.NET en ASP.NET Core.

In de meeste gevallen hoeft u de para meters niet te wijzigen. Apps die geen enkele Tenant zijn, zijn uitzonde ringen. Deze web-apps accepteren gebruikers van elke organisatie of van persoonlijke micro soft-accounts. Uitgevers in dit geval moeten worden gevalideerd.

## <a name="token-validation-in-azure-functions"></a>Token validatie in Azure Functions

U kunt ook binnenkomende toegangs tokens in Azure Functions valideren. Voor beelden van dergelijke validatie vindt u in [Microsoft .net](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions), [NodeJS](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions)en [python](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions).

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Bereiken en app-rollen controleren in uw code](scenario-protected-web-api-verification-scope-app-roles.md)
