---
title: Evolutie van micro soft Identity platform-Azure
description: Meer informatie over micro soft Identity platform, een evolutie van de identiteits service van Azure Active Directory (Azure AD) en het ontwikkelaars platform.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/03/2019
ms.author: ryanwi
ms.reviewer: agirling, saeeda, benv
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c5fdc1c937136eb512eccf1d4df02a4fcdc3911
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533106"
---
# <a name="evolution-of-microsoft-identity-platform"></a>Evolutie van het Microsoft Identity Platform

Het Microsoft Identity Platform bouwt voort op het ontwikkelaarsplatform van Azure Active Directory (Azure AD). Ontwikkel aars kunnen toepassingen maken die zich aanmelden bij gebruikers, tokens ophalen om Api's aan te roepen, zoals Microsoft Graph, of Api's die ontwikkel aars hebben gebouwd. Het bestaat uit een verificatie service, open source-bibliotheken, toepassings registratie en configuratie (via een ontwikkelaars Portal en toepassings-API), volledige documentatie voor ontwikkel aars, Quick start-voor beelden, code voorbeelden, zelf studies, procedures-naar-gidsen en andere inhoud voor ontwikkel aars. Het Microsoft Identity Platform biedt ondersteuning voor standaardprotocollen als OAuth 2.0 en OpenID Connect.

Tot nu toe hebben de meeste ontwikkel aars met het Azure AD v 1.0-platform gewerkt voor het verifiëren van werk-en school accounts (ingericht door Azure AD) door het aanvragen van tokens van het Azure AD v 1.0-eind punt met behulp van Azure AD Authentication Library (ADAL), Azure Portal voor registratie en configuratie van toepassingen en Azure AD-Graph API voor de configuratie van een programmatische toepassing.

Met micro soft Identity platform (v 2.0) breidt u uw bereik uit voor dit soort gebruikers:

- Werk-en school accounts (voor Azure AD ingerichte accounts)
- Persoonlijke accounts (zoals Outlook.com of Hotmail.com)
- Uw klanten die hun eigen e-mail adres of sociale identiteit (zoals LinkedIn, Facebook, Google) meenemen via de Azure AD B2C aanbieding

Met het geïntegreerde identiteits platform van micro soft kunt u eenmaal code schrijven en een micro soft-identiteit verifiëren in uw toepassing. Voor verschillende platforms is er een volledig ondersteunde open-source bibliotheek met de naam micro soft Authentication Library (MSAL). MSAL is eenvoudig te gebruiken. biedt uw gebruikers een fantastische SSO-ervaring (eenmalige aanmelding), helpt u bij het bezorgen van hoge betrouw baarheid en prestaties en is ontwikkeld met behulp van micro soft Secure Development Lifecycle (SDL). Wanneer u Api's aanroept, kunt u uw toepassing zo configureren dat deze gebruikmaakt van incrementele toestemming, zodat u de aanvraag voor toestemming voor meer invasieve bereiken kunt vertragen tot het gebruik van de toepassing dit tijdens runtime garandeert.

U kunt de Azure Portal gebruiken om uw toepassing te registreren en te configureren, en de Microsoft Graph API gebruiken voor de programmatische toepassings configuratie.

Werk uw toepassing in uw eigen tempo bij. Toepassingen die zijn gebouwd met ADAL-Bibliotheken, worden nog steeds ondersteund. Gemengde toepassings portefeuilles, die bestaan uit toepassingen die zijn gebouwd met ADAL en toepassingen die zijn gebouwd met MSAL-Bibliotheken, worden ook ondersteund. Dit betekent dat toepassingen die gebruikmaken van de nieuwste ADAL en de nieuwste MSAL, SSO kunnen leveren over de Port Folio, die wordt aangeboden door de gedeelde token cache tussen deze bibliotheken. Toepassingen die zijn bijgewerkt van ADAL naar MSAL, behouden de aanmeldings status van gebruikers bij een upgrade.

## <a name="microsoft-identity-platform-experience"></a>Gebruikerservaring met het Microsoft Identity Platform

In het volgende schema wordt de gebruikerservaring met Microsoft Identity weergegeven op hoog niveau, inclusief de registratie van apps, SDK’s, eindpunten en ondersteunde identiteiten.

![Het Microsoft Identity Platform vandaag](./media/about-microsoft-identity-platform/about-microsoft-identity-platform.svg)

### <a name="app-registration-experience"></a>App-registratie-ervaring

De Azure Portal- **[app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908)** -ervaring is de portal-ervaring voor het beheren van alle toepassingen die u hebt geïntegreerd met het micro soft Identity-platform. Als u de portal voor toepassings registratie hebt gebruikt, kunt u in plaats daarvan de Azure Portal app registratie-ervaring gebruiken.

Voor integratie met Azure AD B2C (bij het verifiëren van sociale of lokale identiteiten) moet u uw toepassing registreren in een Azure AD B2C-Tenant. Deze ervaring maakt ook deel uit van de Azure Portal.

De **API van de toepassing in Microsoft Graph** is momenteel beschikbaar als preview-versie. Gebruik deze API om op een programmatische manier uw toepassingen te configureren die zijn geïntegreerd met het micro soft Identity platform voor de verificatie van een micro soft-identiteit. U kunt echter pas de Azure AD Graph 1,6 API en het toepassings manifest gebruiken totdat deze API de algemene Beschik baarheid bereikt.

### <a name="msal-libraries"></a>MSAL-bibliotheken

U kunt de MSAL-bibliotheek gebruiken om toepassingen te bouwen die alle micro soft-identiteiten verifiëren. De MSAL-bibliotheken in .NET en Java script zijn algemeen beschikbaar. MSAL-bibliotheken voor iOS en Android zijn beschikbaar als preview-versie en zijn geschikt voor gebruik in een productie omgeving. We bieden dezelfde productie niveau ondersteuning voor MSAL-bibliotheken als in Preview zoals we voor versies van MSAL en ADAL die algemeen beschikbaar zijn.

U kunt ook de MSAL-bibliotheken gebruiken om uw toepassing te integreren met Azure AD B2C.

Bibliotheken aan de server zijde voor het bouwen van web-apps en Web-Api's zijn algemeen beschikbaar: [ASP.net](https://docs.microsoft.com/aspnet/overview) en [ASP.net core](https://docs.microsoft.com/aspnet/core/?view=aspnetcore-2.2)

### <a name="microsoft-identity-platform-endpoint"></a>Micro soft Identity platform-eind punt

Het micro soft Identity platform (v 2.0)-eind punt is nu OIDC gecertificeerd. Het werkt met de micro soft-verificatie bibliotheken (MSAL) of een andere, aan normen compatibele bibliotheek. Het implementeert menselijke Lees bare bereiken, conform de industrie normen.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over v1.0 en v2.0.

* [Overzicht van micro soft Identity platform (v 2.0)](v2-overview.md)
* [Overzicht van Azure Active Directory voor ontwikkel aars (v 1.0)](v1-overview.md)
