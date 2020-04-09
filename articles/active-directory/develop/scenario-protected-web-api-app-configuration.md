---
title: Beveiligde web-API-apps configureren | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het bouwen van een beveiligde web-API en het configureren van de code van uw toepassing.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 073eca94ad93c69811b02abe2c8649940a394e8e
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80882468"
---
# <a name="protected-web-api-code-configuration"></a>Beveiligde web-API: codeconfiguratie

Als u de code voor uw beveiligde web-API wilt configureren, moet u het als:

- Wat definieert API's als beschermd.
- Een token aan toonder configureren.
- Hoe het token te valideren.

## <a name="what-defines-aspnet-and-aspnet-core-apis-as-protected"></a>Wat definieert ASP.NET en ASP.NET Core API's als beschermd?

Net als web-apps worden de ASP.NET- en ASP.NET Core-web-API's beschermd omdat hun controlleracties vooraf zijn vastgelegd met het kenmerk **[Machtigen].** De controlleracties kunnen alleen worden aangeroepen als de API wordt aangeroepen met een geautoriseerde identiteit.

Denk na over de volgende vragen:

- Alleen een app kan een web-API aanroepen. Hoe weet de API de identiteit van de app die de app aanroept?
- Als de app de API aanroept namens een gebruiker, wat is dan de identiteit van de gebruiker?

## <a name="bearer-token"></a>Token aan toonder

Het token aan toonder dat is ingesteld in de koptekst wanneer de app wordt aangeroepen, bevat informatie over de app-identiteit. Het bevat ook informatie over de gebruiker, tenzij de web-app service-to-service-oproepen van een daemon-app accepteert.

Hier is een C#-codevoorbeeld waarin een client die de API aanroept, wordt weergegeven nadat deze een token met Microsoft-verificatiebibliotheek voor .NET (MSAL.NET) heeft overgedaan:

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
> Een clienttoepassing vraagt het token aan toonder aan het eindpunt van het Microsoft-identiteitsplatform *voor de web-API*. De web-API is de enige toepassing die het token moet verifiëren en de claims moet bekijken die het bevat. Client-apps mogen nooit proberen om de claims in tokens te inspecteren.
>
> In de toekomst vereist de web-API mogelijk dat het token wordt versleuteld. Deze vereiste zou de toegang voor client-apps die toegangstokens kunnen bekijken, voorkomen.

## <a name="jwtbearer-configuration"></a>JwtBearer-configuratie

In deze sectie wordt beschreven hoe u een token aan toonder configureert.

### <a name="config-file"></a>Config-bestand

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

### <a name="code-initialization"></a>Codeinitialisatie

Wanneer een app wordt aangeroepen op een controlleractie met een **[Authorize]** kenmerk, halen ASP.NET en ASP.NET Core het toegangstoken uit het token aan de drager van de autorisatiekop. Het toegangstoken wordt vervolgens doorgestuurd naar de Middleware JwtBearer, die Microsoft IdentityModel Extensions voor .NET aanroept.

In ASP.NET Core wordt deze middleware geïnitialiseerd in het Startup.cs bestand.

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
```

De middleware wordt toegevoegd aan de web-API door deze instructie:

```csharp
 services.AddAuthentication(AzureADDefaults.JwtBearerAuthenticationScheme)
         .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

 Op dit moment maken de ASP.NET Core-sjablonen Azure Active Directory (Azure AD)-web-API's die gebruikers binnen uw organisatie of organisatie aanmelden. Ze loggen niet in op gebruikers met persoonlijke accounts. U de sjablonen echter wijzigen om het eindpunt van het Microsoft-identiteitsplatform te gebruiken door deze code toe te voegen aan Startup.cs:

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

