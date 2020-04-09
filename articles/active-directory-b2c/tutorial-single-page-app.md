---
title: 'Zelfstudie: Verificatie inschakelen in een app van één pagina'
titleSuffix: Azure AD B2C
description: In deze zelfstudie leert u hoe u Azure Active Directory B2C gebruikt om gebruikers aan te geven voor een op JavaScript gebaseerde toepassing (SPA).
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 04/04/2020
ms.custom: mvc, seo-javascript-september2019
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: d7cd437f597fc34fe83904715fc2e459dfe4550f
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875547"
---
# <a name="tutorial-enable-authentication-in-a-single-page-application-with-azure-ad-b2c"></a>Zelfstudie: Verificatie inschakelen in één pagina-toepassing met Azure AD B2C

In deze zelfstudie ziet u hoe u Azure Active Directory B2C (Azure AD B2C) gebruiken om gebruikers aan te melden en in te loggen in een spa (SPA).

In deze zelfstudie, de eerste in een tweedelige serie:

> [!div class="checklist"]
> * Een antwoord-URL toevoegen aan een toepassing die is geregistreerd in uw Azure AD B2C-tenant
> * Een codevoorbeeld downloaden van GitHub
> * De code van de voorbeeldtoepassing wijzigen om met uw tenant te werken
> * Aanmelden met uw aanmeldings-/aanmeldingsstroom

De [volgende zelfstudie](tutorial-single-page-app-webapi.md) in de reeks maakt het web-API-gedeelte van het codevoorbeeld mogelijk.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

U hebt de volgende Azure AD B2C-resources nodig voordat u verdergaat met de stappen in deze zelfstudie:

* [Azure AD B2C-tenant](tutorial-create-tenant.md)
* [Aanvraag geregistreerd](tutorial-register-applications.md) in uw tenant
* [Gebruikersstromen die](tutorial-create-user-flows.md) in uw tenant zijn gemaakt

Daarnaast hebt u het volgende nodig in uw lokale ontwikkelingsomgeving:

