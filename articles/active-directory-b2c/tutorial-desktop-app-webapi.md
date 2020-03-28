---
title: 'Zelfstudie: Toegang verlenen tot een Node.js-web-API vanuit een bureaubladtoepassing'
description: Zelfstudie over het gebruik van Active Directory B2C om een Node.js-web-API te beschermen en aan te roepen vanuit een .NET-bureaubladapp.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/12/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 59670cda68f54e4c0b20b361f0688e6766acba61
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78183369"
---
# <a name="tutorial-grant-access-to-a-nodejs-web-api-from-a-desktop-app-using-azure-active-directory-b2c"></a>Zelfstudie: toegang verlenen aan een web-API van Node.js vanuit een desktop-app met behulp van Azure Active Directory B2C

In deze zelfstudie ziet u hoe u een Node.js-web-API aanroept die is beveiligd door Azure Active Directory B2C (Azure AD B2C) vanuit een WPF-bureaubladapp (Windows Presentation Foundation), die ook wordt beschermd door Azure AD B2C.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een web-API-toepassing toevoegen
> * Bereiken voor een web-API configureren
> * Machtigingen verlenen aan de web-API
> * Voorbeeld bijwerken voor gebruik van de toepassing

## <a name="prerequisites"></a>Vereisten

Voer de stappen en vereisten uit in [Zelfstudie: Gebruikers verifiëren in een native desktopclient.](tutorial-desktop-app.md)

## <a name="add-a-web-api-application"></a>Een web-API-toepassing toevoegen

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>Bereiken configureren

Bereiken bieden een manier om toegang tot beveiligde resources te reguleren. Bereiken worden door de web-API gebruikt om toegangsbeheer op basis van een bereik te implementeren. Sommige gebruikers kunnen bijvoorbeeld zowel lees- als schrijftoegang hebben, terwijl andere gebruikers mogelijk alleen-lezen-machtigingen hebben. In deze zelfstudie gaat u lees- en schrijfmachtigingen definiëren voor de web-API.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

Noteer de waarde onder `demo.read` **SCOPES** voor het bereik dat in een latere stap moet worden gebruikt wanneer u de bureaubladtoepassing configureert. De volledige scopewaarde `https://contosob2c.onmicrosoft.com/api/demo.read`is vergelijkbaar met .

## <a name="grant-permissions"></a>Machtigingen verlenen

Als u een beveiligde web-API wilt aanroepen vanuit een native clienttoepassing, moet u de beheerders van de geregistreerde native clienttoepassing toestaan aan de web-API die u hebt geregistreerd in Azure AD B2C.

In de vereiste zelfstudie hebt u een native clienttoepassing met de naam *nativeapp1*geregistreerd. In de volgende stappen wordt de registratie van native toepassingen geconfigureerd met de API-scopes die u in de vorige sectie voor *webapi1* hebt weergegeven. Hierdoor kan de bureaubladtoepassing een toegangstoken verkrijgen van Azure AD B2C dat de web-API kan gebruiken om de resources met een bereik te verifiëren en te bieden. U configureert en voert later in de zelfstudie zowel de voorbeelden van de bureaubladtoepassing als de web-API-code uit.

#### <a name="applications"></a>[Toepassingen](#tab/applications/)

1. Selecteer **Toepassingen** en vervolgens *nativeapp1*.
1. Selecteer **API-toegang** en vervolgens **Toevoegen**.
1. Selecteer *webapi1* in de vervolgkeuzelijst **API selecteren**.
1. Selecteer **in** de vervolgkeuzelijst Bereik selecteren de scopes die u eerder hebt gedefinieerd. Bijvoorbeeld, *demo.read* en *demo.write*.
1. Selecteer **OK**.

#### <a name="app-registrations-preview"></a>[App-registraties (voorbeeld)](#tab/app-reg-preview/)

1. Selecteer **App-registraties (Voorbeeld)** en selecteer vervolgens de native clienttoepassing die toegang moet hebben tot de API. Bijvoorbeeld *nativeapp1*.
1. Selecteer **API-machtigingen** **onder Beheren**.
1. Selecteer **Onder Geconfigureerde machtigingen**de optie Een machtiging **toevoegen**.
1. Selecteer het tabblad **Mijn API's.**
1. Selecteer de API waartoe de native clienttoepassing toegang moet krijgen. Bijvoorbeeld *webapi1*.
1. Vouw **onder Machtiging**demo uit en selecteer vervolgens de scopes die u eerder hebt gedefinieerd. **demo** Bijvoorbeeld, *demo.read* en *demo.write*.
1. Selecteer **Machtigingen toevoegen**. Zoals aangegeven, wacht een paar minuten voordat u doorgaat naar de volgende stap.
1. Selecteer **Toestemming voor beheerders verlenen voor (uw tenantnaam)**.
1. Selecteer uw momenteel aangemelde beheerdersaccount of meld u aan met een account in uw Azure AD B2C-tenant die ten minste de functie *voor cloudtoepassingsbeheerder* is toegewezen.
1. Selecteer **Accepteren**.
1. Selecteer **Vernieuwen**en controleer vervolgens of 'Verleend voor ..." wordt weergegeven onder **Status** voor beide scopes. Het kan enkele minuten duren voordat de machtigingen worden doorgegeven.

