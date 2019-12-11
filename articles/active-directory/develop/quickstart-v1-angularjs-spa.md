---
title: Azure AD AngularJS-app met één pagina maken voor aanmelden & afmelden | Microsoft Docs
description: Meer informatie over het bouwen van een AngularJS-app met één pagina die Azure AD integreert voor aanmelding & afmeldings & Azure AD-beveiligde Api's aanroept met behulp van OAuth.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: f2991054-8146-4718-a5f7-59b892230ad7
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.devlang: javascript
ms.topic: quickstart
ms.date: 10/25/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e66b51199d19ea6af2b88c48aa89a93c1ed731c
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74963525"
---
# <a name="quickstart-build-an-angularjs-single-page-app-for-sign-in-and-sign-out-with-azure-active-directory"></a>Snelstart: een AngularJS-app met één pagina compileren voor aanmelden en afmelden met Azure Active Directory

[!INCLUDE [active-directory-develop-applies-v1-adal](../../../includes/active-directory-develop-applies-v1-adal.md)]

> [!IMPORTANT]
> [Micro soft Identity platform](v2-overview.md) is een evolutie van het ontwikkel platform van de Azure Active Directory (Azure AD). Met het Microsoft Identity Platform kunnen ontwikkelaars toepassingen maken waarbij gebruikers zich met alle Microsoft-identiteiten kunnen aanmelden en waarmee tokens worden opgehaald voor het aanroepen van Microsoft-API's, zoals Microsoft Graph, of API's die door ontwikkelaars zijn gemaakt.
> Als u aanmelding voor persoonlijke accounts naast werk-en school accounts wilt inschakelen, kunt u het *[micro soft Identity platform-eind punt](azure-ad-endpoint-comparison.md)* gebruiken.
> Deze Quick start is voor het oudere Azure AD v 1.0-eind punt. Het is raadzaam om het v 2.0-eind punt voor nieuwe projecten te gebruiken. Raadpleeg voor meer informatie [deze Java script Spa-zelf studie](tutorial-v2-javascript-spa.md) en in [dit artikel](active-directory-v2-limitations.md) wordt het *micro soft Identity platform-eind punt*uitgelegd.

Azure Active Directory (Azure AD) maakt het toevoegen, aanmelden en afmelden van OAuth-API-aanroepen naar uw app met één pagina simpel en duidelijk. Uw apps kunnen hiermee gebruikers verifiëren met hun Windows Server Active Directory-accounts en een web-API gebruiken dat Azure AD helpt beveiligen, zoals de Office 365 API's of de API van Azure.

Azure AD biedt voor JavaScript-toepassingen die worden uitgevoerd in een browser, de Active Directory Authentication Library (ADAL) of adal.js. Het enige doel van adal.js is het ophalen van toegangstokens voor uw app gemakkelijker te maken.

In deze snelstart leert u hoe een AngularJS- takenlijsttoepassing te compileren die:

* De gebruiker aanmeldt bij de app met behulp van Azure AD als id-provider.
* Meer informatie weergeeft over de gebruiker.
* Veilig de takenlijst-API van de app aanroept met behulp van Azure AD-bearer-tokens.
* De gebruiker afmeldt voor de app.

Om de volledige, werkende toepassing te compileren, moet u het volgende doen:

1. Uw app bij Azure AD registreren.
2. ADAL installeren en van de app met één pagina configureren.
3. Gebruik ADAL voor beveiligde pagina's in de app met één pagina.

## <a name="prerequisites"></a>Vereisten

Voordat u begint, zorgt u dat u aan deze vereisten voldoet:

* [Download het raamwerk van de app](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/skeleton.zip) of [download het voltooide voorbeeld](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip).
* Zorg voor een Azure AD-tenant waarin u gebruikers kunt maken en een toepassing kunt registreren. [Lees hier hoe u een tenant kunt verkrijgen](quickstart-create-new-tenant.md) als u er nog geen hebt.

## <a name="step-1-register-the-directorysearcher-application"></a>Stap 1: de toepassing DirectorySearcher registreren

