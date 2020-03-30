---
title: Evolutie van microsoft-identiteitsplatform - Azure
description: Meer informatie over het Microsoft-identiteitsplatform, een evolutie van de Azure Active Directory -identiteitsservice en het ontwikkelaarsplatform (Azure Active Directory).
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: overview
ms.workload: identity
ms.date: 12/09/2019
ms.author: ryanwi
ms.reviewer: agirling, saeeda, benv
ms.custom: aaddev
ms.openlocfilehash: 8714b7a96197cb4a59b29bada31b5559961bf8e3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78300210"
---
# <a name="evolution-of-microsoft-identity-platform"></a>Evolutie van het Microsoft Identity Platform

Het Microsoft Identity Platform bouwt voort op het ontwikkelaarsplatform van Azure Active Directory (Azure AD). Hiermee kunnen ontwikkelaars toepassingen bouwen die gebruikers aanmelden, tokens ertoe brengen API's te bellen, zoals Microsoft Graph, of API's die ontwikkelaars hebben gebouwd. Het bestaat uit een verificatieservice, open-source bibliotheken, applicatieregistratie en configuratie (via een ontwikkelaarsportal en toepassings-API), volledige ontwikkelaarsdocumentatie, quickstart-voorbeelden, codevoorbeelden, zelfstudies, handleidingen en andere ontwikkelaarsinhoud. Het Microsoft Identity Platform biedt ondersteuning voor standaardprotocollen als OAuth 2.0 en OpenID Connect.

Tot nu toe hebben de meeste ontwikkelaars samengewerkt met het Azure AD v1.0-platform om werk- en schoolaccounts te verifiëren (ingericht door Azure AD) door tokens aan te vragen bij het Azure AD v1.0-eindpunt, met Azure AD-verificatiebibliotheek (ADAL), Azure-portal voor toepassingsregistratie en -configuratie en de Microsoft Graph-API voor programmatische toepassingsconfiguratie.

Met het uniforme Microsoft-identiteitsplatform (v2.0) u één keer code schrijven en elke Microsoft-identiteit in uw toepassing verifiëren. Voor verschillende platforms wordt de volledig ondersteunde open-source Microsoft Authentication Library (MSAL) aanbevolen voor gebruik tegen de eindpunten van het identiteitsplatform. MSAL is eenvoudig te gebruiken, biedt geweldige single sign-on (SSO) ervaringen voor uw gebruikers, helpt u hoge betrouwbaarheid en prestaties te bereiken en is ontwikkeld met behulp van Microsoft Secure Development Lifecycle (SDL). Wanneer u API's aanroept, u uw toepassing configureren om te profiteren van incrementele toestemming, waardoor u het verzoek om toestemming voor meer invasieve scopes uitstellen totdat het gebruik van de toepassing dit bij runtime rechtvaardigt.  MSAL ondersteunt ook Azure Active Directory B2C, zodat uw klanten hun favoriete sociale, zakelijke of lokale accountidentiteiten gebruiken om toegang te krijgen tot uw toepassingen en API's.

Met het Microsoft-identiteitsplatform u uw bereik uitbreiden naar dit soort gebruikers:

- Werk- en schoolaccounts (azure AD-ingerichte accounts)
- Persoonlijke accounts (zoals Outlook.com of Hotmail.com)
- Uw klanten die hun eigen e-mail of sociale identiteit (zoals LinkedIn, Facebook, Google) via MSAL en Azure AD B2C

U de Azure-portal gebruiken om uw toepassing te registreren en te configureren en de Microsoft Graph-API te gebruiken voor programmatische toepassingsconfiguratie.

Werk uw toepassing in uw eigen tempo bij. Toepassingen die zijn gebouwd met ADAL-bibliotheken worden nog steeds ondersteund. Gemengde applicatieportfolio's, die bestaan uit applicaties die zijn gebouwd met ADAL en applicaties gebouwd met MSAL bibliotheken, worden ook ondersteund. Dit betekent dat toepassingen met behulp van de nieuwste ADAL en de nieuwste MSAL SSO in het hele portfolio zullen leveren, geleverd door de gedeelde tokencache tussen deze bibliotheken. Toepassingen die zijn bijgewerkt van ADAL naar MSAL, behouden de aanmeldingsstatus van de gebruiker bij de upgrade.

## <a name="microsoft-identity-platform-experience"></a>Gebruikerservaring met het Microsoft Identity Platform

In het volgende schema wordt de gebruikerservaring met Microsoft Identity weergegeven op hoog niveau, inclusief de registratie van apps, SDK’s, eindpunten en ondersteunde identiteiten.

![Het Microsoft Identity Platform vandaag](./media/about-microsoft-identity-platform/about-microsoft-identity-platform.svg)

### <a name="app-registration-experience"></a>Ervaring met app-registratie

De ervaring met Azure portal **[App-registraties](https://go.microsoft.com/fwlink/?linkid=2083908)** is de enige portalervaring voor het beheren van alle toepassingen die u hebt geïntegreerd met het Microsoft-identiteitsplatform. Als u de portal voor toepassingsregistratie hebt gebruikt, u in plaats daarvan de registratie-ervaring van de Azure-portal-app gebruiken.

Voor integratie met Azure AD B2C (bij het verifiëren van sociale of lokale identiteiten) moet u uw toepassing registreren in een Azure AD B2C-tenant. Deze ervaring maakt ook deel uit van de Azure-portal.

Gebruik de [Application API](https://docs.microsoft.com/graph/api/resources/application?view=graph-rest-1.0) om uw toepassingen die zijn geïntegreerd met het Microsoft-identiteitsplatform programmatisch te configureren voor het verifiëren van elke Microsoft-identiteit.

### <a name="msal-libraries"></a>MSAL-bibliotheken

U de MSAL-bibliotheek gebruiken om toepassingen te bouwen die alle Microsoft-identiteiten verifiëren. De MSAL-bibliotheken in .NET en JavaScript zijn algemeen beschikbaar. MSAL-bibliotheken voor iOS en Android zijn in preview en geschikt voor gebruik in een productieomgeving. We bieden dezelfde ondersteuning op productieniveau voor MSAL-bibliotheken in preview als voor versies van MSAL en ADAL die algemeen beschikbaar zijn.

U de MSAL-bibliotheken ook gebruiken om uw toepassing te integreren met Azure AD B2C.

Serverbibliotheken voor het bouwen van web-apps en web-API's zijn algemeen beschikbaar: [ASP.NET](https://docs.microsoft.com/aspnet/overview) en [ASP.NET Core](https://docs.microsoft.com/aspnet/core/?view=aspnetcore-2.2)

### <a name="microsoft-identity-platform-endpoint"></a>Eindpunt van microsoft-identiteitsplatform

Microsoft identity platform (v2.0) endpoint is nu OIDC gecertificeerd. Het werkt met de Microsoft Authentication Libraries (MSAL) of een andere bibliotheek die voldoet aan standaarden. Het implementeert menselijke leesbare scopes, in overeenstemming met de industrienormen.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over v1.0 en v2.0.

* [Overzicht van Microsoft Identity Platform (v2.0)](v2-overview.md)
* [Overzicht van Azure Active Directory voor ontwikkelaars (v1.0)](../azuread-dev/v1-overview.md)
