---
title: 'Zelfstudie: Een Node.js-web-API beveiligen met Azure AD B2C en toegang verlenen tot een enkele pagina-toepassing (SPA)'
titleSuffix: Azure AD B2C
description: In deze zelfstudie leert u hoe u Active Directory B2C gebruikt om een Node.js-web-API te beschermen en deze aan te roepen tegen een toepassing van één pagina.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 04/04/2020
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 50524159186987b7a30015c878fa3fac949afc79
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875667"
---
# <a name="tutorial-protect-and-grant-access-to-a-nodejs-web-api-from-a-single-page-application-with-azure-ad-b2c"></a>Zelfstudie: Een Node.js-web-API beveiligen en verlenen vanuit een toepassing met één pagina met Azure AD B2C

In deze zelfstudie ziet u hoe u een Azure Active Directory B2C (Azure AD B2C)-beveiligde Node.js-web-API aanroepen vanuit een toepassing met één pagina.

In deze zelfstudie, de tweede in een tweedelige serie:

> [!div class="checklist"]
> * Een web-API-toepassingsregistratie maken in uw Azure AD B2C-tenant
> * Scopes configureren voor de web-API
> * Machtigingen verlenen aan de web-API
> * Een voorbeeld van een web-API-code wijzigen om met uw tenant te werken

In de [eerste zelfstudie](tutorial-single-page-app.md) in deze serie hebt u het codevoorbeeld gedownload en gewijzigd om gebruikers aan te melden met een gebruikersstroom in uw Azure AD B2C-tenant.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

