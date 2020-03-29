---
title: Verificatieprotocollen voor Microsoft-identiteitsplatform | Microsoft Documenten
description: Een overzicht van de verificatieprotocollen die worden ondersteund door het identiteitsplatform van Microsoft
author: rwike77
services: active-directory
manager: CelesteDG
ms.assetid: 7a838ae2-c24c-4304-b6c0-e77fb888e6c0
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 12/18/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: hirsin
ms.openlocfilehash: 43168ec7217d8f016857ba6dc54ca30bce2dd594
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76699288"
---
# <a name="microsoft-identity-platform-authentication-protocols"></a>Verificatieprotocollen voor Microsoft-identiteitsplatform

Microsoft identity platform ondersteunt een aantal van de meest gebruikte authenticatie- en autorisatieprotocollen. De onderwerpen in deze sectie beschrijven de ondersteunde protocollen en de implementatie ervan in het Microsoft-identiteitsplatform. De onderwerpen omvatten een herziening van ondersteunde claimtypen, een inleiding tot het gebruik van federatiemetadata, gedetailleerde OAuth 2.0. en SAML 2.0-protocolreferentiedocumentatie en een sectie voor probleemoplossing.

## <a name="authentication-protocols-articles-and-reference"></a>Verificatieprotocollen artikelen en referentie

* [Belangrijke informatie over het ondertekenen van sleutelrollover in het Microsoft-identiteitsplatform](active-directory-signing-key-rollover.md) : Lees meer over de ondertekeningssleutelcadans van het Microsoft-identiteitsplatform, wijzigingen die u aanbrengen om de sleutel automatisch bij te werken en discussie over hoe u de meest voorkomende toepassingsscenario's bijwerken.
* [Ondersteunde token- en claimtypen](id-tokens.md) - Meer informatie over de claims in de tokens die microsoft-identiteitsplatform uitgeeft.
* [OAuth 2.0 in Microsoft-identiteitsplatform](v2-oauth2-auth-code-flow.md) - Meer informatie over de implementatie van OAuth 2.0 in het identiteitsplatform van Microsoft.
* [OpenID Connect 1.0](v2-protocols-oidc.md) - Meer informatie over het gebruik van OAuth 2.0, een autorisatieprotocol, voor verificatie.
* [Service to Service Calls with Client Credentials](v2-oauth2-client-creds-grant-flow.md) - Lees hoe u de verleningsstroom van OAuth 2.0-clientreferenties gebruiken voor service aan serviceoproepen.
* [Service to Service Calls met On-Behalf-Of Flow](v2-oauth2-on-behalf-of-flow.md) - Meer informatie over het gebruik van OAuth 2.0 On-Behalf-Of flow voor service-to-servicegesprekken.
* [SAML Protocol Reference](active-directory-saml-protocol-reference.md) - Meer informatie over de SAML-profielen voor één aanmelding en één aanmelding van het Microsoft-identiteitsplatform.

## <a name="see-also"></a>Zie ook

* [Overzicht van microsoft-identiteitsplatform](v2-overview.md)
* [Voorbeelden van Active Directory-code](sample-v2-code.md)