* * *

Een gebruiker voert een verificatie uit met Azure AD B2C om de WPF-bureaubladtoepassing te kunnen gebruiken. De bureaubladtoepassing verkrijgt een autorisatietoekenning van Azure AD B2C voor toegang tot de beveiligde web-API.

## <a name="configure-the-samples"></a>De voorbeelden configureren

Nu de web-API is geregistreerd en u scopes en machtigingen hebt geconfigureerd, configureert u de desktoptoepassing en web-API-voorbeelden om uw Azure AD B2C-tenant te gebruiken.

### <a name="update-the-desktop-application"></a>De bureaubladtoepassing bijwerken

In een voorwaarde voor dit artikel hebt u een [WPF-bureaubladtoepassing](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) gewijzigd om aanmelding met een gebruikersstroom in uw Azure AD B2C-tenant in te schakelen. In deze sectie werkt u diezelfde toepassing bij om te verwijzen naar de web-API die u eerder hebt geregistreerd, *webapi1*.

1. Open de **active-directory-b2c-wpf-oplossing** (`active-directory-b2c-wpf.sln`) in Visual Studio.
1. Open in het **active-directory-b2c-wpf-project** het *App.xaml.cs-bestand* en vind de volgende variabele definities.
    1. Vervang de waarde `ApiScopes` van de variabele door de waarde die u eerder hebt geregistreerd toen u het **demo.read-bereik** definieerde.
    1. Vervang de waarde `ApiEndpoint` van de variabele door de **OmleidingsURI die** u eerder hebt geregistreerd toen u de web-API (bijvoorbeeld *webapi1)* in uw tenant registreerde.

    Hier volgt een voorbeeld:

    ```csharp
    public static string[] ApiScopes = { "https://contosob2c.onmicrosoft.com/api/demo.read" };
    public static string ApiEndpoint = "http://localhost:5000";
    ```

### <a name="get-and-update-the-nodejs-api-sample"></a>Het API-voorbeeld van Node.js inpakken en bijwerken

Download vervolgens het voorbeeld van de Node.js-webAPI-code van GitHub en configureer deze om de web-API te gebruiken die u hebt geregistreerd in uw Azure AD B2C-tenant.

[Download een ZIP-bestand ](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip) of kloon de voorbeeld-web-app vanuit GitHub.

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

Het Node.js web-API-voorbeeld maakt gebruik van de bibliotheek Passport.js zodat Azure AD B2C aanroepen naar de API kan beveiligen.

1. Open het `index.js`-bestand.
1. Werk deze variabele definities bij met de volgende waarden. Wijziging `<web-API-application-ID>` in de **toepassings-id (client)** van de web-API die u eerder hebt geregistreerd *(webapi1*). Wijzig `<your-b2c-tenant>` de naam van uw Azure AD B2C-tenant.

    ```nodejs
    var clientID = "<web-API-application-ID>";
    var b2cDomainHost = "<your-b2c-tenant>.b2clogin.com";
    var tenantIdGuid = "<your-b2c-tenant>.onmicrosoft.com";
    var policyName = "B2C_1_signupsignin1";
    ```
1. Aangezien u de API lokaal uitvoert, werkt u het pad `/` in de route voor de `/hello`GET-methode bij in plaats van de locatie van de demo-app van:

    ```nodejs
    app.get("/",
    ```

## <a name="run-the-samples"></a>De voorbeelden uitvoeren

### <a name="run-the-nodejs-web-api"></a>De web-API van Node.js uitvoeren

1. Start een opdrachtprompt voor Node.js.
2. Ga naar de map met het Node.js-voorbeeld. Bijvoorbeeld `cd c:\active-directory-b2c-javascript-nodejs-webapi`
3. Voer de volgende opdrachten uit:
    ```console
    npm install && npm update
    ```
    ```console
    node index.js
    ```

### <a name="run-the-desktop-application"></a>De desktop-app uitvoeren

1. Open de oplossing **active directory-b2c wpf** in Visual Studio.
2. Druk op **F5** om de desktop-app uit te voeren.
3. Meld u aan met het e-mailadres en het wachtwoord dat wordt gebruikt in de zelfstudie [Verificatie van gebruikers met Azure Active Directory B2C in een desktop-app](tutorial-desktop-app.md).
4. Selecteer de **knop API-aanroepen.**

De bureaubladtoepassing doet een verzoek aan de lokaal draaiende web-API en toont na verificatie van een geldig toegangstoken de weergavenaam van de aangemelde gebruiker.

![Weergavenaam in het bovenste deelvenster van de WPF-bureaubladtoepassing](./media/tutorial-desktop-app-webapi/desktop-app-01-post-api-call.png)

Uw bureaubladtoepassing, beschermd door Azure AD B2C, roept de lokaal draaiende web-API aan die ook wordt beschermd door Azure AD B2C.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Een web-API-toepassing toevoegen
> * Bereiken voor een web-API configureren
> * Machtigingen verlenen aan de web-API
> * Voorbeeld bijwerken voor gebruik van de toepassing

> [!div class="nextstepaction"]
> [Zelfstudie: identiteitsproviders toevoegen aan uw toepassingen in Azure Active Directory B2C](tutorial-add-identity-providers.md)