Als u wilt inschakelen voor de app om gebruikers te verifiëren en tokens te verkrijgen, moet u deze eerst registreren in uw Azure AD-tenant:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Als u bent aangemeld bij meerdere mappen, moet u mogelijk controleren of dat u de juiste map bekijkt. Klik op uw account op de bovenste balk om dit te doen. Kies de Azure AD-tenant waar u uw toepassing wilt registreren onder de lijst **Directory**.
1. Selecteer **Alle services** in het linkerdeelvenster en selecteer vervolgens **Azure Active Directory**.
1. Klik op **app-registraties**en selecteer vervolgens **nieuwe registratie**.
1. Wanneer de pagina **Een toepassing registreren** wordt weergegeven, voert u een naam in voor de toepassing.
1. Selecteer onder **Ondersteunde accounttypen** de optie **Accounts in een organisatieadreslijst en persoonlijke Microsoft-account**.
1. Selecteer het **webplatform onder de sectie** **omleidings-URI** en stel de waarde in op `https://localhost:44326/` (de locatie waarnaar Azure AD tokens retourneert).
1. Selecteer **Registreren** wanneer u klaar bent. Noteer de waarde **Toepassings-id (client)** op de app-pagina **Overzicht**.
1. Adal.js gebruikt de impliciete flow OAuth om te communiceren met Azure AD. U moet de impliciete stroom voor uw toepassing inschakelen. Selecteer in het deelvenster aan de linkerkant van de geregistreerde toepassing de optie **Verificatie**.
1. Schakel in **Geavanceerde instellingen**, onder **Impliciete toekenning**, de selectievakjes **Id-tokens** en **toegangstoken** in. Id-tokens en toegangstokens zijn vereist, omdat via de app gebruikers moeten worden aangemeld en een API moet worden aangeroepen.
1. Selecteer **Opslaan**.
1. Machtigingen verlenen binnen uw tenant voor uw toepassing. Ga naar **API-machtigingen**en selecteer de knop **toestemming geven voor beheerders** onder **toestemming verlenen**.
1. Selecteer **Ja** om te bevestigen.

## <a name="step-2-install-adal-and-configure-the-single-page-app"></a>Stap 2: ADAL installeren en de app met één pagina configureren

Nu u een toepassing hebt in Azure AD, kunt u adal.js installeren en uw identiteit gerelateerde code schrijven.

### <a name="configure-the-javascript-client"></a>De JavaScript-client configureren

Begin met het toevoegen van adal.js aan het project TodoSPA met behulp van de Package Manager-Console:

1. Download [adal.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal.js) en voegen dit toe aan de `App/Scripts/` projectmap.
2. Download [adal-angular.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal-angular.js) en voegen dit toe aan de `App/Scripts/` projectmap.
3. Laad elk script voor het einde van de `</body>` in `index.html`:

    ```js
    ...
    <script src="App/Scripts/adal.js"></script>
    <script src="App/Scripts/adal-angular.js"></script>
    ...
    ```

### <a name="configure-the-back-end-server"></a>De back-endserver configureren

De back-end heeft configuratie-informatie nodig over de app-registratie zodat de back-end takenlijst-API van de app met één pagina tokens van de browser accepteert. Open `web.config` in het project TodoSPA. Vervang de waarden van de elementen in de sectie `<appSettings>` om de waarden die u hebt gebruikt in Azure Portal weer te geven. Uw code verwijst naar deze waarden wanneer deze gebruikmaakt van ADAL.

   * `ida:Tenant` is het domein van uw Azure AD-tenant, bijvoorbeeld contoso.onmicrosoft.com.
   * `ida:Audience` is de client-id van uw toepassing die u hebt gekopieerd uit de portal.

## <a name="step-3-use-adal-to-help-secure-pages-in-the-single-page-app"></a>Stap 3: ADAL gebruiken om pagina's in de app met één pagina te helpen beveiligen

Adal.js integreert met de AngularJS route en HTTP-providers, zodat u afzonderlijke weergaven in uw app met één pagina kunt helpen beveiligen.

1. In `App/Scripts/app.js`, voeg de module adal.js in:

    ```js
    angular.module('todoApp', ['ngRoute','AdalAngular'])
    .config(['$routeProvider','$httpProvider', 'adalAuthenticationServiceProvider',
     function ($routeProvider, $httpProvider, adalProvider) {
    ...
    ```
