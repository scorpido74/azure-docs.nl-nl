---
title: Een ASP.NET-web-API aanroepen die wordt beveiligd door Microsoft-identiteitsplatform
description: In deze quickstart leert u hoe u een ASP.NET-web-API die wordt beveiligd door Azure Active Directory aanroept vanuit een Windows-desktoptoepassing (WPF). De WPF-client verifieert een gebruiker, vraagt een toegangstoken aan en roept de web-API aan.
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
ms.openlocfilehash: 22bf7e85a48e0d138bfdbca82cf032287d982899
ms.sourcegitcommit: 61d92af1d24510c0cc80afb1aebdc46180997c69
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/24/2020
ms.locfileid: "85339593"
---
# <a name="quickstart-call-an-aspnet-web-api-protected-by-microsoft-identity-platform"></a>Quickstart: Een ASP.NET-web-API aanroepen die wordt beveiligd door Microsoft-identiteitsplatform

In deze quickstart maakt u een web-API beschikbaar en beveiligt u deze zodat alleen geverifieerde gebruikers er toegang toe hebben. Dit voorbeeld laat zien hoe u een ASP.NET-web-API beschikbaar maakt, zodat deze tokens kan accepteren die worden uitgegeven door persoonlijke accounts (inclusief outlook.com, live.com en andere), evenals werk- en schoolaccounts van bedrijven of organisaties die zijn geïntegreerd met het Microsoft-identiteitsplatform.

Het voorbeeld omvat ook een WPF-client (Windows Desktop Application) die laat zien hoe u een toegangstoken kunt aanvragen om toegang te krijgen tot een web-API.

## <a name="prerequisites"></a>Vereisten

Als u dit voorbeeld wilt uitvoeren, hebt u het volgende nodig:

