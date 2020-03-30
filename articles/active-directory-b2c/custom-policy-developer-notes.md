---
title: Notities voor ontwikkelaars voor aangepast beleid
titleSuffix: Azure AD B2C
description: Opmerkingen voor ontwikkelaars over het configureren en onderhouden van Azure AD B2C met aangepast beleid.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ee3b5bd3278412949074b77f9d1c53d63a467280
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189392"
---
# <a name="developer-notes-for-custom-policies-in-azure-active-directory-b2c"></a>Ontwikkelaarsnotities voor aangepast beleid in Azure Active Directory B2C

Aangepaste beleidsconfiguratie in Azure Active Directory B2C is nu algemeen beschikbaar. Deze manier van configureren is gericht op geavanceerde identiteitsontwikkelaars die complexe identiteitsoplossingen bouwen. Aangepaste beleidsregels maken de kracht van het Identity Experience Framework beschikbaar in Azure AD B2C-tenants.
Geavanceerde identiteitsontwikkelaars die aangepaste beleidsregels gebruiken, moeten van plan zijn om enige tijd te investeren in het voltooien van doorloopwerken en het lezen van referentiedocumenten.

Hoewel de meeste beschikbare aangepaste beleidsopties nu algemeen beschikbaar zijn, zijn er onderliggende mogelijkheden, zoals technische profieltypen en API's voor inhoudsdefinitie die zich in verschillende stadia van de levenscyclus van de software bevinden. Er komen er nog veel meer aan. De onderstaande tabel geeft het beschikbaarheidsniveau op een gedetailleerder niveau aan.

## <a name="features-that-are-generally-available"></a>Functies die algemeen beschikbaar zijn

- Ontwerp en upload aangepaste gebruikersreizen voor verificatie met behulp van aangepast beleid.
    - Beschrijf gebruikersreizen stap voor stap als uitwisselingen tussen claimproviders.
    - Definieer voorwaardelijke vertakking in gebruikersreizen.
- Samenwerken met REST API-services in uw aangepaste verificatiegebruikerstrajecten.
- Federatie met identiteitsproviders die voldoen aan het OpenIDConnect-protocol.
- Federatie met identiteitsproviders die zich houden aan het SAML 2.0-protocol.

## <a name="responsibilities-of-custom-policy-feature-set-developers"></a>Verantwoordelijkheden van aangepaste beleidsfunctie-set ontwikkelaars

Handmatige beleidsconfiguratie biedt toegang op een lager niveau tot het onderliggende platform van Azure AD B2C en resulteert in het creëren van een uniek vertrouwenskader. De vele mogelijke permutaties van aangepaste identiteitsproviders, vertrouwensrelaties, integraties met externe services en stapsgewijze workflows vereisen een methodische benadering van ontwerp en configuratie.

Ontwikkelaars die de aangepaste beleidsfunctieset gebruiken, moeten zich houden aan de volgende richtlijnen:

- Maak kennis met de configuratietaal van het aangepaste beleid en het sleutel-/geheimenbeheer. Zie [TrustFrameworkPolicy](trustframeworkpolicy.md)voor meer informatie.
- Neem eigenaarschap van scenario's en aangepaste integraties. Documenteer uw werk en informeer uw live siteorganisatie.
- Methodische scenariotests uitvoeren.
- Volg softwareontwikkeling en best practices met minimaal één ontwikkel- en testomgeving en één productieomgeving.
- Blijf op de hoogte van nieuwe ontwikkelingen van de identiteitsaanbieders en diensten waarmee u integreert. Houd bijvoorbeeld wijzigingen in geheimen en geplande en ongeplande wijzigingen in de service bij.
- Stel actieve bewaking in en monitor de responsiviteit van productieomgevingen. Zie [Azure Active Directory B2C: Logboeken verzamelen voor](analytics-with-application-insights.md)meer informatie over integratie met Application Insights.
- Houd contacte-mailadressen actueel in het Azure-abonnement en blijf reageren op de e-mails van het Microsoft Live-site-team.
- Neem tijdig actie wanneer u dit wordt geadviseerd door het Microsoft live-site team.

## <a name="terms-for-features-in-public-preview"></a>Voorwaarden voor functies in openbare preview

- We raden u aan om de openbare preview-functies alleen voor evaluatiedoeleinden te gebruiken.
- Servicelevel agreements (SLA's) zijn niet van toepassing op de openbare preview-functies.
- Ondersteuningsverzoeken voor openbare preview-functies kunnen worden ingediend via reguliere ondersteuningskanalen.

## <a name="features-by-stage-and-known-issues"></a>Functies per fase en bekende problemen

De mogelijkheden van het Aangepaste beleids-/Identity Experience Framework worden voortdurend en snel ontwikkeld. De volgende tabel is een index van functies en beschikbaarheid van onderdelen.

### <a name="identity-providers-tokens-protocols"></a>Identiteitsproviders, tokens, protocollen

| Functie | Ontwikkeling | Preview | Algemene beschikbaarheid | Opmerkingen |
|-------- | :-----------: | :-------: | :--: | ----- |
| IDP-OpenIDConnect |  |  | X | Bijvoorbeeld Google+.  |
| IDP-OAUTH2 |  |  | X | Bijvoorbeeld Facebook.  |
| IDP-OAUTH1 (twitter) |  | X |  | Bijvoorbeeld Twitter. |
| IDP-OAUTH1 (ex-twitter) |  |  |  | Niet ondersteund |
| IDP-SAML |  |   | X | Bijvoorbeeld Salesforce, ADFS. |
| IDP-WSFED | X |  |  |  |
| Relying Party OAUTH1 |  |  |  | Wordt niet ondersteund. |
| Relying Party OAUTH2 |  |  | X |  |
| Relying Party OIDC |  |  | X |  |
| Relying Party SAML |  |X  |  |  |
| Relying Party WSFED | X |  |  |  |
| REST API met basis- en certificaatauth |  |  | X | Bijvoorbeeld Azure Logic Apps. |

### <a name="component-support"></a>Ondersteuning voor onderdelen

| Functie | Ontwikkeling | Preview | Algemene beschikbaarheid | Opmerkingen |
| ------- | :-----------: | :-------: | :--: | ----- |
| Azure-verificatie met meerdere factoren |  |  | X |  |
| Azure Active Directory als lokale map |  |  | X |  |
| Azure-e-mailsubsysteem voor e-mailverificatie |  |  | X |  |
| Ondersteuning voor meerdere talen|  |  | X |  |
| Predicaatvalidaties |  |  | X | Bijvoorbeeld wachtwoordcomplexiteit. |
| Externe e-mailserviceproviders gebruiken |  |X  |  |  |

### <a name="content-definition"></a>Inhoudsdefinitie

| Functie | Ontwikkeling | Preview | Algemene beschikbaarheid | Opmerkingen |
| ------- | :-----------: | :-------: | :--: | ----- |
| Foutpagina, api.error |  |  | X |  |
| IDP-selectiepagina, api.idpselecties |  |  | X |  |
| IDP-selectie voor aanmelding, api.idpselections.signup |  |  | X |  |
| Wachtwoord vergeten, api.localaccountpasswordreset |  |  | X |  |
| Aanmelding voor lokaal account, api.localaccountsignin |  |  | X |  |
| Aanmelden voor lokaal account, api.localaccountsignup |  |  | X |  |
| MFA-pagina, api.phonefactor |  |  | X |  |
| Self-asserted social account sign-up, api.selfasserted |  |  | X |  |
| Self-asserted profile update, api.selfasserted.profileupdate |  |  | X |  |
| Uniforme aanmeldings- of aanmeldingspagina, api.signuporsignin, met parameter "disableSignup" |  |  | X |  |
| JavaScript / Pagina-indeling |  | X |  |  |

### <a name="app-ief-integration"></a>App-IEF-integratie

| Functie | Ontwikkeling | Preview | Algemene beschikbaarheid | Opmerkingen |
| ------- | :-----------: | :-------: | :--: | ----- |
| Parameter querytekenreeks domain_hint |  |  | X | Beschikbaar als claim, kan worden doorgegeven aan IDP. |
| Parameter querytekenreeks login_hint |  |  | X | Beschikbaar als claim, kan worden doorgegeven aan IDP. |
| Json invoegen in UserJourney via client_assertion | X |  |  | Zal worden afgeschaft. |
| Json invoegen in UserJourney als id_token_hint |  | X |  | Voorwaartse benadering om JSON te passeren. |
| IDP-token doorgeven aan de toepassing |  | X |  | Bijvoorbeeld van Facebook naar app. |

### <a name="session-management"></a>Sessiebeheer

| Functie | Ontwikkeling | Preview | Algemene beschikbaarheid | Opmerkingen |
| ------- | :-----------: | :-------: | :--: | ----- |
| SSO-sessieprovider |  |  | X |  |
| Externe inlogsessieprovider |  |  | X |  |
| SAML SSO-sessieprovider |  |  | X |  |
| Standaard SSO-sessieprovider |  |  | X |  |

### <a name="security"></a>Beveiliging

| Functie | Ontwikkeling | Preview | Algemene beschikbaarheid | Opmerkingen |
|-------- | :-----------: | :-------: | :--: | ----- |
| Beleidssleutels- Genereren, handmatig, uploaden |  |  | X |  |
| Beleidssleutels- RSA/Cert, Geheimen |  |  | X |  |
| Beleidsupload |  |  | X |  |

### <a name="developer-interface"></a>Ontwikkelaarsinterface

| Functie | Ontwikkeling | Preview | Algemene beschikbaarheid | Opmerkingen |
| ------- | :-----------: | :-------: | :--: | ----- |
| Azure Portal-IEF UX |  |  | X |  |
| UserJourney-logboeken van Application Insights |  | X |  | Wordt gebruikt voor het oplossen van problemen tijdens de ontwikkeling.  |
| Eventlogs voor Application Insights (via orkestratiestappen) |  | X |  | Wordt gebruikt om gebruikersstromen in productie te controleren. |

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [aangepast beleid en de verschillen met gebruikersstromen](custom-policy-overview.md).
