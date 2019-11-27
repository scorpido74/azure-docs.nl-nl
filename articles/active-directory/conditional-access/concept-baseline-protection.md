---
title: Basis beleid voor voorwaardelijke toegang-Azure Active Directory
description: Beleid voor voorwaardelijke toegang basis lijn voor de beveiliging van organisaties tegen algemene aanvallen
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9bb384045c8b2e0a5743fdc301a829792639b7e
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420556"
---
# <a name="what-are-baseline-policies"></a>Wat zijn basislijn beleidsregels?

Basislijn beleid is een set vooraf gedefinieerde beleids regels waarmee organisaties worden beschermd tegen veel voorkomende aanvallen. Deze veelvoorkomende aanvallen kunnen wacht woord-spray, herhaling en phishing bevatten. Basislijn beleid is beschikbaar in alle versies van Azure AD. Micro soft maakt deze basis beveiligings beleidsregels voor iedereen beschikbaar, omdat aanvallen op basis van de identiteit in de afgelopen jaren zijn ontstaan. Het doel van deze vier beleids regels is om ervoor te zorgen dat alle organisaties een basis niveau van beveiliging hebben ingeschakeld zonder extra kosten.  

Voor het beheren van het aangepaste beleid voor voorwaardelijke toegang is een Azure AD Premium-licentie vereist.

## <a name="baseline-policies"></a>Basislijnbeleid

![Basis beleid voor voorwaardelijke toegang in de Azure Portal](./media/concept-baseline-protection/conditional-access-policies.png)

Er zijn vier beleids regels voor de basis lijn:

* MFA vereisen voor beheerders (preview-versie)
* Beveiliging van eind gebruikers (preview-versie)
* Verouderde verificatie blok keren (preview-versie)
* MFA vereisen voor Service beheer (preview-versie)

Deze vier van deze beleids regels zijn van invloed op oudere verificatie stromen, zoals POP-, IMAP-en oudere bureau blad-clients.

### <a name="require-mfa-for-admins-preview"></a>MFA vereisen voor beheerders (preview-versie)

Als gevolg van de kracht en toegang die beheerders accounts hebben, moet u deze met speciale zorg behandelen. Een gemeen schappelijke methode voor het verbeteren van de beveiliging van geprivilegieerde accounts is om een sterkere vorm van account verificatie te vereisen wanneer deze worden gebruikt om u aan te melden. In Azure Active Directory kunt u een sterkere account verificatie verkrijgen door te vereisen dat beheerders zich registreren voor en Azure Multi-Factor Authentication gebruiken.

MFA vereisen voor beheerders (preview) is een basislijn beleid waarvoor multi-factor Authentication (MFA) is vereist voor de volgende Directory rollen, gezien als de meest privilegede Azure AD-rollen:

* Globale beheerder
* SharePoint-beheerder
* Exchange-beheerder
* Beheerder van voorwaardelijke toegang
* Beveiligingsbeheerder
* Helpdesk beheerder/wachtwoord beheerder
* Factureringsbeheerder
* Gebruikers beheerder

Als uw organisatie deze accounts in gebruik heeft in scripts of code, kunt u overwegen deze te vervangen door [beheerde identiteiten](../managed-identities-azure-resources/overview.md).

### <a name="end-user-protection-preview"></a>Beveiliging van eind gebruikers (preview-versie)

Beheerders met een hoge bevoegdheid zijn niet de enige die zijn gericht op aanvallen. Ongeldige actors richten zich vaak op op normale gebruikers. Na het verkrijgen van de toegang, kunnen deze beschadigde actors namens de houder van het oorspronkelijke account toegang tot bevoegde informatie vragen of de volledige directory downloaden en een phishing-aanval uitvoeren op uw hele organisatie. Een gemeen schappelijke methode voor het verbeteren van de beveiliging van alle gebruikers is het vereisen van een sterkere vorm van account verificatie wanneer een Risk ante aanmelding wordt gedetecteerd.

**Beveiliging van eind gebruikers (preview)** is een basislijn beleid waarmee alle gebruikers in een directory worden beveiligd. Als u dit beleid inschakelt, moeten alle gebruikers binnen 14 dagen worden geregistreerd voor Azure Multi-Factor Authentication. Zodra de registratie is geregistreerd, worden gebruikers alleen om MFA gevraagd tijdens het aanmelden met een Risk ante poging. Gebruikers accounts die zijn aangetast, worden geblokkeerd tot het opnieuw instellen van het wacht woord en het risico zijn afgebroken. 

[!NOTE]
Gebruikers die eerder zijn gemarkeerd voor risico, worden geblokkeerd tot het opnieuw instellen van het wacht woord en het risico op beleids activering.

### <a name="block-legacy-authentication-preview"></a>Verouderde verificatie blok keren (preview-versie)

Verouderde verificatie protocollen (bijvoorbeeld: IMAP, SMTP, POP3) zijn protocollen die doorgaans door oudere e-mailclients worden gebruikt om te verifiëren. Verouderde protocollen bieden geen ondersteuning voor multi-factor Authentication. Zelfs als u een beleid hebt waarvoor multi-factor Authentication voor uw Directory vereist is, kan een ongeldige actor worden geverifieerd met een van deze verouderde protocollen en multi-factor Authentication overs Laan.

De beste manier om uw account te beschermen tegen kwaad aardige verificatie aanvragen door verouderde protocollen, is ze te blok keren.

Het basis beleid **voor het blok keren van verouderde verificatie (preview)** blokkeert verificatie aanvragen die zijn gemaakt met behulp van verouderde protocollen. Moderne authenticatie moet worden gebruikt om u aan te melden voor alle gebruikers. In combi natie met de andere basislijn beleids regels worden aanvragen die afkomstig zijn van verouderde protocollen geblokkeerd. Daarnaast moeten alle gebruikers op elk gewenst moment MFA opgeven. Met dit beleid wordt Exchange ActiveSync niet geblokkeerd.

### <a name="require-mfa-for-service-management-preview"></a>MFA vereisen voor Service beheer (preview-versie)

Organisaties gebruiken verschillende Azure-Services en beheren ze via Azure Resource Manager op basis van hulpprogram ma's zoals:

* Azure-portal
* Azure PowerShell
* Azure-CLI

Het gebruik van een van deze hulpprogram ma's voor het uitvoeren van resource beheer is een zeer geprivilegieerde actie. Deze hulpprogram ma's kunnen configuratie-brede configuraties wijzigen, zoals service-instellingen en abonnements facturering.

Voor het beveiligen van geprivilegieerde acties moet voor het beleid **MFA voor Service Management (preview-versie)** multi-factor Authentication worden vereist voor gebruikers die toegang hebben tot Azure Portal, Azure PowerShell of Azure cli.

## <a name="next-steps"></a>Volgende stappen

Ga voor meer informatie naar:

* [Algemeen beleid voor voorwaardelijke toegang](concept-conditional-access-policy-common.md)
* [Vijf stappen om uw identiteitsinfrastructuur te beveiligen](../../security/fundamentals/steps-secure-identity.md)
* [Wat is voorwaardelijke toegang in Azure Active Directory?](overview.md)