* De stappen en vereisten uitvoeren in [Zelfstudie: Verificatie inschakelen in een toepassing met één pagina met Azure AD B2C](tutorial-single-page-app.md)
* [Visual Studio Code](https://code.visualstudio.com/) of een andere codeeditor
* [Node.js](https://nodejs.org/en/download/)

## <a name="add-a-web-api-application"></a>Een web-API-toepassing toevoegen

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>Bereiken configureren

Bereiken bieden een manier om toegang tot beveiligde resources te reguleren. Bereiken worden door de web-API gebruikt om toegangsbeheer op basis van een bereik te implementeren. Sommige gebruikers kunnen bijvoorbeeld zowel lees- als schrijftoegang hebben, terwijl andere gebruikers mogelijk alleen-lezen-machtigingen hebben. In deze zelfstudie definieert u zowel lees- als schrijfmachtigingen voor de web-API.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

Noteer de waarde onder `demo.read` **Scopes** voor het bereik dat in een latere stap moet worden gebruikt wanneer u de toepassing met één pagina configureert. De volledige scopewaarde `https://contosob2c.onmicrosoft.com/api/demo.read`is vergelijkbaar met .

## <a name="grant-permissions"></a>Machtigingen verlenen

Als u een beveiligde web-API wilt aanroepen vanuit een andere toepassing, moet u die toepassingsmachtigingen toekennen aan de web-API.

In de vereiste zelfstudie hebt u een webtoepassing met de naam *webapp1*gemaakt. In deze zelfstudie configureert u die toepassing om de web-API aan te roepen die u in een vorige sectie hebt gemaakt, *webapi1*.

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

Uw webtoepassing met één pagina heeft nu machtigingen verleend aan de beveiligde web-API voor de opgegeven scopes. Een gebruiker verifieert met Azure AD B2C om de toepassing met één pagina te gebruiken. De app met één pagina gebruikt de autorisatieverleningsstroom om toegang te krijgen tot de beveiligde web-API met een toegangstoken dat is geretourneerd door Azure AD B2C.

## <a name="configure-the-sample"></a>Configureren van het voorbeeld

Nu de web-API is geregistreerd en u scopes hebt gedefinieerd, configureert u de web-API-code om te werken met uw Azure AD B2C-tenant. In deze zelfstudie configureert u een voorbeeld van Node.js web-API die u downloadt van GitHub.

[Download \*een .zip-archief](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip) of kloon het voorbeeld web API-project van GitHub. U ook rechtstreeks naar het [Azure-Samples/active-directory-b2c-javascript-nodejs-webapi-project](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) op GitHub bladeren.

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

### <a name="configure-the-web-api"></a>De web-API configureren

1. Open het *bestand config.js* in uw codeeditor.
1. Wijzig de variabele waarden om die van de eerder gemaakte toepassingsregistratie weer te geven. Werk ook `policyName` de met de gebruikersstroom die u hebt gemaakt als onderdeel van de vereisten bij. bijvoorbeeld *B2C_1_signupsignin1*.

    ```javascript
    const clientID = "<your-webapi-application-ID>"; // Application (client) ID
    const b2cDomainHost = "<your-tenant-name>.b2clogin.com";
    const tenantId = "<your-tenant-ID>.onmicrosoft.com"; // Alternatively, you can use your Directory (tenant) ID (a GUID)
    const policyName = "B2C_1_signupsignin1";
    ```

#### <a name="enable-cors"></a>CORS inschakelen

Als u wilt dat uw toepassing met één pagina de Node.js-web-API aanroept, moet u [CORS](https://expressjs.com/en/resources/middleware/cors.html) inschakelen in de web-API. In een productietoepassing moet u voorzichtig zijn met welk domein het verzoek doet, maar voor deze zelfstudie u aanvragen uit elk domein toestaan.

Als u CORS wilt inschakelen, gebruikt u de volgende middleware. In het voorbeeld van de Node.js-webAPI-code in deze zelfstudie is het al toegevoegd aan het *bestand index.js.*

```javascript
app.use((req, res, next) => {
    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "Authorization, Origin, X-Requested-With, Content-Type, Accept");
    next();
});
```

### <a name="configure-the-single-page-application"></a>De toepassing met één pagina configureren

De single-page applicatie (SPA) uit de [vorige zelfstudie](tutorial-single-page-app.md) in de serie maakt gebruik van Azure AD B2C voor gebruikers aanmelding en aanmelding, en standaard, roept de Node.js web API beschermd door de *fabrikamb2c* demo tenant.

In deze sectie werkt u de webtoepassing met één pagina bij om de Web API van Node.js aan te roepen die wordt beschermd door *uw* Azure AD B2C-tenant (en die u uitvoert op uw lokale machine).

Ga als bedoeld als het gaat om het wijzigen van de instellingen in de SPA:

1. Open het bestand apiConfig.js in het project [active-directory-b2c-javascript-msal-singlepageapp][github-js-spa] dat u in de vorige zelfstudie hebt gedownload of gekloond, het *bestand apiConfig.js* in de *JavaSPAScript-map.*
1. Configureer het voorbeeld met de URI voor de *demo.read-scope* die u eerder hebt gemaakt en de URL van de web-API.
    1. Vervang `apiConfig` in de `b2cScopes` definitie de waarde door de volledige URI voor de *demo.read-scope* (de **scopewaarde** die u eerder hebt geregistreerd).
    1. Wijzig het domein `webApi` in de waarde in de omleidinguri die u hebt toegevoegd toen u de web-API-toepassing in een eerdere stap registreerde.

    Omdat de API toegankelijk `/hello` is op het eindpunt, laat */hello* in de URI.

    De `apiConfig` definitie moet lijken op het volgende codeblok, maar met de naam `<your-tenant-name>`van uw B2C-tenant in de plaats van:

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    const apiConfig = {
      b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/api/demo.read"],
      webApi: "http://localhost:5000/hello" // '/hello' should remain in the URI
    };
    ```

## <a name="run-the-spa-and-web-api"></a>De SPA- en web-API uitvoeren

U bent nu klaar om de scoped-toegang van de toepassing met één pagina tot de API te testen. Voer zowel de Node.js-web-API als de voorbeeldtoepassing JavaScript met één pagina uit op uw lokale machine. Meld u vervolgens aan bij de toepassing met één pagina en selecteer de knop **API-aanroep** om een aanvraag voor de beveiligde API te starten.

Hoewel beide toepassingen lokaal worden uitgevoerd wanneer u deze zelfstudie volgt, hebt u ze geconfigureerd om Azure AD B2C te gebruiken voor veilige aanmelding/aanmelding en om toegang te verlenen tot de beveiligde web-API.

### <a name="run-the-nodejs-web-api"></a>De web-API van Node.js uitvoeren

1. Open een consolevenster en wijzig de map met het voorbeeld van de Node.js-web-API. Bijvoorbeeld:

    ```console
    cd active-directory-b2c-javascript-nodejs-webapi
    ```

1. Voer de volgende opdrachten uit:

    ```console
    npm install && npm update
    node index.js
    ```

    In het consolevenster wordt het poortnummer weergegeven waar de toepassing wordt gehost.

    ```console
    Listening on port 5000...
    ```

### <a name="run-the-single-page-app"></a>De app met één pagina uitvoeren

1. Open een ander consolevenster en wijzig de map met het JavaScript SPA-voorbeeld. Bijvoorbeeld:

    ```console
    cd active-directory-b2c-javascript-msal-singlepageapp
    ```

1. Voer de volgende opdrachten uit:

    ```console
    npm install && npm update
    npm start
    ```

    Het consolevenster geeft het poortnummer van waar de app wordt gehost.

    ```console
    Listening on port 6420...
    ```

1. Navigeer `http://localhost:6420` naar in uw browser om de toepassing te bekijken.

    ![Voorbeeld-app voor toepassingen op één pagina die in de browser wordt weergegeven](./media/tutorial-single-page-app-webapi/tutorial-01-sample-app-browser.png)

1. Meld u aan met het e-mailadres en wachtwoord dat u in de [vorige zelfstudie hebt](tutorial-single-page-app.md)gebruikt. Bij een succesvolle login `User 'Your Username' logged-in` moet u het bericht zien.
1. Selecteer de **knop API-aanroepen.** De SPA krijgt een autorisatiesubsidie van Azure AD B2C en heeft vervolgens toegang tot de beveiligde web-API om de naam van de ingelogde gebruiker weer te geven:

    ![Toepassing met één pagina in browser met gebruikersnaam JSON-resultaat geretourneerd door API](./media/tutorial-single-page-app-webapi/tutorial-02-call-api.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u:

> [!div class="checklist"]
> * Een web-API-toepassingsregistratie maken in uw Azure AD B2C-tenant
> * Geconfigureerde scopes voor de web-API
> * Machtigingen voor de web-API
> * Een voorbeeld van een web-API-code wijzigen om met uw tenant te werken

Nu u een SPA-aanvraag van een bron van een beveiligde web-API hebt gezien, krijgt u een beter inzicht in hoe deze toepassingstypen met elkaar en met Azure AD B2C communiceren.

> [!div class="nextstepaction"]
> [Toepassingstypen die kunnen worden gebruikt in Active Directory B2C->](application-types.md)

<!-- Links - EXTERNAL -->
[github-js-spa]: https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp
