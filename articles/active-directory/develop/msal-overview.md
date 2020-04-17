---
title: Meer informatie over MSAL | Azure
titleSuffix: Microsoft identity platform
description: Microsoft Authentication Library (MSAL) stelt toepassingsontwikkelaars in staat om tokens te verwerven om beveiligde web-API's te bellen. Deze web-API's kunnen de Microsoft Graph, andere Microsoft API's, webAPI's van derden of uw eigen web-API zijn. MSAL ondersteunt meerdere applicatiearchitecturen en platformen.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 89c5117e59f7856c3bd572bbea297a836b5ad589
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536213"
---
# <a name="overview-of-microsoft-authentication-library-msal"></a>Overzicht van Microsoft Authentication Library (MSAL)
Microsoft Authentication Library (MSAL) stelt ontwikkelaars in staat om [tokens](developer-glossary.md#security-token) te verwerven van het eindpunt van het Microsoft-identiteitsplatform om toegang te krijgen tot beveiligde web-API's. Deze web-API's kunnen de Microsoft Graph, andere Microsoft API's, webAPI's van derden of uw eigen web-API zijn. MSAL is beschikbaar voor .NET, JavaScript, Android en iOS, die veel verschillende applicatiearchitecturen en -platforms ondersteunen.

MSAL biedt u vele manieren om tokens te krijgen, met een consistente API voor een aantal platforms. Het gebruik van MSAL biedt de volgende voordelen:

* U hoeft de OAuth-bibliotheken of -code niet direct te gebruiken tegen het protocol in uw toepassing.
* Verwerft tokens namens een gebruiker of namens een toepassing (indien van toepassing op het platform).
* Onderhoudt een tokencache en vernieuwt tokens voor u wanneer ze bijna verlopen. U hoeft de vervaldatum van token niet alleen af te handelen.
* Hiermee u opgeven welk publiek u wilt dat uw toepassing inlogt (uw organisatie, verschillende organisaties, werk- en school- en Microsoft-persoonlijke accounts, sociale identiteiten met Azure AD B2C, gebruikers in soevereine en nationale clouds).
* Hiermee u uw toepassing instellen op configuratiebestanden.
* Hiermee u uw app oplossen door bruikbare uitzonderingen, logboekregistratie en telemetrie bloot te leggen.

## <a name="application-types-and-scenarios"></a>Typen apps en scenario's
Met behulp van MSAL kan een token worden verkregen uit een aantal toepassingstypen: webtoepassingen, web-API's, apps met één pagina (JavaScript), mobiele en native toepassingen en daemons en servertoepassingen.

MSAL kan worden gebruikt in veel toepassingsscenario's, waaronder:

* [Toepassingen met één pagina (JavaScript)](scenario-spa-overview.md)
* [Aanmelden bij webapps voor gebruikers](scenario-web-app-sign-user-overview.md)
* [Webtoepassing die een gebruiker aantekent en namens de gebruiker een web-API aanroept](scenario-web-app-call-api-overview.md)
* [Een web-API beveiligen zodat alleen geverifieerde gebruikers er toegang toe hebben](scenario-protected-web-api-overview.md)
* [Web API die een andere downstream web-API aanroept namens de aangemelde gebruiker](scenario-web-api-call-api-overview.md)
* [Bureaubladtoepassing die namens de aangemelde gebruiker een web-API aanroept](scenario-desktop-overview.md)
* [Mobiele toepassing die een web-API aanroept namens de gebruiker die interactief is aangemeld.](scenario-mobile-overview.md)
* [Desktop/service daemon-toepassing die namens zichzelf web-API aanroept](scenario-daemon-overview.md)

## <a name="languages-and-frameworks"></a>Talen en kaders

| Bibliotheek | Ondersteunde platforms en frameworks|
| --- | --- |
| [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)| .NET Framework, .NET Core, Xamarin Android, Xamarin iOS, Universal Windows Platform|
| [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)| JavaScript/TypeScript-frameworks zoals AngularJS, Ember.js of Durandal.js|
| [MSAL voor Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)|Android|
| [MSAL voor iOS en macOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc)|iOS en macOS|
| [MSAL Java (preview)](https://github.com/AzureAD/microsoft-authentication-library-for-java)|Java|
| [MSAL Python (preview)](https://github.com/AzureAD/microsoft-authentication-library-for-python)|Python|

## <a name="differences-between-adal-and-msal"></a>Verschillen tussen ADAL en MSAL

Active Directory Authentication Library (ADAL) integreert met het Azure AD voor ontwikkelaars (v1.0) eindpunt, waar MSAL integreert met het Microsoft Identity Platform (v2.0) eindpunt. Het v1.0-eindpunt ondersteunt werkaccounts, maar geen persoonlijke accounts. Het v2.0-eindpunt is de eenmaking van persoonlijke accounts van Microsoft en het verwerken van accounts in één verificatiesysteem. Bovendien u met MSAL ook verificaties voor Azure AD B2C krijgen.

Lees voor meer specifieke informatie over [migreren naar MSAL.NET van ADAL.NET](msal-net-migration.md) en migreren naar [MSAL.js van ADAL.js](msal-compare-msal-js-and-adal-js.md).
