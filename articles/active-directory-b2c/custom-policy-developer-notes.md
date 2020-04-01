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
ms.date: 03/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 403ca480bcf0743d81e375c122c888db96bbf543
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80408705"
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


### <a name="protocols-and-authorization-flows"></a>Protocollen en autorisatiestromen

| Functie | Ontwikkeling | Preview | Algemene beschikbaarheid | Opmerkingen |
|-------- | :-----------: | :-------: | :--: | ----- |
| [OAuth2-autorisatiecode](authorization-code-flow.md) |  |  | X |  |
| OAuth2-autorisatiecode met PKCE |  |  | X | Alleen mobiele toepassingen  |
| [Impliciete stroom oAuth2](implicit-flow-single-page-application.md) |  |  | X |  |
| [Wachtwoordreferenties voor oAuth2-bronnen](ropc-custom.md) |  | X |  |  |
| [OIDC Connect](openid-connect.md) |  |  | X |  |
| [SAML2 SAML2](connect-with-saml-service-providers.md)  |  |X  |  | POST en Redirect bindingen. |
| OAuth1 |  |  |  | Wordt niet ondersteund. |
| WSFED | X |  |  |  |

### <a name="identify-providers-federation"></a>Providers federatie identificeren 

| Functie | Ontwikkeling | Preview | Algemene beschikbaarheid | Opmerkingen |
|-------- | :-----------: | :-------: | :--: | ----- |
| [OpenID Connect](openid-connect-technical-profile.md) |  |  | X | Bijvoorbeeld Google+.  |
| [OAuth2](oauth2-technical-profile.md) |  |  | X | Bijvoorbeeld Facebook.  |
| [OAuth1](oauth1-technical-profile.md) |  | X |  | Bijvoorbeeld Twitter. |
| [SAML2 SAML2](saml-technical-profile.md) |  |   | X | Bijvoorbeeld Salesforce, ADFS. |
| WSFED| X |  |  |  |


### <a name="rest-api-integration"></a>REST API-integratie

| Functie | Ontwikkeling | Preview | Algemene beschikbaarheid | Opmerkingen |
|-------- | :-----------: | :-------: | :--: | ----- |
| [REST API met basisauth](secure-rest-api.md#http-basic-authentication) |  |  | X |  |
| [REST API met clientcertificaat auth](secure-rest-api.md#https-client-certificate-authentication) |  |  | X |  |
| [REST API met OAuth2 drager auth](secure-rest-api.md#oauth2-bearer-authentication) |  | X |  |  |

### <a name="component-support"></a>Componentondersteuning

| Functie | Ontwikkeling | Preview | Algemene beschikbaarheid | Opmerkingen |
| ------- | :-----------: | :-------: | :--: | ----- |
| [Verificatie van telefoonfactor](phone-factor-technical-profile.md) |  |  | X |  |
| [Azure MFA-verificatie](multi-factor-auth-technical-profile.md) |  | X |  |  |
| [Eenmalig wachtwoord](one-time-password-technical-profile.md) |  | X |  |  |
| [Azure Active Directory](active-directory-technical-profile.md) als lokale map |  |  | X |  |
| Azure-e-mailsubsysteem voor e-mailverificatie |  |  | X |  |
| [Externe e-mailserviceproviders](custom-email.md) |  |X  |  |  |
| [Ondersteuning voor meerdere talen](localization.md)|  |  | X |  |
| [Predicaatvalidaties](predicates.md) |  |  | X | Bijvoorbeeld wachtwoordcomplexiteit. |
| [Besturingselementen weergeven](display-controls.md) |  |X  |  |  |


### <a name="page-layout-versions"></a>Versie van pagina-indeling

| Functie | Ontwikkeling | Preview | Algemene beschikbaarheid | Opmerkingen |
| ------- | :-----------: | :-------: | :--: | ----- |
| [2.0.0](page-layout.md#200) |  | X |  |  |
| [1.2.0](page-layout.md#120) |  | X |  |  |
| [1.1.0](page-layout.md#110) |  |  | X |  |
| [1.0.0](page-layout.md#100) |  |  | X |  |
| [JavaScript-ondersteuning](javascript-samples.md) |  | X |  |  |

### <a name="app-ief-integration"></a>App-IEF-integratie

| Functie | Ontwikkeling | Preview | Algemene beschikbaarheid | Opmerkingen |
| ------- | :-----------: | :-------: | :--: | ----- |
| Querytekenreeks, parameter`domain_hint` |  |  | X | Beschikbaar als claim, kan worden doorgegeven aan IDP. |
| Querytekenreeks, parameter`login_hint` |  |  | X | Beschikbaar als claim, kan worden doorgegeven aan IDP. |
| Json invoegen in gebruikersreis via`client_assertion` | X |  |  | Zal worden afgeschaft. |
| JSON invoegen in gebruikersreis als`id_token_hint` |  | X |  | Voorwaartse benadering om JSON te passeren. |
| [Token van identiteitsprovider doorgeven aan de toepassing](idp-pass-through-custom.md) |  | X |  | Bijvoorbeeld van Facebook naar app. |

### <a name="session-management"></a>Sessiebeheer

| Functie | Ontwikkeling | Preview | Algemene beschikbaarheid | Opmerkingen |
| ------- | :-----------: | :-------: | :--: | ----- |
| [Standaard SSO-sessieprovider](custom-policy-reference-sso.md#defaultssosessionprovider) |  |  | X |  |
| [Externe inlogsessieprovider](custom-policy-reference-sso.md#externalloginssosessionprovider) |  |  | X |  |
| [SAML SSO-sessieprovider](custom-policy-reference-sso.md#samlssosessionprovider) |  |  | X |  |


### <a name="security"></a>Beveiliging

| Functie | Ontwikkeling | Preview | Algemene beschikbaarheid | Opmerkingen |
|-------- | :-----------: | :-------: | :--: | ----- |
| Beleidssleutels- Genereren, handmatig, uploaden |  |  | X |  |
| Beleidssleutels- RSA/Cert, Geheimen |  |  | X |  |


### <a name="developer-interface"></a>Ontwikkelaarsinterface

| Functie | Ontwikkeling | Preview | Algemene beschikbaarheid | Opmerkingen |
| ------- | :-----------: | :-------: | :--: | ----- |
| Azure Portal-IEF UX |  |  | X |  |
| Beleidsupload |  |  | X |  |
| [Gebruikersreislogboeken van Application Insights](troubleshoot-with-application-insights.md) |  | X |  | Wordt gebruikt voor het oplossen van problemen tijdens de ontwikkeling.  |
| [Gebeurtenislogboeken voor Application Insights](application-insights-technical-profile.md) |  | X |  | Wordt gebruikt om gebruikersstromen in productie te controleren. |


## <a name="next-steps"></a>Volgende stappen

- De [Microsoft Graph-bewerkingen controleren die beschikbaar zijn voor Azure AD B2C](microsoft-graph-operations.md)
- Meer informatie over [aangepast beleid en de verschillen met gebruikersstromen](custom-policy-overview.md).
