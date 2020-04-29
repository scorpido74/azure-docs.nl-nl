---
title: 'Zelf studie: een node. js-Web-API beveiligen met Azure AD B2C en toegang verlenen tot een toepassing met één pagina (SPA)'
titleSuffix: Azure AD B2C
description: In deze zelf studie leert u hoe u Active Directory B2C kunt gebruiken om een node. js-Web-API te beveiligen en deze aan te roepen vanuit een toepassing met één pagina.
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80875667"
---
# <a name="tutorial-protect-and-grant-access-to-a-nodejs-web-api-from-a-single-page-application-with-azure-ad-b2c"></a>Zelf studie: een node. js-Web-API beveiligen en toegang verlenen vanuit een toepassing met één pagina met Azure AD B2C

In deze zelf studie wordt uitgelegd hoe u een met Azure Active Directory B2C (Azure AD B2C) beveiligde node. js-Web-API aanroept vanuit een toepassing met één pagina.

In deze zelf studie wordt de tweede in een reeks van twee delen:

> [!div class="checklist"]
> * Een web-API-toepassings registratie maken in uw Azure AD B2C-Tenant
> * Scopes configureren voor de Web-API
> * Machtigingen verlenen aan de web-API
> * Een voor beeld van een web-API-code wijzigen om samen te werken met uw Tenant

In de [eerste zelf studie](tutorial-single-page-app.md) in deze reeks hebt u het code voorbeeld gedownload en gewijzigd om gebruikers aan te melden met een gebruikers stroom in uw Azure AD B2C-Tenant.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

