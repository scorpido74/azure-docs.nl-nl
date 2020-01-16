---
title: Gebruikers aanmelden in Java script-apps met één pagina | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over hoe een Java script-app een API kan hebben die toegangs tokens vereist met het micro soft Identity-platform.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:JavaScript
ms.topic: quickstart
ms.workload: identity
ms.date: 04/11/2019
ms.author: nacanuma
ms.collection: M365-identity-device-management
ms.openlocfilehash: f003daea188c6f556d0981c83c98f3328362f864
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75975116"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-a-javascript-spa"></a>Snelstartgids: gebruikers aanmelden en een toegangs token verkrijgen in een Java script-beveiligd-wachtwoord verificatie

In deze Snelstartgids gebruikt u een code voorbeeld om te zien hoe een Java script-toepassing met één pagina (SPA) gebruikers van persoonlijke accounts, werk accounts en school accounts kan aanmelden. Een Java script-SPA kan ook een toegangs token verkrijgen om de Microsoft Graph-API of een web-API aan te roepen. (Zie [hoe het voor beeld werkt](#how-the-sample-works) voor een illustratie.)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Node.js](https://nodejs.org/en/download/).
* [Visual Studio code](https://code.visualstudio.com/download) (om project bestanden te bewerken) of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) (ro voert het project uit als een Visual Studio-oplossing).

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>De quickstart-toepassing registreren en downloaden
> Gebruik een van de volgende opties om uw Quick Start-toepassing te starten.
>
> ### <a name="option-1-express-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Optie 1 (Express): uw app registreren en automatisch configureren en vervolgens uw voorbeeld code downloaden
>
> 1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
> 1. Als uw account u toegang geeft tot meer dan één Tenant, selecteert u het account in de rechter bovenhoek en stelt u vervolgens uw portal-sessie in op de Azure Active Directory (Azure AD)-Tenant die u wilt gebruiken.
> 1. Ga naar het deel venster nieuwe [Azure Portal-app-registraties](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs) .
> 1. Voer een naam in voor uw toepassing en selecteer **registreren**.
> 1. Volg de instructies om uw nieuwe toepassing te downloaden en automatisch te configureren.
>
> ### <a name="option-2-manual-register-and-manually-configure-your-application-and-code-sample"></a>Optie 2 (hand matig): uw toepassings-en code voorbeeld registreren en hand matig configureren
>
> #### <a name="step-1-register-your-application"></a>Stap 1: Uw toepassing registreren
>
> 1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
>
> 1. Als uw account u toegang geeft tot meer dan één Tenant, selecteert u uw account in de rechter bovenhoek en stelt u vervolgens uw portal-sessie in op de Azure AD-Tenant die u wilt gebruiken.
> 1. Ga naar de pagina micro soft-identiteits platform voor ontwikkel aars [app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) .
> 1. Selecteer **nieuwe registratie**.
> 1. Wanneer de pagina **Een toepassing registreren** wordt weergegeven, voert u een naam in voor de toepassing.
> 1. Selecteer onder **Ondersteunde accounttypen** de optie **Accounts in een organisatieadreslijst en persoonlijke Microsoft-account**.
> 1. Selecteer onder de sectie **omleidings-URI** in de vervolg keuzelijst het **webplatform en** stel vervolgens de waarde in op `http://localhost:30662/`.
> 1. Selecteer **Registreren**. Noteer de waarde van de **toepassing (client)** op de pagina app- **overzicht** voor later gebruik.
> 1. Voor deze quickstart moet de [Impliciete toekenningsstroom](v2-oauth2-implicit-grant-flow.md) zijn ingeschakeld. Selecteer in het linkerdeel venster van de geregistreerde toepassing **verificatie**.
> 1. Selecteer in de sectie **Geavanceerde instellingen** onder **impliciete toekenning**de selectie vakjes **id-tokens** en **toegangs tokens** . ID-tokens en toegangs tokens zijn vereist omdat deze app gebruikers moet aanmelden en een API kan aanroepen.
> 1. Selecteer boven in het deel venster **Opslaan**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Stap 1: uw toepassing configureren in de Azure-portal
> Het code voorbeeld voor deze Quick Start werkt alleen als u een `redirectUri` als `http://localhost:30662/` toevoegt en **impliciete toekenning**inschakelt.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Breng deze wijzigingen voor mij aan]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Al geconfigureerd](media/quickstart-v2-javascript/green-check.png) Uw toepassing is al geconfigureerd met deze kenmerken.

#### <a name="step-2-download-the-project"></a>Stap 2: Het project downloaden

Selecteer de optie die geschikt is voor uw ontwikkel omgeving:

* Als u het project wilt uitvoeren met een webserver met behulp van node. js, [downloadt u de kern project bestanden](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip). Als u de bestanden wilt openen, gebruikt u een editor zoals [Visual Studio code](https://code.visualstudio.com/).

* Beschrijving Als u het project wilt uitvoeren met de IIS-server, [downloadt u het Visual Studio-project](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip). Pak het zip-bestand uit naar een lokale map (bijvoorbeeld *C:\Azure-samples*).

#### <a name="step-3-configure-your-javascript-app"></a>Stap 3: uw Java script-app configureren

> [!div renderon="docs"]
> Bewerk in de map *JavaScriptSPA* *index. html*en stel de waarden `clientID` en `authority` onder `msalConfig`in.

> [!div class="sxs-lookup" renderon="portal"]
> Bewerk in de map *JavaScriptSPA* *index. html*en vervang `msalConfig` door de volgende code:

```javascript
var msalConfig = {
    auth: {
        clientId: "Enter_the_Application_Id_here",
        authority: "https://login.microsoftonline.com/Enter_the_Tenant_info_here",
        redirectUri: "http://localhost:30662/"
    },
    cache: {
        cacheLocation: "localStorage",
        storeAuthStateInCookie: true
    }
};

```
> [!div renderon="portal"]
> > [!NOTE]
> > Deze Quick Start ondersteunt Enter_the_Supported_Account_Info_Here.


> [!div renderon="docs"]
>
> Waar:
> - *\<Enter_the_Application_Id_here >* de **client-id** is van de toepassing die u hebt geregistreerd.
> - *\<Enter_the_Tenant_info_here >* is ingesteld op een van de volgende opties:
>    - Als uw toepassing *accounts in deze organisatie Directory*ondersteunt, vervangt u deze waarde door de **Tenant-id** of **Tenant naam** (bijvoorbeeld *contoso.Microsoft.com*).
>    - Als uw toepassing *accounts in een organisatorische Directory*ondersteunt, vervangt u deze waarde door **organisaties**.
>    - Als uw toepassing *accounts in een organisatorische map en persoonlijke micro soft-accounts*ondersteunt, vervangt u deze waarde door **common**. Als u de ondersteuning voor *persoonlijke micro soft-accounts*wilt beperken, moet u deze waarde vervangen door **consumenten**.
>
> > [!TIP]
> > Om de waarden van **Toepassings-id (client-id)** , **Map-id (tenant-id)** en **Ondersteunde accounttypen** te achterhalen, gaat u naar de **Overzichtspagina** van de app in de Azure-portal.
>

#### <a name="step-4-run-the-project"></a>Stap 4: het project uitvoeren

* Als u [node. js](https://nodejs.org/en/download/)gebruikt:

    1. Als u de server wilt starten, voert u de volgende opdracht uit vanuit de projectmap:

        ```batch
        npm install
        node server.js
        ```

    1. Open een webbrowser en ga naar `http://localhost:30662/`.
    1. Selecteer **Aanmelden** om de aanmelding te starten en roep vervolgens de Microsoft Graph-API aan.


* Als u [Visual Studio](https://visualstudio.microsoft.com/downloads/)gebruikt, selecteert u de project-oplossing en selecteert u vervolgens F5 om het project uit te voeren.

Nadat de toepassing door de browser is geladen, selecteert u **Aanmelden**. De eerste keer dat u zich aanmeldt, wordt u gevraagd uw toestemming te geven om uw profiel te openen en u aan te melden. Nadat u bent aangemeld, moeten de gegevens van uw gebruikers profiel worden weer gegeven op de pagina.

## <a name="more-information"></a>Meer informatie

### <a name="how-the-sample-works"></a>Hoe het voor beeld werkt

![Hoe de voor beeld-app in deze Snelstartgids werkt](media/quickstart-v2-javascript/javascriptspa-intro.svg)

### <a name="msaljs"></a>msal.js

De MSAL-bibliotheek ondertekent gebruikers en vraagt de tokens aan die worden gebruikt voor toegang tot een API die wordt beveiligd door micro soft Identity platform. Het bestand Quick Start *index. html* bevat een verwijzing naar de bibliotheek:

```html
<script src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.0/js/msal.min.js"></script>
```
> [!TIP]
> U kunt de voor gaande versie vervangen door de nieuwste release versie onder [MSAL. js-releases](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).


Als node. js is geïnstalleerd, kunt u ook de nieuwste versie downloaden via node. js Package Manager (NPM):

```batch
npm install msal
```

### <a name="msal-initialization"></a>MSAL initialiseren

In de Quick Start-code ziet u ook hoe u de MSAL-bibliotheek initialiseert:

```javascript
var msalConfig = {
    auth: {
        clientId: "Enter_the_Application_Id_here",
        authority: "https://login.microsoftonline.com/Enter_the_Tenant_Info_Here",
        redirectUri: "http://localhost:30662/"
    },
    cache: {
        cacheLocation: "localStorage",
        storeAuthStateInCookie: true
    }
};

var myMSALObj = new Msal.UserAgentApplication(msalConfig);
```

> |Waar  |  |
> |---------|---------|
> |`clientId`     | De toepassings-ID van de toepassing die is geregistreerd in de Azure Portal.|
> |`authority`    | Beschrijving De CA-URL die ondersteuning biedt voor account typen, zoals eerder is beschreven in de sectie configuratie. De standaard instantie is `https://login.microsoftonline.com/common`. |
> |`redirectUri`     | De geconfigureerde antwoord-redirectUri voor de registratie van de toepassing. In dit geval `http://localhost:30662/`. |
> |`cacheLocation`  | Beschrijving Hiermee stelt u de opslag voor de browser in voor de verificatie status. De standaard waarde is sessionStorage.   |
> |`storeAuthStateInCookie`  | Beschrijving De bibliotheek waarin de status van de verificatie aanvraag wordt opgeslagen die vereist is voor de validatie van de verificatie stromen in de browser cookies. Deze cookie is ingesteld voor IE-en Edge-browsers om bepaalde [bekende problemen](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues)te verhelpen. |

Zie [client toepassingen initialiseren](msal-js-initializing-client-applications.md)voor meer informatie over beschik bare opties die kunnen worden geconfigureerd.

### <a name="sign-in-users"></a>Gebruikers aanmelden

In het volgende codefragment wordt weergegeven hoe u gebruikers kunt aanmelden:

```javascript
var requestObj = {
    scopes: ["user.read"]
};

myMSALObj.loginPopup(requestObj).then(function (loginResponse) {
    //Login Success callback code here
}).catch(function (error) {
    console.log(error);
});
```

> |Waar  |  |
> |---------|---------|
> | `scopes`   | Beschrijving Bevat bereiken die worden aangevraagd voor toestemming van de gebruiker bij het aanmelden. Bijvoorbeeld, `[ "user.read" ]` voor Microsoft Graph of `[ "<Application ID URL>/scope" ]` voor aangepaste web-API’s (`api://<Application ID>/access_as_user`). |

> [!TIP]
> U kunt ook de `loginRedirect` methode gebruiken om de huidige pagina om te leiden naar de aanmeldings pagina in plaats van een pop-upvenster.

### <a name="request-tokens"></a>Tokens aanvragen

MSAL maakt gebruik van drie methoden om tokens te verkrijgen: `acquireTokenRedirect`, `acquireTokenPopup`en `acquireTokenSilent`

#### <a name="get-a-user-token-silently"></a>Een gebruikerstoken op de achtergrond ophalen

Met de methode `acquireTokenSilent` wordt het verkrijgen en vernieuwen van tokens verwerkt zonder tussenkomst van de gebruiker. Nadat de methode `loginRedirect` of `loginPopup` de eerste keer is uitgevoerd, wordt voor volgende aanroepen meestal de methode `acquireTokenSilent` gebruikt om tokens te verkrijgen die worden gebruikt voor toegang tot beveiligde resources. Aanroepen voor het aanvragen of vernieuwen van tokens worden op de achtergrond uitgevoerd.

```javascript
var requestObj = {
    scopes: ["user.read"]
};

myMSALObj.acquireTokenSilent(requestObj).then(function (tokenResponse) {
    // Callback code here
    console.log(tokenResponse.accessToken);
}).catch(function (error) {
    console.log(error);
});
```

> |Waar  |  |
> |---------|---------|
> | `scopes`   | Bevat bereiken die worden aangevraagd om te worden geretourneerd in het toegangstoken voor de API. Bijvoorbeeld, `[ "user.read" ]` voor Microsoft Graph of `[ "<Application ID URL>/scope" ]` voor aangepaste web-API’s (`api://<Application ID>/access_as_user`).|

#### <a name="get-a-user-token-interactively"></a>Een gebruikerstoken interactief ophalen

Er zijn situaties waarin u gebruikers moet dwingen te communiceren met het micro soft Identity platform-eind punt. Bijvoorbeeld:
* Gebruikers moeten mogelijk hun referenties opnieuw invoeren, omdat het wacht woord is verlopen.
* Uw toepassing vraagt toegang tot extra resource scopes aan die de gebruiker nodig heeft om toestemming te geven.
* Verificatie met twee factoren is vereist.

Het gebruikelijke aanbevolen patroon voor de meeste toepassingen is om `acquireTokenSilent` eerst aan te roepen, vervolgens de uitzonde ring op te vangen en vervolgens `acquireTokenPopup` (of `acquireTokenRedirect`) aan te roepen om een interactieve aanvraag te starten.

Het aanroepen van de `acquireTokenPopup` resultaten in een pop-upvenster voor het aanmelden. (Of `acquireTokenRedirect` resulteert in het omleiden van gebruikers naar het micro soft Identity platform-eind punt.) In dat venster moeten gebruikers communiceren door hun referenties te bevestigen, toestemming te geven voor de vereiste resource of de twee ledige verificatie uit te voeren.

```javascript
var requestObj = {
    scopes: ["user.read"]
};

myMSALObj.acquireTokenPopup(requestObj).then(function (tokenResponse) {
    // Callback code here
    console.log(tokenResponse.accessToken);
}).catch(function (error) {
    console.log(error);
});
```

> [!NOTE]
> In deze Quick Start worden de methoden `loginRedirect` en `acquireTokenRedirect` van micro soft Internet Explorer gebruikt, vanwege een [bekend probleem](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) met betrekking tot het verwerken van pop-upvensters in Internet Explorer.

## <a name="next-steps"></a>Volgende stappen

Zie voor een gedetailleerde stapsgewijze hand leiding voor het bouwen van de toepassing voor deze Quick Start:

> [!div class="nextstepaction"]
> [Zelf studie om u aan te melden en MS Graph aan te roepen](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-javascriptspa)

Als u wilt zoeken in de MSAL-opslag plaats op documentatie, veelgestelde vragen, problemen en meer, raadpleegt u:

> [!div class="nextstepaction"]
> [MSAL.js GitHub repo](https://github.com/AzureAD/microsoft-authentication-library-for-js)

Help ons het micro soft Identity-platform te verbeteren. Vertel ons wat u denkt door een korte enquête met twee vragen te volt ooien.

> [!div class="nextstepaction"]
> [Micro soft Identity platform-enquête](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
