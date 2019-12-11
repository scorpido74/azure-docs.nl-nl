---
title: OIDC-aanmelding toevoegen aan een node. js-web-app-micro soft Identity platform | Azure
description: Meer informatie over het implementeren van verificatie in een node. js-webtoepassing met behulp van OpenID Connect Connect.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/28/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.collection: M365-identity-device-management
ms.openlocfilehash: a2869333fe0e9103724b9b1075270a5c3301c472
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74968014"
---
# <a name="quickstart-add-sign-in-using-openid-connect-to-a-nodejs-web-app"></a>Snelstartgids: aanmelden toevoegen met OpenID connect verbinding maken met een node. js-web-app

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

In deze Quick Start leert u hoe u OpenID Connect Connect-verificatie kunt instellen in een webtoepassing die is gebouwd met behulp van node. js met Express. Het voor beeld is ontworpen om te worden uitgevoerd op elk platform.

## <a name="prerequisites"></a>Vereisten

Als u dit voor beeld wilt uitvoeren, hebt u het volgende nodig:

* Node. js installeren vanaf http://nodejs.org/

* Een [Microsoft-account](https://www.outlook.com) -of [Office 365-ontwikkelaars programma](/office/developer-program/office-365-developer-program)

## <a name="register-your-application"></a>Uw toepassing registreren 
1. Meld u bij de [Azure-portal](https://portal.azure.com/) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Als uw account in meer dan één Azure AD-Tenant aanwezig is:
    - Selecteer uw profiel in het menu in de rechter bovenhoek van de pagina en **Schakel vervolgens over naar de map**.
    - Wijzig uw sessie in de Azure AD-Tenant waar u de toepassing wilt maken.

1. Navigeer naar [Azure Active Directory > app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) om uw app te registreren.

1. Selecteer **nieuwe registratie.**

1. Wanneer de pagina **een toepassing registreren** wordt weer gegeven, voert u de registratie gegevens van uw app in:
    - Voer in de sectie **naam** een duidelijke naam in die wordt weer gegeven voor gebruikers van de app. Bijvoorbeeld: MyWebApp
    - Selecteer in de sectie **ondersteunde account typen** de optie **accounts in een organisatorische map en persoonlijke micro soft-accounts (bijvoorbeeld Skype, Xbox, Outlook.com)** .

    Als er meer dan een omleidings-Uri's zijn, moet u deze van het tabblad **verificatie** later toevoegen nadat de app is gemaakt.

1. Selecteer **registreren** om de app te maken.

1. Zoek op de **overzichts** pagina van de app naar de waarde van de **toepassings-id** en noteer deze voor later. U hebt deze waarde nodig om de toepassing later in dit project te configureren.

1. Selecteer in de lijst met pagina’s voor de app de optie **Verificatie**.
    - Selecteer in de sectie **omleidings-uri's** de optie **Web** in de keuze lijst met invoervak en voer de volgende omleidings-URI in: `http://localhost:3000/auth/openid/return`
    - Bij **Geavanceerde instellingen** stelt u de **afmeldings-URL** in op `http://localhost:3000`.
    - Controleer in de sectie **Geavanceerde instellingen > impliciete toekenning** de **id-tokens** als voor dit voor beeld moet de [impliciete toekennings stroom](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-implicit-grant-flow) zijn ingeschakeld om de gebruiker aan te melden.

1. Selecteer **Opslaan**.

1. Kies op de pagina **certificaten & geheimen** in de sectie **client geheimen** de optie **Nieuw client geheim**.
    - Voer een beschrijving in voor de sleutel van het app-geheim.
    - Selecteer een sleutel duur van ofwel **in één jaar, in twee jaar** of **nooit verloopt**.
    - Wanneer u op de knop **toevoegen** klikt, wordt de waarde van de sleutel weer gegeven. Kopieer de sleutel waarde en sla deze op een veilige locatie op.

    U hebt deze sleutel later nodig om de toepassing te configureren. Deze sleutel waarde wordt niet opnieuw weer gegeven en kan niet worden opgehaald op een andere manier, dus noteer deze zo snel als deze wordt weer gegeven in de Azure Portal.

## <a name="download-the-sample-application-and-modules"></a>De voorbeeld toepassing en modules downloaden

Vervolgens kloont u de voor beeld-opslag plaats en installeert u de NPM-modules.

Vanuit de shell of vanaf de opdrachtregel:

`$ git clone git@github.com:AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git`

of

`$ git clone https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git`

Voer de volgende opdracht uit in de hoofdmap van het project:

`$ npm install`  

## <a name="configure-the-application"></a>De toepassing configureren

Geef de para meters in `exports.creds` in config. js op als een instructie.

* Werk `<tenant_name>` in `exports.identityMetadata` bij met de naam van de Azure AD-Tenant van de indeling \*. onmicrosoft.com.
* `exports.clientID` bijwerken met de toepassings-ID die wordt vermeld in de app-registratie.
* `exports.clientSecret` bijwerken met het toepassings geheim dat is vermeld in de app-registratie.
* `exports.redirectUrl` bijwerken met de omleidings-URI die wordt vermeld bij de app-registratie.

**Optionele configuratie voor productie-apps:**

* Werk `exports.destroySessionUrl` in config. js bij als u een andere `post_logout_redirect_uri`wilt gebruiken.

* Stel `exports.useMongoDBSessionStore` in config. js in op True als u [mongoDB](https://www.mongodb.com) of andere [compatibele sessie archieven](https://github.com/expressjs/session#compatible-session-stores)wilt gebruiken.
De standaard sessie opslag in dit voor beeld is `express-session`. De standaard sessie opslag is niet geschikt voor productie.

* Werk `exports.databaseUri`bij als u mongoDB-sessie archief en een andere data base-URI wilt gebruiken.

* Update `exports.mongoDBSessionMaxAge`. Hier kunt u opgeven hoe lang u een sessie in mongoDB wilt blijven gebruiken. De eenheid is seconde (n).

## <a name="build-and-run-the-application"></a>De toepassing bouwen en uitvoeren.

Start de mongoDB-service. Als u mongoDB-sessie archief in deze app gebruikt, moet u [mongoDB installeren](http://www.mongodb.org/) en de service eerst starten. Als u de standaard sessie opslag gebruikt, kunt u deze stap overs Laan.

Voer de app uit met behulp van de volgende opdracht vanaf de opdracht regel.

```
$ node app.js
```

**Is de server uitvoer moeilijk te begrijpen?:** We gebruiken `bunyan` voor het registreren van dit voor beeld. De-console is niet veel handig voor u, tenzij u ook Bunyan installeert en de server uitvoert zoals hierboven, maar pipet via de binaire Bunyan:

```
$ npm install -g bunyan

$ node app.js | bunyan
```

### <a name="youre-done"></a>U bent klaar.

U hebt een server met succes op `http://localhost:3000`.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het scenario van de web-app dat het micro soft Identity-platform ondersteunt:
> [!div class="nextstepaction"]
> [Scenario voor web-app die in gebruikers wordt ondertekend](scenario-web-app-sign-user-overview.md)
