---
title: 'Zelfstudie: Verificatie inschakelen voor een toepassing met één pagina'
titleSuffix: Azure AD B2C
description: In deze zelfstudie leert u hoe u Azure Active Directory B2C gebruikt voor het opgeven van gebruikersaanmeldingsreferenties voor een toepassing met één pagina (JavaScript).
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 04/04/2020
ms.custom: mvc, seo-javascript-september2019
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: cd696b6aba96b06a7b84722d61eb40bbfa15c0ff
ms.sourcegitcommit: 0a5bb9622ee6a20d96db07cc6dd45d8e23d5554a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/05/2020
ms.locfileid: "84447931"
---
# <a name="tutorial-enable-authentication-in-a-single-page-application-with-azure-ad-b2c"></a>Zelfstudie: Verificatie inschakelen in een toepassing met één pagina met Azure AD B2C

Deze zelfstudie laat u zien hoe u Azure Active Directory B2C (Azure AD B2C) kunt gebruiken voor het aanmelden en registreren van gebruikers in een toepassing met één pagina.

Deze zelfstudie, de eerste in een tweedelige serie, bevat:

> [!div class="checklist"]
> * Een antwoord-URL toevoegen aan een toepassing die is geregistreerd in uw Azure AD B2C-Tenant
> * Een codevoorbeeld downloaden van GitHub
> * De code van de voorbeeldtoepassing wijzigen zodat deze met uw tenant werkt
> * Meld u aan met uw aanmeldings-/registratiegebruikersstroom

In de [volgende zelfstudie](tutorial-single-page-app-webapi.md) wordt uitgelegd hoe het Web-API-gedeelte van het codevoorbeeld wordt ingeschakeld.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

U hebt de volgende Azure AD B2C-resources nodig om door te gaan met de stappen in deze zelfstudie:

* [Azure AD B2C-tenant](tutorial-create-tenant.md)
* [Toepassing](tutorial-register-applications.md) die is geregistreerd in uw tenant
* [Gebruikersstromen die zijn gemaakt](tutorial-create-user-flows.md) in uw tenant

Daarnaast hebt u het volgende nodig voor uw lokale ontwikkelomgeving:

