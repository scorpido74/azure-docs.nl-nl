---
title: MSAL.JS-client-apps initialiseren | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het initialiseren van clienttoepassingen met behulp van de Microsoft-verificatiebibliotheek voor JavaScript (MSAL.js).
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
ms.openlocfilehash: fbd700c787a844fa7538ed198f76ed5c06af2c28
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010151"
---
# <a name="initialize-client-applications-using-msaljs"></a>Clienttoepassingen initialiseren met MSAL.js
In dit artikel wordt beschreven dat microsoft-verificatiebibliotheek voor JavaScript (MSAL.js) wordt geïnitialiseren met een instantie van een gebruikersagenttoepassing. De user-agent applicatie is een vorm van openbare client applicatie waarin de client code wordt uitgevoerd in een user-agent, zoals een webbrowser. Deze clients slaan geen geheimen op, omdat de browsercontext openlijk toegankelijk is. Lees het [overzicht](msal-client-applications.md)voor meer informatie over de typen clienttoepassingen en toepassingsconfiguratie.

## <a name="prerequisites"></a>Vereisten
Voordat u een toepassing initialiseert, moet u deze eerst [registreren bij de Azure-portal,](scenario-spa-app-registration.md) zodat uw app kan worden geïntegreerd met het Microsoft-identiteitsplatform. Na registratie hebt u mogelijk de volgende informatie nodig (die te vinden is in de Azure-portal):

- De client-id (een tekenreeks die een GUID voor uw toepassing vertegenwoordigt)
- De URL van de identiteitsprovider (de naam van de instantie) en de aanmeldingsdoelgroep voor uw toepassing. Deze twee parameters staan gezamenlijk bekend als de autoriteit.
- De tenant-id als u een line-of-business-toepassing schrijft die uitsluitend voor uw organisatie is ingediend (ook wel single-tenant-toepassing genoemd).
- Voor web-apps moet u ook de redirectUri instellen waar de identiteitsprovider terugkeert naar uw toepassing met de beveiligingstokens.

## <a name="initializing-applications"></a>Aanvragen initialiseren

U MSAL.js als volgt gebruiken in een eenvoudige JavaScript/Typescript-toepassing. De context van MSAL-verificatie initialiseren door te instantiën `UserAgentApplication` met een configuratieobject. De minimaal vereiste config om MSAL.js te initialiseren is de clientID van uw applicatie die u moet krijgen van de applicatie registratie portal.

Voor verificatiemethoden met omleidingsstromen (en)`loginRedirect` `acquireTokenRedirect`in MSAL.js 1.2.x of eerder moet u een callback expliciet registreren voor succes of fout via `handleRedirectCallback()` de methode. Dit is nodig omdat omleidingsstromen geen beloftes retourneren zoals de methoden met een pop-upervaring dat doen. Dit werd optioneel in MSAL.js versie 1.3.0.

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

MSAL.js is ontworpen om één exemplaar `UserAgentApplication` en configuratie van de context te hebben om één verificatiecontext weer te geven. Meerdere exemplaren worden niet aanbevolen omdat ze conflicterende cachevermeldingen en -gedrag in de browser veroorzaken.

## <a name="configuration-options"></a>Configuratie-opties

MSAL.js heeft hieronder een configuratieobject dat een groepering van configureerbare `UserAgentApplication`opties biedt die beschikbaar zijn voor het maken van een instantie van .

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

Hieronder vindt u de totale set configureerbare opties die momenteel worden ondersteund in het config-object:

- **clientID**: Vereist. De clientID van uw aanvraag, moet u dit krijgen van de applicatie registratie portal.

- **autoriteit**: Facultatief. Een URL die een map aangeeft waar MSAL tokens van kan aanvragen. Standaardwaarde is: `https://login.microsoftonline.com/common`.
    * In Azure AD is dit&lt;van&gt;/&lt;&gt;het &lt;formulier&gt; https:// instantiedoelgroep , waarbij `https://login.microsoftonline.com`instantie &lt;&gt; het domein van de identiteitsprovider is (bijvoorbeeld ) en de doelgroep een id is die de aanmeldingsdoelgroep vertegenwoordigt. Dit kunnen de volgende waarden zijn:
        * `https://login.microsoftonline.com/<tenant>`- tenant is een domein dat is gekoppeld aan de `TenantID` tenant, zoals contoso.onmicrosoft.com of de GUID die de eigenschap van de map vertegenwoordigt die alleen wordt gebruikt om gebruikers van een specifieke organisatie aan te melden.
        * `https://login.microsoftonline.com/common`- Wordt gebruikt om gebruikers aan te melden met werk- en schoolaccounts of een persoonlijk Microsoft-account.
        * `https://login.microsoftonline.com/organizations/`- Wordt gebruikt om gebruikers aan te melden met werk- en schoolaccounts.
        * `https://login.microsoftonline.com/consumers/`- Wordt gebruikt om gebruikers aan te melden met alleen een persoonlijk Microsoft-account (live).
    * In Azure AD B2C is `https://<instance>/tfp/<tenant>/<policyName>/`het de naam van de Azure AD B2C-tenant , waarbij instantie het Azure AD B2C-domein is, d.w.z. {your-tenant-name}.b2clogin.com, tenant is de naam van de Azure AD B2C-tenant, d.w.z. {your-tenant-name}.onmicrosoft.com, policyName is de naam van het B2C-beleid dat moet worden toegepast.