* Visual Studio 2017 of 2019.  U kunt [Visual Studio gratis](https://www.visualstudio.com/downloads/) downloaden.

* Een [Microsoft-account](https://www.outlook.com) of [Microsoft 365 Developer Program](/office/developer-program/office-365-developer-program)

## <a name="download-or-clone-this-sample"></a>Dit voorbeeld downloaden of klonen

U kunt dit voorbeeld klonen vanuit de shell of de opdrachtregel:

  ```console
  git clone https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git
  ```

U kunt ook [het voorbeeld downloaden als een zip-bestand](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/complete.zip).

## <a name="register-your-web-api-in-the-application-registration-portal"></a>De web-API registreren in de portal voor toepassingsregistratie

### <a name="choose-the-azure-ad-tenant-where-you-want-to-create-your-applications"></a>Kies de Azure AD-tenant waar u de toepassing wilt maken

Als u uw apps handmatig wilt registreren, moet u eerst het volgende doen:

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Als uw account aanwezig is in meer dan één Azure AD-tenant, selecteert u uw profiel in het menu rechts bovenaan de pagina en vervolgens **Schakelen tussen mappen**.
   Wijzig uw portalsessie naar de gewenste Azure AD-tenant.

### <a name="register-the-service-app-todolistservice"></a>De service-app registreren (TodoListService)

1. Ga naar de pagina [App-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) in het Microsoft identity platform voor ontwikkelaars.
1. Selecteer **Nieuwe registratie**.
1. Wanneer de pagina **Een toepassing registreren** verschijnt, voert u de registratiegegevens van de toepassing in:
   - Voer in de sectie **Naam** een beschrijvende toepassingsnaam. Deze wordt zichtbaar voor gebruikers van de app. Bijvoorbeeld: `AppModelv2-NativeClient-DotNet-TodoListService`.
   - Wijzig **Ondersteunde accounttypen** in **Accounts in een organisatieadreslijst**.
   - Selecteer **Registreren** om de toepassing te maken.

1. Zoek de waarde **Toepassings-id (client)** op de app-pagina **Overzicht** en noteer deze voor later. U hebt deze nodig om het Visual Studio-configuratiebestand van dit project te configureren (`ClientId` in `TodoListService\Web.config`).
1. Selecteer de sectie **Een API beschikbaar maken** en:
   - Selecteer **Een bereik toevoegen**
   - Accepteer de voorgestelde URI voor de toepassings-id (API://{clientId}) door **Opslaan en doorgaan** te selecteren
   - Voer de volgende parameters in:
     - gebruik voor **Naam van bereik** `access_as_user`
     - Zorg ervoor dat de optie **Beheerders en gebruikers** is geselecteerd voor **Wie kan toestemming verlenen?**
     - typ `Access TodoListService as a user` in **Weergavenaam van beheerderstoestemming**
     - typ `Accesses the TodoListService web API as a user` in **Beschrijving van beheerderstoestemming**
     - typ `Access TodoListService as a user` in **Weergavenaam van gebruikerstoestemming**
     - typ `Accesses the TodoListService web API as a user` in **Beschrijving van gebruikerstoestemming**
     - Laat **Status** op **Ingeschakeld** staan
     - Selecteer **Bereik toevoegen**

### <a name="configure-the-service-project-to-match-the-registered-web-api"></a>Het serviceproject configureren zodat dit overeenkomt met de geregistreerde web-API

1. Open de oplossing in Visual Studio en open vervolgens het bestand **Web.config** in de hoofdmap van **TodoListService**-project.
1. Vervang de waarde van parameter `ida:ClientId` door de **Client-id (Toepassings-id)** van de toepassing die u zojuist hebt geregistreerd in de portal voor toepassingsregistratie.

### <a name="add-the-new-scope-to-the-todolistclients-appconfig"></a>Het nieuwe bereik toevoegen aan de app.config van de *TodoListClient*

1. Open het bestand **app.config** dat zich bevindt in de hoofdmap van het project **TodoListClient** en plak **Toepassings-id** vanuit de toepassing die u zojuist hebt geregistreerd voor uw *TodoListService* onder parameter `TodoListServiceScope`, waarbij u de tekenreeks `{Enter the Application ID of your TodoListService from the app registration portal}`vervangt.

   > Opmerking: Zorg ervoor dat het de volgende indeling gebruikt:
   >
   > `api://{TodoListService-Application-ID}/access_as_user`
   >
   >(waarbij {TodoListService-Application-ID} de GUID is die de toepassings-id voor uw TodoListService vertegenwoordigt).

## <a name="register-the-client-app-todolistclient"></a>De client-app registreren (TodoListClient)

In deze stap configureert u uw *TodoListClient*-project door een nieuwe toepassing te registreren in de portal voor toepassingsregistratie. In de gevallen waarin de client en server worden beschouwd als *dezelfde toepassing*, kunt u ook gewoon dezelfde toepassing die is geregistreerd in Stap 2 opnieuw gebruiken. U moet dezelfde toepassing gebruiken als u wilt dat gebruikers zich aanmelden met persoonlijke Microsoft-accounts

### <a name="register-the-todolistclient-application-in-the-application-registration-portal"></a>Registreer de toepassing *TodoListClient* in de *portal voor toepassingsregistratie*

1. Ga naar de pagina [App-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) in het Microsoft identity platform voor ontwikkelaars.
1. Selecteer **Nieuwe registratie**.
1. Wanneer de pagina **Een toepassing registreren** verschijnt, voert u de registratiegegevens van de toepassing in:
   - Voer in de sectie **Naam** een beschrijvende toepassingsnaam. Deze wordt zichtbaar voor gebruikers van de app. Bijvoorbeeld: `NativeClient-DotNet-TodoListClient`.
   - Wijzig **Ondersteunde accounttypen** in **Accounts in een organisatieadreslijst**.
   - Selecteer **Registreren** om de toepassing te maken.
1. Selecteer op de pagina Overzicht van de app de sectie **Verificatie**.
   - In de sectie **Omleidings-URI's** | **Voorgestelde omleidings-URI's voor openbare clients (mobiel, desktop)** raadpleegt u **https://login.microsoftonline.com/common/oauth2/nativeclient**
   - Selecteer **Opslaan**.
1. Selecteer het gedeelte **API-machtigingen**
   - Klik op de knop **Een machtiging toevoegen** en vervolgens
   - Selecteer het tabblad **Mijn API's**.
   - Selecteer in de lijst met API's de `AppModelv2-NativeClient-DotNet-TodoListService API` of de naam die u hebt ingevoerd voor de web-API.
   - Schakel de machtiging **access_as_user** in als deze nog niet is ingeschakeld. Gebruik het zoekvak indien nodig.
   - Selecteer de knop **Toestemmingen toevoegen**

### <a name="configure-your-todolistclient-project"></a>Uw *TodoListClient*-project configureren

1. In de *portal voor toepassingsregistratie*, op de pagina **Overzicht** kopieert u de waarde van de **Toepassings-id (client)**
1. Open het bestand **app.config** dat zich bevindt in de hoofdmap van het project **TodoListClient** en plak de waarde in de parameterwaarde `ida:ClientId`

## <a name="run-your-project"></a>Uw project uitvoeren

1. Druk op `<F5>` om het project uit te voeren. Als het goed is, wordt uw *TodoListClient* geopend.
1. Selecteer **Aanmelden** rechtsboven en meld u aan met dezelfde gebruiker die u ook hebt gebruikt om uw toepassing te registreren of een gebruiker in dezelfde map.
1. Als u zich voor de eerste keer aanmeldt, wordt u mogelijk gevraagd om toestemming te geven voor *TodoListService* Web-API.
1. De aanmelding vraagt ook het toegangstoken voor het bereik *access_as_user* aan om toegang te krijgen tot de Web-API *TodoListService* en om de *takenlijst* te bewerken.

## <a name="pre-authorize-your-client-application"></a>Uw clienttoepassing vooraf autoriseren

Een van de manieren om gebruikers uit andere directory's toegang te geven tot uw web-API is door de clienttoepassingen *vooraf te autoriseren* voor toegang tot uw web-API door de toepassings-id's uit clienttoepassingen toe te voegen aan de lijst met *vooraf geautoriseerde* toepassingen voor uw web-API. Door een vooraf geautoriseerde client toe te voegen, hoeft de gebruiker geen toestemming te geven om uw web-API te gebruiken. Volg de onderstaande stappen om uw webtoepassing vooraf te autoriseren:

1. Ga terug naar de *Portal voor toepassingsregistratie* en open de eigenschappen van uw **TodoListService**.
1. Klik in de sectie **Een API beschikbaar maken** op **Een clienttoepassing toevoegen** onder het gedeelte *Gemachtigde clienttoepassingen*.
1. Plak in het veld *Client-id* de toepassings-id van de toepassing `TodoListClient`.
1. Selecteer in de sectie *Gemachtigde bereiken* het bereik voor deze web-API `api://<Application ID>/access_as_user`.
1. Klik onder aan de pagina op de knop **Toepassing toevoegen**.

## <a name="run-your-project"></a>Uw project uitvoeren

1. Druk op `<F5>` om het project uit te voeren. Als het goed is, wordt uw *TodoListClient* geopend.
1. Selecteer **Aanmelden** rechtsboven (of Cache/Aanmelding wissen) en meld u vervolgens aan met behulp van een persoonlijk Microsoft-account (live.com of hotmail.com) of een werk- of schoolaccount.

## <a name="optional-restrict-sign-in-access-to-your-application"></a>Optioneel: Aanmeldingstoegang tot uw toepassing beperken

Wanneer u dit codevoorbeeld downloadt en de toepassing configureert om het Azure Active Directory v2-eindpunt te gebruiken volgens de voorgaande stappen, kunnen zowel persoonlijke accounts, zoals outlook.com, live.com en andere, als werk- of schoolaccounts van organisaties die zijn geïntegreerd met Azure AD, tokens aanvragen en toegang krijgen tot uw web-API.

Als u wilt beperken wie zich kan aanmelden bij uw toepassing, gebruikt u een van de volgende opties:

### <a name="option-1-restrict-access-to-a-single-organization-single-tenant"></a>Optie 1: Toegang tot één organisatie beperken (één tenant)

U kunt aanmeldingstoegang voor uw toepassing beperken tot alleen gebruikersaccounts die zich in één Azure AD-tenant bevinden, waaronder *gastaccounts* van die tenant. Dit scenario is gebruikelijk voor *Line-Of-Business-toepassingen*:

1. Open het bestand **App_Start \Startup.Auth** en wijzig de waarde van het eindpunt voor metagegevens dat wordt doorgegeven aan de `OpenIdConnectSecurityTokenProvider` in `"https://login.microsoftonline.com/{Tenant ID}/v2.0/.well-known/openid-configuration"` (u kunt ook de naam van de tenant gebruiken, bijvoorbeeld `contoso.onmicrosoft.com`).
2. Stel in hetzelfde bestand de eigenschap `ValidIssuer` op de `TokenValidationParameters` in op `"https://sts.windows.net/{Tenant ID}/"` en het argument `ValidateIssuer` op `true`.

### <a name="option-2-use-a-custom-method-to-validate-issuers"></a>Optie 2: Een aangepaste methode gebruiken om uitgevers te valideren

U kunt een aangepaste methode implementeren om uitgevers te valideren met behulp van de parameter **IssuerValidator**. Als u meer informatie wilt over het gebruik van deze parameter, leest u de informatie over de [TokenValidationParameters-klasse](/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters?view=azure-dotnet).

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het beveiligde web-appscenario dat het Microsoft Identity-platform ondersteunt:
> [!div class="nextstepaction"]
> [Scenario met beveiligde web-API](scenario-protected-web-api-overview.md)
