---
title: Verificatie versus autorisatie | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over de basis principes van verificatie en autorisatie in micro soft Identity platform (v 2.0).
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/18/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: cfc2a7d161619efcd9eee2c32a4dabc2e3a2e6ed
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83772060"
---
# <a name="authentication-vs-authorization"></a>Verificatie vs. autorisatie

In dit artikel worden verificatie en autorisatie gedefinieerd en wordt kort beschreven hoe u het micro soft-identiteits platform kunt gebruiken om gebruikers te verifiëren en te autoriseren in uw web-apps, Web-Api's of apps die beveiligde web-Api's aanroepen. Als u een term ziet die u niet kent, probeert u onze [woorden lijst](developer-glossary.md) of onze [Video's over micro soft Identity platform](identity-videos.md) die basis concepten omvatten.

## <a name="authentication"></a>Verificatie

**Verificatie** is het proces van waaruit u wordt gedicteerd. Verificatie wordt soms afgekort tot 'AuthN'. Micro soft Identity platform implementeert het [OpenID Connect Connect](https://openid.net/connect/) -protocol voor het afhandelen van authenticatie.

## <a name="authorization"></a>Autorisatie

**Autorisatie** is het verlenen van een geverifieerde partij toestemming om iets te doen. Hiermee geeft u op welke gegevens u toegang hebt tot en wat u met die gegevens kunt doen. Autorisatie wordt soms afgekort tot 'AuthZ'. Micro soft Identity platform implementeert het [OAuth 2,0](https://oauth.net/2/) -protocol voor het afhandelen van autorisatie.

## <a name="authentication-and-authorization-using-microsoft-identity-platform"></a>Verificatie en autorisatie met behulp van micro soft Identity platform

In plaats van apps te maken die elk eigen gebruikers naam en wacht woord behouden, waardoor een hoge administratieve belasting optreedt wanneer u gebruikers wilt toevoegen aan of verwijderen uit meerdere apps, kunnen apps die verantwoordelijkheid delegeren aan een gecentraliseerde ID-provider.

Azure Active Directory (Azure AD) is een gecentraliseerde ID-provider in de Cloud. Door de verificatie en autorisatie te delegeren, worden scenario's zoals beleids regels voor voorwaardelijke toegang die een gebruiker in een specifieke locatie vereisen, het gebruik van [multi-factor Authentication](../authentication/concept-mfa-howitworks.md) (ook wel twee ledige authenticatie of twee ledige genoemd), en wordt een gebruiker in staat gesteld om zich eenmaal aan te melden bij alle web-apps die dezelfde gecentraliseerde map delen. Deze mogelijkheid wordt **eenmalige aanmelding (SSO)** genoemd.

Micro soft Identity platform vereenvoudigt autorisatie en verificatie voor toepassings ontwikkelaars door identiteit als een service te bieden, met ondersteuning voor industrie-standaard protocollen zoals OAuth 2,0 en OpenID Connect Connect, evenals open-source-bibliotheken voor verschillende platformen, zodat u snel kunt beginnen met coderen. Ontwikkel aars kunnen toepassingen bouwen die zich aanmelden bij alle micro soft-identiteiten, tokens verkrijgen om [Microsoft Graph](https://developer.microsoft.com/graph/), andere Api's van micro soft of api's te roepen die ontwikkel aars hebben gebouwd. Zie [ontwikkeling van micro soft Identity platform](about-microsoft-identity-platform.md)voor meer informatie.

Hieronder vindt u een korte vergelijking van de verschillende protocollen die worden gebruikt door het micro soft Identity-platform:

* **OAuth versus OpenID Connect Connect**: OAuth wordt gebruikt voor autorisatie en OpenID Connect Connect (OIDC) wordt gebruikt voor verificatie. OpenID Connect Connect is gebaseerd op OAuth 2,0, dus de terminologie en de stroom zijn vergelijkbaar tussen de twee. U kunt zelfs een gebruiker verifiëren (met behulp van OpenID Connect Connect) en toestemming krijgen om toegang te krijgen tot een beveiligde resource die de gebruiker in een aanvraag bezit (met behulp van OAuth 2,0). Zie [OAuth 2,0 en OpenID Connect Connect protocollen](active-directory-v2-protocols.md) en [OpenID Connect Connect protocol](v2-protocols-oidc.md)(Engelstalig) voor meer informatie.
* **OAuth versus SAML**: OAuth wordt gebruikt voor autorisatie en SAML wordt gebruikt voor verificatie. Zie [micro soft Identity platform en OAuth 2,0-bevestigings stroom voor SAML Bearer](v2-saml-bearer-assertion.md) voor meer informatie over de manier waarop de twee protocollen kunnen worden gebruikt om een gebruiker te verifiëren (met behulp van SAML) en om autorisatie te verkrijgen voor toegang tot een beveiligde bron (met behulp van OAuth 2,0).
* **OpenID Connect Connect versus SAML**: zowel OpenID Connect Connect als SAML worden gebruikt voor het verifiëren van een gebruiker en worden gebruikt om eenmalige aanmelding in te scha kelen. SAML-verificatie wordt meestal gebruikt met id-providers, zoals Active Directory Federation Services (ADFS), die zijn gekoppeld aan Azure AD en worden daarom vaak gebruikt in bedrijfs toepassingen. OpenID Connect Connect wordt meestal gebruikt voor apps die zich uitsluitend in de cloud bevinden, zoals mobiele apps, websites en Web-Api's.

## <a name="next-steps"></a>Volgende stappen

Voor andere onderwerpen met betrekking tot de basis van verificatie en autorisatie:

* Zie [beveiligings tokens](security-tokens.md) voor meer informatie over de toegangs tokens, het vernieuwen van tokens en id-tokens die worden gebruikt in autorisatie en verificatie.
* Zie [toepassings model](application-model.md) voor meer informatie over het proces van het registreren van uw toepassing, zodat deze kan worden geïntegreerd met het micro soft Identity-platform.
* Zie [app-aanmeldings flow](app-sign-in-flow.md) voor meer informatie over de aanmeldings stroom van web-, desktop-en Mobile-apps in het micro soft Identity-platform.

* Zie [OAuth 2,0 en OpenID Connect Connect protocols op het micro soft Identity platform](active-directory-v2-protocols.md)voor meer informatie over de protocollen die door micro soft Identity platform worden geïmplementeerd.
* Zie het [SAML-protocol voor eenmalige aanmelding](single-sign-on-saml-protocol.md) voor meer informatie over hoe micro soft Identity-platform eenmalige aanmelding ondersteunt.
* Zie [eenmalige aanmelding bij toepassingen in azure Active Directory](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de verschillende manieren waarop u eenmalige aanmelding in uw app kunt implementeren.
