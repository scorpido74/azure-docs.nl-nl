---
title: Verschillen tussen MSAL.js en ADAL.js | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over de verschillen tussen Microsoft Authentication Library voor JavaScript (MSAL.js) en Azure AD Authentication Library for JavaScript (ADAL.js) en hoe u kiezen welke te gebruiken.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/10/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 7238a78279528b4522d09178d00bf916f14bad88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696415"
---
# <a name="differences-between-msal-js-and-adal-js"></a>Verschillen tussen MSAL JS en ADAL JS

Zowel de Microsoft-verificatiebibliotheek voor JavaScript (MSAL.js) als de Azure AD-verificatiebibliotheek voor JavaScript (ADAL.js) worden gebruikt om Azure AD-entiteiten te verifiëren en tokens van Azure AD aan te vragen. Tot nu toe hebben de meeste ontwikkelaars samengewerkt met Azure AD voor ontwikkelaars (v1.0) om Azure AD-identiteiten (werk- en schoolaccounts) te verifiëren door tokens aan te vragen met ADAL. Nu u met MSAL.js een bredere set Microsoft-identiteiten (Azure AD-identiteiten en Microsoft-accounts en sociale en lokale accounts verifiëren via Azure AD B2C) via het Microsoft-identiteitsplatform (v2.0).

In dit artikel wordt beschreven hoe u kiezen tussen de Microsoft-verificatiebibliotheek voor JavaScript (MSAL.js) en azure AD-verificatiebibliotheek voor JavaScript (ADAL.js) en de twee bibliotheken.

## <a name="choosing-between-adaljs-and-msaljs"></a>Kiezen tussen ADAL.js en MSAL.js

In de meeste gevallen wilt u het Microsoft-identiteitsplatform en MSAL.js gebruiken, de nieuwste generatie Microsoft-verificatiebibliotheken. Met MSAL.js verwerft u tokens voor gebruikers die zich aanmelden bij uw toepassing met Azure AD (werk- en schoolaccounts), Microsoft (personal) accounts (MSA) of Azure AD B2C.

Als u al bekend bent met het v1.0-eindpunt (en ADAL.js), wilt u misschien lezen [Wat is er anders aan het v2.0-eindpunt?](active-directory-v2-compare.md).

U moet ADAL.js echter nog steeds gebruiken als uw toepassing zich moet aanmelden bij gebruikers met eerdere versies van [ADFS (Active Directory Federation Services).](/windows-server/identity/active-directory-federation-services)

## <a name="key-differences-in-authentication-with-msaljs"></a>Belangrijkste verschillen in verificatie met MSAL.js

### <a name="core-api"></a>Core API

