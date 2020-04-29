---
title: Gebruikers aanmelden in Java script-apps met één pagina met auth-code | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over hoe een Java script-app een API kan aanroepen waarvoor toegangs tokens zijn vereist met het micro soft Identity-platform.
services: active-directory
author: hahamil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 04/22/2020
ms.author: hahamil
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:JavaScript
ROBOTS: NOINDEX
ms.openlocfilehash: d362db3a51848603c78d663c5b628192ff028d02
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82209530"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-a-javascript-spa-using-the-auth-code-flow"></a>Snelstartgids: gebruikers aanmelden en een toegangs token verkrijgen in een Java script-beveiligd-wachtwoord verificatie met behulp van de autorisatie code stroom 

> [!IMPORTANT]
> Deze functie is momenteel beschikbaar als preview-product. Previews worden voor u beschikbaar gesteld op voorwaarde dat u akkoord gaat met de [aanvullende gebruiksvoorwaarden](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Sommige aspecten van deze functie kunnen worden gewijzigd vóór algemene Beschik baarheid (GA).


Deze Snelstartgids maakt gebruik van MSAL. js 2,0 met de autorisatie code stroom. Als u MSAL. js 1,0 met de impliciete stroom wilt gebruiken, bekijkt u [deze Snelstartgids](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v2-javascript).

In deze Snelstartgids gebruikt u een code voorbeeld om te zien hoe een Java script-toepassing met één pagina (SPA) gebruikers van persoonlijke accounts, werk accounts en school accounts kan aanmelden. Een Java script-SPA kan ook een toegangs token verkrijgen om de Microsoft Graph-API of een web-API aan te roepen. Bekijk [hoe het voor beeld werkt](#how-the-sample-works) voor een afbeelding.

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Maak gratis een Azure-abonnement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio code](https://code.visualstudio.com/download) (om project bestanden te bewerken)


> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>De quickstart-toepassing registreren en downloaden
> Gebruik een van de volgende opties om uw Quick Start-toepassing te starten.
>
> ### <a name="option-1-express-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Optie 1 (Express): uw app registreren en automatisch configureren en vervolgens uw voorbeeld code downloaden
>
> 1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
> 1. Als uw account u toegang geeft tot meer dan één Tenant, selecteert u het account in de rechter bovenhoek en stelt u vervolgens uw portal-sessie in op de Azure Active Directory (Azure AD)-Tenant die u wilt gebruiken.
> 1. Selecteer [app-registraties](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs).
> 1. Voer een naam in voor de toepassing.
> 1. Selecteer onder **Ondersteunde accounttypen** de optie **Accounts in een organisatieadreslijst en persoonlijke Microsoft-account**.
> 1. Selecteer **Registreren**.
> 1. Ga naar het deel venster Quick Start en volg de instructies om uw nieuwe toepassing te downloaden en automatisch te configureren.
>
> ### <a name="option-2-manual-register-and-manually-configure-your-application-and-code-sample"></a>Optie 2 (hand matig): uw toepassings-en code voorbeeld registreren en hand matig configureren
>
> #### <a name="step-1-register-your-application"></a>Stap 1: Uw toepassing registreren
>
> 1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
>
> 1. Als uw account u toegang geeft tot meer dan één Tenant, selecteert u uw account in de rechter bovenhoek en stelt u vervolgens uw portal-sessie in op de Azure AD-Tenant die u wilt gebruiken.
> 1. Selecteer [app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908).
> 1. Selecteer **nieuwe registratie**.
> 1. Wanneer de pagina **Een toepassing registreren** wordt weergegeven, voert u een naam in voor de toepassing.
> 1. Selecteer onder **Ondersteunde accounttypen** de optie **Accounts in een organisatieadreslijst en persoonlijke Microsoft-account**.
> 1. Selecteer **Registreren**. Noteer de waarde van de **toepassing (client)** op de pagina app- **overzicht** voor later gebruik.
> 1. Selecteer in het linkerdeel venster van de geregistreerde toepassing **verificatie**.
> 1. Onder **platform configuraties**selecteert u **een platform toevoegen**. Er wordt een paneel aan de linkerkant geopend. Hier selecteert u de regio **met één pagina** .
> 1. Stel nog aan de linkerkant de **omleidings** -URI `http://localhost:3000/`-waarde in op. 
> 1. Selecteer **Configureren**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Stap 1: uw toepassing configureren in de Azure-portal
> Om het code voorbeeld in deze Snelstartgids te laten werken, moet u een `redirectUri` as `http://localhost:3000/`toevoegen.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Breng deze wijzigingen voor mij aan]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Al geconfigureerd](media/quickstart-v2-javascript/green-check.png) Uw toepassing is al geconfigureerd met deze kenmerken.

#### <a name="step-2-download-the-project"></a>Stap 2: Het project downloaden

> [!div renderon="docs"]
> Als u het project wilt uitvoeren met een webserver met behulp van node. js, [downloadt u de kern project bestanden](https://github.com/Azure-Samples/ms-identity-javascript-v2/archive/quickstart.zip).

> [!div renderon="portal" class="sxs-lookup"]
> Het project uitvoeren met een webserver met behulp van node. js

> [!div renderon="portal" id="autoupdate" class="nextstepaction" class="sxs-lookup"]
> [Het code voorbeeld downloaden](https://github.com/Azure-Samples/ms-identity-javascript-v2/archive/quickstart.zip)

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-javascript-app"></a>Stap 3: uw Java script-app configureren
>
> Bewerk *authConfig. js*in de app-map en stel de `clientID`waarden, `authority` en `redirectUri` in. *app* `msalConfig`
>
> ```javascript
>
>  // Config object to be passed to Msal on creation
>  const msalConfig = {
>    auth: {
>      clientId: "Enter_the_Application_Id_Here",
>      authority: "Enter_the_Cloud_Instance_Id_HereEnter_the_Tenant_Info_Here",
>      redirectUri: "Enter_the_Redirect_Uri_Here",
>    },
>    cache: {
>      cacheLocation: "sessionStorage", // This configures where your cache will be stored
>      storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
>    }
>  };
>
>```

> [!div renderon="portal" class="sxs-lookup"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
>
> Waar:
> - Enter_the_Application_Id_Here>de **client-id** is van de toepassing die u hebt geregistreerd. * \<*
> - Enter_the_Cloud_Instance_Id_Here>is het exemplaar van de Azure-Cloud. * \<* Voor de hoofd-of wereld wijde Azure- *https://login.microsoftonline.com/* Cloud voert u in. Zie [nationale Clouds](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud)voor **nationale** Clouds (bijvoorbeeld China).
> - Enter_the_Tenant_info_here>is ingesteld op een van de volgende opties: * \<*
>    - Als uw toepassing *accounts in deze organisatie Directory*ondersteunt, vervangt u deze waarde door de **Tenant-id** of **Tenant naam** (bijvoorbeeld *contoso.Microsoft.com*).
>    - Als uw toepassing *accounts in een organisatorische Directory*ondersteunt, vervangt u deze waarde door **organisaties**.
>    - Als uw toepassing *accounts in een organisatorische map en persoonlijke micro soft-accounts*ondersteunt, vervangt u deze waarde door **common**. Als u de ondersteuning voor *persoonlijke micro soft-accounts*wilt beperken, moet u deze waarde vervangen door **consumenten**.
> - * \<Enter_the_Redirect_Uri_Here>* is`http://localhost:3000`
> > [!TIP]
> > Ga naar de **overzichts** pagina van de app-registratie in de Azure Portal om de waarden van de **toepassings**-id, de **Directory**-id en de **ondersteunde account typen**te vinden.
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Stap 3: uw app is geconfigureerd en klaar om te worden uitgevoerd
> Uw project is geconfigureerd met waarden van de eigenschappen van uw app.

> [!div renderon="docs"]
>
> Bewerk vervolgens in dezelfde map het bestand *graphConfig. js* om de `graphMeEndpoint` en `graphMailEndpoint` voor het `apiConfig` object in te stellen.
> ```javascript
>   // Add here the endpoints for MS Graph API services you would like to use.
>   const graphConfig = {
>     graphMeEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me",
>     graphMailEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me/messages"
>   };
>
>   // Add here scopes for access token to be used at MS Graph API endpoints.
>   const tokenRequest = {
>       scopes: ["Mail.Read"]
>   };
> ```
>

> [!div renderon="docs"]
>
> Enter_the_Graph_Endpoint_Here>is het eind punt waarop API-aanroepen worden uitgevoerd. * \<* Voer `https://graph.microsoft.com`voor de hoofd-of Global Microsoft Graph API-service in. Zie [National Cloud Deployment](https://docs.microsoft.com/graph/deployments)(Engelstalig) voor meer informatie.
>
> #### <a name="step-4-run-the-project"></a>Stap 4: het project uitvoeren

Voer het project uit met een webserver met behulp van [node. js](https://nodejs.org/en/download/):

1. Als u de server wilt starten, voert u de volgende opdrachten uit vanuit de projectmap:
    ```bash
    npm install
    npm start
    ```
1. Blader naar `http://localhost:3000/`.

1. Selecteer **Aanmelden** om het aanmeldings proces te starten en vervolgens Microsoft Graph-API aan te roepen.

    De eerste keer dat u zich aanmeldt, wordt u gevraagd uw toestemming te geven om uw profiel te openen en u aan te melden. Nadat u bent aangemeld, moeten de gegevens van uw gebruikers profiel worden weer gegeven op de pagina.

## <a name="more-information"></a>Meer informatie

### <a name="how-the-sample-works"></a>Hoe het voor beeld werkt

![Hoe het voor beeld van Java script SPA werkt: 1. Het beveiligd-wachtwoord verificatie initieert aanmelden. 2. Het beveiligd-wachtwoord verificatie verkrijgt een ID-token van het micro soft Identity-platform. 3. De beveiligd-wachtwoord verificatie aanroepen Token ophalen. 4. Het micro soft Identity-platform retourneert een toegangs token voor de beveiligd-wachtwoord verificatie. 5. De beveiligd-wachtwoord verificatie maakt en HTTP GET-aanvraag met het toegangs token aan de Microsoft Graph-API. 6. De Graph API retourneert een HTTP-antwoord naar de beveiligd-wachtwoord verificatie.](media/quickstart-v2-javascript/javascriptspa-intro.svg)

### <a name="msaljs"></a>msal. js

De MSAL. JS-bibliotheek meldt gebruikers en vraagt de tokens aan die worden gebruikt voor toegang tot een API die wordt beveiligd door het micro soft Identity-platform. Het bestand *index. html* van het voor beeld bevat een verwijzing naar de bibliotheek:

```html
<script type="text/javascript" src="https://alcdn.msftauth.net/lib/1.2.1/js/msal.js" integrity="sha384-9TV1245fz+BaI+VvCjMYL0YDMElLBwNS84v3mY57pXNOt6xcUYch2QLImaTahcOP" crossorigin="anonymous"></script>
```
> [!TIP]
> U kunt de voor gaande versie vervangen door de nieuwste release versie onder [MSAL. js-releases](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).

Als node. js is geïnstalleerd, kunt u ook de nieuwste versie downloaden met behulp van de node. js-pakket beheer (NPM):

```batch
npm install msal
```

## <a name="next-steps"></a>Volgende stappen

De [MSAL. js github opslag plaats](https://github.com/AzureAD/microsoft-authentication-library-for-js) bevat aanvullende Bibliotheek Documentatie, een veelgestelde vragen en biedt ondersteuning voor het probleem.

Zie voor een gedetailleerde stapsgewijze hand leiding voor het bouwen van de toepassing voor deze Quick Start:

> [!div class="nextstepaction"]
> [Zelf studie om u aan te melden en MS Graph aan te roepen](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-javascript-auth-code)
