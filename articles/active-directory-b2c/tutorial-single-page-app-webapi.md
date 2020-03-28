---
title: 'Zelfstudie: Toegang verlenen tot een ASP.NET Core-web-API vanuit een app met één pagina'
titleSuffix: Azure AD B2C
description: In deze zelfstudie leert u hoe u Active Directory B2C gebruikt om een .NET Core-web-API te beschermen en de API aan te roepen vanaf een Node.js-toepassing met één pagina.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 07/24/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: f6f9ff7bb0d504ecc163f6ce1f87477b1ea9c2d1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78186136"
---
# <a name="tutorial-grant-access-to-an-aspnet-core-web-api-from-a-single-page-application-using-azure-active-directory-b2c"></a>Zelfstudie: Toegang verlenen tot een ASP.NET Core-web-API vanuit een toepassing van één pagina met Azure Active Directory B2C

In deze zelfstudie ziet u hoe u een Azure Active Directory B2C (Azure AD B2C)-beveiligde ASP.NET Core-web-API-bron aanroept vanuit één pagina.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een web-API-toepassing toevoegen
> * Bereiken voor een web-API configureren
> * Machtigingen verlenen aan de web-API
> * Het voorbeeld configureren voor gebruik van de toepassing

## <a name="prerequisites"></a>Vereisten

* Voer de stappen en vereisten uit in [Zelfstudie: Verificatie inschakelen in een toepassing van één pagina met Azure Active Directory B2C](tutorial-single-page-app.md).
* Visual Studio 2019 of hoger, of Visual Studio Code
* .NET Core 2.2 of hoger
* Node.js

## <a name="add-a-web-api-application"></a>Een web-API-toepassing toevoegen

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>Bereiken configureren

Bereiken bieden een manier om toegang tot beveiligde resources te reguleren. Bereiken worden door de web-API gebruikt om toegangsbeheer op basis van een bereik te implementeren. Sommige gebruikers kunnen bijvoorbeeld zowel lees- als schrijftoegang hebben, terwijl andere gebruikers mogelijk alleen-lezen-machtigingen hebben. In deze zelfstudie definieert u zowel lees- als schrijfmachtigingen voor de web-API.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

Noteer de waarde onder `demo.read` **SCOPES** voor het bereik dat in een latere stap moet worden gebruikt wanneer u de toepassing met één pagina configureert. De volledige scopewaarde `https://contosob2c.onmicrosoft.com/api/demo.read`is vergelijkbaar met .

## <a name="grant-permissions"></a>Machtigingen verlenen

Als u een beveiligde web-API wilt aanroepen vanuit een andere toepassing, moet u die toepassingsmachtigingen toekennen aan de web-API.

In de vereiste zelfstudie hebt u een webtoepassing met de naam *webapp1*gemaakt. In deze zelfstudie configureert u die toepassing om de web-API aan te roepen die u in een vorige sectie hebt gemaakt, *webapi1*.

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

Uw webtoepassing met één pagina is geregistreerd om de beveiligde web-API aan te roepen. Een gebruiker verifieert met Azure AD B2C om de toepassing met één pagina te gebruiken. De app met één pagina krijgt een autorisatiesubsidie van Azure AD B2C om toegang te krijgen tot de beveiligde web-API.

## <a name="configure-the-sample"></a>Configureren van het voorbeeld

Nu de web-API is geregistreerd en u bereiken hebt gedefinieerd, moet u de web-API-code configureren om uw Azure AD B2C-tenant te gebruiken. In deze zelfstudie configureert u een voorbeeld .NET Core-webtoepassing die u downloadt van GitHub.

[Download \*een .zip-archief](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi/archive/master.zip) of kloon het voorbeeld web API-project van GitHub.

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi.git
```

### <a name="configure-the-web-api"></a>De web-API configureren

1. Open het <em>bestand B2C-WebApi/**appsettings.json** </em> in Visual Studio of Visual Studio Code.
1. Wijzig `AzureAdB2C` het blok om uw tenantnaam, de toepassings-id van de web-API-toepassing, de naam van uw aanmeldings-/aanmeldingsbeleid en de eerder gedefinieerde scopes weer te geven. Het blok moet er hetzelfde uitzien `Tenant` `ClientId` als het volgende voorbeeld (met de juiste en waarden):

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

Als u wilt dat uw toepassing met één pagina de ASP.NET Core-web-API aanroept, moet u [CORS](https://docs.microsoft.com/aspnet/core/security/cors) inschakelen in de web-API.

1. In *Startup.cs* voegt u CORS toe aan de methode `ConfigureServices()`.

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddCors();
    ```

1. Ook binnen `ConfigureServices()` de methode `jwtOptions.Authority` stelt u de waarde in op de volgende tokenuitgever URI.

    Vervang `<your-tenant-name>` door de naam van uw B2C-tenant.

    ```csharp
    jwtOptions.Authority = $"https://<your-tenant-name>.b2clogin.com/{Configuration["AzureAdB2C:Tenant"]}/{Configuration["AzureAdB2C:Policy"]}/v2.0";
    ```

1. Configureer `Configure()` in de methode CORS.

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
    {
        app.UseCors(builder =>
            builder.WithOrigins("http://localhost:6420").AllowAnyHeader().AllowAnyMethod());
    ```

1. (Alleen Visual Studio) Open **onder Eigenschappen** in de Oplossingsverkenner het bestand *launchSettings.json* en zoek het `iisExpress` blok.
1. (Alleen Visual Studio) Werk `applicationURL` de waarde bij met het poortnummer dat u hebt opgegeven toen u de *webapi1-toepassing* in een eerdere stap registreerde. Bijvoorbeeld:

    ```json
    "iisExpress": {
      "applicationUrl": "http://localhost:5000/",
      "sslPort": 0
    }
    ```

### <a name="configure-the-single-page-application"></a>De toepassing met één pagina configureren

De single-page applicatie (SPA) uit de [vorige zelfstudie](tutorial-single-page-app.md) in de serie maakt gebruik van Azure AD B2C voor gebruikersaanmelding en aanmelding, en noemt de ASP.NET Core web API beschermd door de *frabrikamb2c* demo tenant.

In deze sectie werkt u de toepassing met één pagina bij om de ASP.NET Core-web-API aan te roepen die is beschermd door *uw* Azure AD B2C-tenant en die u uitvoert op uw lokale machine.

Ga als bedoeld als het gaat om het wijzigen van de instellingen in de SPA:

1. Open het bestand *index.html* in het project [active-directory-b2c-javascript-msal-singlepageapp][github-js-spa] dat u in de vorige zelfstudie hebt gedownload of gekloond.
1. Configureer het voorbeeld met de URI voor de *demo.read-scope* die u eerder hebt gemaakt en de URL van de web-API.
    1. Vervang `appConfig` in de `b2cScopes` definitie de waarde door de volledige URI voor het bereik (de **scopewaarde** die u eerder hebt geregistreerd).
    1. Wijzig `webApi` de waarde in de omleidings-URI die u hebt toegevoegd toen u de web-API-toepassing in een eerdere stap registreerde.

    De `appConfig` definitie moet lijken op het volgende codeblok (met `<your-tenant-name>`uw tenantnaam in de plaats van):

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    var appConfig = {
      b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/api/demo.read"],
      webApi: "http://localhost:5000/"
    };
    ```

## <a name="run-the-spa-and-web-api"></a>De SPA- en web-API uitvoeren

Ten slotte voert u zowel de ASP.NET Core web-API als de Node.js single-page applicatie uit op uw lokale machine. Vervolgens meldt u zich aan bij de toepassing met één pagina en drukt u op een knop om een aanvraag voor de beveiligde API te starten.

Hoewel beide toepassingen lokaal worden uitgevoerd in deze zelfstudie, gebruiken ze Azure AD B2C voor veilige aanmelding/aanmelding en om toegang te verlenen tot de beveiligde web-API.

### <a name="run-the-aspnet-core-web-api"></a>De ASP.NET Core-web-API uitvoeren

Druk in Visual Studio op **F5** om de *B2C-WebAPI.sln-oplossing* te bouwen en te debuggen. Wanneer het project wordt gestart, wordt een webpagina weergegeven in uw standaardbrowser waarin de web-API wordt aangekondigd, beschikbaar is voor aanvragen.

Als u de `dotnet` CLI liever gebruikt in plaats van Visual Studio:

1. Open een consolevenster en wijzig de map met het * \*.csproj-bestand.* Bijvoorbeeld:

    `cd active-directory-b2c-dotnetcore-webapi/B2C-WebApi`

1. De web-API bouwen en `dotnet run`uitvoeren door het uitvoeren van .

    Wanneer de API actief is, ziet u uitvoer die vergelijkbaar is met de `NETSDK1059` volgende (voor de zelfstudie u waarschuwingen veilig negeren):

    ```console
    $ dotnet run
    Hosting environment: Production
    Content root path: /home/user/active-directory-b2c-dotnetcore-webapi/B2C-WebApi
    Now listening on: http://localhost:5000
    Application started. Press Ctrl+C to shut down.
    ```

### <a name="run-the-single-page-app"></a>Voer de app met één pagina uit

1. Open een consolevenster en wijzig de map met het voorbeeld Node.js. Bijvoorbeeld:

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

1. Navigeer `http://localhost:6420` naar in uw browser om de toepassing te bekijken.
1. Meld u aan met het e-mailadres en wachtwoord dat u in de [vorige zelfstudie hebt](tutorial-single-page-app.md)gebruikt. Bij een succesvolle login `User 'Your Username' logged-in` moet u het bericht zien.
1. Selecteer de knop **Call Web API.** De SPA krijgt een autorisatiesubsidie van Azure AD B2C en opent vervolgens de beveiligde web-API om de inhoud van de indexpagina weer te geven:

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

Nu u een SPA-aanvraag van een bron van een beveiligde web-API hebt gezien, krijgt u een beter inzicht in hoe deze toepassingstypen met elkaar en met Azure AD B2C communiceren.

> [!div class="nextstepaction"]
> [Toepassingstypen die kunnen worden gebruikt in Active Directory B2C->](application-types.md)

<!-- Links - EXTERNAL -->
[github-js-spa]: https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp
