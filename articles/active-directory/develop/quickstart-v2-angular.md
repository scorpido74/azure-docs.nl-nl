---
title: Gebruikers aanmelden in hoekige apps met één pagina - Azure
titleSuffix: Microsoft identity platform
description: Lees hoe een Hoekse-app een API kan aanroepen waarvoor toegangstokens vereist zijn met behulp van het Microsoft-identiteitsplatform.
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
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81380020"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-an-angular-single-page-application"></a>Snelstart: gebruikers aanmelden en een toegangstoken krijgen in een toepassing met één pagina met één pagina

> [!IMPORTANT]
> Deze functie is momenteel beschikbaar als preview-product. Previews worden voor u beschikbaar gesteld op voorwaarde dat u akkoord gaat met de [aanvullende gebruiksvoorwaarden](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Sommige aspecten van deze functie kunnen veranderen voordat algemene beschikbaarheid (GA) is.

In deze quickstart gebruikt u een codevoorbeeld om te leren hoe een Toepassing met één pagina (Angular) gebruikers kan aanmelden die persoonlijke Microsoft-accounts, werkaccounts of schoolaccounts hebben. Een Angular SPA kan ook een toegangstoken krijgen om de Microsoft Graph API of een web-API aan te roepen.

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement. [Maak er gratis een.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Knooppunt.js.](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) om projectbestanden te bewerken, of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) om het project uit te voeren.

> [!div renderon="docs"]
> ## <a name="register-and-download-the-quickstart-app"></a>De quickstart-app registreren en downloaden
> Als u de quickstart-app wilt starten, gebruikt u een van de volgende opties.
>
> ### <a name="option-1-express-register-and-automatically-configure-the-app-and-then-download-the-code-sample"></a>Optie 1 (express): De app registreren en automatisch configureren en download vervolgens het codevoorbeeld
>
> 1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
> 1. Als uw account toegang heeft tot meer dan één tenant, selecteert u het account rechtsboven en stelt u uw portalsessie in op de Azure AD-tenant (Azure Ad) van Azure Active Directory.
> 1. Open het nieuwe deelvenster [App-registraties](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs) in de Azure-portal.
> 1. Voer een naam in voor uw toepassing en selecteer **Vervolgens Registreren**.
> 1. Ga naar het deelvenster Snelstart en bekijk de Snelstart hoek. Volg de instructies om uw nieuwe toepassing te downloaden en automatisch te configureren.
>
> ### <a name="option-2-manual-register-and-manually-configure-the-application-and-code-sample"></a>Optie 2 (handleiding): De toepassing en het codevoorbeeld handmatig registreren en configureren
>
> #### <a name="step-1-register-the-application"></a>Stap 1: De aanvraag registreren
>
> 1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
> 1. Als uw account toegang heeft tot meer dan één tenant, selecteert u uw account rechtsboven en stelt u uw portalsessie in op de Azure AD-tenant die u wilt gebruiken.
> 1. Volg de instructies om een toepassing van één pagina te registreren in de [Azure-portal.](https://docs.microsoft.com/azure/active-directory/develop/scenario-spa-app-registration)
> 1. Voeg een nieuw platform toe aan het **verificatievenster** van `http://localhost:4200/`uw app-registratie en registreer de omleidinguri: .
> 1. Deze quickstart maakt gebruik van de [impliciete subsidiestroom](v2-oauth2-implicit-grant-flow.md). Selecteer de **impliciete subsidie-instellingen** voor **ID-tokens** en **Toegangstokens.** ID-tokens en toegangstokens zijn vereist omdat deze app gebruikers aantekent en een API aanroept.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-the-application-in-the-azure-portal"></a>Stap 1: De toepassing configureren in de Azure-portal
> Voor het codevoorbeeld voor deze quickstart moet u een **http://localhost:4200/** omleidingsURI toevoegen als impliciete **subsidie**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Breng deze wijzigingen voor mij aan]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Al geconfigureerd](media/quickstart-v2-javascript/green-check.png) Uw toepassing is al geconfigureerd met deze kenmerken.

#### <a name="step-2-download-the-code-sample"></a>Stap 2: Download het voorbeeld van de code
>[!div renderon="docs"]
>Als u het project met een webserver wilt uitvoeren met Behulp van Node.js, [kloont u de voorbeeldopslagplaats](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular) of [downloadt u de kernprojectbestanden.](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular/archive/master.zip) Open de bestanden met behulp van een editor zoals Visual Studio Code.

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
> Vervang deze waarden:
>
>|Waardenaam|Beschrijving|
>|---------|---------|
>|Enter_the_Application_Id_Here|Op de **overzichtspagina** van uw aanvraagregistratie is dit de **id-waarde van uw toepassing(client).** |
>|Enter_the_Cloud_Instance_Id_Here|Dit is de instantie van de Azure-cloud. Voer voor de hoofd- **https://login.microsoftonline.com**of globale Azure-cloud . Voor nationale wolken (bijvoorbeeld, China), zie [Nationale wolken](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud).|
>|Enter_the_Tenant_Info_Here| Stel in op een van de volgende opties: Als uw toepassing *accounts in deze organisatiemap*ondersteunt, vervangt u deze waarde door de id of tenantnaam (bijvoorbeeld **contoso.microsoft.com).** Als uw toepassing *accounts in een organisatiemap*ondersteunt, vervangt u deze waarde door **organisaties**. Als uw toepassing *accounts in een organisatiemap en persoonlijke Microsoft-accounts*ondersteunt, vervangt u deze waarde door **algemene**. Als u de ondersteuning alleen voor *persoonlijke Microsoft-accounts*wilt beperken, vervangt u deze waarde door **consumenten**. |
>|Enter_the_Redirect_Uri_Here|Vervangen **http://localhost:4200**door .|
>|cacheLocatie  | (Optioneel) Stel de browseropslag in voor de verificatiestatus. De standaardinstelling is **sessionStorage**.   |
>|storeAuthStateInCookie  | (Optioneel) Identificeer de bibliotheek die de status van het verificatieverzoek opslaat. Deze status is vereist om de verificatiestromen in de browsercookies te valideren. Deze cookie is ingesteld voor Internet Explorer en Edge om deze twee browsers aan te passen. Zie voor meer informatie de [bekende problemen.](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues->on-IE-and-Edge-Browser#issues) |
> > [!TIP]
> > Om de waarden van **Toepassings-id (client-id)**, **Map-id (tenant-id)** en **Ondersteunde accounttypen** te achterhalen, gaat u naar de **Overzichtspagina** van de app in de Azure-portal.

Zie [Clienttoepassingen initialiseren](msal-js-initializing-client-applications.md)voor meer informatie over beschikbare configureerbare opties. 

U vindt de broncode voor de MSAL.js-bibliotheek in de [AzureAD/microsoft-authentication-library-for-js-repository](https://github.com/AzureAD/microsoft-authentication-library-for-js) op GitHub.

>[!div class="sxs-lookup" renderon="portal"]
>#### <a name="step-3-run-the-project"></a>Stap 3: Het project uitvoeren

>[!div renderon="docs"]
>#### <a name="step-4-run-the-project"></a>Stap 4: Het project uitvoeren

Als u Node.js gebruikt:

1. Start de server door de volgende opdrachten uit de projectmap uit te voeren:

   ```console
   npm install
   npm start
   ```

1. Blader **http://localhost:4200/** naar .
1. Selecteer **Aanmelding**.
1. Selecteer **Profiel** dat u Microsoft Graph wilt aanroepen.

Nadat de browser de toepassing heeft geladen, selecteert u **Aanmelding**. De eerste keer dat u zich aanmeldt, wordt u gevraagd uw toestemming te geven om de toepassing toegang te geven tot uw profiel en u aan te melden. Nadat u bent aangemeld, selecteert u **Profiel**en worden uw gebruikersprofielgegevens op de pagina weergegeven.

## <a name="how-the-sample-works"></a>Hoe het voorbeeld werkt

![Diagram dat laat zien hoe de voorbeeld-app in deze quickstart werkt](media/quickstart-v2-javascript/javascriptspa-intro.svg)


## <a name="next-steps"></a>Volgende stappen

Leer vervolgens hoe u zich aanmeldt bij een gebruiker en tokens aanschaffen in de instructie Lestekst Angular:

> [!div class="nextstepaction"]
> [Hoekige zelfstudie](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-angular)

