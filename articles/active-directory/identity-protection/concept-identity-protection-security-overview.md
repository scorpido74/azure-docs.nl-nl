---
title: Overzicht van de beveiliging van Azure Active Directory-beveiliging
description: Ontdek hoe het overzicht Beveiliging u inzicht geeft in de beveiligingshouding van uw organisatie.
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
ms.openlocfilehash: a9550684ad154f28a02ee347fd0a79c1ec286beb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74382185"
---
# <a name="azure-active-directory-identity-protection---security-overview"></a>Azure Active Directory Identity Protection - Overzicht van beveiliging

Het [beveiligingsoverzicht](https://aka.ms/IdentityProtectionRefresh) in de Azure-portal geeft u inzicht in de beveiligingshouding van uw organisatie. Het helpt bij het identificeren van potentiële aanvallen en het begrijpen van de effectiviteit van uw beleid.

Het 'Beveiligingsoverzicht' is grofweg verdeeld in twee secties:

- Trends, aan de linkerkant, bieden een tijdlijn van risico's in uw organisatie.
- Tegels, aan de rechterkant, markeer de belangrijkste lopende problemen in uw organisatie en stel voor hoe u snel actie ondernemen.

![Beveiligingsoverzicht](./media/concept-identity-protection-security-overview/01.png)
  
## <a name="trends"></a>Trends

### <a name="new-risky-users-detected"></a>Nieuwe riskante gebruikers gedetecteerd

Deze grafiek toont het aantal nieuwe riskante gebruikers dat is gedetecteerd gedurende de gekozen periode. U de weergave van deze grafiek filteren op het risiconiveau van de gebruiker (laag, gemiddeld, hoog). Plaats de plaats boven de utc-datumstappen om het aantal riskante gebruikers te zien dat voor die dag is gedetecteerd. Een klik op deze grafiek brengt u naar het rapport 'Riskante gebruikers'. Als u gebruikers wilt herstellen die risico lopen, u overwegen hun wachtwoord te wijzigen.

### <a name="new-risky-sign-ins-detected"></a>Nieuwe risicovolle aanmeldingen gedetecteerd

Deze grafiek toont het aantal risicovolle aanmeldingen dat is gedetecteerd gedurende de gekozen periode. U de weergave van deze grafiek filteren op het aanmeldingsrisicotype (real-time of aggregaat) en het aanmeldingsrisiconiveau (laag, gemiddeld, hoog). Onbeschermde aanmeldingen zijn succesvolle real-time risicoaanmeldingen die niet werden uitgedaagd door MFA. (Opmerking: Aanmeldingen die riskant zijn vanwege offline detecties, kunnen niet in realtime worden beschermd door aanmeldingsrisicobeleid). Plaats de plaats boven de UTC-datumstappen om het aantal aanmeldingen te zien dat voor die dag wordt gedetecteerd. Een klik op deze grafiek brengt u naar het rapport 'Riskante aanmeldingen'.

## <a name="tiles"></a>Tegels
 
### <a name="high-risk-users"></a>Gebruikers met een hoog risico

De tegel 'Gebruikers met een hoog risico' toont het laatste aantal gebruikers met een grote kans op identiteitscompromis. Deze moeten een topprioriteit zijn voor onderzoek. Een klik op de tegel 'Gebruikers met een hoog risico' wordt omgeleid naar een gefilterde weergave van het rapport 'Riskante gebruikers' met alleen gebruikers met een hoog risiconiveau. Met dit rapport u meer informatie en deze gebruikers herstellen met een wachtwoordreset.

![Beveiligingsoverzicht](./media/concept-identity-protection-security-overview/02.png)

### <a name="medium-risk-users"></a>Gebruikers met een gemiddeld risico
De tegel 'Gebruikers met een gemiddeld risico' toont het laatste aantal gebruikers met een gemiddelde kans op identiteitscompromis. Een klik op de tegel 'Gebruikers met een gemiddeld risico' wordt omgeleid naar een gefilterde weergave van het rapport 'Riskante gebruikers' met alleen gebruikers met een risiconiveau van medium. Met behulp van dit rapport u deze gebruikers verder onderzoeken en herstellen.

### <a name="unprotected-risky-sign-ins"></a>Onbeschermde risicovolle aanmeldingen

De tegel 'Onbeschermde risicovolle aanmeldingen' toont de telling van succesvolle, realtime risicovolle aanmeldingen van vorige week die niet zijn geblokkeerd of MFA die is aangevochten door een beleid voor voorwaardelijke toegang, het risicobeleid voor identiteitsbescherming of mfa per gebruiker. Dit zijn mogelijk gecompromitteerde aanmeldingen die succesvol waren en niet MFA uitgedaagd. Om dergelijke aanmeldingen in de toekomst te beschermen, past u een aanmeldingsrisicobeleid toe. Met een klik op de tegel 'Onbeschermde riskante aanmeldingen' wordt omgeleid naar het configuratieblad voor aanmeldingsbeleid, waar u het aanmeldingsrisicobeleid configureren om MFA te vereisen op een aanmelding met een opgegeven risiconiveau.

### <a name="legacy-authentication"></a>Verouderde verificatie

De tegel 'Legacy-verificatie' toont het aantal oudere verificaties van vorige week in uw organisatie. Verouderde verificatieprotocollen bieden geen ondersteuning voor moderne beveiligingsmethoden zoals een MFA. Als u verouderde verificatie wilt voorkomen, u een beleid voor voorwaardelijke toegang toepassen. Met een klik op de tegel 'Legacy authentication' wordt u doorverwezen naar de 'Identity Secure Score'.

### <a name="identity-secure-score"></a>Identiteitsbeveiligde score

De Identity Secure Score meet en vergelijkt uw beveiligingshouding met branchepatronen. Als u op de tegel 'Identity Secure Score (Preview)' klikt, wordt deze omgeleid naar het mes 'Identity Secure Score' waar u meer te weten komen over het verbeteren van uw beveiligingshouding.

## <a name="next-steps"></a>Volgende stappen

- [Wat is risico](concept-identity-protection-risks.md)

- [Beleid beschikbaar om risico's te beperken](concept-identity-protection-policies.md)
