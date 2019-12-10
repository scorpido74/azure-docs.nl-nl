---
title: 'Zelf studie: toegang verlenen aan een node. js-Web-API vanuit een bureaublad toepassing'
description: Zelf studie over het gebruik van Active Directory B2C om een node. js-Web-API te beveiligen en deze aan te roepen vanuit een .NET-desktop-app.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 10/12/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: f271dd23501aa0514faff29f638f695c80c844f2
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2019
ms.locfileid: "74949769"
---
# <a name="tutorial-grant-access-to-a-nodejs-web-api-from-a-desktop-app-using-azure-active-directory-b2c"></a>Zelfstudie: toegang verlenen aan een web-API van Node.js vanuit een desktop-app met behulp van Azure Active Directory B2C

In deze zelf studie leert u hoe u een node. js-Web-API aanroept die wordt beveiligd door Azure Active Directory B2C (Azure AD B2C) vanuit een Windows Presentation Foundation-bureaublad toepassing (WPF), ook beschermd door Azure AD B2C.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een web-API-toepassing toevoegen
> * Bereiken voor een web-API configureren
> * Machtigingen verlenen aan de web-API
> * Voorbeeld bijwerken voor gebruik van de toepassing

## <a name="prerequisites"></a>Vereisten

Voer de stappen en vereisten in de [zelf studie uit: Verifieer gebruikers in een systeem eigen desktop-client](active-directory-b2c-tutorials-desktop-app.md).

## <a name="add-a-web-api-application"></a>Een web-API-toepassing toevoegen

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>Bereiken configureren

Bereiken bieden een manier om toegang tot beveiligde resources te reguleren. Bereiken worden door de web-API gebruikt om toegangsbeheer op basis van een bereik te implementeren. Sommige gebruikers kunnen bijvoorbeeld zowel lees- als schrijftoegang hebben, terwijl andere gebruikers mogelijk alleen-lezen-machtigingen hebben. In deze zelfstudie gaat u lees- en schrijfmachtigingen definiëren voor de web-API.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

Noteer de waarde onder **scopes** voor het `demo.read` bereik voor gebruik in een latere stap wanneer u de bureaublad toepassing configureert. De volledige bereik waarde is vergelijkbaar met `https://contosob2c.onmicrosoft.com/api/demo.read`.

## <a name="grant-permissions"></a>Machtigingen verlenen

Als u een beveiligde web-API wilt aanroepen vanuit een systeem eigen client toepassing, moet u de geregistreerde systeem eigen client toepassings machtigingen verlenen aan de Web-API die u hebt geregistreerd in Azure AD B2C.

In de hand leiding voor vereisten hebt u een systeem eigen client toepassing geregistreerd met de naam *nativeapp1*. Met de volgende stappen wordt de registratie van de systeem eigen toepassing geconfigureerd met de API-bereiken die u hebt gemaakt voor *webapi1* in de vorige sectie. Op die manier kan de bureaublad toepassing een toegangs token verkrijgen van Azure AD B2C dat de Web-API kan gebruiken om de toegang tot de resources van het netwerk te controleren. U kunt de voor beelden van de bureaublad toepassing en de Web-API-code later in de zelf studie configureren en uitvoeren.

#### <a name="applicationstabapplications"></a>[Toepassingen](#tab/applications/)

