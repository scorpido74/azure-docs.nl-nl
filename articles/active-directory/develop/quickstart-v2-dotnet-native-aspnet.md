---
title: Een ASP.NET-Web-API aanroepen die wordt beveiligd door micro soft Identity platform
description: In deze Quick Start leert u hoe u een ASP.NET-Web-API aanroept die wordt beveiligd door micro soft Identity platform van een Windows Desktop-toepassing (WPF). De WPF-client verifieert een gebruiker, vraagt een toegangs token aan en roept de Web-API aan.
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81536145"
---
# <a name="quickstart-call-an-aspnet-web-api-protected-by-microsoft-identity-platform"></a>Quick Start: een ASP.NET-Web-API aanroepen die wordt beveiligd door micro soft Identity platform

In deze Quick Start maakt u een web-API beschikbaar en kunt u deze beveiligen zodat alleen geverifieerde gebruikers er toegang toe hebben. Dit voor beeld laat zien hoe u een ASP.NET-Web-API beschikbaar maakt, zodat deze tokens kan accepteren die worden uitgegeven door persoonlijke accounts (inclusief outlook.com, live.com en anderen), evenals werk-en school accounts van elk bedrijf of organisatie dat is geïntegreerd met het micro soft Identity-platform.

Het voor beeld omvat ook een WPF-client (Windows Desktop Application) die laat zien hoe u een toegangs token kunt aanvragen om toegang te krijgen tot een web-API.

## <a name="prerequisites"></a>Vereisten

Als u dit voor beeld wilt uitvoeren, hebt u het volgende nodig:

* Visual Studio 2017 of 2019.  Down load [Visual Studio gratis](https://www.visualstudio.com/downloads/).

* Een [Microsoft-account](https://www.outlook.com) -of [Office 365-ontwikkelaars programma](/office/developer-program/office-365-developer-program)

## <a name="download-or-clone-this-sample"></a>Dit voor beeld downloaden of klonen

U kunt dit voor beeld klonen vanuit de shell of de opdracht regel:

  ```console
  git clone https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git
  ```

Of u kunt [het voor beeld downloaden als een zip-bestand](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/complete.zip).

## <a name="register-your-web-api-in-the-application-registration-portal"></a>De Web-API registreren in de portal voor toepassings registratie

### <a name="choose-the-azure-ad-tenant-where-you-want-to-create-your-applications"></a>Kies de Azure AD-Tenant waar u uw toepassingen wilt maken

Als u uw apps hand matig wilt registreren, moet u eerst het volgende doen:

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Als uw account zich in meer dan één Azure AD-Tenant bevindt, selecteert u uw profiel in de rechter bovenhoek in het menu boven aan de pagina en **gaat u naar de map**.
   Wijzig uw portal sessie in de gewenste Azure AD-Tenant.

### <a name="register-the-service-app-todolistservice"></a>De service-app registreren (TodoListService)

1. Navigeer naar de pagina micro soft-identiteits platform voor ontwikkel aars [app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) .
1. Selecteer **nieuwe registratie**.
1. Wanneer de **pagina een toepassing registreren** wordt weer gegeven, voert u de registratie gegevens van uw toepassing in:
   - Voer in de sectie **Naam** een beschrijvende toepassingsnaam in die zichtbaar is voor gebruikers van de app. Bijvoorbeeld: `AppModelv2-NativeClient-DotNet-TodoListService`.
   - Wijzig **ondersteunde account typen** **in accounts in elke organisatie Directory**.
   - Selecteer **Registreren** om de toepassing te maken.

1. Zoek op de pagina app- **overzicht** de waarde van de **toepassing (client)** en noteer deze voor later. U hebt deze nodig voor het configureren van het Visual Studio-configuratie bestand voor`ClientId` dit `TodoListService\Web.config`project (in).
1. Selecteer de sectie **een API beschikbaar** maken en:
   - Selecteer **een bereik toevoegen**
   - Accepteer de voorgestelde URI voor de toepassings-ID (API://{clientId}) door **opslaan en door gaan** te selecteren.
   - Voer de volgende para meters in:
     - voor gebruik van **Scope namen**`access_as_user`
     - Zorg ervoor dat de optie **beheerders en gebruikers** is geselecteerd voor **wie toestemming kan** geven
     - **weergave naam type beheerder toestemming**`Access TodoListService as a user`
     - **beschrijvings type in beheerders toestemming**`Accesses the TodoListService web API as a user`
     - type **weergave naam in de gebruikers toestemming**`Access TodoListService as a user`
     - type **Beschrijving van de gebruikers toestemming**`Accesses the TodoListService web API as a user`
     - **Status** als **ingeschakeld** blijven
     - **Bereik toevoegen** selecteren

### <a name="configure-the-service-project-to-match-the-registered-web-api"></a>Het service project configureren zodat dit overeenkomt met de geregistreerde Web-API

1. Open de oplossing in Visual Studio en open vervolgens het bestand **Web. config** in de hoofdmap van **TodoListService** -project.
1. Vervang de waarde van `ida:ClientId` para meter door de **client-ID (toepassings-id)** van de toepassing die u zojuist hebt geregistreerd in de portal voor toepassings registratie.

### <a name="add-the-new-scope-to-the-todolistclients-appconfig"></a>De nieuwe scope toevoegen aan de app. config van de *TodoListClient*

1. Open het **bestand app. config** dat zich in de hoofdmap van het **TodoListClient** -project bevindt en plak vervolgens **toepassings-id** uit de toepassing die u zojuist hebt geregistreerd `{Enter the Application ID of your TodoListService from the app registration portal}`voor uw *TodoListService* onder `TodoListServiceScope` para meter, waarbij de teken reeks wordt vervangen.

   > Opmerking: Zorg ervoor dat deze gebruikmaakt van de volgende indeling:
   >
   > `api://{TodoListService-Application-ID}/access_as_user`
   >
   >(waarbij {TodoListService-Application-ID} de GUID voor de toepassings-ID voor uw TodoListService is).

## <a name="register-the-client-app-todolistclient"></a>De client-app registreren (TodoListClient)

In deze stap configureert u uw *TodoListClient* -project door een nieuwe toepassing te registreren in de portal voor toepassings registratie. In de gevallen waarin de-client en-server worden beschouwd als *dezelfde toepassing* , kunt u ook gewoon dezelfde toepassing die is geregistreerd in de stap 2. opnieuw gebruiken. Het gebruik van dezelfde toepassing is nodig als u wilt dat gebruikers zich aanmelden met persoonlijke micro soft-accounts

### <a name="register-the-todolistclient-application-in-the-application-registration-portal"></a>De *TodoListClient* -toepassing registreren in de *Portal voor toepassings registratie*

1. Navigeer naar de pagina micro soft-identiteits platform voor ontwikkel aars [app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) .
1. Selecteer **nieuwe registratie**.
1. Wanneer de **pagina een toepassing registreren** wordt weer gegeven, voert u de registratie gegevens van uw toepassing in:
   - Voer in de sectie **Naam** een beschrijvende toepassingsnaam in die zichtbaar is voor gebruikers van de app. Bijvoorbeeld: `NativeClient-DotNet-TodoListClient`.
   - Wijzig **ondersteunde account typen** **in accounts in elke organisatie Directory**.
   - Selecteer **Registreren** om de toepassing te maken.
1. Selecteer op de pagina Overzicht van de app de sectie **Verificatie**.
   - Schakel in de sectie **omleidings-uri's** | **voorgestelde omleidings-uri's voor open bare clients (Mobile, Desktop)** in**https://login.microsoftonline.com/common/oauth2/nativeclient**
   - Selecteer **Opslaan**.
1. De sectie **API-machtigingen** selecteren
   - Klik op de knop **een machtiging toevoegen** en vervolgens op
   - Selecteer het tabblad **mijn api's** .
   - Selecteer in de lijst met Api's de `AppModelv2-NativeClient-DotNet-TodoListService API`of de naam die u hebt ingevoerd voor de Web-API.
   - Controleer de **access_as_user** machtiging als deze nog niet is ingeschakeld. Gebruik het zoekvak als dat nodig is.
   - Selecteer de knop **machtigingen toevoegen**

### <a name="configure-your-todolistclient-project"></a>Uw *TodoListClient* -project configureren

1. In de *Portal voor toepassings registratie*op de pagina **overzicht** kopieert u de waarde van de **toepassings-id (client)**
1. Open het bestand **app. config** dat zich in de hoofdmap van het **TodoListClient** -project bevindt en plak `ida:ClientId` de waarde in de parameter waarde

## <a name="run-your-project"></a>Uw project uitvoeren

1. Druk `<F5>` op om uw project uit te voeren. Uw *TodoListClient* moet worden geopend.
1. Selecteer **in** de rechter bovenhoek de optie aanmelden en meld u aan met dezelfde gebruiker die u hebt gebruikt om uw toepassing te registreren, of een gebruiker in dezelfde map.
1. Als u zich voor de eerste keer aanmeldt, wordt u mogelijk gevraagd om toestemming te geven voor *TodoListService* Web API.
1. De aanmelding vraagt het toegangs token ook aan het *access_as_user* bereik om toegang te krijgen tot de *TodoListService* -Web-API en de *taken* lijst te bewerken.

## <a name="pre-authorize-your-client-application"></a>Uw client toepassing vooraf autoriseren

Een van de manieren om gebruikers uit andere directory's toegang te geven tot uw web-API is door de client toepassingen *vooraf te autoriseren* voor toegang tot uw web-API door de toepassings-id's van client toepassingen toe te voegen aan de lijst met *vooraf geautoriseerde* toepassingen voor uw web-API. Door een vooraf geautoriseerde client toe te voegen, hoeft u geen toestemming te geven voor het gebruik van uw web-API. Volg de onderstaande stappen om uw webtoepassing vooraf te autoriseren::

1. Ga terug naar de *Portal voor toepassings registratie* en open de eigenschappen van uw **TodoListService**.
1. Klik in de sectie **een API beschikbaar** maken op **een client toepassing toevoegen** onder de sectie *geautoriseerde client toepassingen* .
1. Plak in het veld *client-id* de toepassings-id van `TodoListClient` de toepassing.
1. Selecteer in de sectie *gemachtigde bereiken* het bereik voor deze web-API `api://<Application ID>/access_as_user`.
1. Klik onder aan de pagina op de knop **toepassing toevoegen** .

## <a name="run-your-project"></a>Uw project uitvoeren

1. Druk `<F5>` op om uw project uit te voeren. Uw *TodoListClient* moet worden geopend.
1. Selecteer aan de rechter kant **Aanmelden** (of cache/aanmelden wissen) en meld u vervolgens aan met behulp van een persoonlijke Microsoft-account (live.com of Hotmail.com) of werk-of school account.

## <a name="optional-restrict-sign-in-access-to-your-application"></a>Optioneel: aanmeldings toegang tot uw toepassing beperken

Wanneer u dit code voorbeeld downloadt en de toepassing configureert voor gebruik van het Azure Active Directory v2-eind punt volgens de voor gaande stappen, kunnen persoonlijke accounts, zoals outlook.com, live.com en anderen, evenals werk-of school accounts van organisaties die zijn geïntegreerd met Azure AD, tokens aanvragen en toegang krijgen tot uw web-API.

Als u wilt beperken wie zich kan aanmelden bij uw toepassing, gebruikt u een van de volgende opties:

### <a name="option-1-restrict-access-to-a-single-organization-single-tenant"></a>Optie 1: de toegang tot één organisatie beperken (één Tenant)

U kunt aanmeldings toegang voor uw toepassing beperken tot alleen gebruikers accounts die zich in één Azure AD-Tenant bevinden, waaronder *Gast accounts* van die Tenant. Dit scenario is gebruikelijk voor *line-of-business-toepassingen*:

1. Open het **\Startup.auth** -bestand van de App_Start en wijzig de waarde van het eind punt voor meta gegevens `OpenIdConnectSecurityTokenProvider` dat `"https://login.microsoftonline.com/{Tenant ID}/v2.0/.well-known/openid-configuration"` is door gegeven aan de to (u kunt ook de `contoso.onmicrosoft.com`naam van de Tenant gebruiken, bijvoorbeeld).
2. Stel `ValidIssuer` in hetzelfde bestand de eigenschap in op de `TokenValidationParameters` aan `"https://sts.windows.net/{Tenant ID}/"` en het `ValidateIssuer` argument in. `true`

### <a name="option-2-use-a-custom-method-to-validate-issuers"></a>Optie 2: een aangepaste methode gebruiken om verleners te valideren

U kunt een aangepaste methode implementeren om verleners te valideren met behulp van de para meter **IssuerValidator** . Meer informatie over het gebruik van deze para meter vindt u in de [TokenValidationParameters-klasse](/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters?view=azure-dotnet).

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het scenario voor de beveiligde web-API dat het micro soft Identity-platform ondersteunt:
> [!div class="nextstepaction"]
> [Beveiligd web API-scenario](scenario-protected-web-api-overview.md)
