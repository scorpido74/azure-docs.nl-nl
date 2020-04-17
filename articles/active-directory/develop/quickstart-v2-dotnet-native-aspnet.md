---
title: Een ASP.NET web-API aanroepen die is beveiligd door het identiteitsplatform van Microsoft
description: In deze quickstart leert u hoe u een ASP.NET web-API aanroept die is beveiligd door microsoft-identiteitsplatform en een WPF-toepassing (Windows Desktop). De WPF-client verifieert een gebruiker, vraagt een toegangstoken aan en roept de web-API aan.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 12/12/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.openlocfilehash: 084083a704a007e6675234883c62350d1d9a0849
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536145"
---
# <a name="quickstart-call-an-aspnet-web-api-protected-by-microsoft-identity-platform"></a>Snelstart: een ASP.NET web-API aanroepen die is beveiligd door het identiteitsplatform van Microsoft

In deze quickstart stelt u een web-API bloot en beschermt u deze zodat alleen geverifieerde gebruikers er toegang toe hebben. In dit voorbeeld wordt uitgelegd hoe u een ASP.NET web-API blootleggen, zodat deze tokens kan accepteren die zijn uitgegeven door persoonlijke accounts (waaronder outlook.com, live.com en anderen) en werk- en schoolaccounts van elk bedrijf of organisatie dat is geïntegreerd met het Identiteitsplatform van Microsoft.

Het voorbeeld bevat ook een WPF-client (Windows Desktop-toepassing) die laat zien hoe u een toegangstoken aanvragen om toegang te krijgen tot een web-API.

## <a name="prerequisites"></a>Vereisten

Als u dit voorbeeld wilt uitvoeren, hebt u het volgende nodig:

* Visual Studio 2017 of 2019.  Download [Visual Studio gratis.](https://www.visualstudio.com/downloads/)

* Een [Microsoft-account](https://www.outlook.com) of [een Office 365 Developer Program](/office/developer-program/office-365-developer-program)

## <a name="download-or-clone-this-sample"></a>Dit voorbeeld downloaden of klonen

U dit voorbeeld klonen vanuit uw shell- of opdrachtregel:

  ```console
  git clone https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git
  ```

U [het voorbeeld ook downloaden als ZIP-bestand.](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/complete.zip)

## <a name="register-your-web-api-in-the-application-registration-portal"></a>Uw web-API registreren in de portal voor toepassingsregistratie

### <a name="choose-the-azure-ad-tenant-where-you-want-to-create-your-applications"></a>Kies de Azure AD-tenant waar u uw toepassingen wilt maken

Als u uw apps handmatig wilt registreren, moet u als eerste stap:

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Als uw account aanwezig is in meer dan één Azure AD-tenant, selecteert u uw profiel rechtsboven in het menu boven aan de pagina en schakelt u **van map**.
   Wijzig uw portalsessie in de gewenste Azure AD-tenant.

### <a name="register-the-service-app-todolistservice"></a>De service-app registreren (TodoListService)

1. Navigeer naar de pagina Microsoft-identiteitsplatform voor ontwikkelaars [App-registraties.](https://go.microsoft.com/fwlink/?linkid=2083908)
1. Selecteer **Nieuwe registratie**.
1. Wanneer de **pagina Een aanvraag registreren** wordt weergegeven, voert u de registratiegegevens van uw aanvraag in:
   - Voer in de sectie **Naam** een beschrijvende toepassingsnaam in die zichtbaar is voor gebruikers van de app. Bijvoorbeeld: `AppModelv2-NativeClient-DotNet-TodoListService`.
   - **Ondersteunde accounttypen** wijzigen in **accounts in een organisatiemap.**
   - Selecteer **Registreren** om de toepassing te maken.

1. Zoek op de pagina **Overzicht** van de app de waarde van de **id-toepassing (client)** en leg deze vast voor later. U hebt het nodig om het configuratiebestand visual`ClientId` `TodoListService\Web.config`studio voor dit project (in) te configureren.
1. Selecteer de sectie **Een API blootleggen** en:
   - Een **bereik toevoegen selecteren**
   - accepteer de voorgestelde Application ID URI (api://{clientId}) door **Opslaan en doorgaan te** selecteren
   - Voer de volgende parameters in:
     - voor het gebruik **van de naam scope**`access_as_user`
     - Ervoor zorgen dat de optie **Beheerders en gebruikers** is geselecteerd voor Wie toestemming kan **geven**
     - in het **weergavetype beheerderstoestemming**`Access TodoListService as a user`
     - in het **type beschrijving van beheerderstoestemming**`Accesses the TodoListService web API as a user`
     - in **het weergavenaamtype gebruikerstoestemming**`Access TodoListService as a user`
     - in type **beschrijving van gebruikerstoestemming**`Accesses the TodoListService web API as a user`
     - **Status behouden** als **ingeschakeld**
     - Bereik **toevoegen selecteren**

### <a name="configure-the-service-project-to-match-the-registered-web-api"></a>Het serviceproject configureren op de geregistreerde web-API

1. Open de oplossing in Visual Studio en open het **web.config-bestand** onder de hoofdmap van het **TodoListService-project.**
1. Vervang de `ida:ClientId` waarde van de parameter door de **client-id (toepassings-id)** van de toepassing die u zojuist hebt geregistreerd in de Portal voor toepassingsregistratie.

### <a name="add-the-new-scope-to-the-todolistclients-appconfig"></a>Voeg het nieuwe bereik toe aan de app.config van *De TodoListClient*

1. Open het **app.config-bestand** in de hoofdmap van **het TodoListClient-project** en plak vervolgens **toepassings-id** uit de `{Enter the Application ID of your TodoListService from the app registration portal}`toepassing die u zojuist hebt geregistreerd voor uw *TodoListService* onder `TodoListServiceScope` parameter, ter vervanging van de tekenreeks.

   > Opmerking: Controleer of de volgende indeling wordt gebruikt:
   >
   > `api://{TodoListService-Application-ID}/access_as_user`
   >
   >(waarbij {TodoListService-Application-ID} de GUID is die de toepassings-id voor uw TodoListService vertegenwoordigt).

## <a name="register-the-client-app-todolistclient"></a>De client-app registreren (TodoListClient)

In deze stap configureert u uw *TodoListClient-project* door een nieuwe toepassing te registreren in de toepassingsregistratieportal. In de gevallen waarin de client en server als *dezelfde toepassing* worden beschouwd, u dezelfde toepassing die is geregistreerd in de 'Stap 2'. Dezelfde toepassing gebruiken is nodig als u wilt dat gebruikers zich aanmelden met persoonlijke Microsoft-accounts

### <a name="register-the-todolistclient-application-in-the-application-registration-portal"></a>De *Toepassing TodoListClient* registreren in de *portal voor toepassingsregistratie*

1. Navigeer naar de pagina Microsoft-identiteitsplatform voor ontwikkelaars [App-registraties.](https://go.microsoft.com/fwlink/?linkid=2083908)
1. Selecteer **Nieuwe registratie**.
1. Wanneer de **pagina Een aanvraag registreren** wordt weergegeven, voert u de registratiegegevens van uw aanvraag in:
   - Voer in de sectie **Naam** een beschrijvende toepassingsnaam in die zichtbaar is voor gebruikers van de app. Bijvoorbeeld: `NativeClient-DotNet-TodoListClient`.
   - **Ondersteunde accounttypen** wijzigen in **accounts in een organisatiemap.**
   - Selecteer **Registreren** om de toepassing te maken.
1. Selecteer op de pagina Overzicht van de app de sectie **Verificatie**.
   - Controleer in de sectie **URL's** | die worden voorgesteld om**te leiden voor openbare clients (mobiel, desktop)****https://login.microsoftonline.com/common/oauth2/nativeclient**
   - Selecteer **Opslaan**.
1. De sectie **API-machtigingen** selecteren
   - Klik **op** de knop Een machtiging toevoegen en vervolgens op de knop Een machtiging toevoegen en vervolgens
   - Selecteer het tabblad **Mijn API's.**
   - Selecteer in de lijst met `AppModelv2-NativeClient-DotNet-TodoListService API`API's de naam of de naam die u voor de web-API hebt ingevoerd.
   - Controleer de **access_as_user** toestemming als deze nog niet is gecontroleerd. Gebruik indien nodig het zoekvak.
   - De knop **Machtigingen toevoegen selecteren**

### <a name="configure-your-todolistclient-project"></a>Uw *TodoListClient-project* configureren

1. Kopieer in de *portal Voor toepassingsregistratie*op de **pagina Overzicht** de waarde van de **id (toepassing)**
1. Open het **app.config-bestand** in de hoofdmap van het **TodoListClient-project** en plak de waarde in de `ida:ClientId` parameterwaarde

## <a name="run-your-project"></a>Uw project uitvoeren

1. Druk `<F5>` op om uw project uit te voeren. Uw *TodoListClient* moet worden geopend.
1. Selecteer **Rechtsboven inloggen** en meld u aan bij dezelfde gebruiker die u hebt gebruikt om uw toepassing te registreren, of een gebruiker in dezelfde map.
1. Als u zich op dit moment voor het eerst aanmeldt, wordt u mogelijk gevraagd toestemming te geven voor *de TodoListService* Web Api.
1. De aanmelding vraagt ook het toegangstoken tot het *access_as_user-scope* om toegang te krijgen tot *TodoListService* Web Api en de *to-do-lijst te* manipuleren.

## <a name="pre-authorize-your-client-application"></a>Uw clienttoepassing vooraf autoriseren

Een van de manieren om gebruikers van andere mappen toegang te geven tot uw web-API is door de clienttoepassingen *vooraf toestemming* te geven om toegang te krijgen tot uw web-API door de toepassings-id's van clienttoepassingen toe te voegen aan de lijst met *vooraf geautoriseerde* toepassingen voor uw web-API. Door een vooraf geautoriseerde client toe te voegen, hoeft u de gebruiker geen toestemming te geven voor het gebruik van uw web-API. Volg de onderstaande stappen om uw webtoepassing vooraf te autoriseren::

1. Ga terug naar de *portal voor toepassingsregistratie* en open de eigenschappen van uw **TodoListService.**
1. Klik **in de** sectie Een API bloot leggen op **Een clienttoepassing toevoegen** onder de sectie Geautoriseerde *clienttoepassingen.*
1. Plak in het veld *Client-id* `TodoListClient` de toepassings-id van de toepassing.
1. Selecteer in de sectie *Geautoriseerde scopes* het `api://<Application ID>/access_as_user`bereik voor deze web-API .
1. Druk onder aan de pagina op de knop **Toepassing toevoegen.**

## <a name="run-your-project"></a>Uw project uitvoeren

1. Druk `<F5>` op om uw project uit te voeren. Uw *TodoListClient* moet worden geopend.
1. Selecteer **Rechtsboven** inloggen (of Cache/Aanmelding wissen) en meld u aan via een persoonlijk Microsoft-account (live.com of hotmail.com) of werk- of schoolaccount.

## <a name="optional-restrict-sign-in-access-to-your-application"></a>Optioneel: aanmeldingstoegang tot uw toepassing beperken

Wanneer u dit voorbeeld van deze code downloadt en de toepassing configureert om het Azure Active Directory v2-eindpunt volgens de voorgaande stappen te gebruiken, kunnen zowel persoonlijke accounts - zoals outlook.com, live.com en anderen - als Werk- of schoolaccounts van organisaties die zijn geïntegreerd met Azure AD tokens aanvragen en toegang krijgen tot uw web-API.

Als u wilt beperken wie zich bij uw toepassing kan aanmelden, gebruikt u een van de opties:

### <a name="option-1-restrict-access-to-a-single-organization-single-tenant"></a>Optie 1: Toegang tot één organisatie beperken (één tenant)

U de aanmeldingstoegang voor uw toepassing beperken tot alleen gebruikersaccounts die zich in één Azure AD-tenant bevinden, inclusief *gastaccounts* van die tenant. Dit scenario is een veelvoorkomende voor *bedrijfstoepassingen:*

1. Open het **bestand App_Start\Startup.Auth** en wijzig de waarde van het `OpenIdConnectSecurityTokenProvider` metagegevenseindpunt dat is doorgegeven aan `"https://login.microsoftonline.com/{Tenant ID}/v2.0/.well-known/openid-configuration"` (u ook de tenantnaam gebruiken, zoals `contoso.onmicrosoft.com`).
2. Stel in hetzelfde `ValidIssuer` bestand de `TokenValidationParameters` `"https://sts.windows.net/{Tenant ID}/"` eigenschap `ValidateIssuer` op `true`de aan en het argument in op .

### <a name="option-2-use-a-custom-method-to-validate-issuers"></a>Optie 2: Een aangepaste methode gebruiken om emittenten te valideren

U een aangepaste methode implementeren om emittenten te valideren met behulp van de parameter **IssuerValidator.** Lees voor meer informatie over het gebruik van deze parameter de [klasse TokenValidationParameters.](/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters?view=azure-dotnet)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het beveiligde web-API-scenario dat het Microsoft-identiteitsplatform ondersteunt:
> [!div class="nextstepaction"]
> [Beveiligd web-API-scenario](scenario-protected-web-api-overview.md)