* ADAL.js gebruikt [AuthenticationContext](https://github.com/AzureAD/azure-activedirectory-library-for-js/wiki/Config-authentication-context#authenticationcontext) als weergave van een instantie van de verbinding van uw toepassing met de autorisatieserver of identiteitsprovider via een url van de autoriteit. MsAL.js API is daarentegen ontworpen rond de clienttoepassing van gebruikersagenten (een vorm van openbare clienttoepassing waarin de clientcode wordt uitgevoerd in een user-agent zoals een webbrowser). Het biedt `UserAgentApplication` de klasse om een instantie van de verificatiecontext van de toepassing te vertegenwoordigen met de autorisatieserver. Zie [Initialiseren met MSAL.js](msal-js-initializing-client-applications.md)voor meer informatie.

* In ADAL.js worden de methoden voor het verkrijgen van `AuthenticationContext`tokens gekoppeld aan één autoriteit die is ingesteld in de . In MSAL.js kunnen de tokenaanvragen voor verwerven andere autoriteitswaarden aannemen dan wat is ingesteld in de `UserAgentApplication`. Hierdoor kan MSAL.js tokens afzonderlijk aanschaffen en opslaan voor meerdere tenants en gebruikersaccounts in dezelfde toepassing.

* De methode om tokens in stilte te `acquireToken` verkrijgen en te vernieuwen zonder gebruikers te vragen wordt genoemd in ADAL.js. In MSAL.js wordt deze `acquireTokenSilent` methode genoemd als meer beschrijvend voor deze functionaliteit.

### <a name="authority-value-common"></a>Waarde van de autoriteit`common`

In v1.0 kunnen `https://login.microsoftonline.com/common` gebruikers zich met de autoriteit aanmelden met een Azure AD-account (voor elke organisatie).

In v2.0, `https://login.microsoftonline.com/common` met behulp van de autoriteit, kunnen gebruikers zich aanmelden met een Azure AD-organisatieaccount of een Microsoft-persoonlijk account (MSA). Als u de aanmelding wilt beperken tot alleen Azure AD-accounts (hetzelfde gedrag als bij ADAL.js), moet u . `https://login.microsoftonline.com/organizations` Zie voor meer `authority` informatie de optie config in [Initialiseren met MSAL.js](msal-js-initializing-client-applications.md).

### <a name="scopes-for-acquiring-tokens"></a>Scopes voor het verwerven van tokens
* Scope in plaats van resourceparameter in verificatieaanvragen om tokens te verkrijgen

    v2.0-protocol gebruikt scopes in plaats van resource in de aanvragen. Met andere woorden, wanneer uw toepassing tokens moet aanvragen met machtigingen voor een bron zoals MS Graph, is het verschil in waarden dat wordt doorgegeven aan de bibliotheekmethoden als volgt:

    v1.0: resource\:= https -graph.microsoft.com

    v2.0: scope\:= https -graph.microsoft.com/User.Read

    U scopes voor elke resource-API aanvragen met de URI van de API\/in deze indeling: appidURI/scope Bijvoorbeeld: https: /mytenant.onmicrosoft.com/myapi/api.read

    Alleen voor de MS Graph `user.read` API wordt\/een scopewaarde toegewezen aan https: /graph.microsoft.com/User.Read en kan door elkaar worden gebruikt.

    ```javascript
    var request = {
        scopes = ["User.Read"];
    };

    acquireTokenPopup(request);   
    ```

* Dynamische scopes voor incrementele toestemming.

    Bij het bouwen van toepassingen met behulp van v1.0, moest u de volledige set van machtigingen (statische scopes) vereist door de toepassing voor de gebruiker om toestemming te geven aan op het moment van inloggen registreren. In v2.0 u de bereikparameter gebruiken om de machtigingen aan te vragen op het moment dat u ze wilt. Dit worden dynamische scopes genoemd. Hierdoor kan de gebruiker incrementele toestemming geven voor scopes. Dus als je in het begin gewoon wilt dat de gebruiker zich aanmeldt bij je applicatie en je hebt geen toegang nodig, dan kun je dat doen. Als u later de mogelijkheid nodig hebt om de agenda van de gebruiker te lezen, u vervolgens het agendabereik in de acquireToken-methoden aanvragen en de toestemming van de gebruiker verkrijgen. Bijvoorbeeld:

    ```javascript
    var request = {
        scopes = ["https://graph.microsoft.com/User.Read", "https://graph.microsoft.com/Calendar.Read"];
    };

    acquireTokenPopup(request);   
    ```

* Scopes voor V1.0 API's

    Wanneer u tokens voor V1.0-API's krijgt met MSAL.js, u `.default` alle statische scopes aanvragen die op de API zijn geregistreerd door toe te nemen aan de App ID URI van de API als scope. Bijvoorbeeld:

    ```javascript
    var request = {
        scopes = [ appidURI + "/.default"];
    };

    acquireTokenPopup(request);
    ```

## <a name="next-steps"></a>Volgende stappen
Zie voor meer informatie [v1.0 en v2.0-vergelijking](active-directory-v2-compare.md).
