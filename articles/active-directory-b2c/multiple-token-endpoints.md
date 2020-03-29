---
title: OWIN-gebaseerde web-API's migreren naar b2clogin.com
titleSuffix: Azure AD B2C
description: Meer informatie over hoe u een .NET-web-API inschakelen om tokens te ondersteunen die zijn uitgegeven door meerdere token-instellingen terwijl u uw toepassingen migreert naar b2clogin.com.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5daf88e746ea803f345c79bd31d656f2615b6754
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184091"
---
# <a name="migrate-an-owin-based-web-api-to-b2clogincom"></a>Een OWIN-gebaseerde web-API migreren naar b2clogin.com

In dit artikel wordt een techniek beschreven voor het inschakelen van ondersteuning voor meerdere token-emittenten in web-API's die de [Open Web Interface voor .NET (OWIN)](http://owin.org/)implementeren. Ondersteuning van meerdere tokeneindpunten is handig wanneer u Azure Active Directory B2C (Azure AD B2C) API's en hun toepassingen migreert van *login.microsoftonline.com* naar *b2clogin.com*.

Door ondersteuning toe te voegen aan uw API voor het accepteren van tokens die zijn uitgegeven door zowel b2clogin.com als login.microsoftonline.com, u uw webtoepassingen gefaseerd migreren voordat u ondersteuning voor login.microsoftonline.com uitgegeven tokens uit de API verwijdert.

In de volgende secties wordt een voorbeeld gegeven van hoe u meerdere emittenten inschakelt in een web-API die gebruikmaakt van de [Microsoft OWIN][katana] middleware-componenten (Katana). Hoewel de codevoorbeelden specifiek zijn voor de Microsoft OWIN middleware, moet de algemene techniek van toepassing zijn op andere OWIN-bibliotheken.

> [!NOTE]
> Dit artikel is bedoeld voor Azure AD B2C-klanten met `login.microsoftonline.com` momenteel geïmplementeerde API's `b2clogin.com` en toepassingen die verwijzen naar en die willen migreren naar het aanbevolen eindpunt. Als u een nieuwe toepassing instelt, gebruikt u [b2clogin.com](b2clogin.md) zoals aangegeven.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende Azure AD B2C-resources nodig voordat u verdergaat met de stappen in dit artikel:

* [Gebruikersstromen](tutorial-create-user-flows.md) of [aangepast beleid](custom-policy-get-started.md) dat in uw tenant is gemaakt

## <a name="get-token-issuer-endpoints"></a>Eindpunten van tokenuitgevende instellingen

U moet eerst de url's van het token-emittenteindpunt krijgen voor elke uitgever die u in uw API wilt ondersteunen. Als u de *b2clogin.com* en *login.microsoftonline.com* eindpunten wilt krijgen die worden ondersteund door uw Azure AD B2C-tenant, gebruikt u de volgende procedure in de Azure-portal.

Begin met het selecteren van een van uw bestaande gebruikersstromen:

1. Navigeren naar uw Azure AD B2C-tenant in de [Azure-portal](https://portal.azure.com)
1. Selecteer **onder Beleid** **gebruikersstromen (beleidsregels)**
1. Selecteer een bestaand beleid, *bijvoorbeeld B2C_1_signupsignin1*, selecteer **Vervolgens Gebruikersstroom uitvoeren**
1. Selecteer onder de kop **Gebruikersstroom uitvoeren** boven aan de pagina de hyperlink om naar het eindpunt voor detectie van OpenID Connect voor die gebruikersstroom te navigeren.

    ![Bekende URI-hyperlink op de nu-pagina uitvoeren van de Azure-portal](media/multi-token-endpoints/portal-01-policy-link.png)

1. Neem op de pagina die in `issuer` uw browser wordt geopend de waarde op, bijvoorbeeld:

    `https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/`

1. Gebruik de vervolgkeuzelijst **Domein selecteren** om het andere domein te selecteren `issuer` en vervolgens de vorige twee stappen opnieuw uit te voeren en de waarde ervan vast te leggen.

U moet nu twee opgenomen URI's hebben die vergelijkbaar zijn met:

```
https://login.microsoftonline.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/
https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/
```

### <a name="custom-policies"></a>Aangepast beleid

Als u aangepaste beleidsregels hebt in plaats van gebruikersstromen, u een vergelijkbaar proces gebruiken om de URL's van de uitgever op te halen.

1. Navigeren naar uw Azure AD B2C-tenant
1. Selecteer **Identity Experience Framework**
1. Selecteer een van uw beleid voor relying party, bijvoorbeeld *B2C_1A_signup_signin*
1. Gebruik de vervolgkeuzelijst **Domein selecteren** om een domein te selecteren, bijvoorbeeld *yourtenant.b2clogin.com*
1. De hyperlink selecteren die wordt weergegeven onder **detectieeindpunt Van OpenID Connect**
1. De `issuer` waarde vastleggen
1. Voer de stappen 4-6 uit voor het andere domein, bijvoorbeeld *login.microsoftonline.com*

## <a name="get-the-sample-code"></a>De voorbeeldcode halen

Nu u beide token-eindpunt-URI's hebt, moet u uw code bijwerken om op te geven dat beide eindpunten geldige emittenten zijn. Als u een voorbeeld wilt doorlopen, downloadt of kloont u de voorbeeldtoepassing en werkt u het voorbeeld bij om beide eindpunten te ondersteunen als geldige emittenten.

Download het archief: [active-directory-b2c-dotnet-webapp-and-webapi-master.zip][sample-archive]

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

## <a name="enable-multiple-issuers-in-web-api"></a>Meerdere emittenten inschakelen in web-API

In deze sectie werkt u de code bij om op te geven dat beide eindpunten van tokenuitgevende instellingen geldig zijn.

1. Open de **B2C-WebAPI-DotNet.sln-oplossing** in Visual Studio
1. Open in het **Taakserviceproject** het *bestand\\TaskService App_Start\\**Startup.Auth.cs*** in uw editor
1. Voeg de `using` volgende richtlijn toe aan de bovenkant van het bestand:

    `using System.Collections.Generic;`
1. Voeg [`ValidIssuers`][validissuers] de eigenschap [`TokenValidationParameters`][tokenvalidationparameters] toe aan de definitie en geef beide URI's op die u in de vorige sectie hebt opgenomen:

    ```csharp
    TokenValidationParameters tvps = new TokenValidationParameters
    {
        // Accept only those tokens where the audience of the token is equal to the client ID of this app
        ValidAudience = ClientId,
        AuthenticationType = Startup.DefaultPolicy,
        ValidIssuers = new List<string> {
            "https://login.microsoftonline.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/",
            "https://{your-b2c-tenant}.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/"
        }
    };
    ```

`TokenValidationParameters`wordt geleverd door MSAL.NET en wordt verbruikt door de OWIN middleware in het volgende gedeelte van de code in *Startup.Auth.cs*. Als meerdere geldige emittenten zijn opgegeven, wordt de OWIN-toepassingspijplijn ervan bewust gemaakt dat beide tokeneindpunten geldige emittenten zijn.

```csharp
app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
{
    // This SecurityTokenProvider fetches the Azure AD B2C metadata &  from the OpenID Connect metadata endpoint
    AccessTokenFormat = new JwtFormat(tvps, new tCachingSecurityTokenProvider(String.Format(AadInstance, ultPolicy)))
});
```

Zoals eerder vermeld, bieden andere OWIN-bibliotheken doorgaans een vergelijkbare faciliteit voor het ondersteunen van meerdere emittenten. Hoewel het geven van voorbeelden voor elke bibliotheek buiten het bereik van dit artikel valt, u voor de meeste bibliotheken een vergelijkbare techniek gebruiken.

## <a name="switch-endpoints-in-web-app"></a>Eindpunten in web-app schakelen

Met beide URI's die nu door uw web-API worden ondersteund, hebt u nu uw webtoepassing bijgewerkt, zodat deze tokens uit het b2clogin.com eindpunt ophaalt.

U de voorbeeldwebtoepassing bijvoorbeeld configureren om het nieuwe `ida:AadInstance` eindpunt te gebruiken door de waarde in het *bestand TaskWebApp\\**Web.config*** van het **TaakWebApp-project** te wijzigen.

Wijzig `ida:AadInstance` de waarde in de *Web.config* van TaskWebApp, zodat deze verwijst `{your-b2c-tenant-name}.b2clogin.com` in plaats van `login.microsoftonline.com`.

Voor:

```xml
<!-- Old value -->
<add key="ida:AadInstance" value="https://login.microsoftonline.com/tfp/{0}/{1}" />
```

Na (vervangen `{your-b2c-tenant}` door de naam van uw B2C huurder):

```xml
<!-- New value -->
<add key="ida:AadInstance" value="https://{your-b2c-tenant}.b2clogin.com/tfp/{0}/{1}" />
```

Wanneer de eindpunttekenreeksen worden opgebouwd tijdens de uitvoering van de web-app, worden de op b2clogin.com gebaseerde eindpunten gebruikt wanneer deze tokens aanvragen.

## <a name="next-steps"></a>Volgende stappen

In dit artikel wordt een methode gepresenteerd voor het configureren van een web-API die de Microsoft OWIN middleware (Katana) implementeert om tokens van meerdere emittenteindpunten te accepteren. Zoals u wellicht opmerkt, zijn er verschillende andere tekenreeksen in de *Web.Config-bestanden* van zowel de TaskService- als TaskWebApp-projecten die moeten worden gewijzigd als u deze projecten wilt bouwen en uitvoeren tegen uw eigen tenant. U bent van harte welkom om de projecten op de juiste manier te wijzigen als u ze in actie wilt zien, maar een volledige walk-through om dit te doen valt buiten het bereik van dit artikel.

Zie [Overzicht van tokens in Azure Active Directory B2C](tokens-overview.md)voor meer informatie over de verschillende typen beveiligingstokens die worden uitgezonden door Azure AD B2C.

<!-- LINKS - External -->
[sample-archive]: https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip
[sample-repo]: https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi

<!-- LINKS - Internal -->
[katana]: https://docs.microsoft.com/aspnet/aspnet/overview/owin-and-katana/
[validissuers]: https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters.validissuers
[tokenvalidationparameters]: https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters
