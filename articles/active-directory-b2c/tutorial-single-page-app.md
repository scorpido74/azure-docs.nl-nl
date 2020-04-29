---
title: 'Zelf studie: verificatie inschakelen in een app met één pagina'
titleSuffix: Azure AD B2C
description: In deze zelf studie leert u hoe u Azure Active Directory B2C kunt gebruiken om gebruikers aanmelding te bieden voor een op Java script gebaseerde toepassing met één pagina (SPA).
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80875547"
---
# <a name="tutorial-enable-authentication-in-a-single-page-application-with-azure-ad-b2c"></a>Zelf studie: verificatie inschakelen in een toepassing met één pagina met Azure AD B2C

In deze zelf studie wordt uitgelegd hoe u Azure Active Directory B2C (Azure AD B2C) gebruikt om gebruikers te registreren en aan te melden bij een toepassing met één pagina (SPA).

In deze zelf studie wordt de eerste in een reeks van twee delen:

> [!div class="checklist"]
> * Een antwoord-URL toevoegen aan een toepassing die is geregistreerd in uw Azure AD B2C-Tenant
> * Een code voorbeeld downloaden van GitHub
> * De code van de voorbeeld toepassing wijzigen zodat deze samenwerkt met uw Tenant
> * Meld u aan met uw aanmeldings-en aanmeldings gebruikers stroom

Met de [volgende zelf studie](tutorial-single-page-app-webapi.md) in de reeks wordt de Web-API-gedeelte van het code voorbeeld ingeschakeld.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

U hebt de volgende Azure AD B2C resources nodig om door te gaan met de stappen in deze zelf studie:

* [Azure AD B2C Tenant](tutorial-create-tenant.md)
* De [toepassing is geregistreerd](tutorial-register-applications.md) in uw Tenant
* [Gebruikers stromen die zijn gemaakt](tutorial-create-user-flows.md) in uw Tenant

Daarnaast hebt u het volgende nodig in uw lokale ontwikkel omgeving:

* [Visual Studio code](https://code.visualstudio.com/) of een andere code-editor
* [Node.js](https://nodejs.org/en/download/)

## <a name="update-the-application"></a>De toepassing bijwerken

In de tweede zelf studie die u als onderdeel van de vereisten hebt voltooid, hebt u een webtoepassing geregistreerd in Azure AD B2C. Als u communicatie met het code voorbeeld in deze zelf studie wilt inschakelen, voegt u een antwoord-URL (ook wel een omleidings-URI genoemd) toe aan de registratie van de toepassing.

U kunt de huidige **toepassingen** ervaring of onze nieuwe **Preview-ervaring (Unified app-registraties)** gebruiken om de toepassing bij te werken. [Meer informatie over de nieuwe ervaring](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Toepassingen](#tab/applications/)

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Zorg ervoor dat u de map met uw Azure AD B2C-Tenant gebruikt door het filter **Directory + abonnement** te selecteren in het bovenste menu en de map te kiezen die uw Tenant bevat.
1. Selecteer **alle services** in de linkerbovenhoek van de Azure Portal, zoek naar en selecteer **Azure AD B2C**.
1. Selecteer **Toepassingen** en selecteer vervolgens de toepassing *webapp1*.
1. Voeg onder **Antwoord-URL**`http://localhost:6420` toe.
1. Selecteer **Opslaan**.
1. Noteer de **toepassings-id**op de pagina Eigenschappen. U gebruikt de App-ID in een latere stap wanneer u de code in de webtoepassing met één pagina bijwerkt.

#### <a name="app-registrations-preview"></a>[App-registraties (preview-versie)](#tab/app-reg-preview/)

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer het filter **Directory + abonnement** in het bovenste menu en selecteer vervolgens de map die uw Azure AD B2C Tenant bevat.
1. Selecteer in het linkermenu **Azure AD B2C**. U kunt ook **alle services** selecteren en **Azure AD B2C**zoeken en selecteren.
1. Selecteer **app-registraties (preview)**, selecteer het tabblad **toepassingen die eigendom** zijn en selecteer vervolgens de toepassing *webapp1* .
1. Selecteer **verificatie**en selecteer vervolgens **de nieuwe ervaring uitproberen** (indien weer gegeven).
1. Selecteer onder **Web**de koppeling **URI toevoegen** , Voer `http://localhost:6420`in en selecteer vervolgens **Opslaan**.
1. Selecteer **Overzicht**.
1. Noteer de **id van de toepassing (client)** voor gebruik in een latere stap wanneer u de code in de webtoepassing met één pagina bijwerkt.

* * *

## <a name="get-the-sample-code"></a>De voorbeeldcode halen

In deze zelf studie configureert u een code voorbeeld dat u van GitHub downloadt om met uw B2C-Tenant te werken. Het voor beeld laat zien hoe een toepassing met één pagina Azure AD B2C kan gebruiken voor aanmelding bij gebruikers en bij het aanroepen van een beveiligde web-API (u schakelt de Web-API in in de volgende zelf studie in de reeks).

[Download een ZIP-bestand ](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) of kloon de voorbeeld-web-app vanuit GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

## <a name="update-the-sample"></a>Het voor beeld bijwerken

Nu u het voor beeld hebt verkregen, werkt u de code bij met de naam van uw Azure AD B2C Tenant en de toepassings-ID die u in een eerdere stap hebt vastgelegd.

1. Open het bestand *authConfig. js* in de map *JavaScriptSPA* .
1. In het `msalConfig` object update:
    * `clientId`met de waarde van de **toepassings-id** die u in een eerdere stap hebt vastgelegd
    * `authority`URI met uw Azure AD B2C Tenant naam en de naam van de gebruikers stroom voor registratie/aanmelding die u hebt gemaakt als onderdeel van de vereisten (bijvoorbeeld *B2C_1_signupsignin1*)

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

1. Open een console venster en ga naar de map met het voor beeld. Bijvoorbeeld:

    ```console
    cd active-directory-b2c-javascript-msal-singlepageapp
    ```
1. Voer de volgende opdrachten uit:

    ```console
    npm install && npm update
    npm start
    ```

    In het console venster wordt het poort nummer van de lokaal uitgevoerde node. js-server weer gegeven:

    ```console
    Listening on port 6420...
    ```
1. Blader naar `http://localhost:6420` om de webtoepassing weer te geven die op uw lokale machine wordt uitgevoerd.

    :::image type="content" source="media/tutorial-single-page-app/web-app-spa-01-not-logged-in.png" alt-text="Webbrowser met een toepassing met één pagina die lokaal wordt uitgevoerd":::

### <a name="sign-up-using-an-email-address"></a>Aanmelden met een e-mailadres

Deze voorbeeld toepassing ondersteunt registreren, aanmelden en wacht woord opnieuw instellen. In deze zelf studie meldt u zich aan met een e-mail adres.

1. Selecteer **Aanmelden** om de *B2C_1_signupsignin1* gebruikers stroom te initiëren die u in een eerdere stap hebt opgegeven.
1. Azure AD B2C geeft een aanmeldings pagina weer die een koppeling voor aanmelden bevat. Omdat u nog geen account hebt, selecteert u de koppeling **nu registreren** .
1. De registratie werk stroom bevat een pagina voor het verzamelen en verifiëren van de identiteit van de gebruiker met behulp van een e-mail adres. De registratie werk stroom verzamelt ook het wacht woord van de gebruiker en de aangevraagde kenmerken die in de gebruikers stroom zijn gedefinieerd.

    Gebruik een geldig e-mailadres en voer de verificatie uit met de verificatiecode. Stel een wachtwoord in. Geef waarden voor de aangevraagde kenmerken op.

    :::image type="content" source="media/tutorial-single-page-app/user-flow-sign-up-workflow-01.png" alt-text="Pagina registreren die wordt weer gegeven door Azure AD B2C gebruikers stroom":::

1. Selecteer **maken** om een lokaal account te maken in de Azure AD B2C Directory.

Wanneer u **maken**selecteert, wordt de naam van de aangemelde gebruiker weer gegeven in de toepassing.

:::image type="content" source="media/tutorial-single-page-app/web-app-spa-02-logged-in.png" alt-text="Webbrowser waarin een toepassing met één pagina wordt weer gegeven met een aangemelde gebruiker":::

Als u het aanmelden wilt testen, selecteert u de knop **Afmelden** , selecteert u **Aanmelden** en meldt u zich aan met het e-mail adres en het wacht woord dat u hebt ingevoerd toen u zich hebt geregistreerd.

### <a name="what-about-calling-the-api"></a>Hoe zit het met het aanroepen van de API?

Als u de knop **API aanroepen** selecteert nadat u zich hebt aangemeld, wordt de pagina registratie-en aanmeldings gebruikers stroom weer gegeven in plaats van de resultaten van de API-aanroep. Dit wordt verwacht omdat u het API-gedeelte van de toepassing nog niet hebt geconfigureerd om te communiceren met een web API-toepassing die is geregistreerd in *uw* Azure AD B2C-Tenant.

Op dit moment probeert de toepassing nog steeds te communiceren met de API die is geregistreerd in de demo Tenant (fabrikamb2c.onmicrosoft.com), en omdat u niet met die Tenant bent geverifieerd, wordt de registratie-en aanmeldings pagina weer gegeven.

Ga verder met de volgende zelf studie in de reeks in om de beveiligde API in te scha kelen (Zie de sectie [volgende stappen](#next-steps) ).

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u een toepassing met één pagina geconfigureerd om te werken met een gebruikers stroom in uw Azure AD B2C-Tenant om registratie en aanmeldings mogelijkheden te bieden. U hebt de volgende stappen uitgevoerd:

> [!div class="checklist"]
> * Er is een antwoord-URL toegevoegd aan een toepassing die is geregistreerd in uw Azure AD B2C-Tenant
> * Er is een code voorbeeld gedownload van GitHub
> * De voorbeeld code van de toepassing is gewijzigd zodat deze samenwerkt met uw Tenant
> * Aangemeld met uw aanmeldings-en aanmeldings gebruikers stroom

Ga nu verder met de volgende zelf studie in de reeks om toegang te verlenen tot een beveiligde web-API van de beveiligd-wachtwoord verificatie:

> [!div class="nextstepaction"]
> [Zelf studie: een toepassing met één pagina beveiligen en toegang verlenen tot Web-API >](tutorial-single-page-app-webapi.md)
