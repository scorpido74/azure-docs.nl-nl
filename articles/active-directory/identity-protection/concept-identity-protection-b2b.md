---
title: Identiteitsbeveiliging en B2B-gebruikers - Azure Active Directory
description: Identiteitsbescherming gebruiken met B2B-gebruikers hoe het werkt en bekende beperkingen
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 95ea7eb470a5880bc88b3df903d33854f363e974
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72881315"
---
# <a name="identity-protection-and-b2b-users"></a>Identiteitsbeveiliging en B2B-gebruikers

Met Azure AD B2B-samenwerking kunnen organisaties op risico's gebaseerd beleid afdwingen voor B2B-gebruikers met behulp van Identiteitsbeveiliging. Deze beleidsregels worden op twee manieren geconfigureerd:

- Beheerders kunnen het ingebouwde beleid voor identiteitsbescherming, dat van toepassing is op alle apps, die gastgebruikers bevatten, configureren.
- Beheerders kunnen hun beleid voor voorwaardelijke toegang configureren, met aanmeldingsrisico als voorwaarde, inclusief gastgebruikers.

## <a name="how-is-risk-evaluated-for-b2b-collaboration-users"></a>Hoe wordt risico's geëvalueerd voor Gebruikers van B2B-samenwerkingsgebruikers

Het gebruikersrisico voor B2B-samenwerkingsgebruikers wordt geëvalueerd in hun thuismap. Het realtime aanmeldingsrisico voor deze gebruikers wordt geëvalueerd in de bronmap wanneer ze toegang proberen te krijgen tot de bron.

## <a name="limitations-of-identity-protection-for-b2b-collaboration-users"></a>Beperkingen van identiteitsbescherming voor Gebruikers van B2B-samenwerking

Er zijn beperkingen in de implementatie van Identity Protection voor B2B-samenwerkingsgebruikers in een bronmap vanwege hun identiteit die in hun thuismap bestaat. De belangrijkste beperkingen zijn als volgt:

- Als een gastgebruiker het gebruikersrisicobeleid voor identiteitsbescherming activeert om het opnieuw instellen van wachtwoorden af te dwingen, **worden deze geblokkeerd.** Dit blok is te wijten aan het onvermogen om wachtwoorden in de bronmap te resetten.
- **Gastgebruikers worden niet weergegeven in het rapport riskante gebruikers.** Dit verlies van zichtbaarheid is te wijten aan de risico-evaluatie die zich in de B2B-gebruiker home directory.
- Beheerders **kunnen een riskante B2B-samenwerkingsgebruiker niet verwijderen of herstellen** in hun bronmap. Dit verlies van functionaliteit is te wijten aan beheerders in de bronmap die geen toegang hebben tot de thuismap van de B2B-gebruiker.

### <a name="why-cant-i-remediate-risky-b2b-collaboration-users-in-my-directory"></a>Waarom kan ik riskante B2B-samenwerkingsgebruikers niet herstellen in mijn directory?

De risicobeoordeling en -herstel voor B2B-gebruikers vindt plaats in hun home directory. Vanwege dit feit worden de gastgebruikers niet weergegeven in het rapport riskante gebruikers in de bronmap en kunnen beheerders in de bronmap geen veilige wachtwoordreset voor deze gebruikers forceren.

### <a name="what-do-i-do-if-a-b2b-collaboration-user-was-blocked-due-to-a-risk-based-policy-in-my-organization"></a>Wat moet ik doen als een B2B-samenwerkingsgebruiker is geblokkeerd vanwege een risicogebaseerd beleid in mijn organisatie?

Als een riskante B2B-gebruiker in uw directory wordt geblokkeerd door uw op risico's gebaseerde beleid, moet de gebruiker dat risico in zijn thuismap herstellen. Gebruikers kunnen hun risico herstellen door een veilige wachtwoordreset uit te voeren in hun home directory. Als ze geen selfservice wachtwoordreset hebben ingeschakeld in hun thuismap, moeten ze contact opnemen met de IT-medewerkers van hun eigen organisatie om een beheerder hun risico handmatig te laten verwijderen of hun wachtwoord opnieuw in te stellen.

### <a name="how-do-i-prevent-b2b-collaboration-users-from-being-impacted-by-risk-based-policies"></a>Hoe voorkom ik dat gebruikers van B2B-samenwerking worden beïnvloed door risicogebaseerd beleid?

Als B2B-gebruikers worden uitgesloten van het op risico's gebaseerde conditional access-beleid van uw organisatie, wordt voorkomen dat B2B-gebruikers worden beïnvloed of geblokkeerd door hun risicobeoordeling. Als u deze B2B-gebruikers wilt uitsluiten, maakt u een groep in Azure AD die alle gastgebruikers van uw organisatie bevat. Voeg deze groep vervolgens toe als uitsluiting voor uw ingebouwde gebruikersrisico- en aanmeldingsrisicobeleid voor identiteitsbescherming, evenals alle beleid voor voorwaardelijke toegang dat aanmeldingsrisico als voorwaarde gebruikt.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen over Azure AD B2B-samenwerking:

- [Wat is Azure AD B2B-samenwerking?](../b2b/what-is-b2b.md)
