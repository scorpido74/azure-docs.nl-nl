---
title: OIDC-aanmelding toevoegen aan een Node.js-web-app - Microsoft-identiteitsplatform | Azure
description: Meer informatie over het implementeren van verificatie in een Node.js-webtoepassing met OpenID Connect.
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
ms.openlocfilehash: 4aa0cce83f9adc8c648656899ec6dc12d498e26b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77160445"
---
# <a name="quickstart-add-sign-in-using-openid-connect-to-a-nodejs-web-app"></a>Snelstart: aanmelden toevoegen met OpenID Connect naar een Web App node.js

In deze quickstart leert u hoe u OpenID Connect-verificatie instelt in een webtoepassing die is gebouwd met Node.js met Express. Het monster is ontworpen om op elk platform te draaien.

## <a name="prerequisites"></a>Vereisten

Als u dit voorbeeld wilt uitvoeren, moet u het volgende doen:

* Installeer Node.js vanhttp://nodejs.org/

* Een [Microsoft-account](https://www.outlook.com) of [een Office 365 Developer Program](/office/developer-program/office-365-developer-program)

## <a name="register-your-application"></a>Uw toepassing registreren 
1. Meld u aan bij de [Azure-portal](https://portal.azure.com/) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Als uw account aanwezig is in meer dan één Azure AD-tenant:
    - Selecteer uw profiel in het menu rechtsboven op de pagina en **schakel de map over.**
    - Wijzig uw sessie in de Azure AD-tenant waar u uw toepassing wilt maken.

1. Navigeer naar [Azure Active Directory >-app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) om uw app te registreren.

1. Selecteer **Nieuwe registratie selecteren.**

1. Wanneer de pagina **Een aanvraag registreren** wordt weergegeven, voert u de registratiegegevens van uw app in:
    - Voer in de sectie **Naam** een betekenisvolle naam in die wordt weergegeven aan gebruikers van de app. Bijvoorbeeld: MyWebApp
    - Selecteer **accounts in een organisatiemap en persoonlijke Microsoft-accounts (bijvoorbeeld Skype, Xbox, Outlook.com) in**de sectie Ondersteunde **accounttypen.**

    Als er meer dan één omleidings-URI's zijn, moet u deze later toevoegen op het tabblad **Verificatie** nadat de app is gemaakt.

1. Selecteer **Registreren** om de app te maken.

1. Zoek op de **pagina Overzicht** van de app de waarde van de **id-toepassing (client)** en leg deze vast voor later. U hebt deze waarde nodig om de toepassing later in dit project te configureren.

1. Selecteer in de lijst met pagina’s voor de app de optie **Verificatie**.
    - Selecteer **web** in het keuzekader voor invoermet invoer en voer in de sectie **URI** omleiden:`http://localhost:3000/auth/openid/return`
    - Bij **Geavanceerde instellingen** stelt u de **afmeldings-URL** in op `http://localhost:3000`.
    - Controleer in de sectie **Geavanceerde instellingen > Impliciete subsidie** **ID-tokens,** omdat voor dit voorbeeld de [impliciete subsidiestroom](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-implicit-grant-flow) moet worden ingeschakeld om de gebruiker aan te melden.

1. Selecteer **Opslaan**.

1. Kies in de sectie **Certificaten & geheimen** in de sectie **Clientgeheimen** de optie **Nieuw clientgeheim**.
    - Voer een sleutelbeschrijving in (bijvoorbeeld app-geheim).
    - Selecteer een sleutelduur van **1 jaar, in 2 jaar** of nooit **verloopt.**
    - Wanneer u op de knop **Toevoegen** klikt, wordt de sleutelwaarde weergegeven. Kopieer de sleutelwaarde en sla deze op een veilige locatie op.

    U hebt deze sleutel later nodig om de toepassing te configureren. Deze sleutelwaarde wordt niet opnieuw weergegeven en wordt op geen enkele andere manier opgehaald, dus neem deze op zodra deze zichtbaar is vanaf de Azure-portal.

## <a name="download-the-sample-application-and-modules"></a>De voorbeeldtoepassing en modules downloaden

Kloon vervolgens de voorbeeldrepo en installeer de NPM-modules.

Vanuit de shell of vanaf de opdrachtregel:

`$ git clone git@github.com:AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git`

of

`$ git clone https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git`

Voer in de hoofdmap van het project de opdracht uit:

`$ npm install`  

## <a name="configure-the-application"></a>De toepassing configureren

Geef de `exports.creds` parameters in config.js op zoals geïnstrueerd.

* Update `<tenant_name>` `exports.identityMetadata` in met de Azure AD-tenantnaam van de indeling \*.onmicrosoft.com.
* Update `exports.clientID` met de toepassings-id die is genoteerd op basis van app-registratie.
* Update `exports.clientSecret` met het geheim van de Toepassing dat van appregistratie wordt genoteerd.
* Update `exports.redirectUrl` met de Redirect URI genoteerd van app registratie.

**Optionele configuratie voor productie-apps:**

* Update `exports.destroySessionUrl` in config.js, als u `post_logout_redirect_uri`een andere wilt gebruiken.

* Stel `exports.useMongoDBSessionStore` in config.js in op true, als u [mongoDB](https://www.mongodb.com) of andere [compatibele sessiewinkels](https://github.com/expressjs/session#compatible-session-stores)wilt gebruiken.
Het standaardsessiearchief in `express-session`dit voorbeeld is . Het standaardsessiearchief is niet geschikt voor productie.

* Update, `exports.databaseUri`als u mongoDB sessie store en een andere database URI wilt gebruiken.

* Update `exports.mongoDBSessionMaxAge`. Hier u opgeven hoe lang u een sessie in mongoDB wilt houden. Het toestel is tweede(en).

## <a name="build-and-run-the-application"></a>De toepassing bouwen en uitvoeren.

Start mongoDB service. Als u mongoDB session store in deze app gebruikt, moet u [mongoDB installeren](http://www.mongodb.org/) en de service eerst starten. Als u het standaardsessiearchief gebruikt, u deze stap overslaan.

Voer de app uit met de volgende opdracht van uw opdrachtregel.

```
$ node app.js
```

**Is de server-uitvoer moeilijk te begrijpen?:** We `bunyan` gebruiken voor het inloggen in dit monster. De console zal niet veel zin om u, tenzij je ook bunyan installeren en voer de server zoals hierboven, maar pijp het door de bunyan binaire:

```
$ npm install -g bunyan

$ node app.js | bunyan
```

### <a name="youre-done"></a>Je bent klaar!

Er wordt een server `http://localhost:3000`uitgevoerd op .

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het scenario voor web-apps dat het Microsoft-identiteitsplatform ondersteunt:
> [!div class="nextstepaction"]
> [Web-app die zich aanmeldt in het scenario van gebruikers](scenario-web-app-sign-user-overview.md)