* [Visual Studio Code](https://code.visualstudio.com/) of een andere code-editor
* [Node.js](https://nodejs.org/en/download/)

## <a name="update-the-application"></a>De toepassing bijwerken

In de tweede zelfstudie die u als onderdeel van de vereisten hebt voltooid, hebt u een webtoepassing geregistreerd in Azure AD B2C. Als u communicatie wilt inschakelen voor het codevoorbeeld in deze zelfstudie, voegt u een antwoord-URL (ook wel een omleidings-URL genoemd) toe aan de registratie van de toepassing.

Als u een toepassing wilt bijwerken in de Azure AD B2C-tenant, kunt u de nieuwe uniforme ervaring voor **App-registraties** of de verouderde ervaring **Toepassingen (verouderd)** gebruiken. [Meer informatie over de nieuwe ervaring](https://aka.ms/b2cappregtraining).

#### <a name="app-registrations"></a>[App-registraties](#tab/app-reg-ga/)

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer het filter **Map + Abonnement** in het bovenste menu en selecteer vervolgens de map die uw Azure AD B2C-tenant bevat.
1. Selecteer **Azure AD B2C** in het linkermenu. Of selecteer **Alle services** en zoek naar en selecteer **Azure AD B2C**.
1. Selecteer **App-registraties**, selecteer het tabblad **Toepassingen in eigendom** en selecteer vervolgens de toepassing *webapp1*.
1. Selecteer onder **Web** de koppeling **URL toevoegen** en voer `http://localhost:6420` in.
1. Selecteer onder **Impliciete toekenning** de selectievakjes voor **Toegangstokens** en **Id-tokens** en selecteer **Opslaan**.
1. Selecteer **Overzicht**.
1. Registreer de **toepassings-ID (client)** voor gebruik in een latere stap wanneer u de code in de webtoepassing met één pagina bijwerkt.

#### <a name="applications-legacy"></a>[Toepassingen (verouderd)](#tab/applications-legacy/)

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C-tenant bevat door in het bovenste menu te klikken op het filter **Map en abonnement** en de map te kiezen waarin de tenant zich bevindt.
1. Kies **Alle services** linksboven in de Azure-portal en zoek vervolgens **Azure AD B2C** en selecteer deze.
1. Selecteer **Toepassingen (verouderd)** en selecteer vervolgens de toepassing *webapp1*.
1. Voeg onder **Antwoord-URL**`http://localhost:6420` toe.
1. Selecteer **Opslaan**.
1. Op de pagina eigenschappen stelt u de **Toepassings-ID** in. U gebruikt de toepassings-ID in een latere stap wanneer u de code in de webtoepassing met één pagina bijwerkt.

* * *

## <a name="get-the-sample-code"></a>De voorbeeldcode halen

In deze zelfstudie configureert u een codevoorbeeld dat u van GitHub downloadt om met uw B2C-tenant te werken. Het voorbeeld laat zien hoe een toepassing met één pagina Azure AD B2C kan gebruiken voor het aanmelden en registreren van gebruikers en bij het aanroepen van een beveiligde web-API (de Web-API wordt ingeschakeld in de volgende zelfstudie van de serie).

[Download een ZIP-bestand ](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) of kloon de voorbeeld-web-app vanuit GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

## <a name="update-the-sample"></a>Het voorbeeld bijwerken

Nu u het voorbeeld hebt verkregen, werkt u de code bij met de naam van uw Azure AD B2C=tenant en de toepassings-ID die u in een eerdere stap hebt ingesteld.

1. Open het bestand *authConfig.js* in de map *JavaScriptSPA*.
1. Werk het `msalConfig`-object bij
    * `clientId` met de waarde van de **toepassings-ID (client)** die u in een eerdere stap hebt ingesteld
    * `authority` URL met de naam van uw Azure AD B2C-tenant en de naam van de registratie-/aanmeldingsgebruikersstroom die u hebt gemaakt als onderdeel van de vereisten (bijvoorbeeld *B2C_1_signupsignin1*)

    ```javascript
    const msalConfig = {
        auth: {
          clientId: "00000000-0000-0000-0000-000000000000", // Replace this value with your Application (client) ID
          authority: b2cPolicies.authorities.signUpSignIn.authority,
          validateAuthority: false
        },
        cache: {
          cacheLocation: "localStorage",
          storeAuthStateInCookie: true
        }
    };

    const loginRequest = {
       scopes: ["openid", "profile"],
    };

    const tokenRequest = {
      scopes: apiConfig.b2cScopes // i.e. ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"]
    };
    ```

## <a name="run-the-sample"></a>De voorbeeldtoepassing uitvoeren

1. Open een consolevenster en ga naar de map met het voorbeeld. Bijvoorbeeld:

    ```console
    cd active-directory-b2c-javascript-msal-singlepageapp
    ```
1. Voer de volgende opdrachten uit:

    ```console
    npm install && npm update
    npm start
    ```

    In het consolevenster wordt het poortnummer van de lokaal uitgevoerde Node.js-server weergegeven:

    ```console
    Listening on port 6420...
    ```
1. Blader naar `http://localhost:6420` om de webtoepassing weer te geven die op uw lokale machine wordt uitgevoerd.

    :::image type="content" source="media/tutorial-single-page-app/web-app-spa-01-not-logged-in.png" alt-text="Webbrowser met toepassing met één pagina die lokaal wordt uitgevoerd":::

### <a name="sign-up-using-an-email-address"></a>Aanmelden met een e-mailadres

Deze voorbeeldtoepassing ondersteunt registreren, aanmelden en wachtwoorden opnieuw instellen. In deze zelfstudie meldt u zich aan met een e-mailadres.

1. Selecteer **Aanmelden** om de *B2C_1_signupsignin1*-gebruikersstroom te initiëren die u in een eerdere stap hebt opgegeven.
1. Azure AD B2C toont een aanmeldingspagina met een registratielink. Aangezien u nog geen account hebt, klikt u op de link **Nu registreren**.
1. Tijdens de aanmeldingswerkstroom wordt een pagina weergegeven waarmee de identiteit wordt opgehaald en gecontroleerd van de gebruiker die een e-mailadres heeft gebruikt. Tijdens de aanmeldingswerkstroom worden ook het wachtwoord van de gebruiker en de aangevraagde kenmerken opgehaald die in de gebruikersstroom zijn gedefinieerd.

    Gebruik een geldig e-mailadres en voer de verificatie uit met de verificatiecode. Stel een wachtwoord in. Geef waarden voor de aangevraagde kenmerken op.

    :::image type="content" source="media/tutorial-single-page-app/user-flow-sign-up-workflow-01.png" alt-text="Registratiepagina die wordt weergegeven door de Azure AD B2C-gebruikersstroom":::

1. Selecteer **Maken** om een lokaal account te maken in de Azure AD B2C-directory.

Wanneer u **Maken**selecteert, wordt de naam van de aangemelde gebruiker weergegeven in de toepassing.

:::image type="content" source="media/tutorial-single-page-app/web-app-spa-02-logged-in.png" alt-text="Webbrowser waarin een toepassing met één pagina met een aangemelde gebruiker wordt weergegeven":::

Als u het aanmelden wilt testen, selecteert u de knop **Afmelden** en vervolgens **Aanmelden** en meldt u zich aan met het e-mailadres en het wachtwoord dat u hebt ingevoerd toen u zich hebt geregistreerd.

### <a name="what-about-calling-the-api"></a>Hoe zit het met het aanroepen van de API?

Als u na het aanmelden de **Aanroep-API** selecteert, ziet u de pagina voor de registratie-/aanmeldingsgebruikersstroom in plaats van de resultaten van de API-aanroep. Dit komt omdat u het API-gedeelte van de toepassing nog niet hebt geconfigureerd om te communiceren met een web-API-toepassing die is geregistreerd in *uw* Azure AD B2C-tenant.

Op dit moment probeert de toepassing nog steeds te communiceren met de API die is geregistreerd in de demotenant (fabrikamb2c.onmicrosoft.com), en omdat u niet met die tenant bent geverifieerd, wordt de registratie- en aanmeldingspagina weergegeven.

Ga verder met de volgende zelfstudie van deze serie om de beveiligde API in te schakelen (Zie de sectie [Volgende stappen](#next-steps)).

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een toepassing met één pagina geconfigureerd om met een gebruikersstroom in uw Azure AD B2C-tenant te werken om registratie en aanmelding mogelijk te maken. U hebt de volgende stappen uitgevoerd:

> [!div class="checklist"]
> * U hebt een antwoord-URL toegevoegd aan een toepassing die is geregistreerd in uw Azure AD B2C-tenant
> * U hebt een codevoorbeeld gedownload van GitHub
> * De voorbeeldcode van de toepassing is gewijzigd zodat deze met uw tenant werkt
> * U hebt zich aangemeld met uw aanmeldings-/registratiegebruikersstroom

Ga nu verder met de volgende zelfstudie van deze serie om toegang te verlenen tot een beveiligde web-API van de toepassing met één pagina:

> [!div class="nextstepaction"]
> [Zelfstudie: Beveiligen en toegang verlenen tot de web-API vanuit een toepassing met één pagina >](tutorial-single-page-app-webapi.md)
