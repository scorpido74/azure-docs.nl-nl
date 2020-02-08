---
title: MSAL. js-client-apps initialiseren | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het initialiseren van client toepassingen met behulp van de micro soft-verificatie bibliotheek voor Java script (MSAL. js).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/12/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: a0a2c5fc971c3f1f3283d95c5617bdf1e88a6a58
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/08/2020
ms.locfileid: "77084038"
---
# <a name="initialize-client-applications-using-msaljs"></a>Client toepassingen initialiseren met MSAL. js
In dit artikel wordt beschreven hoe u de micro soft-verificatie bibliotheek voor Java script (MSAL. js) initialiseert met een exemplaar van een toepassing van een gebruikers agent. De toepassing van de gebruikers agent is een vorm van een open bare client toepassing waarin de client code wordt uitgevoerd in een gebruikers agent, zoals een webbrowser. Deze clients slaan geen geheimen op omdat de browser context geopend is. Lees het [overzicht](msal-client-applications.md)voor meer informatie over de client toepassings typen en configuratie opties voor toepassingen.

## <a name="prerequisites"></a>Vereisten
Voordat u een toepassing initialiseert, moet u [deze eerst registreren bij de Azure Portal](scenario-spa-app-registration.md) zodat uw app kan worden geïntegreerd met het micro soft Identity-platform. Na de registratie hebt u mogelijk de volgende informatie nodig (die kan worden gevonden in de Azure Portal):

- De client-ID (een teken reeks die een GUID voor uw toepassing vertegenwoordigt)
- De URL van de identiteits provider (de naam van het exemplaar) en de aanmeldings doel groep voor uw toepassing. Deze twee para meters worden gezamenlijk bekend als de-instantie.
- De Tenant-ID als u alleen een line-of-Business-toepassing schrijft voor uw organisatie (ook wel een toepassing met één Tenant genoemd).
- Voor web-apps moet u ook de redirectUri instellen waar de ID-provider naar uw toepassing wordt teruggestuurd met de beveiligings tokens.

## <a name="initializing-applications"></a>Toepassingen initialiseren

U kunt MSAL. js als volgt gebruiken in een gewone Java script/type script-toepassing. Initialiseer de MSAL-verificatie context door `UserAgentApplication` te instantiëren met een configuratie object. De mini maal vereiste configuratie voor het initialiseren van MSAL. js is de clientID van uw toepassing, die u vanuit de portal voor toepassings registratie zou moeten ontvangen.

Voor verificatie methoden met een omleidings stroom (`loginRedirect` en `acquireTokenRedirect`), moet u expliciet een call back voor geslaagde of fout registratie via `handleRedirectCallback()`-methode registreren. Dit is nodig omdat omleidings stromen geen belofte retour neren als de methoden met een pop-upervaring.

```javascript
// Configuration object constructed
const config = {
    auth: {
        clientId: "abcd-ef12-gh34-ikkl-ashdjhlhsdg"
    }
}

// create UserAgentApplication instance
const myMSALObj = new UserAgentApplication(config);

function authCallback(error, response) {
    //handle redirect response
}

// (optional when using redirect methods) register redirect call back for Success or Error
myMSALObj.handleRedirectCallback(authCallback);
```

MSAL. js is ontworpen voor een enkele instantie en configuratie van de `UserAgentApplication` om een enkele verificatie context aan te duiden. Meerdere exemplaren worden niet aanbevolen omdat deze conflicterende cache vermeldingen en gedrag veroorzaken in de browser.

## <a name="configuration-options"></a>Configuratie-opties

MSAL. js bevat een configuratie object dat hieronder wordt weer gegeven, met een groepering van Configureer bare opties die beschikbaar zijn voor het maken van een instantie van `UserAgentApplication`.

```javascript
type storage = "localStorage" | "sessionStorage";

// Protocol Support
export type AuthOptions = {
    clientId: string;
    authority?: string;
    validateAuthority?: boolean;
    redirectUri?: string | (() => string);
    postLogoutRedirectUri?: string | (() => string);
    navigateToLoginRequestUrl?: boolean;
};

// Cache Support
export type CacheOptions = {
    cacheLocation?: CacheLocation;
    storeAuthStateInCookie?: boolean;
};

// Library support
export type SystemOptions = {
    logger?: Logger;
    loadFrameTimeout?: number;
    tokenRenewalOffsetSeconds?: number;
    navigateFrameWait?: number;
};

// Developer App Environment Support
export type FrameworkOptions = {
    isAngular?: boolean;
    unprotectedResources?: Array<string>;
    protectedResourceMap?: Map<string, Array<string>>;
};

// Configuration Object
export type Configuration = {
    auth: AuthOptions,
    cache?: CacheOptions,
    system?: SystemOptions,
    framework?: FrameworkOptions
};
```

Hieronder ziet u de totale set Configureer bare opties die momenteel worden ondersteund in het configuratie object:

- **clientID**: vereist. De clientID van uw toepassing, moet u dit doen via de portal voor toepassings registratie.

- **instantie**: optioneel. Een URL die een Directory aanduidt waar MSAL tokens kan aanvragen. De standaard waarde is: `https://login.microsoftonline.com/common`.
    * In azure AD heeft het de vorm https://&lt;instantie&gt;/&lt;publiek&gt;, waarbij &lt;instantie&gt; het domein van de identiteits provider is (bijvoorbeeld `https://login.microsoftonline.com`) en &lt;doel groep&gt; een id is die de doel groep voor het aanmelden vertegenwoordigt. Dit kunnen de volgende waarden zijn:
        * `https://login.microsoftonline.com/<tenant>`-Tenant is een domein dat is gekoppeld aan de Tenant, zoals contoso.onmicrosoft.com, of de GUID die de eigenschap `TenantID` vertegenwoordigt van de map die alleen wordt gebruikt om gebruikers van een specifieke organisatie aan te melden.
        * `https://login.microsoftonline.com/common`: wordt gebruikt voor het aanmelden van gebruikers met werk-en school accounts of een persoonlijk micro soft-account.
        * `https://login.microsoftonline.com/organizations/`: wordt gebruikt voor het aanmelden van gebruikers met werk-en school accounts.
        * `https://login.microsoftonline.com/consumers/`: wordt gebruikt voor het aanmelden van gebruikers met alleen persoonlijke Microsoft-account (Live).
    * In Azure AD B2C heeft het de vorm `https://<instance>/tfp/<tenant>/<policyName>/`, waarbij instance het Azure AD B2C domein is, d.w.z. {uw-Tenant naam}. b2clogin. com. de Tenant is de naam van de Azure AD B2C Tenant, d.w.z. {uw-Tenant naam}. onmicrosoft. com, beleidsregel is de naam van het B2C-beleid dat moet worden toegepast.


- **validateAuthority**: optioneel.  Valideer de verlener van tokens. De standaardwaarde is `true`. Voor B2C-toepassingen, omdat de waarde van de autoriteit bekend is en per beleid kan verschillen, werkt de verificatie van de certificerings instantie niet en moet deze worden ingesteld op `false`.

- **redirectUri**: optioneel.  De omleidings-URI van uw app, waar verificatie reacties kunnen worden verzonden en ontvangen door uw app. Het moet exact overeenkomen met een van de omleidings-Uri's die u in de portal hebt geregistreerd. De standaard instelling is `window.location.href`.

- **postLogoutRedirectUri**: optioneel.  De gebruiker wordt omgeleid naar `postLogoutRedirectUri` na het afmelden. De standaard waarde is `redirectUri`.

- **navigateToLoginRequestUrl**: optioneel. De mogelijkheid om de standaard navigatie voor het starten van de pagina na de aanmelding uit te scha kelen. De standaard waarde is True. Dit wordt alleen gebruikt voor omleidings stromen.

- **cacheLocation**: optioneel.  Hiermee stelt u de browser opslag in op `localStorage` of `sessionStorage`. De standaardwaarde is `sessionStorage`.

- **storeAuthStateInCookie**: optioneel.  Deze markering is geïntroduceerd in MSAL. js v 0.2.2 als een oplossing voor de [problemen](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#1-issues-due-to-security-zones) met de verificatie-lus in micro soft Internet Explorer en micro soft Edge. Schakel de vlag `storeAuthStateInCookie` in op waar om te profiteren van deze oplossing. Wanneer dit is ingeschakeld, slaat MSAL. js de status van de verificatie aanvraag op die vereist is voor de validatie van de verificatie stromen in de browser cookies. Deze markering is standaard ingesteld op `false`.

- **logger**: optioneel.  Een logger-object met een call back-exemplaar dat kan worden geleverd door de ontwikkelaar om logboeken op een aangepaste manier te gebruiken en publiceren. Zie [logboek registratie met msal. js](msal-logging.md)voor meer informatie over het door geven van het traceer object.

- **loadFrameTimeout**: optioneel.  Het aantal milliseconden van inactiviteit voordat een reactie van het vernieuwen van tokens van Azure AD moet worden beschouwd als een time-out. De standaard waarde is zes seconden.

- **tokenRenewalOffsetSeconds**: optioneel. Het aantal milliseconden waarmee het vernieuwings venster wordt ingesteld voor het vernieuwen van het token vóór de verval datum. De standaard waarde is 300 milliseconden.

- **navigateFrameWait**: optioneel. Het aantal milliseconden waarmee de wacht tijd wordt ingesteld voordat verborgen iframes naar het doel worden genavigeerd. De standaard waarde is 500 milliseconden.

Deze zijn alleen van toepassing op door gegeven in de MSAL-hoek wrapper-bibliotheek:
- **unprotectedResources**: optioneel.  Matrix van Uri's die niet-beveiligde resources zijn. MSAL koppelt geen token aan uitgaande aanvragen die deze URI hebben. De standaard instelling is `null`.

- **protectedResourceMap**: optioneel.  Dit is een toewijzing van resources aan scopes die worden gebruikt door MSAL voor het automatisch koppelen van toegangs tokens in Web API-aanroepen. Er wordt één toegangs token opgehaald voor de resource. U kunt bijvoorbeeld als volgt een specifiek bronpad toewijzen: {"https://graph.microsoft.com/v1.0/me", ["gebruiker. Read"]}, of de app-URL van de resource als: {"https://graph.microsoft.com/", ["gebruiker. Read", "mail. Send"]}. Dit is vereist voor CORS-aanroepen. De standaard instelling is `null`.