1. Selecteer **Toepassingen** en vervolgens *nativeapp1*.
1. Selecteer **API-toegang** en vervolgens **Toevoegen**.
1. Selecteer *webapi1* in de vervolgkeuzelijst **API selecteren**.
1. Selecteer in de vervolg keuzelijst **bereiken selecteren** de bereiken die u eerder hebt gedefinieerd. Bijvoorbeeld: *demo. Read* en *demo. write*.
1. Selecteer **OK**.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[App-registraties (preview-versie)](#tab/app-reg-preview/)

1. Selecteer **app-registraties (preview)** en selecteer vervolgens de systeem eigen client toepassing die toegang moet hebben tot de API. Bijvoorbeeld *nativeapp1*.
1. Selecteer onder **beheren**de optie **API-machtigingen**.
1. Selecteer onder **geconfigureerde machtigingen** **de optie een machtiging toevoegen**.
1. Selecteer het tabblad **mijn api's** .
1. Selecteer de API waaraan de systeem eigen client toepassing toegang moet worden verleend. Bijvoorbeeld *webapi1*.
1. Vouw onder **machtiging**de optie **demo**uit en selecteer vervolgens de bereiken die u eerder hebt gedefinieerd. Bijvoorbeeld: *demo. Read* en *demo. write*.
1. Selecteer **machtigingen toevoegen**. Wacht een paar minuten voordat u verdergaat met de volgende stap.
1. Selecteer **beheerder toestemming geven voor (uw Tenant naam)** .
1. Selecteer het momenteel aangemelde Administrator-account of Meld u aan met een account in uw Azure AD B2C-Tenant waaraan ten minste de rol van *Cloud toepassings beheerder* is toegewezen.
1. Selecteer **Accepteren**.
1. Selecteer **vernieuwen**en controleer vervolgens of ' verleend voor... ' wordt onder **status** weer gegeven voor beide bereiken. Het kan enkele minuten duren voordat de machtigingen zijn door gegeven.

* * *

Een gebruiker voert een verificatie uit met Azure AD B2C om de WPF-bureaubladtoepassing te kunnen gebruiken. De bureaubladtoepassing verkrijgt een autorisatietoekenning van Azure AD B2C voor toegang tot de beveiligde web-API.

## <a name="configure-the-samples"></a>De voor beelden configureren

Nu de Web-API is geregistreerd en u scopes en machtigingen hebt geconfigureerd, configureert u de bureaublad toepassing en Web-API-voor beelden om uw Azure AD B2C-Tenant te gebruiken.

### <a name="update-the-desktop-application"></a>De bureaublad toepassing bijwerken

In een vereiste voor dit artikel hebt u een WPF- [bureaublad toepassing](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) aangepast zodat u zich kunt aanmelden met een gebruikers stroom in uw Azure AD B2C-Tenant. In deze sectie werkt u diezelfde toepassing bij om te verwijzen naar de Web-API die u eerder hebt geregistreerd, *webapi1*.

1. Open de **Active Directory-B2C-WPF-** oplossing (`active-directory-b2c-wpf.sln`) in Visual Studio.
1. Open het *app.xaml.cs* -bestand in het **Active Directory-B2C-WPF-** project en zoek de volgende variabele-definities.
    1. Vervang de waarde van de variabele `ApiScopes` door de waarde die u eerder hebt vastgelegd toen u de **demo. Read** scope hebt gedefinieerd.
    1. Vervang de waarde van de variabele `ApiEndpoint` door de **omleidings-URI** die u eerder hebt vastgelegd toen u de Web-API registreerde (bijvoorbeeld *webapi1*) in uw Tenant.

    Hier volgt een voorbeeld:

    ```csharp
    public static string[] ApiScopes = { "https://contosob2c.onmicrosoft.com/api/demo.read" };
    public static string ApiEndpoint = "http://localhost:5000";
    ```

### <a name="get-and-update-the-nodejs-api-sample"></a>Het node. js-API-voor beeld ophalen en bijwerken

Vervolgens haalt u het voor beeld van de Web-API-code van node. js op uit GitHub en configureert u deze voor het gebruik van de Web-API die u hebt geregistreerd in uw Azure AD B2C-Tenant.

[Download een ZIP-bestand ](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip) of kloon de voorbeeld-web-app vanuit GitHub.

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

Het Node.js web-API-voorbeeld maakt gebruik van de bibliotheek Passport.js zodat Azure AD B2C aanroepen naar de API kan beveiligen.

1. Open het `index.js`-bestand.
1. Werk deze variabele definities bij met de volgende waarden. Wijzig `<web-API-application-ID>` in de **toepassings-id (client)** van de Web-API die u eerder hebt geregistreerd (*webapi1*). Wijzig `<your-b2c-tenant>` in de naam van uw Azure AD B2C Tenant.

    ```nodejs
    var clientID = "<web-API-application-ID>";
    var b2cDomainHost = "<your-b2c-tenant>.b2clogin.com";
    var tenantIdGuid = "<your-b2c-tenant>.onmicrosoft.com";
    var policyName = "B2C_1_signupsignin1";
    ```
1. Omdat u de API lokaal uitvoert, werkt u het pad in de route voor de methode GET naar `/` in plaats van de locatie van `/hello`van de demo-app:

    ```nodejs
    app.get("/",
    ```

## <a name="run-the-samples"></a>De voorbeelden uitvoeren

### <a name="run-the-nodejs-web-api"></a>De node. js-Web-API uitvoeren

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
3. Meld u aan met het e-mailadres en het wachtwoord dat wordt gebruikt in de zelfstudie [Verificatie van gebruikers met Azure Active Directory B2C in een desktop-app](active-directory-b2c-tutorials-desktop-app.md).
4. Selecteer de knop **API aanroepen** .

De bureaublad toepassing maakt een aanvraag voor de lokaal uitgevoerde Web-API en bij verificatie van een geldig toegangs token wordt de weergave naam van de aangemelde gebruiker weer gegeven.

![Weergave naam die wordt weer gegeven in het bovenste deel venster van de WPF-bureaublad toepassing](media/active-directory-b2c-tutorials-desktop-app-webapi/desktop-app-01-post-api-call.png)

Uw bureaublad toepassing, beveiligd door Azure AD B2C, roept de lokaal uitgevoerde Web-API aan die ook wordt beveiligd door Azure AD B2C.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Een web-API-toepassing toevoegen
> * Bereiken voor een web-API configureren
> * Machtigingen verlenen aan de web-API
> * Voorbeeld bijwerken voor gebruik van de toepassing

> [!div class="nextstepaction"]
> [Zelf studie: id-providers toevoegen aan uw toepassingen in Azure Active Directory B2C](tutorial-add-identity-providers.md)