Het vorige codefragment wordt geëxtraheerd uit de ASP.NET Core web API incrementele zelfstudie in [Microsoft.Identity.Web/WebApiServiceCollectionExtensions.cs#L50-L63](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/154282843da2fc2958fad151e2a11e521e358d42/Microsoft.Identity.Web/WebApiServiceCollectionExtensions.cs#L50-L63). De **Methode AddProtectedWebApi,** die meer doet dan het fragment laat zien, wordt aangeroepen vanaf Startup.cs.

## <a name="token-validation"></a>Tokenvalidatie

In het vorige fragment valideert de JwtBearer middleware, net als de OpenID Connect middleware in web-apps, het token op basis van de waarde van `TokenValidationParameters`. Het token wordt waar nodig gedecodeerd, de claims worden geëxtraheerd en de handtekening wordt geverifieerd. De middleware valideert vervolgens het token door te controleren op deze gegevens:

- Doelgroep: het token is gericht op de web-API.
- Sub: Het is uitgegeven voor een app die de web-API mag aanroepen.
- Uitgever: Het werd uitgegeven door een vertrouwde security token service (STS).
- Verlopen: de levensduur is binnen bereik.
- Handtekening: Er is niet mee geknoeid.

Er kunnen ook speciale validaties zijn. Het is bijvoorbeeld mogelijk om te valideren dat ondertekeningssleutels, wanneer ze zijn ingesloten in een token, worden vertrouwd en dat het token niet wordt afgespeeld. Ten slotte vereisen sommige protocollen specifieke validaties.

### <a name="validators"></a>Validators

De validatiestappen worden vastgelegd in validators, die worden geleverd door de [Microsoft IdentityModel Extensions voor .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) open source-bibliotheek. De validators worden gedefinieerd in het bibliotheekbronbestand [Microsoft.IdentityModel.Tokens/Validators.cs](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/Microsoft.IdentityModel.Tokens/Validators.cs).

In deze tabel worden de validators beschreven:

| Validator | Beschrijving |
|---------|---------|
| **Gevalideerdpubliek** | Hiermee zorgt u ervoor dat het token voor de toepassing is die het token voor u valideert. |
| **ValidateIssuer ValidateIssuer ValidateIssuer ValidateIssuer** | Hiermee zorgt u ervoor dat het token is uitgegeven door een vertrouwde STS, wat betekent dat het afkomstig is van iemand die u vertrouwt. |
| **ValidateIssuerSigningKey ValidateIssuerSigningKey ValidateIssuerSigningKey ValidateIssuer** | Hiermee garandeert u dat de toepassing die de token valideert, de sleutel vertrouwt die is gebruikt om het token te ondertekenen. Er is een speciaal geval waarbij de sleutel is ingebed in het token. Maar deze zaak doet zich meestal niet voor. |
| **ValiderenLevensduur** | Hiermee zorgt u ervoor dat het token nog steeds of al geldig is. De validator controleert of de levensduur van het token zich binnen het bereik bevindt dat door de **notbefore** is opgegeven en **verloopt** claims. |
| **Gevalideerdhandtekening** | Hiermee is er niet met het token geknoeid. |
| **ValidateTokenReplay ValidateTokenReplay** | Hiermee zorgt u ervoor dat het token niet opnieuw wordt afgespeeld. Er is een speciaal geval voor een eenmalig gebruik. |

De validators zijn gekoppeld aan eigenschappen van de klasse **TokenValidationParameters.** De eigenschappen worden geïnitialiseerd vanuit de ASP.NET- en ASP.NET Core-configuratie.

In de meeste gevallen hoeft u de parameters niet te wijzigen. Apps die geen afzonderlijke tenants zijn, zijn uitzonderingen. Deze webapps accepteren gebruikers van elke organisatie of van persoonlijke Microsoft-accounts. Uitgevende instellingen moeten in dit geval worden gevalideerd.

## <a name="token-validation-in-azure-functions"></a>Tokenvalidatie in Azure-functies

U ook binnenkomende toegangstokens valideren in Azure-functies. Voorbeelden van dergelijke validatie vindt u in [Microsoft .NET,](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions) [NodeJS](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions)en [Python](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions).

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Scopes en app-rollen in uw code verifiëren](scenario-protected-web-api-verification-scope-app-roles.md)
