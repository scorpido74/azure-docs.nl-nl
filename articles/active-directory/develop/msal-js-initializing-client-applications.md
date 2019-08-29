---
title: Client toepassingen initialiseren (micro soft-verificatie bibliotheek voor Java script) | Azure
description: Meer informatie over het initialiseren van client toepassingen met behulp van de micro soft-verificatie bibliotheek voor Java script (MSAL. js).
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/12/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5d36def4faa98f4b8e42c93cf3e222c2ec7ca89a
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70073847"
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

Voor verificatie methoden met een omleidings `acquireTokenRedirect`stroom (`loginRedirect` en) moet u expliciet een call back voor slagen of error registreren `handleRedirectCallback()` via methode. Dit is nodig omdat omleidings stromen geen belofte retour neren als de methoden met een pop-upervaring.

```javascript
// Configuration object constructed
const config = {
    auth: {
        clientId: “abcd-ef12-gh34-ikkl-ashdjhlhsdg”
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

MSAL. js is ontworpen voor een enkele instantie en configuratie van de `UserAgentApplication` om één verificatie context aan te duiden. Meerdere exemplaren worden niet aanbevolen omdat deze conflicterende cache vermeldingen en gedrag veroorzaken in de browser.

## <a name="configuration-options"></a>Configuratie-opties

MSAL. js bevat een configuratie object dat hieronder wordt weer gegeven, met een groepering van Configureer bare opties die beschikbaar zijn `UserAgentApplication`voor het maken van een instantie van.

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

- **clientID**: Vereist. De clientID van uw toepassing, moet u dit doen via de portal voor toepassings registratie.

- **instantie**: Optioneel. Een URL die een Directory aanduidt waar MSAL tokens kan aanvragen. De standaard waarde is `https://login.microsoftonline.com/common`:.
    * In azure AD bevindt het zich in het&lt;formulier&gt;https://&gt;instantie&gt; /&lt;publiek &lt;, waarbij instance het ID-provider domein is ( `https://login.microsoftonline.com`bijvoorbeeld) en doelgroep&gt; is een id die de doel groep van het aanmelden vertegenwoordigt. &lt; Dit kunnen de volgende waarden zijn:
        * `https://login.microsoftonline.com/<tenant>`-Tenant is een domein dat is gekoppeld aan de Tenant, zoals contoso.onmicrosoft.com, of de GUID die `TenantID` de eigenschap van de map vertegenwoordigt die alleen wordt gebruikt voor het aanmelden van gebruikers van een specifieke organisatie.
        * `https://login.microsoftonline.com/common`-Wordt gebruikt voor het aanmelden van gebruikers met werk-en school accounts of een persoonlijk micro soft-account.
        * `https://login.microsoftonline.com/organizations/`-Wordt gebruikt voor het aanmelden van gebruikers met werk-en school accounts.
        * `https://login.microsoftonline.com/consumers/`-Wordt gebruikt voor het aanmelden van gebruikers met alleen persoonlijke Microsoft-account (Live).
    * In azure AD B2C is dit het formulier `https://<instance>/tfp/<tenant>/<policyName>/`, waarbij instance het Azure AD B2C domein is, Tenant de naam van de Azure AD B2C Tenant is, is de naam van het B2C-beleid dat moet worden toegepast.


- **validateAuthority**: Optioneel.  Valideer de verlener van tokens. De standaardwaarde is `true`. Voor B2C-toepassingen, omdat de waarde van de autoriteit bekend is en per beleid kan verschillen, werkt de verificatie van de certificerings instantie niet en `false`moet deze worden ingesteld op.

- **redirectUri**: Optioneel.  De omleidings-URI van uw app, waar verificatie reacties kunnen worden verzonden en ontvangen door uw app. Het moet exact overeenkomen met een van de omleidings-Uri's die u in de portal hebt geregistreerd. Standaard ingesteld op `window.location.href`.

- **postLogoutRedirectUri**: Optioneel.  Leidt de gebruiker om `postLogoutRedirectUri` na het afmelden. De standaardwaarde is `redirectUri`.

- **navigateToLoginRequestUrl**: Optioneel. De mogelijkheid om de standaard navigatie voor het starten van de pagina na de aanmelding uit te scha kelen. De standaard waarde is True. Dit wordt alleen gebruikt voor omleidings stromen.

- **cacheLocation**: Optioneel.  Hiermee wordt de browser opslag `localStorage` ingesteld `sessionStorage`op ofwel of. De standaardwaarde is `sessionStorage`.

- **storeAuthStateInCookie**: Optioneel.  Deze markering is geïntroduceerd in MSAL. js v 0.2.2 als een oplossing voor de [problemen](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#1-issues-due-to-security-zones) met de verificatie-lus in micro soft Internet Explorer en micro soft Edge. Schakel de vlag `storeAuthStateInCookie` in op True om te profiteren van deze oplossing. Wanneer dit is ingeschakeld, slaat MSAL. js de status van de verificatie aanvraag op die vereist is voor de validatie van de verificatie stromen in de browser cookies. Deze markering is standaard ingesteld op `false`.

- **logger**: Optioneel.  Een logger-object met een call back-exemplaar dat kan worden geleverd door de ontwikkelaar om logboeken op een aangepaste manier te gebruiken en publiceren. Zie [logboek registratie met msal. js](msal-logging.md)voor meer informatie over het door geven van het traceer object.

- **loadFrameTimeout**: Optioneel.  Het aantal milliseconden van inactiviteit voordat een reactie van het vernieuwen van tokens van Azure AD moet worden beschouwd als een time-out. De standaard waarde is zes seconden.

- **tokenRenewalOffsetSeconds**: Optioneel. Het aantal milliseconden waarmee het vernieuwings venster wordt ingesteld voor het vernieuwen van het token vóór de verval datum. De standaard waarde is 300 milliseconden.

Deze zijn alleen van toepassing op door gegeven in de MSAL-hoek wrapper-bibliotheek:
- **unprotectedResources**: Optioneel.  Matrix van Uri's die niet-beveiligde resources zijn. MSAL koppelt geen token aan uitgaande aanvragen die deze URI hebben. Standaard ingesteld op `null`.

- **protectedResourceMap**: Optioneel.  Dit is een toewijzing van resources aan scopes die worden gebruikt door MSAL voor het automatisch koppelen van toegangs tokens in Web API-aanroepen. Er wordt één toegangs token opgehaald voor de resource. U kunt bijvoorbeeld als volgt een specifiek bronpad toewijzen: {"https://graph.microsoft.com/v1.0/me", ["gebruiker. Read"]}, of de app-URL van de resource als: {"https://graph.microsoft.com/", ["gebruiker. lezen", "mail. Send"]}. Dit is vereist voor CORS-aanroepen. Standaard ingesteld op `null`.