* [Visual Studio Code](https://code.visualstudio.com/) of een andere codeeditor
* [Node.js](https://nodejs.org/en/download/)

## <a name="update-the-application"></a>De toepassing bijwerken

In de tweede zelfstudie die u hebt voltooid als onderdeel van de vereisten, hebt u een webtoepassing geregistreerd in Azure AD B2C. Als u de communicatie met het codevoorbeeld in deze zelfstudie wilt inschakelen, voegt u een antwoord-URL (ook wel een omleidingsURI genoemd) toe aan de toepassingsregistratie.

U de huidige **ervaring met toepassingen** of onze nieuwe uniforme **app-registratie (Preview)** gebruiken om de toepassing bij te werken. [Meer informatie over de nieuwe ervaring](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Toepassingen](#tab/applications/)

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Zorg ervoor dat u de map met uw Azure AD B2C-tenant gebruikt door het **filter Directory + abonnement** in het bovenste menu te selecteren en de map te kiezen die uw tenant bevat.
1. Selecteer **Alle services** in de linkerbovenhoek van de Azure-portal en zoek en selecteer Azure AD **B2C**.
1. Selecteer **Toepassingen** en selecteer vervolgens de toepassing *webapp1*.
1. Voeg onder **Antwoord-URL**`http://localhost:6420` toe.
1. Selecteer **Opslaan**.
1. Noteer op de pagina Eigenschappen de **toepassings-id**. U gebruikt de app-id in een latere stap wanneer u de code in de webtoepassing met één pagina bijwerkt.

#### <a name="app-registrations-preview"></a>[App-registraties (voorbeeld)](#tab/app-reg-preview/)

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer het **filter Directory + abonnement** in het bovenste menu en selecteer vervolgens de map met uw Azure AD B2C-tenant.
1. Selecteer Azure AD **B2C**in het linkermenu . Selecteer Ook **Alle services** en zoek naar Azure AD **B2C**en selecteer deze .
1. Selecteer **App-registraties (Voorbeeld)**, selecteer het tabblad **Bezeten toepassingen** en selecteer vervolgens de *webapp1-toepassing.*
1. Selecteer **Verificatie**en selecteer **Vervolgens Probeer de nieuwe ervaring uit** (indien weergegeven).
1. Selecteer **onder Web**de koppeling URI **toevoegen,** voer `http://localhost:6420`enter en selecteer **Opslaan**.
1. Selecteer **Overzicht**.
1. Neem de **toepassings-id (client) op** voor gebruik in een latere stap wanneer u de code in de webtoepassing met één pagina bijwerkt.

* * *

## <a name="get-the-sample-code"></a>De voorbeeldcode halen

In deze zelfstudie configureert u een codevoorbeeld dat u van GitHub downloadt om met uw B2C-tenant te werken. Het voorbeeld laat zien hoe een toepassing met één pagina Azure AD B2C kan gebruiken voor aanmelding en aanmelding door gebruikers en een beveiligde web-API kan aanroepen (u schakelt de web-API in de volgende zelfstudie in de reeks in).

[Download een ZIP-bestand ](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) of kloon de voorbeeld-web-app vanuit GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

## <a name="update-the-sample"></a>Het voorbeeld bijwerken

Nu u het voorbeeld hebt verkregen, werkt u de code bij met de naam van uw Azure AD B2C-tenant en de toepassings-id die u in een eerdere stap hebt geregistreerd.

1. Open het *bestand authConfig.js* in de *JavaScriptSPA-map.*
1. Werk `msalConfig` in het object het:
    * `clientId`met waarde met de **toepassings-id (client)** die u in een eerdere stap hebt geregistreerd
    * `authority`URI met de naam van uw Azure AD B2C-tenant en de naam van de aanmeldings-/aanmeldingsgebruikersstroom die u hebt gemaakt als onderdeel van de vereisten (bijvoorbeeld *B2C_1_signupsignin1)*

    ```javascript
    const msalConfig = {
        auth: {
            clientId: "00000000-0000-0000-0000-000000000000", // Replace this value with your Application (client) ID
            authority: "https://your-b2c-tenant.b2clogin.com/your-b2c-tenant.onmicrosoft.com/B2C_1_signupsignin1", // Update with your tenant and user flow names
            validateAuthority: false
        },
        cache: {
            cacheLocation: "localStorage",
            storeAuthStateInCookie: true
        }
    };
    ```

## <a name="run-the-sample"></a>De voorbeeldtoepassing uitvoeren

1. Open een consolevenster en wijzig de map met het voorbeeld. Bijvoorbeeld:

    ```console
    cd active-directory-b2c-javascript-msal-singlepageapp
    ```
1. Voer de volgende opdrachten uit:

    ```console
    npm install && npm update
    npm start
    ```

    In het consolevenster wordt het poortnummer weergegeven van de lokaal draaiende Node.js-server:

    ```console
    Listening on port 6420...
    ```
1. Blader `http://localhost:6420` naar de webtoepassing die op uw lokale machine wordt uitgevoerd.

    :::image type="content" source="media/tutorial-single-page-app/web-app-spa-01-not-logged-in.png" alt-text="Webbrowser met toepassing met één pagina die lokaal wordt uitgevoerd":::

### <a name="sign-up-using-an-email-address"></a>Aanmelden met een e-mailadres

Deze voorbeeldtoepassing ondersteunt aanmelden, aanmelden en het opnieuw instellen van wachtwoorden. In deze zelfstudie meldt u zich aan met een e-mailadres.

1. Selecteer **Aanmelden** om de *B2C_1_signupsignin1* gebruikersstroom te starten die u in een eerdere stap hebt opgegeven.
1. Azure AD B2C presenteert een aanmeldingspagina met een aanmeldingskoppeling. Omdat je nog geen account hebt, selecteer je de koppeling **Nu aanmelden.**
1. De aanmeldingswerkstroom presenteert een pagina om de identiteit van de gebruiker te verzamelen en te verifiëren met behulp van een e-mailadres. De aanmeldingswerkstroom verzamelt ook het wachtwoord van de gebruiker en de gevraagde kenmerken die zijn gedefinieerd in de gebruikersstroom.

    Gebruik een geldig e-mailadres en voer de verificatie uit met de verificatiecode. Stel een wachtwoord in. Geef waarden voor de aangevraagde kenmerken op.

    :::image type="content" source="media/tutorial-single-page-app/user-flow-sign-up-workflow-01.png" alt-text="Aanmeldingspagina weergegeven door Azure AD B2C-gebruikersstroom":::

1. Selecteer **Maken** om een lokaal account te maken in de Azure AD B2C-map.

Wanneer u **Maken**selecteert, wordt in de toepassing de naam van de aangemelde gebruiker weergegeven.

:::image type="content" source="media/tutorial-single-page-app/web-app-spa-02-logged-in.png" alt-text="Webbrowser met toepassing met één pagina met ingelogde gebruiker":::

Als u aanmelding wilt testen, selecteert u de knop **Afmelden,** selecteert u **Aanmelden** en meldt u zich aan met het e-mailadres en wachtwoord dat u hebt ingevoerd toen u zich hebt aangemeld.

### <a name="what-about-calling-the-api"></a>Hoe zit het met het aanroepen van de API?

Als u de **knop Api-aanroep** selecteert nadat u zich hebt aangemeld, krijgt u de aanmeldings-/aanmeldingspagina voor gebruikersstroom te zien in plaats van de resultaten van de API-aanroep. Dit wordt verwacht omdat u het API-gedeelte van de toepassing nog niet hebt geconfigureerd om te communiceren met een web-API-toepassing die is geregistreerd in *uw* Azure AD B2C-tenant.

Op dit moment probeert de toepassing nog steeds te communiceren met de API die is geregistreerd in de demotenant (fabrikamb2c.onmicrosoft.com), en omdat u niet bent geverifieerd met die tenant, wordt de aanmeldings-/aanmeldingspagina weergegeven.

Ga naar de volgende zelfstudie in de reeks om de beveiligde API in te schakelen (zie de sectie [Volgende stappen).](#next-steps)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een toepassing van één pagina geconfigureerd om te werken met een gebruikersstroom in uw Azure AD B2C-tenant om aanmeldings- en aanmeldingsmogelijkheden te bieden. U hebt de volgende stappen voltooid:

> [!div class="checklist"]
> * Een antwoord-URL toegevoegd aan een toepassing die is geregistreerd in uw Azure AD B2C-tenant
> * Een codevoorbeeld van GitHub gedownload
> * De code van de voorbeeldtoepassing wijzigen om met uw tenant te werken
> * Aangemeld met uw aanmeldings-/aanmeldingsstroom

Ga nu verder met de volgende zelfstudie in de serie om toegang te verlenen tot een beveiligde web-API van de SPA:

> [!div class="nextstepaction"]
> [Zelfstudie: Web-API beveiligen en verlenen vanuit een toepassing van één pagina >](tutorial-single-page-app-webapi.md)
