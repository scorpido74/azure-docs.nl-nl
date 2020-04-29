---
title: Gebruikers aanmelden in hoek-apps met één pagina-Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over hoe een hoek-app een API kan aanroepen die toegangs tokens vereist met behulp van het micro soft Identity-platform.
services: active-directory
author: jasonnutter
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:JavaScript
ms.topic: quickstart
ms.workload: identity
ms.date: 03/18/2020
ms.author: janutter
ms.openlocfilehash: 4b6a2481c18314a44470a020033ffdc4ba1d7259
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81380020"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-an-angular-single-page-application"></a>Snelstartgids: gebruikers aanmelden en een toegangs token verkrijgen in een hoek toepassing met één pagina

> [!IMPORTANT]
> Deze functie is momenteel beschikbaar als preview-product. Previews worden voor u beschikbaar gesteld op voorwaarde dat u akkoord gaat met de [aanvullende gebruiksvoorwaarden](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Sommige aspecten van deze functie kunnen worden gewijzigd vóór algemene Beschik baarheid (GA).

In deze Quick Start gebruikt u een code voorbeeld om te leren hoe een hoek met één pagina (SPA) kan worden aangemeld bij gebruikers met persoonlijke micro soft-accounts, werk accounts of school accounts. Een hoek-beveiligd-wachtwoord verificatie kan ook een toegangs token krijgen om de Microsoft Graph-API of een web-API aan te roepen.

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement. [Maak er gratis een](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Node. js](https://nodejs.org/en/download/).
* [Visual Studio code](https://code.visualstudio.com/download) voor het bewerken van Project bestanden of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) om het project uit te voeren.

> [!div renderon="docs"]
> ## <a name="register-and-download-the-quickstart-app"></a>De Quick Start-app registreren en downloaden
> Gebruik een van de volgende opties om de Quick Start-app te starten.
>
> ### <a name="option-1-express-register-and-automatically-configure-the-app-and-then-download-the-code-sample"></a>Optie 1 (Express): de app registreren en automatisch configureren en vervolgens het code voorbeeld downloaden
>
> 1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
> 1. Als uw account toegang heeft tot meer dan één Tenant, selecteert u het account in de rechter bovenhoek en stelt u vervolgens uw portal-sessie in op de Azure Active Directory (Azure AD)-Tenant die u wilt gebruiken.
> 1. Open het deel venster nieuw [app-registraties](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs) in het Azure Portal.
> 1. Voer een naam in voor uw toepassing en selecteer vervolgens **registreren**.
> 1. Ga naar het deel venster Quick Start en Bekijk de hoek Snelstartgids. Volg de instructies om uw nieuwe toepassing te downloaden en automatisch te configureren.
>
> ### <a name="option-2-manual-register-and-manually-configure-the-application-and-code-sample"></a>Optie 2 (hand matig): de toepassing en het code voorbeeld registreren en hand matig configureren
>
> #### <a name="step-1-register-the-application"></a>Stap 1: de toepassing registreren
>
> 1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
> 1. Als uw account toegang heeft tot meer dan één Tenant, selecteert u uw account in de rechter bovenhoek en stelt u uw portal-sessie in op de Azure AD-Tenant die u wilt gebruiken.
> 1. Volg de instructies voor het [registreren van een toepassing met één pagina](https://docs.microsoft.com/azure/active-directory/develop/scenario-spa-app-registration) in de Azure Portal.
> 1. Voeg een nieuw platform toe aan het **verificatie** venster van de registratie van uw app en registreer de `http://localhost:4200/`omleidings-URI:.
> 1. In deze Quick Start wordt de [impliciete toekennings stroom](v2-oauth2-implicit-grant-flow.md)gebruikt. Selecteer de **impliciete toekennings** instellingen voor **id-tokens** en **toegangs tokens**. ID-tokens en toegangs tokens zijn vereist omdat deze app zich aanmeldt bij gebruikers en een API aanroept.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-the-application-in-the-azure-portal"></a>Stap 1: de toepassing configureren in de Azure Portal
> Het code voorbeeld voor deze Quick Start werkt alleen als **http://localhost:4200/** u een omleidings-URI toevoegt en **impliciete toekenning**inschakelt.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Breng deze wijzigingen voor mij aan]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Al geconfigureerd](media/quickstart-v2-javascript/green-check.png) Uw toepassing is al geconfigureerd met deze kenmerken.

#### <a name="step-2-download-the-code-sample"></a>Stap 2: het code voorbeeld downloaden
>[!div renderon="docs"]
>Als u het project wilt uitvoeren met een webserver met behulp van node. js, moet u [de voor beeld-opslag plaats klonen](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular) of [de kern project bestanden downloaden](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular/archive/master.zip). Open de bestanden met behulp van een editor zoals Visual Studio code.

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Het code voorbeeld downloaden](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular/archive/master.zip)

> [!div renderon="docs"]
>#### <a name="step-3-configure-the-javascript-app"></a>Stap 3: de Java script-app configureren
>
> Bewerk in de map *src/app* de opties *app. module. TS* en stel `clientId` de `authority` waarden en `MsalModule.forRoot`in op.
>
>```javascript
>MsalModule.forRoot({
>    auth: {
>        clientId: 'Enter_the_Application_Id_here', // This is your client ID
>        authority: 'https://login.microsoftonline.com/Enter_the_Tenant_Info_Here', // This is your tenant info
>        redirectUri: 'Enter_the_Redirect_Uri_Here' // This is your redirect URI
>    },
>    cache: {
>        cacheLocation: 'localStorage',
>        storeAuthStateInCookie: isIE, // set to true for IE 11
>    },
>},
> //... )
>```

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > Enter_the_Supported_Account_Info_Here


> [!div renderon="docs"]
>
> Vervang deze waarden:
>
>|Waardenaam|Beschrijving|
>|---------|---------|
>|Enter_the_Application_Id_Here|Op de pagina **overzicht** van de registratie van uw toepassing is dit de waarde van uw **toepassing (client)** . |
>|Enter_the_Cloud_Instance_Id_Here|Dit is het exemplaar van de Azure-Cloud. Voer **https://login.microsoftonline.com**in voor de hoofd-of wereld wijde Azure-Cloud. Zie [nationale Clouds](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud)voor nationale Clouds (bijvoorbeeld China).|
>|Enter_the_Tenant_Info_Here| Stel in op een van de volgende opties: als uw toepassing *accounts in deze organisatie-Directory*ondersteunt, vervangt u deze waarde door de Directory-id (Tenant) of Tenant naam (bijvoorbeeld **contoso.Microsoft.com**). Als uw toepassing *accounts in een organisatorische Directory*ondersteunt, vervangt u deze waarde door **organisaties**. Als uw toepassing *accounts in een organisatorische map en persoonlijke micro soft-accounts*ondersteunt, vervangt u deze waarde door **common**. Als u de ondersteuning voor *persoonlijke micro soft-accounts*wilt beperken, moet u deze waarde vervangen door **consumenten**. |
>|Enter_the_Redirect_Uri_Here|Vervang door **http://localhost:4200**.|
>|cacheLocation  | Beschrijving Stel de browser opslag in voor de verificatie status. De standaard waarde is **sessionStorage**.   |
>|storeAuthStateInCookie  | Beschrijving De bibliotheek identificeren waarin de status van de verificatie aanvraag wordt opgeslagen. Deze status is vereist voor het valideren van de verificatie stromen in de browser cookies. Deze cookie is ingesteld voor Internet Explorer en Edge om deze twee browsers te kunnen ondersteunen. Zie [bekende problemen](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues->on-IE-and-Edge-Browser#issues)voor meer informatie. |
> > [!TIP]
> > Om de waarden van **Toepassings-id (client-id)**, **Map-id (tenant-id)** en **Ondersteunde accounttypen** te achterhalen, gaat u naar de **Overzichtspagina** van de app in de Azure-portal.

Zie [client toepassingen initialiseren](msal-js-initializing-client-applications.md)voor meer informatie over beschik bare opties die kunnen worden geconfigureerd. 

U vindt de bron code voor de bibliotheek MSAL. js in de [AzureAD/micro soft-Authentication-library-for-js](https://github.com/AzureAD/microsoft-authentication-library-for-js) -opslag plaats op github.

>[!div class="sxs-lookup" renderon="portal"]
>#### <a name="step-3-run-the-project"></a>Stap 3: het project uitvoeren

>[!div renderon="docs"]
>#### <a name="step-4-run-the-project"></a>Stap 4: het project uitvoeren

Als u Node.js gebruikt:

1. Start de server door de volgende opdrachten uit te voeren in de projectmap:

   ```console
   npm install
   npm start
   ```

1. Blader naar **http://localhost:4200/**.
1. Selecteer **Aanmelden**.
1. Selecteer een **profiel** om Microsoft Graph aan te roepen.

Nadat de toepassing door de browser is geladen, selecteert u **Aanmelden**. De eerste keer dat u zich aanmeldt, wordt u gevraagd uw toestemming te geven om uw profiel te openen en u aan te melden. Nadat u zich hebt aangemeld, selecteert u **profiel**en worden de gegevens van uw gebruikers profiel op de pagina weer gegeven.

## <a name="how-the-sample-works"></a>Hoe het voor beeld werkt

![Diagram dat laat zien hoe de voor beeld-app in deze Snelstartgids werkt](media/quickstart-v2-javascript/javascriptspa-intro.svg)


## <a name="next-steps"></a>Volgende stappen

Lees vervolgens hoe u zich aanmeldt bij een gebruiker en tokens aanschaft in de hoek zelf studie:

> [!div class="nextstepaction"]
> [Zelf studie voor hoek](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-angular)

