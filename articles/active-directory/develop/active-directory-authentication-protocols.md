---
title: Verificatie protocollen voor Azure Active Directory | Microsoft Docs
description: Een overzicht van de verificatie protocollen die worden ondersteund door Azure Active Directory (AD)
author: rwike77
services: active-directory
manager: CelesteDG
ms.assetid: 7a838ae2-c24c-4304-b6c0-e77fb888e6c0
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/27/2017
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: hirsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: b370736a5e4994651499716e3a923cf59465ca96
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74845413"
---
# <a name="azure-active-directory-authentication-protocols"></a>Verificatie protocollen Azure Active Directory

Azure Active Directory (Azure AD) ondersteunt een aantal van de meest gebruikte verificatie-en autorisatie protocollen. De onderwerpen in deze sectie beschrijven de ondersteunde protocollen en de implementatie ervan in azure AD. De onderwerpen bevatten een overzicht van de ondersteunde claim typen, een inleiding tot het gebruik van federatieve meta gegevens, gedetailleerde OAuth 2,0. en de informatie over het SAML 2,0-protocol en een sectie voor probleem oplossing.

## <a name="authentication-protocols-articles-and-reference"></a>Verificatie protocollen artikelen en naslag informatie

* [Belang rijke informatie over de rollover van de handtekening sleutel in azure AD](active-directory-signing-key-rollover.md) : meer informatie over de rollover van de uitgebracht van Azure AD, wijzigingen die u kunt aanbrengen om de sleutel automatisch bij te werken en om te zien hoe u de meest voorkomende toepassings scenario's bijwerkt.
* [Ondersteunde token-en claim typen](v1-id-and-access-tokens.md) : meer informatie over de claims in de tokens die door Azure AD worden uitgegeven.
* [Federatieve meta gegevens](azure-ad-federation-metadata.md) : informatie over het zoeken en interpreteren van de meta gegevens documenten die door Azure AD worden gegenereerd.
* [OAuth 2,0 in azure AD](v1-protocols-oauth-code.md) : meer informatie over de implementatie van OAuth 2,0 in azure AD.
* [OpenID Connect Connect 1,0](v1-protocols-openid-connect-code.md) -meer informatie over het gebruik van OAuth 2,0, een autorisatie protocol, voor authenticatie.
* [Service-to-service aanroepen met client referenties](v1-oauth2-client-creds-grant-flow.md) : informatie over het gebruik van OAuth 2,0-client referenties toekenning stroom voor service-to-service aanroepen.
* [Service-to-service-aanroepen met](v1-oauth2-on-behalf-of-flow.md) namens een stroom-meer informatie over het gebruik van OAuth 2,0-stroom voor service-naar-service-aanroepen.
* [SAML-protocol referentie](active-directory-saml-protocol-reference.md) -meer informatie over de SAML-profielen eenmalige aanmelding en eenmalige afmelding van Azure AD.

## <a name="see-also"></a>Zie ook

* [Ontwikkelaars handleiding Azure Active Directory](v1-overview.md)
* [Voor beelden van Active Directory code](sample-v1-code.md)