- **validateAuthority**: Optioneel.  Valideer de uitgever van tokens. De standaardwaarde is `true`. Voor B2C-toepassingen, aangezien de waarde van de autoriteit bekend is en per beleid `false`kan verschillen, werkt de validatie van de autoriteit niet en moet deze worden ingesteld op .

- **redirectUri**: Optioneel.  De omleiding uri van uw app, waar authenticatie reacties kunnen worden verzonden en ontvangen door uw app. Het moet precies overeenkomen met een van de omleiding URI's die u geregistreerd in de portal. Standaard instellingen `window.location.href`voor .

- **postLogoutRedirectUri:** Optioneel.  Hiermee wordt de `postLogoutRedirectUri` gebruiker omgeleid naar na afmelding. De standaardinstelling is `redirectUri`.

- **navigateToLoginRequestUrl**: Optioneel. Mogelijkheid om de standaardnavigatie uit te schakelen naar de startpagina na het inloggen. De standaardwaarde is true. Dit wordt alleen gebruikt voor omleidingsstromen.

- **cacheLocatie:** optioneel.  Hiermee stelt u `localStorage` `sessionStorage`browseropslag in op een van beide of . De standaardwaarde is `sessionStorage`.

- **storeAuthStateInCookie**: Optioneel.  Deze vlag is geïntroduceerd in MSAL.js v0.2.2 als oplossing voor de problemen met de [verificatielus](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#1-issues-due-to-security-zones) op Microsoft Internet Explorer en Microsoft Edge. Schakel de `storeAuthStateInCookie` vlag in om te profiteren van deze oplossing. Wanneer dit is ingeschakeld, slaat MSAL.js de auth-aanvraagstatus op die nodig is voor validatie van de auth-stromen in de browsercookies. Standaard is deze vlag `false`ingesteld op .

- **logger**: Optioneel.  Een Logger-object met een callback-instantie die door de ontwikkelaar kan worden geleverd om logboeken op een aangepaste manier te gebruiken en te publiceren. Zie [logboekregistratie met msal.js](msal-logging.md)voor meer informatie over het doorgeven van loggerobject.

- **loadFrameTimeout:** optioneel.  Het aantal milliseconden van inactiviteit voordat een tokenvernieuwingsreactie van Azure AD wordt beschouwd, moet als een time-out worden beschouwd. Standaard is 6 seconden.

- **tokenRenewalOffsetSeconds:** optioneel. Het aantal milliseconden dat het venster van de verschuiving instelt dat nodig is om het token te vernieuwen vóór het verstrijken. Standaard is 300 milliseconden.

- **navigateFrameWait:** optioneel. Het aantal milliseconden dat de wachttijd instelt voordat verborgen iframes naar hun bestemming navigeren. Standaard is 500 milliseconden.

Deze zijn alleen van toepassing om te worden doorgegeven van de MSAL Angular wrapper bibliotheek:
- **unprotectedResources**: Optioneel.  Array van URI's die onbeschermde bronnen zijn. MSAL zal geen token koppelen aan uitgaande aanvragen die deze URI hebben. Standaard instellingen `null`voor .

- **protectedResourceMap:** optioneel.  Dit is het toewijzen van resources aan scopes die door MSAL worden gebruikt voor het automatisch koppelen van toegangstokens in web-API-aanroepen. Voor de resource wordt één toegangstoken verkregen. U dus een specifiek bronpadhttps://graph.microsoft.com/v1.0/meals volgt toewijzen: {, [user.read]},https://graph.microsoft.com/of de app-URL van de bron als: {, [user.read, mail.send]}. Dit is vereist voor CORS-gesprekken. Standaard instellingen `null`voor .
