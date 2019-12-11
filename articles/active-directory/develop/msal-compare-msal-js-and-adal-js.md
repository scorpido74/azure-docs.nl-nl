---
title: Verschillen tussen MSAL. js en ADAL. js | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over de verschillen tussen de micro soft-verificatie bibliotheek voor Java script (MSAL. js) en de Azure AD-verificatie bibliotheek voor Java script (ADAL. js) en hoe u kunt kiezen welke u wilt gebruiken.
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: d156ccd4b3f81081433962dcd939a91ab1bc7143
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74963222"
---
# <a name="differences-between-msal-js-and-adal-js"></a>Verschillen tussen MSAL JS en ADAL JS

Zowel micro soft-verificatie bibliotheek voor Java script (MSAL. js) als Azure AD-verificatie bibliotheek voor Java script (ADAL. js) worden gebruikt voor het verifiëren van Azure AD-entiteiten en het aanvragen van tokens van Azure AD. Tot nu toe hebben de meeste ontwikkel aars met Azure AD voor ontwikkel aars (v 1.0) gewerkt voor het verifiëren van Azure AD-identiteiten (werk-en school accounts) door tokens aan te vragen met behulp van ADAL. Nu kunt u met behulp van MSAL. js een uitgebreidere set micro soft-identiteiten (Azure AD-identiteiten en micro soft-accounts en sociale en lokale accounts via Azure AD B2C) verifiëren via micro soft Identity platform (v 2.0).

In dit artikel wordt beschreven hoe u kiest tussen de micro soft-verificatie bibliotheek voor Java script (MSAL. js) en de Azure AD-verificatie bibliotheek voor Java script (ADAL. js) en vergelijkt de twee bibliotheken.

## <a name="choosing-between-adaljs-and-msaljs"></a>Kiezen tussen ADAL. js en MSAL. js

In de meeste gevallen wilt u het micro soft Identity platform en MSAL. js gebruiken. Dit is de nieuwste generatie micro soft-verificatie bibliotheken. Met behulp van MSAL. js kunt u tokens verkrijgen voor gebruikers die zich aanmelden bij uw toepassing met Azure AD (werk-en school accounts), micro soft (persoonlijke) accounts (MSA) of Azure AD B2C.

Als u al bekend bent met het v 1.0-eind punt (en ADAL. js), wilt u wellicht lezen [wat er anders is dan het v 2.0-eind punt?](active-directory-v2-compare.md).

U moet echter nog steeds ADAL. js gebruiken als uw toepassing zich moet aanmelden bij eerdere versies van [Active Directory Federation Services (ADFS)](/windows-server/identity/active-directory-federation-services).

## <a name="key-differences-in-authentication-with-msaljs"></a>Belangrijkste verschillen in verificatie met MSAL. js

### <a name="core-api"></a>Core-API

* ADAL. js gebruikt [AuthenticationContext](https://github.com/AzureAD/azure-activedirectory-library-for-js/wiki/Config-authentication-context#authenticationcontext) als representatie van een exemplaar van de verbinding van uw toepassing met de autorisatie server of ID-provider via een instantie-URL. In tegens telling tot de MSAL. js-API is ontworpen rond client toepassing voor gebruikers agent (een vorm van een open bare client toepassing waarin de client code wordt uitgevoerd in een gebruikers agent, zoals een webbrowser). Het biedt de `UserAgentApplication` klasse om een exemplaar van de verificatie context van de toepassing met de autorisatie server weer te geven. Zie [initialiseren met MSAL. js](msal-js-initializing-client-applications.md)voor meer informatie.

* In ADAL. js worden de methoden voor het verkrijgen van tokens gekoppeld aan één instantie die in de `AuthenticationContext`is ingesteld. In MSAL. js kunnen de aanvragen voor het ophalen van tokens verschillende instanties waarden hebben dan wat in de `UserAgentApplication`is ingesteld. Hierdoor kan MSAL. js tokens afzonderlijk verkrijgen en in de cache opslaan voor meerdere tenants en gebruikers accounts in dezelfde toepassing.

* De methode om tokens op de achtergrond te verkrijgen en te vernieuwen zonder dat de gebruiker om toestemming wordt gevraagd, wordt `acquireToken` genoemd in ADAL. js. In MSAL. js heeft deze methode de naam `acquireTokenSilent` om meer beschrijvende van deze functionaliteit te maken.

### <a name="authority-value-common"></a>Waarde van instantie `common`

In v 1.0 kunnen gebruikers zich aanmelden met een Azure AD-account (voor elke organisatie) met behulp van de `https://login.microsoftonline.com/common`-instantie.

In v 2.0 kunnen gebruikers met behulp van de `https://login.microsoftonline.com/common`-instantie zich aanmelden met een Azure AD-organisatie account of een persoonlijk micro soft-account (MSA). Als u de aanmelding wilt beperken tot alleen Azure AD-accounts (hetzelfde gedrag als met ADAL. js), moet u `https://login.microsoftonline.com/organizations`gebruiken. Zie voor meer informatie de optie `authority` config in [Initialize met behulp van MSAL. js](msal-js-initializing-client-applications.md).

### <a name="scopes-for-acquiring-tokens"></a>Bereiken voor het verkrijgen van tokens
* Bereik in plaats van resource-para meter in verificatie aanvragen voor het verkrijgen van tokens

    v 2.0-protocol gebruikt scopes in plaats van resource in de aanvragen. Met andere woorden, als uw toepassing tokens moet aanvragen met machtigingen voor een resource zoals MS Graph, is het verschil in waarden die worden door gegeven aan de bibliotheek methoden als volgt:

    v 1.0: resource = https\://graph.microsoft.com

    v 2.0: Scope = https\://graph.microsoft.com/User.Read

    U kunt bereiken voor elke resource-API aanvragen met behulp van de URI van de API in deze indeling: appidURI/Scope bijvoorbeeld: https:\//mytenant.onmicrosoft.com/myapi/api.read

    Voor de MS Graph API is een bereik waarde `user.read` toegewezen aan https:\//graph.microsoft.com/User.Read en kan door elkaar worden gebruikt.

    ```javascript
    var request = {
        scopes = ["User.Read"];
    };

    acquireTokenPopup(request);   
    ```

* Dynamische bereiken voor incrementele toestemming.

    Wanneer u toepassingen bouwt met behulp van v 1.0, moet u de volledige set machtigingen (statische bereiken) registreren die de gebruiker nodig heeft om toestemming te geven op het moment van de aanmelding. In v 2.0 kunt u de bereik parameter gebruiken om de machtigingen op te vragen op het moment dat u wilt. Deze worden dynamische bereiken genoemd. Hiermee kan de gebruiker incrementele toestemming geven voor bereiken. Als u aan het begin alleen wilt dat de gebruiker zich bij uw toepassing aanmeldt en u geen toegang nodig hebt, kunt u dat doen. Als u dit later nodig hebt om de agenda van de gebruiker te lezen, kunt u het bereik van de agenda aanvragen in de acquireToken-methoden en de toestemming van de gebruiker krijgen. Bijvoorbeeld:

    ```javascript
    var request = {
        scopes = ["https://graph.microsoft.com/User.Read", "https://graph.microsoft.com/Calendar.Read"];
    };

    acquireTokenPopup(request);   
    ```

* Bereiken voor V 1.0-Api's

    Bij het ophalen van tokens voor V 1.0 Api's met behulp van MSAL. js kunt u alle statische bereiken aanvragen die zijn geregistreerd op de API door `.default` toe te voegen aan de App-ID-URI van de API als bereik. Bijvoorbeeld:

    ```javascript
    var request = {
        scopes = [ appidURI + "/.default"];
    };

    acquireTokenPopup(request);
    ```

## <a name="next-steps"></a>Volgende stappen
Zie [vergelijking van v 1.0 en v 2.0](active-directory-v2-compare.md)voor meer informatie.