* Volg de stappen en vereisten in [zelf studie: verificatie inschakelen in een toepassing met één pagina met Azure AD B2C](tutorial-single-page-app.md)
* [Visual Studio code](https://code.visualstudio.com/) of een andere code-editor
* [Node.js](https://nodejs.org/en/download/)

## <a name="add-a-web-api-application"></a>Een web-API-toepassing toevoegen

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>Bereiken configureren

Bereiken bieden een manier om toegang tot beveiligde resources te reguleren. Bereiken worden door de web-API gebruikt om toegangsbeheer op basis van een bereik te implementeren. Sommige gebruikers kunnen bijvoorbeeld zowel lees- als schrijftoegang hebben, terwijl andere gebruikers mogelijk alleen-lezen-machtigingen hebben. In deze zelf studie definieert u zowel lees-als schrijf machtigingen voor de Web-API.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

Noteer de waarde onder **scopes** voor het `demo.read` bereik dat u in een latere stap wilt gebruiken wanneer u de toepassing met één pagina configureert. De volledige bereik waarde is vergelijkbaar met `https://contosob2c.onmicrosoft.com/api/demo.read`.

## <a name="grant-permissions"></a>Machtigingen verlenen

Als u een beveiligde web-API wilt aanroepen vanuit een andere toepassing, moet u die toepassings machtigingen toewijzen aan de Web-API.

In de hand leiding voor vereisten hebt u een webtoepassing gemaakt met de naam *webapp1*. In deze zelf studie configureert u die toepassing om de Web-API aan te roepen die u hebt gemaakt in een vorige sectie, *webapi1*.

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

Aan uw webtoepassing met één pagina zijn nu machtigingen voor de beveiligde web-API voor de opgegeven scopes verleend. Een gebruiker wordt geverifieerd met Azure AD B2C om de toepassing met één pagina te gebruiken. De app met één pagina gebruikt de autorisatie subsidie stroom om toegang te krijgen tot de beveiligde web-API met een toegangs token dat is geretourneerd door Azure AD B2C.

## <a name="configure-the-sample"></a>Configureren van het voorbeeld

Nu de Web-API is geregistreerd en u bereiken hebt gedefinieerd, moet u de Web-API-code configureren zodat deze samenwerkt met uw Azure AD B2C-Tenant. In deze zelf studie configureert u een voor beeld van een node. js-Web-API die u hebt gedownload van GitHub.

[Down load \*een zip-archief](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip) of kloon het voor beeld-Web-API-project van github. U kunt ook rechtstreeks naar het project [Azure-samples/Active-Directory-B2C-java script-nodejs-webapi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) bladeren op github.

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

### <a name="configure-the-web-api"></a>De web-API configureren

1. Open het bestand *config. js* in de code-editor.
1. Wijzig de variabelen waarden zodat deze overeenkomen met die van de registratie van de toepassing die u eerder hebt gemaakt. Werk ook de `policyName` bij met de gebruikers stroom die u hebt gemaakt als onderdeel van de vereisten. Bijvoorbeeld *B2C_1_signupsignin1*.

    ```javascript
    const clientID = "<your-webapi-application-ID>"; // Application (client) ID
    const b2cDomainHost = "<your-tenant-name>.b2clogin.com";
    const tenantId = "<your-tenant-ID>.onmicrosoft.com"; // Alternatively, you can use your Directory (tenant) ID (a GUID)
    const policyName = "B2C_1_signupsignin1";
    ```

#### <a name="enable-cors"></a>CORS inschakelen

Als u wilt toestaan dat de toepassing met één pagina de node. js-Web-API aanroept, moet u [CORS](https://expressjs.com/en/resources/middleware/cors.html) inschakelen in de Web-API. In een productie toepassing moet u voorzichtig zijn met het domein dat de aanvraag doet, maar in deze zelf studie worden aanvragen van elk domein toegestaan.

Gebruik de volgende middleware om CORS in te scha kelen. In het voor beeld van de Web-API-code van node. js in deze zelf studie is het al toegevoegd aan het bestand *index. js* .

```javascript
app.use((req, res, next) => {
    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "Authorization, Origin, X-Requested-With, Content-Type, Accept");
    next();
});
```

### <a name="configure-the-single-page-application"></a>De toepassing met één pagina configureren

De toepassing met één pagina (SPA) uit de [vorige zelf studie](tutorial-single-page-app.md) in de reeks gebruikt Azure AD B2C voor gebruikers registratie en aanmelding, en roept standaard de node. js-Web-API aan die wordt beveiligd door de *fabrikamb2c* -demo Tenant.

In deze sectie werkt u de webtoepassing met één pagina bij om de node. js-Web-API aan te roepen die wordt beveiligd door *uw* Azure AD B2C-Tenant (en die u op uw lokale computer uitvoert).

Als u de instellingen in de beveiligd-wachtwoord verificatie wilt wijzigen:

1. Open het bestand *apiConfig. js* in de map *JavaScriptSPA* van het project [Active Directory-B2C-java script-msal-singlepageapp][github-js-spa] dat u in de vorige zelf studie hebt gedownload of gekloond.
1. Configureer het voor beeld met de URI voor de *demo. Lees* bereik dat u eerder hebt gemaakt en de URL van de Web-API.
    1. Vervang in `apiConfig` de definitie de `b2cScopes` waarde door de volledige URI voor de *demo. Lees* bereik (de waarde van het **bereik** dat u eerder hebt vastgelegd).
    1. Wijzig het domein in de `webApi` waarde in de omleidings-URI die u hebt toegevoegd toen u de Web-API-toepassing in een eerdere stap hebt geregistreerd.

    Omdat de API toegankelijk is op het `/hello` eind punt, moet u */Hello* in de URI laten staan.

    De `apiConfig` definitie moet er ongeveer uitzien als in het volgende code blok, maar met de naam van uw B2C- `<your-tenant-name>`Tenant in de plaats van:

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    const apiConfig = {
      b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/api/demo.read"],
      webApi: "http://localhost:5000/hello" // '/hello' should remain in the URI
    };
    ```

## <a name="run-the-spa-and-web-api"></a>De SPA-en Web-API uitvoeren

U bent nu klaar om de toegang tot de API van de app met één pagina te testen. Voer de node. js-Web-API en de voor beeld java script-toepassing met één pagina op uw lokale computer uit. Meld u vervolgens aan bij de toepassing met één pagina en selecteer de knop **API aanroepen** om een aanvraag naar de beveiligde API te initiëren.

Hoewel beide toepassingen lokaal worden uitgevoerd wanneer u deze zelf studie volgt, hebt u deze geconfigureerd voor het gebruik van Azure AD B2C voor veilig aanmelden/aanmelden en het verlenen van toegang tot de beveiligde web-API.

### <a name="run-the-nodejs-web-api"></a>De node. js-Web-API uitvoeren

1. Open een console venster en ga naar de map met het node. js-Web-API-voor beeld. Bijvoorbeeld:

    ```console
    cd active-directory-b2c-javascript-nodejs-webapi
    ```

1. Voer de volgende opdrachten uit:

    ```console
    npm install && npm update
    node index.js
    ```

    In het console venster wordt het poort nummer weer gegeven waarop de toepassing wordt gehost.

    ```console
    Listening on port 5000...
    ```

### <a name="run-the-single-page-app"></a>De app met één pagina uitvoeren

1. Open een ander console venster en ga naar de map met het Java script SPA-voor beeld. Bijvoorbeeld:

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

1. Ga `http://localhost:6420` in uw browser naar om de toepassing weer te geven.

    ![Voor beeld-app met één pagina die wordt weer gegeven in de browser](./media/tutorial-single-page-app-webapi/tutorial-01-sample-app-browser.png)

1. Meld u aan met het e-mail adres en het wacht woord dat u in de [vorige zelf studie](tutorial-single-page-app.md)hebt gebruikt. Wanneer de aanmelding is geslaagd, wordt het `User 'Your Username' logged-in` bericht weer gegeven.
1. Selecteer de knop **API aanroepen** . De beveiligd-wachtwoord verificatie verkrijgt een machtigings toekenning van Azure AD B2C en opent vervolgens de beveiligde web-API om de naam van de aangemelde gebruiker weer te geven:

    ![Toepassing met één pagina in de browser die het JSON-resultaat van de gebruikers naam heeft geretourneerd door de API](./media/tutorial-single-page-app-webapi/tutorial-02-call-api.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u:

> [!div class="checklist"]
> * De registratie van een web-API-toepassing in uw Azure AD B2C Tenant is gemaakt
> * Geconfigureerde bereiken voor de Web-API
> * Machtigingen verleend aan de Web-API
> * Een voor beeld van een web-API-code gewijzigd om samen te werken met uw Tenant

Nu u een beveiligd-wachtwoord verificatie-verzoek hebt gezien vanuit een beveiligde web-API, krijgt u een beter inzicht in hoe deze toepassings typen met elkaar en met Azure AD B2C werken.

> [!div class="nextstepaction"]
> [Toepassings typen die kunnen worden gebruikt in Active Directory B2C >](application-types.md)

<!-- Links - EXTERNAL -->
[github-js-spa]: https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp
