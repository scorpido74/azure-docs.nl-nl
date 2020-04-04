---
title: Gebruikers aanmelden in hoekige apps met één pagina - Azure
titleSuffix: Microsoft identity platform
description: Lees hoe een Hoekse-app een API kan aanroepen waarvoor toegangstokens nodig zijn via het Microsoft-identiteitsplatform.
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
ms.openlocfilehash: 61a098b92db13b8422d9cfebb19610c5de7685cd
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631750"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-an-angular-spa"></a>Snelstart: Meld u aan voor gebruikers en ontvang een toegangstoken in een Hoekige SPA

> [!IMPORTANT]
> Deze functie is momenteel beschikbaar als preview-product. Previews worden voor u beschikbaar gesteld op voorwaarde dat u akkoord gaat met de [aanvullende gebruiksvoorwaarden](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Sommige aspecten van deze functionaliteit kunnen wijzigen voordat deze functionaliteit algemeen beschikbaar wordt.

In deze quickstart gebruikt u een codevoorbeeld om te leren hoe een Toepassing met één pagina (Angular) gebruikers met persoonlijke Microsoft-accounts en werkaccounts en schoolaccounts kan aanmelden. Een Angular SPA kan ook een toegangstoken krijgen om de Microsoft Graph API of een web-API aan te roepen.

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement - [maak er gratis een](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) om projectbestanden te bewerken, of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) om het project uit te voeren.

> [!div renderon="docs"]
> ## <a name="register-and-download-the-quickstart-app"></a>De quickstart-app registreren en downloaden
> Als u de app snel start, gebruikt u een van de volgende opties:
>
> ### <a name="option-1-express-register-and-auto-configure-the-app-then-download-the-code-sample"></a>Optie 1 (Express): De app registreren en automatisch configureren. Download vervolgens het codevoorbeeld
>
> 1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
> 1. Als uw account toegang heeft tot meer dan één tenant, selecteert u het account rechtsboven en stelt u uw portalsessie in op de Azure AD-tenant (Azure AD) van Azure Directory.
> 1. Open de nieuwe [Azure-portal - deelvenster App-registraties.](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs)
> 1. Voer een naam in voor uw toepassing en selecteer **Vervolgens Registreren**.
> 1. Navigeer naar het deelvenster Snelstart en bekijk de snelstart van Hoek. Volg de instructies om uw nieuwe toepassing te downloaden en automatisch te configureren.
>
> ### <a name="option-2-manual-register-and-manually-configure-the-application-and-code-sample"></a>Optie 2 (handleiding): De toepassing en het codevoorbeeld handmatig registreren en configureren
>
> #### <a name="step-1-register-the-application"></a>Stap 1: De aanvraag registreren
>
> 1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
>
> 1. Als uw account toegang heeft tot meer dan één tenant, selecteert u uw account rechtsboven en stelt u uw portalsessie in op de Azure AD-tenant die u wilt gebruiken.
> 1. Volg de instructies om een toepassing van één pagina te registreren in de [Azure-portal.](https://docs.microsoft.com/azure/active-directory/develop/scenario-spa-app-registration)
> 1. Voeg een nieuw platform toe aan het **verificatieblad** van uw app-registratie en registreer de omleiding URI:`http://localhost:4200/`
> 1. Deze quickstart maakt gebruik van de [impliciete subsidiestroom](v2-oauth2-implicit-grant-flow.md). Selecteer de **instellingen voor impliciete toekenning** voor **ID-tokens** en **toegangstokens.** ID-tokens en toegangstokens zijn vereist omdat deze app gebruikers aantekent en een API aanroept.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-the-application-in-the-azure-portal"></a>Stap 1: De toepassing configureren in de Azure-portal
> Voor het codevoorbeeld voor deze quickstart moet `redirectUri` u `http://localhost:4200/` een as-as toevoegen en **Impliciete subsidie**inschakelen.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Breng deze wijzigingen voor mij aan]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Al geconfigureerd](media/quickstart-v2-javascript/green-check.png) Uw toepassing is al geconfigureerd met deze kenmerken.

#### <a name="step-2-download-the-code-sample"></a>Stap 2: Download het voorbeeld van de code
>[!div renderon="docs"]
>Als u het project met een webserver wilt https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular uitvoeren met Behulp van Node.js, kloont of [downloadt u](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular/archive/master.zip) de kernprojectbestanden. Open de bestanden met een editor zoals Visual Studio Code.

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Het codevoorbeeld downloaden](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular/archive/master.zip)

> [!div renderon="docs"]
>#### <a name="step-3-configure-the-javascript-app"></a>Stap 3: De JavaScript-app configureren
>
> Bewerk *app.module.ts* in de map *src/app* en `MsalModule.forRoot`stel de `clientId` waarden en `authority` waarden in onder .
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
> Vervang deze waarden als volgt:
>
>|Waardenaam|Beschrijving|
>|---------|---------|
>|Enter_the_Application_Id_Here|Op de **overzichtspagina** van uw aanvraagregistratie is dit uw **applicatie(client) ID** |
>|Enter_the_Cloud_Instance_Id_Here|Dit is de instantie van de Azure-cloud. Voer voor de hoofd- https://login.microsoftonline.comof globale Azure-cloud . Voor nationale wolken (bijvoorbeeld, China), zie [Nationale wolken](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud).|
>|Enter_the_Tenant_Info_Here| Stel in op een van de volgende opties: 1) Als uw toepassing *accounts in deze organisatiemap*ondersteunt, vervangt u deze waarde door de id of **tenantnaam** **directory (tenant)** (bijvoorbeeld *contoso.microsoft.com).* 2) Als uw toepassing *accounts in een organisatiemap*ondersteunt, vervangt u deze waarde door **organisaties**. 3) Als uw toepassing *accounts in een organisatiemap en persoonlijke Microsoft-accounts*ondersteunt, vervangt u deze waarde door **algemene**. 4) Om de ondersteuning alleen voor *persoonlijke Microsoft-accounts*te beperken, vervangt u deze waarde door **consumenten**. |
>|Enter_the_Redirect_Uri_Here|Vervangen door`http://localhost:4200`|
>|cacheLocatie  | (Optioneel) Hiermee stelt u de browseropslag in voor de auth-status. De standaardinstelling is sessionStorage.   |
>|storeAuthStateInCookie  | (Optioneel) De bibliotheek die de status van het verificatieverzoek opslaat. Deze status is vereist om de verificatiestromen in de browsercookies te valideren. Deze cookie is ingesteld voor de Internet Explorer- en Edge-browsers om deze twee browsers aan te passen. Zie [bekende problemen](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues->on-IE-and-Edge-Browser#issues)voor meer informatie. |
> > [!TIP]
> > Om de waarden van **Toepassings-id (client-id)**, **Map-id (tenant-id)** en **Ondersteunde accounttypen** te achterhalen, gaat u naar de **Overzichtspagina** van de app in de Azure-portal.

Zie [Clienttoepassingen initialiseren](msal-js-initializing-client-applications.md)voor meer informatie over beschikbare configureerbare opties.

>[!div class="sxs-lookup" renderon="portal"]
>#### <a name="step-3-run-the-project"></a>Stap 3: Het project uitvoeren

>[!div renderon="docs"]
>#### <a name="step-4-run-the-project"></a>Stap 4: Het project uitvoeren

* Als u Node.js gebruikt:

    1. Start de server door de volgende opdrachten uit de projectmap uit te voeren:

        ```console
        npm install
        npm start
        ```

    1. Bladeren `http://localhost:4200/`.
    1. Selecteer **Aanmelding**.
    1. Selecteer **Profiel** dat u Microsoft Graph wilt aanroepen.

Nadat de browser de toepassing heeft geladen, selecteert u **Aanmelding**. De eerste keer dat u zich aanmeldt, wordt u gevraagd uw toestemming te geven om de toepassing toegang te geven tot uw profiel en u aan te melden. Nadat u bent aangemeld, selecteert u **Profiel**en worden uw gebruikersprofielgegevens op de pagina weergegeven.

## <a name="how-the-sample-works"></a>Hoe het voorbeeld werkt

![Hoe de voorbeeld-app in deze quickstart werkt](media/quickstart-v2-javascript/javascriptspa-intro.svg)


## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> Ga naar de [Hoekige zelfstudie](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-angular) om te leren hoe u zich aanmeldt bij een gebruiker en tokens te verkrijgen.

Blader door de [MSAL repo](https://github.com/AzureAD/microsoft-authentication-library-for-js) voor documentatie, veelgestelde vragen, problemen en meer.
