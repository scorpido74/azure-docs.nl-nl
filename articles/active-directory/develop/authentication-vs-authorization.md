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
ms.date: 04/28/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 61c0202d12756201d45fe829078d84382e44b54e
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82584296"
---
# <a name="authentication-vs-authorization"></a>Verificatie versus autorisatie

In dit artikel worden verificatie en autorisatie gedefinieerd en wordt kort beschreven hoe u het micro soft-identiteits platform kunt gebruiken om gebruikers te verifiëren en te autoriseren in uw web-apps, Web-Api's of apps die beveiligde web-Api's aanroepen. Als u een term ziet die u niet kent, probeert u onze [woorden lijst](developer-glossary.md) of onze [Video's over micro soft Identity platform](identity-videos.md) die basis concepten omvatten.

## <a name="authentication"></a>Verificatie

**Verificatie** is het proces van waaruit u wordt gedicteerd. Verificatie wordt soms afgekort tot 'AuthN'. Micro soft Identity platform implementeert het [OpenID Connect Connect](https://openid.net/connect/) -protocol voor het afhandelen van authenticatie.

## <a name="authorization"></a>Autorisatie

**Autorisatie** is het verlenen van een geverifieerde partij toestemming om iets te doen. Hiermee geeft u op welke gegevens u toegang hebt tot en wat u met die gegevens kunt doen. Autorisatie wordt soms afgekort tot 'AuthZ'. Micro soft Identity platform implementeert het [OAuth 2,0](https://oauth.net/2/) -protocol voor het afhandelen van autorisatie.

## <a name="authentication-and-authorization-using-the-microsoft-identity-platform"></a>Verificatie en autorisatie met het micro soft-identiteits platform

In plaats van apps te maken die elk eigen gebruikers naam en wacht woord behouden, waardoor een hoge administratieve belasting optreedt wanneer u gebruikers wilt toevoegen aan of verwijderen uit meerdere apps, kunnen apps die verantwoordelijkheid delegeren aan een gecentraliseerde ID-provider.

Azure Active Directory (Azure AD) is een gecentraliseerde ID-provider in de Cloud. Door de verificatie en autorisatie te delegeren, worden scenario's zoals beleids regels voor voorwaardelijke toegang die een gebruiker in een specifieke locatie moeten, het gebruik van multi-factor Authentication en het inschakelen van een gebruiker in één keer aanmelden en vervolgens automatisch aangemeld bij alle web-apps die dezelfde gecentraliseerde map delen. Deze mogelijkheid wordt **eenmalige aanmelding (SSO)** genoemd.

Micro soft Identity platform vereenvoudigt verificatie en autorisatie voor toepassings ontwikkelaars door identiteit als een service te bieden, met ondersteuning voor industrie-standaard protocollen zoals OAuth 2,0 en OpenID Connect Connect, evenals open-source-bibliotheken voor verschillende platformen, zodat u snel kunt beginnen met coderen. Ontwikkel aars kunnen toepassingen bouwen die zich aanmelden bij alle micro soft-identiteiten, tokens verkrijgen om [Microsoft Graph](https://developer.microsoft.com/graph/), andere Api's van micro soft of api's te roepen die ontwikkel aars hebben gebouwd. Zie [ontwikkeling van micro soft Identity platform](about-microsoft-identity-platform.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Voor andere onderwerpen met betrekking tot de basis van verificatie en autorisatie:

* Zie [beveiligings tokens](security-tokens.md) voor meer informatie over het gebruik van toegangs tokens, het vernieuwen van tokens en id-tokens in verificatie en autorisatie.
* Zie [toepassings model](application-model.md) voor meer informatie over het proces van het registreren van uw toepassing, zodat deze kan worden geïntegreerd met het micro soft Identity-platform.
* Zie [app-aanmeldings flow](app-sign-in-flow.md) voor meer informatie over de aanmeldings stroom van web-, desktop-en Mobile-apps in het micro soft Identity-platform.

Voor meer informatie over de protocollen die door micro soft Identity platform worden geïmplementeerd:

* Zie [OAuth 2,0 en OpenID Connect Connect protocols op het micro soft Identity platform](active-directory-v2-protocols.md) voor meer informatie over de OpenID Connect Connect en OAuth 2,0-standaarden.
* Zie het [SAML-protocol voor eenmalige aanmelding](single-sign-on-saml-protocol.md) voor meer informatie over hoe micro soft Identity-platform eenmalige aanmelding ondersteunt.