2. Initialiseer `adalProvider` met behulp van de configuratiewaarden van uw toepassingsregistratie, ook in `App/Scripts/app.js`:

    ```js
    adalProvider.init(
      {
          instance: 'https://login.microsoftonline.com/',
          tenant: 'Enter your tenant name here e.g. contoso.onmicrosoft.com',
          clientId: 'Enter your client ID here e.g. e9a5a8b6-8af7-4719-9821-0deef255f68e',
          extraQueryParameter: 'nux=1',
          //cacheLocation: 'localStorage', // enable this for IE, as sessionStorage does not work for localhost.
      },
      $httpProvider
    );
    ```
3. Beveilig de weergave `TodoList` in de app met behulp van slechts één regel code: `requireADLogin`.

    ```js
    ...
    }).when("/TodoList", {
            controller: "todoListCtrl",
            templateUrl: "/App/Views/TodoList.html",
            requireADLogin: true,
    ...
    ```

## <a name="summary"></a>Samenvatting

U hebt nu een beveiligde app met één pagina die gebruikers kan aanmelden en bearer-token beveiligde aanvragen kan verzenden naar de back-end-API. Wanneer een gebruiker klikt op de link **TodoList**, leidt adal.js automatisch om naar Azure AD voor aanmelding indien nodig. Adal.js voegt ook automatisch een toegangstoken toe aan Ajax-aanvragen die worden verzonden naar de back-end van de app.

De voorgaande stappen zijn het absolute minimum voor het compileren van een app met één pagina met behulp van adal.js. Maar een aantal andere functies zijn nuttig in de app met één pagina:

* U kunt functies in uw domeincontrollers definiëren die adal.js aanroepen om expliciet aanmeldings- en afmeldingsaanvragen te verzenden. In `App/Scripts/homeCtrl.js`:

    ```js
    ...
    $scope.login = function () {
        adalService.login();
    };
    $scope.logout = function () {
        adalService.logOut();
    };
    ...
    ```
* Mogelijk wilt u gebruikersgegevens die aanwezig zijn in de gebruikersinterface van de app voorstellen. De ADAL-service al is toegevoegd aan de `userDataCtrl` -controller, zodat u toegang krijgt tot het `userInfo` -object in de bijbehorende weergave `App/Views/UserData.html`:

    ```js
    <p>{{userInfo.userName}}</p>
    <p>aud:{{userInfo.profile.aud}}</p>
    <p>iss:{{userInfo.profile.iss}}</p>
    ...
    ```

* Er zijn veel scenario's waarin u wilt weten of de gebruiker is aangemeld of niet. U kunt ook het `userInfo` -object gebruiken om deze gegevens te verzamelen. Bijvoorbeeld in `index.html`, kunt u de knop **Aanmelden** of **Afmelden** weergeven op basis van de verificatiestatus:

    ```js
    <li><a class="btn btn-link" ng-show="userInfo.isAuthenticated" ng-click="logout()">Logout</a></li>
    <li><a class="btn btn-link" ng-hide=" userInfo.isAuthenticated" ng-click="login()">Login</a></li>
    ```

Uw Azure AD geïntegreerde app met één pagina kan gebruikers verifiëren, veilig de back-end aanroepen met behulp van OAuth 2.0 en elementaire gebruikersgegevens verkrijgen. Nu kunt u de tenant met gebruikers gaan vullen als u dat nog niet hebt gedaan. Voer uw takenlijst-app met één pagina uit en meld u aan met een van deze gebruikers. Voeg taken toe aan de takenlijst van de gebruiker, meld u zich af en meld u opnieuw aan.

ADAL maakt het opnemen van algemene identiteitsfuncties in uw toepassing eenvoudig. Het neemt alle vervelende klusjes voor zijn rekening, zoals cachebeheer, OAuth-protocolondersteuning, het aanbieden van een gebruikersinterface waarmee de gebruiker zich kan aanmelden, het vernieuwen van verlopen tokens en nog veel meer.

Als naslaginformatie is het volledige voorbeeld (zonder uw configuratiewaarden) beschikbaar op [GitHub](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip).

## <a name="next-steps"></a>Volgende stappen

Nu kunt u verdergaan met aanvullende scenario's.

> [!div class="nextstepaction"]
> [Een CORS-web-API aanroepen vanuit een app met één pagina](https://github.com/AzureAdSamples/SinglePageApp-WebAPI-AngularJS-DotNet).
