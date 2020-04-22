---
title: Aangepast Azure Directory B2C-beleid | Microsoft Documenten
description: Meer informatie over het aangepaste Azure Directory B2C-beleid.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: f18f44208b97ab5bc8d9cd9ff01d604c62deb963
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678163"
---
# <a name="custom-policies-in-azure-active-directory-b2c"></a>Aangepast beleid in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Aangepaste beleidsregels zijn configuratiebestanden die het gedrag van uw Azure Active Directory B2C -tenant (Azure AD B2C) definiëren. Gebruikersstromen zijn vooraf gedefinieerd in de Azure AD B2C-portal voor de meest voorkomende identiteitstaken. Aangepaste beleidsregels kunnen volledig worden bewerkt door een identiteitsontwikkelaar om veel verschillende taken uit te voeren.

## <a name="comparing-user-flows-and-custom-policies"></a>Gebruikersstromen en aangepaste beleidsregels vergelijken

| | Gebruikersstromen | Aangepast beleid |
|-|-------------------|-----------------|
| Doelgebruikers | Alle applicatie-ontwikkelaars met of zonder identiteitsexpertise. | Identiteitsprofessionals, systeemintegratoren, consultants en interne identiteitsteams. Ze zijn comfortabel met OpenID Connect-stromen en begrijpen identiteitsproviders en op claims gebaseerde verificatie. |
| Configuratiemethode | Azure-portal met een gebruiksvriendelijke gebruikersinterface (UI). | XML-bestanden rechtstreeks bewerken en vervolgens uploaden naar de Azure-portal. |
| Aanpassing van de gebruikersinterface | Volledige aanpassing van de gebruikersinterface, inclusief HTML, CSS en JavaScript.<br><br>Ondersteuning voor meerdere talen met aangepaste tekenreeksen. | Hetzelfde |
| Aanpassing van kenmerk | Standaard- en aangepaste kenmerken. | Hetzelfde |
| Token- en sessiebeheer | Aangepaste token- en meerdere sessieopties. | Hetzelfde |
| Id-providers | Vooraf gedefinieerde lokale of sociale provider en de meeste OIDC-identiteitsproviders, zoals federatie met Azure Active Directory-tenants. | Oidc, OAUTH en SAML op basis van standaarden.  Authenticatie is ook mogelijk door integratie met REST API's. |
| Identiteitstaken | Meld je aan of meld je aan bij lokale of veel sociale accounts.<br><br>Self-service wachtwoord opnieuw instellen.<br><br>Profielbewerken.<br><br>Multi-Factor Authenticatie.<br><br>Tokens en sessies aanpassen.<br><br>Toegang tot tokenstromen. | Voltooi dezelfde taken als gebruikersstromen met behulp van aangepaste identiteitsproviders of gebruik aangepaste scopes.<br><br>Een gebruikersaccount inrichten in een ander systeem op het moment van registratie.<br><br>Stuur een welkomstmail via uw eigen e-mailserviceprovider.<br><br>Gebruik een gebruikersarchief buiten Azure AD B2C.<br><br>Valideer de door de gebruiker verstrekte informatie met een vertrouwd systeem met behulp van een API. |

## <a name="policy-files"></a>Beleidsbestanden

Deze drie typen beleidsbestanden worden gebruikt:

- **Basisbestand** - bevat de meeste definities. Het wordt aanbevolen om een minimum aantal wijzigingen aan te brengen in dit bestand om te helpen bij het oplossen van problemen en het op lange termijn onderhouden van uw beleid.
- **Extensiesbestand** - bevat de unieke configuratiewijzigingen voor uw tenant.
- **Relying Party (RP) bestand** - De enkele taak-gericht bestand dat rechtstreeks wordt aangeroepen door de toepassing of dienst (ook wel bekend als een Relying Party). Elke unieke taak vereist zijn eigen RP en afhankelijk van de merkvereisten kan het aantal 'totaal aantal toepassingen x totaal aantal use cases' zijn.

Gebruikersstromen in Azure AD B2C volgen het hierboven afgebeelde bestandspatroon, maar de ontwikkelaar ziet alleen het RP-bestand, terwijl de Azure-portal wijzigingen aanbrengt in het extensiebestand.

Hoewel er drie soorten beleidsbestanden zijn, bent u niet beperkt tot slechts drie bestanden. U meerdere bestanden van elk bestandstype hebben. Als u bijvoorbeeld geen wijzigingen wilt aanbrengen in uw extensiesbestand, u een Extensions2-bestand maken om het bestand Extensies verder uit te breiden.

## <a name="custom-policy-core-concepts"></a>Kernconcepten op maat beleid

De CIAM-service (Customer Identity and Access Management) in Azure omvat:

- Een gebruikersmap die toegankelijk is met Microsoft Graph en die gebruikersgegevens bevat voor zowel lokale accounts als federatieve accounts.
- Toegang tot het **Identity Experience Framework** dat vertrouwen tussen gebruikers en entiteiten orkestreert en claims tussen hen doorgeeft om een identiteits- of toegangsbeheertaak te voltooien.
- Een beveiligingstokenservice (STS) die ID-tokens uitgeeft, tokens vernieuwt en toegangstokens (en gelijkwaardige SAML-beweringen) en deze valideert om bronnen te beschermen.

Azure AD B2C werkt samen met identiteitsproviders, gebruikers, andere systemen en met de lokale gebruikersmap om een identiteitstaak te bereiken. Meld u bijvoorbeeld aan bij een gebruiker, registreer een nieuwe gebruiker of stel een wachtwoord opnieuw in. Het Identity Experience Framework en een beleid (ook wel een gebruikersreis of een vertrouwenskaderbeleid genoemd) stelt vertrouwensrelatie van meerdere partijen vast en definieert expliciet de actoren, de acties, de protocollen en de volgorde van de stappen die moeten worden voltooid.

Het Identity Experience Framework is een volledig configureerbaar, beleidsgestuurd, cloudgebaseerd Azure-platform dat vertrouwen orkestreert tussen entiteiten in standaardprotocolformaten zoals OpenID Connect, OAuth, SAML en een paar niet-standaardplatformen, bijvoorbeeld REST API-gebaseerde systeem-naar-systeem claims uitwisselingen. Het framework creëert gebruiksvriendelijke, witgelabelde ervaringen die HTML en CSS ondersteunen.

Een aangepast beleid bestaat uit een of meer XML-bestanden die in een hiërarchische keten naar elkaar verwijzen. De XML-elementen definiëren onder andere het claimschema, claimstransformaties, inhoudsdefinities, claimproviders, technische profielen en orchestration-stappen voor gebruikersreizen. Een aangepast beleid is toegankelijk als een of meer XML-bestanden die worden uitgevoerd door het Identity Experience Framework wanneer het wordt aangeroepen door een relying party. Ontwikkelaars die aangepaste beleidsregels configureren, moeten de vertrouwde relaties zorgvuldig definiëren om metagegevenseindpunten, exacte definities van claimsuitwisseling en geheimen, sleutels en certificaten configureren als dat nodig is voor elke identiteitsprovider.

### <a name="inheritance-model"></a>Overervingsmodel

Wanneer een toepassing het RP-beleidsbestand aanroept, voegt het Identity Experience Framework in Azure AD B2C alle elementen uit het basisbestand, uit het extensiesbestand en vervolgens uit het RP-beleidsbestand toe om het huidige beleid samen te stellen.  Elementen van hetzelfde type en dezelfde naam in het RP-bestand zullen deze in de extensies overschrijven en extensies overschrijven basis.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Aan de slag met aangepaste beleidsregels](custom-policy-get-started.md)
