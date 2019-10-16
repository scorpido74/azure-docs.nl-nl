---
title: 'Zelf studie: toegang verlenen tot een ASP.NET Core Web-API vanuit een toepassing met één pagina-Azure Active Directory B2C'
description: Meer informatie over het gebruik van Active Directory B2C voor het beveiligen van een .NET core web-API en het aanroepen van de API vanuit een node. js-toepassing met één pagina.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 07/24/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 9ac95896e67338437325e8290a96b8e42b2fa3a7
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72374249"
---
# <a name="tutorial-grant-access-to-an-aspnet-core-web-api-from-a-single-page-application-using-azure-active-directory-b2c"></a>Zelf studie: toegang verlenen tot een ASP.NET Core Web-API vanuit een toepassing met één pagina met behulp van Azure Active Directory B2C

In deze zelf studie leert u hoe u een ASP.NET Core Web-API-resource van Azure Active Directory B2C (Azure AD B2C) aanroept vanuit een toepassing met één pagina.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een web-API-toepassing toevoegen
> * Bereiken voor een web-API configureren
> * Machtigingen verlenen aan de web-API
> * Het voorbeeld configureren voor gebruik van de toepassing

## <a name="prerequisites"></a>Vereisten

* Voer de stappen en vereisten in de [zelf studie uit: Schakel verificatie in een toepassing met één pagina in met behulp van Azure Active Directory B2C](active-directory-b2c-tutorials-spa.md).
* Visual Studio 2019 of hoger, of Visual Studio code
* .NET Core 2,2 of hoger
* Node.js

## <a name="add-a-web-api-application"></a>Een web-API-toepassing toevoegen

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>Bereiken configureren

Bereiken bieden een manier om toegang tot beveiligde resources te reguleren. Bereiken worden door de web-API gebruikt om toegangsbeheer op basis van een bereik te implementeren. Sommige gebruikers kunnen bijvoorbeeld zowel lees- als schrijftoegang hebben, terwijl andere gebruikers mogelijk alleen-lezen-machtigingen hebben. In deze zelf studie definieert u zowel lees-als schrijf machtigingen voor de Web-API.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

Noteer de **volledige bereik waarde** voor het `demo.read`-bereik dat u in een latere stap kunt gebruiken wanneer u de toepassing met één pagina configureert. De volledige bereik waarde is vergelijkbaar met `https://yourtenant.onmicrosoft.com/api/demo.read`.

## <a name="grant-permissions"></a>Machtigingen verlenen

Als u een beveiligde web-API wilt aanroepen vanuit een andere toepassing, moet u die toepassings machtigingen toewijzen aan de Web-API.

In de hand leiding voor vereisten hebt u een webtoepassing gemaakt met de naam *webapp1*. In deze zelf studie configureert u die toepassing om de Web-API aan te roepen die u hebt gemaakt in een vorige sectie, *webapi1*.

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

Uw webtoepassing met één pagina is geregistreerd om de beveiligde web-API aan te roepen. Een gebruiker wordt geverifieerd met Azure AD B2C om de toepassing met één pagina te gebruiken. De app met één pagina verkrijgt een autorisatie machtiging van Azure AD B2C om toegang te krijgen tot de beveiligde web-API.

## <a name="configure-the-sample"></a>Voorbeeld configureren

Nu de web-API is geregistreerd en u bereiken hebt gedefinieerd, moet u de web-API-code configureren om uw Azure AD B2C-tenant te gebruiken. In deze zelf studie configureert u een voor beeld van een .NET core-webtoepassing die u kunt downloaden van GitHub.

[Down load een @no__t -1. zip archive](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi/archive/master.zip) of kloon het project voor het voor beeld-Web-API van github.

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi.git
```

### <a name="configure-the-web-api"></a>De web-API configureren

1. Open het bestand <em>B2C-WebApi/**appSettings. json**</em>  in Visual Studio of Visual Studio code.
1. Wijzig het `AzureAdB2C`-blok zodat dit overeenkomt met de naam van uw Tenant, de toepassings-ID van de Web-API-toepassing, de naam van uw registratie-of aanmeldings beleid en de scopes die u eerder hebt gedefinieerd. Het blok moet er ongeveer uitzien als in het volgende voor beeld (met de juiste `Tenant`-en `ClientId`-waarden):

    ```json
    "AzureAdB2C": {
      "Tenant": "<your-tenant-name>.onmicrosoft.com",
      "ClientId": "<webapi-application-ID>",
      "Policy": "B2C_1_signupsignin1",

      "ScopeRead": "demo.read",
      "ScopeWrite": "demo.write"
    },
    ```

#### <a name="enable-cors"></a>CORS inschakelen

Als u wilt toestaan dat uw toepassing met één pagina de ASP.NET Core Web-API aanroept, moet u [CORS](https://docs.microsoft.com/aspnet/core/security/cors) inschakelen in de Web-API.

1. In *Startup.cs* voegt u CORS toe aan de methode `ConfigureServices()`.

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddCors();
    ```

1. Stel ook binnen de `ConfigureServices()`-methode de waarde voor de `jwtOptions.Authority` in op de volgende token Uitgever-URI.

    Vervang `<your-tenant-name>` door de naam van uw B2C-Tenant.

    ```csharp
    jwtOptions.Authority = $"https://<your-tenant-name>.b2clogin.com/{Configuration["AzureAdB2C:Tenant"]}/{Configuration["AzureAdB2C:Policy"]}/v2.0";
    ```

