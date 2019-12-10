---
title: Opmerkingen voor ontwikkel aars voor aangepast beleid
titleSuffix: Azure AD B2C
description: Opmerkingen voor ontwikkel aars over het configureren en onderhouden van Azure AD B2C met aangepast beleid.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: edbd31434715c380badf15118b0779885aed700f
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2019
ms.locfileid: "74949752"
---
# <a name="developer-notes-for-custom-policies-in-azure-active-directory-b2c"></a>Opmerkingen voor ontwikkel aars voor aangepast beleid in Azure Active Directory B2C

Aangepaste beleids configuratie in Azure Active Directory B2C is nu algemeen beschikbaar. Deze configuratie methode is gericht op geavanceerde identiteits ontwikkelaars die complexe identiteits oplossingen bouwen. Aangepaste beleids regels maken de kracht van het Framework voor identiteits ervaring beschikbaar in Azure AD B2C tenants.
Ontwikkel aars van geavanceerde identiteiten die aangepaste beleids regels gebruiken, moeten plannen dat ze enige tijd investeren bij het volt ooien van instructies en het lezen van referentie documenten.

Hoewel de meeste aangepaste beleids opties beschikbaar zijn nu algemeen beschikbaar zijn, zijn er onderliggende mogelijkheden, zoals technische profiel typen en inhouds definitie-Api's die in verschillende fasen van de levens cyclus van de software voor komen. Er zijn nog veel meer. In de volgende tabel wordt het niveau van de beschik baarheid op een gedetailleerder niveau opgegeven.

## <a name="features-that-are-generally-available"></a>Functies die algemeen beschikbaar zijn

- Gebruik aangepaste beleids regels om gebruikers met aangepaste verificatie te schrijven en te uploaden.
    - Beschrijf de door de gebruiker geplaatste stapsgewijze stap-voor-stap als uitwisseling tussen claim providers.
    - Definieer voorwaardelijke vertakking in de reis van de gebruiker.
- Werk met REST API-services in uw aangepaste gebruikers Reiss voor verificatie.
- Communiceren met id-providers die compatibel zijn met het OpenIDConnect-protocol.
- Communiceren met id-providers die voldoen aan het SAML 2,0-protocol.

## <a name="responsibilities-of-custom-policy-feature-set-developers"></a>Verantwoordelijkheden van de aangepaste beleids functie-ontwikkel aars instellen

Hand matige beleids configuratie verleent toegang op lager niveau tot het onderliggende platform van Azure AD B2C en resulteert in het maken van een uniek vertrouwens raamwerk. De vele mogelijke permutaties van aangepaste ID-providers, vertrouwens relaties, integraties met externe services en stap-voor-stap-werk stromen vereisen een methode om te ontwerpen en te configureren.

Ontwikkel aars die de aangepaste beleids functie set gebruiken, moeten voldoen aan de volgende richt lijnen:

- Vertrouwd raken met de configuratie taal van het aangepaste beleid en het beheer van sleutel/geheimen. Zie [TrustFrameworkPolicy](trustframeworkpolicy.md)voor meer informatie.
- Eigenaar worden van scenario's en aangepaste integraties. Documenteer uw werk en laat uw organisatie van uw live site op de hoogte.
- Voer methode testen uit.
- Volg de aanbevolen procedures voor software ontwikkeling en fase ring met mini maal één ontwikkel-en test omgeving en één productie omgeving.
- Blijf op de hoogte van nieuwe ontwikkelingen van de id-providers en services die u integreert met. U kunt bijvoorbeeld wijzigingen bijhouden in geheimen en de geplande en ongeplande wijzigingen in de service.
- Stel actieve bewaking in en bewaak de reactie snelheid van productie omgevingen. Zie [Azure Active Directory B2C: Logboeken verzamelen](active-directory-b2c-custom-guide-eventlogger-appins.md)voor meer informatie over de integratie met Application Insights.
- Houd contact-e-mail adressen actueel in het Azure-abonnement en blijf op de hoogte van de e-mail van micro soft live site-team.
- Onderneem tijdige actie wanneer u dit door het team van micro soft live site hebt door gesteld.

## <a name="terms-for-features-in-public-preview"></a>Voor waarden voor functies in open bare preview

- We raden u aan de open bare preview-functies alleen te gebruiken voor evaluatie doeleinden.
- Service Level Agreements (Sla's) gelden niet voor de open bare preview-functies.
- Ondersteunings aanvragen voor open bare preview-functies kunnen worden ingediend via reguliere ondersteunings kanalen.

## <a name="features-by-stage-and-known-issues"></a>Functies per fase en bekende problemen

Mogelijkheden voor aangepast beleid/identiteits ervaring zijn onder constante en snelle ontwikkeling. De volgende tabel bevat een index van functies en beschik baarheid van onderdelen.

### <a name="identity-providers-tokens-protocols"></a>Id-providers, tokens, protocollen

| Functie | Ontwikkeling | Preview | Algemene beschikbaarheid | Opmerkingen |
|-------- | :-----------: | :-------: | :--: | ----- |
| IDP-OpenIDConnect |  |  | X | Bijvoorbeeld Google +.  |
| IDP-OAUTH2 |  |  | X | Bijvoorbeeld Facebook.  |
| IDP-OAUTH1 (Twitter) |  | X |  | Bijvoorbeeld Twitter. |
| IDP-OAUTH1 (ex-Twitter) |  |  |  | Niet ondersteund |
| IDP-SAML |  |   | X | Bijvoorbeeld Sales Force, ADFS. |
| IDP-WSFED | X |  |  |  |
| OAUTH1 Relying Party |  |  |  | Wordt niet ondersteund. |
| OAUTH2 Relying Party |  |  | X |  |
| OIDC Relying Party |  |  | X |  |
| SAML voor Relying Party | X |  |  |  |
| WSFED Relying Party | X |  |  |  |
| REST API met basis-en certificaat verificatie |  |  | X | Bijvoorbeeld Azure Logic Apps. |

### <a name="component-support"></a>Onderdeel ondersteuning

| Functie | Ontwikkeling | Preview | Algemene beschikbaarheid | Opmerkingen |
| ------- | :-----------: | :-------: | :--: | ----- |
| Azure Multi Factor Authentication |  |  | X |  |
| Azure Active Directory als lokale map |  |  | X |  |
| Azure-e-mail subsysteem voor verificatie via e-mail |  |  | X |  |
| Ondersteuning voor meerdere talen|  |  | X |  |
| Validatie van predikaten |  |  | X | Bijvoorbeeld wachtwoord complexiteit. |
| E-mail providers van derden gebruiken | X |  |  |  |

### <a name="content-definition"></a>Inhouds definitie

| Functie | Ontwikkeling | Preview | Algemene beschikbaarheid | Opmerkingen |
| ------- | :-----------: | :-------: | :--: | ----- |
| Fout pagina, API. error |  |  | X |  |
| Pagina IDP Selection, API. idpselections |  |  | X |  |
| IDP selectie voor aanmelding, API. idpselections. signup |  |  | X |  |
| Wacht woord verg eten, API. localaccountpasswordreset |  |  | X |  |
| Aanmelden met een lokaal account, API. localaccountsignin |  |  | X |  |
| Registreren van een lokaal account, API. localaccountsignup |  |  | X |  |
| MFA-pagina, API. Phone factor |  |  | X |  |
| Aanmelden voor een zelf-bevestigde sociale account, API. selfasserted |  |  | X |  |
| Zelfondertekende profiel update, API. selfasserted. profileupdate |  |  | X |  |
| Unified aanmelden of aanmeldings pagina, API. signuporsignin, met para meter "disableSignup" |  |  | X |  |
| Java script/pagina-indeling |  | X |  |  |

### <a name="app-ief-integration"></a>App-IEF-integratie

| Functie | Ontwikkeling | Preview | Algemene beschikbaarheid | Opmerkingen |
| ------- | :-----------: | :-------: | :--: | ----- |
| Query reeks parameter domain_hint |  |  | X | Beschikbaar als claim kan worden door gegeven aan IDP. |
| Query reeks parameter login_hint |  |  | X | Beschikbaar als claim kan worden door gegeven aan IDP. |
| JSON invoegen in UserJourney via client_assertion | X |  |  | Wordt afgeschaft. |
| JSON invoegen in UserJourney als id_token_hint |  | X |  | Go-Forward-benadering voor het door geven van JSON. |
| IDP-TOKEN door geven aan de toepassing |  | X |  | Bijvoorbeeld van Facebook naar app. |

### <a name="session-management"></a>Sessie beheer

| Functie | Ontwikkeling | Preview | Algemene beschikbaarheid | Opmerkingen |
| ------- | :-----------: | :-------: | :--: | ----- |
| SSO-sessie provider |  |  | X |  |
| Externe aanmeldings sessie provider |  |  | X |  |
| SAML SSO-sessie provider |  |  | X |  |
| Standaard-SSO-sessie provider |  |  | X |  |

### <a name="security"></a>Beveiliging

| Functie | Ontwikkeling | Preview | Algemene beschikbaarheid | Opmerkingen |
|-------- | :-----------: | :-------: | :--: | ----- |
| Beleids sleutels: genereren, hand matig, uploaden |  |  | X |  |
| Beleids sleutels-RSA/cert, geheimen |  |  | X |  |
| Beleid uploaden |  |  | X |  |

### <a name="developer-interface"></a>Ontwikkelaars interface

| Functie | Ontwikkeling | Preview | Algemene beschikbaarheid | Opmerkingen |
| ------- | :-----------: | :-------: | :--: | ----- |
| Azure Portal-IEF UX |  |  | X |  |
| Application Insights UserJourney-logboeken |  | X |  | Wordt gebruikt voor het oplossen van problemen tijdens de ontwikkeling.  |
| Application Insights gebeurtenis Logboeken (via Orchestration-stappen) |  | X |  | Wordt gebruikt voor het bewaken van de gebruikers stromen in de productie omgeving. |

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [aangepaste beleids regels en de verschillen met gebruikers stromen](active-directory-b2c-overview-custom.md).