1. Configureer CORS in de `Configure()`-methode.

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
    {
        app.UseCors(builder =>
            builder.WithOrigins("http://localhost:6420").AllowAnyHeader().AllowAnyMethod());
    ```

1. (Alleen Visual Studio) Open het bestand *launchSettings. json* onder **eigenschappen** in het Solution Explorer en zoek vervolgens het `iisExpress`-blok.
1. (Alleen Visual Studio) Werk de `applicationURL`-waarde bij met het poort nummer dat u hebt opgegeven toen u de *webapi1* -toepassing in een eerdere stap hebt geregistreerd. Bijvoorbeeld:

    ```json
    "iisExpress": {
      "applicationUrl": "http://localhost:5000/",
      "sslPort": 0
    }
    ```

### <a name="configure-the-single-page-application"></a>De toepassing met één pagina configureren

De toepassing met één pagina (SPA) van de [vorige zelf studie](active-directory-b2c-tutorials-spa.md) in de reeks maakt gebruik van Azure AD B2C voor gebruikers registratie en aanmelding, en roept de ASP.net core web-API aan die wordt beveiligd door de *frabrikamb2c* -demo Tenant.

In deze sectie werkt u de toepassing met één pagina bij om de ASP.NET Core Web-API aan te roepen die wordt beveiligd door *uw* Azure AD B2C Tenant en die u op uw lokale computer uitvoert.

Als u de instellingen in de beveiligd-wachtwoord verificatie wilt wijzigen:

1. Open het bestand *index. html* in het project [Active Directory-B2C-java script-msal-singlepageapp][github-js-spa] dat u in de vorige zelf studie hebt gedownload of gekloond.
1. Configureer het voor beeld met de URI voor de *demo. Lees* bereik dat u eerder hebt gemaakt en de URL van de Web-API.
    1. Vervang in de definitie van `appConfig` de waarde van `b2cScopes` door de volledige URI voor het bereik (de **waarde** die u eerder hebt geregistreerd).
    1. Wijzig de waarde voor @no__t 0 in de waarde voor `applicationURL` die u in de vorige sectie hebt opgegeven.

    De definitie van de `appConfig` moet er ongeveer uitzien als in het volgende code blok (met de naam van uw Tenant op de plaats van `<your-tenant-name>`):

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    var appConfig = {
      b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/api/demo.read"],
      webApi: "http://localhost:5000/"
    };
    ```

## <a name="run-the-spa-and-web-api"></a>De SPA-en Web-API uitvoeren

Ten slotte voert u zowel de ASP.NET Core Web-API als de node. js-toepassing met één pagina op uw lokale computer uit. Vervolgens meldt u zich aan bij de toepassing met één pagina en drukt u op een knop om een aanvraag naar de beveiligde API te initiëren.

Hoewel beide toepassingen lokaal worden uitgevoerd in deze zelf studie, gebruiken ze Azure AD B2C voor het veilig aanmelden/aanmelden en het verlenen van toegang tot de beveiligde web-API.

### <a name="run-the-aspnet-core-web-api"></a>De Web-API van ASP.NET Core uitvoeren

Druk in Visual Studio op **F5** om de oplossing *B2C-WebAPI. SLN* te bouwen en fouten op te sporen. Wanneer het project wordt gestart, wordt een webpagina weer gegeven in de standaard browser die aankondigt dat de Web-API beschikbaar is voor aanvragen.

Als u liever de `dotnet` CLI gebruikt in plaats van Visual Studio:

1. Open een console venster en ga naar de map met het bestand *@no__t -1. csproj* . Bijvoorbeeld:

    `cd active-directory-b2c-dotnetcore-webapi/B2C-WebApi`

1. Bouw en voer de Web-API uit door `dotnet run` uit te voeren.

    Wanneer de API actief is, ziet u uitvoer die lijkt op het volgende (voor de zelf studie kunt u elke `NETSDK1059` waarschuwing negeren):

    ```console
    $ dotnet run
    Hosting environment: Production
    Content root path: /home/user/active-directory-b2c-dotnetcore-webapi/B2C-WebApi
    Now listening on: http://localhost:5000
    Application started. Press Ctrl+C to shut down.
    ```

### <a name="run-the-single-page-app"></a>Voer de app met één pagina uit

1. Open een console venster en ga naar de map met het voor beeld van node. js. Bijvoorbeeld:

    `cd active-directory-b2c-javascript-msal-singlepageapp`

1. Voer de volgende opdrachten uit:

    ```console
    npm install && npm update
    node server.js
    ```

    Het consolevenster geeft het poortnummer van waar de app wordt gehost.

    ```console
    Listening on port 6420...
    ```

1. Ga in uw browser naar `http://localhost:6420` om de toepassing weer te geven.
1. Meld u aan met het e-mail adres en het wacht woord dat u in de [vorige zelf studie](active-directory-b2c-tutorials-spa.md)hebt gebruikt. Wanneer de aanmelding is geslaagd, wordt het `User 'Your Username' logged-in`-bericht weer gegeven.
1. Selecteer de knop **Web-API aanroepen** . De beveiligd-wachtwoord verificatie verkrijgt een machtigings toekenning van Azure AD B2C en opent vervolgens de beveiligde web-API om de inhoud van de index pagina weer te geven:

    ```Output
    Web APi returned:
    "<html>\r\n<head>\r\n  <title>Azure AD B2C API Sample</title>\r\n ...
    ```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Een web-API-toepassing toevoegen
> * Bereiken voor een web-API configureren
> * Machtigingen verlenen aan de web-API
> * Het voorbeeld configureren voor gebruik van de toepassing

Nu u een beveiligd-wachtwoord verificatie-verzoek hebt gezien vanuit een beveiligde web-API, krijgt u een beter inzicht in hoe deze toepassings typen met elkaar en met Azure AD B2C werken.

> [!div class="nextstepaction"]
> [Toepassings typen die kunnen worden gebruikt in Active Directory B2C >](active-directory-b2c-apps.md)

<!-- Links - EXTERNAL -->
[github-js-spa]: https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp
