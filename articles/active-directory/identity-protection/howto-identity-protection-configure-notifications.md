---
title: Azure Active Directory Identity Protection meldingen
description: Meer informatie over hoe meldingen uw onderzoeksactiviteiten ondersteunen.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 06/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 291afbdd902c7563e47595132d56b354dab28a3a
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/27/2020
ms.locfileid: "88950388"
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Azure Active Directory Identity Protection meldingen

Azure AD Identity Protection verzendt twee typen e-mail berichten voor automatische meldingen om u te helpen bij het beheren van risico-en risico detecties van gebruikers:

- Gebruikers met een risico voor een gedetecteerd e-mail bericht
- E-mail met wekelijkse samen vatting

In dit artikel vindt u een overzicht van beide e-mail meldingen.

## <a name="users-at-risk-detected-email"></a>Gebruikers met een risico voor een gedetecteerd e-mail bericht

Als reactie op een gedetecteerd account dat risico loopt, genereert Azure AD Identity Protection een e-mail waarschuwing met **gebruikers die risico lopen** als onderwerp. Het e-mail bericht bevat een koppeling naar de gebruikers die zijn **[gemarkeerd voor een risico](./overview-identity-protection.md)** rapport. Als best practice moet u onmiddellijk de gebruikers op risico onderzoeken.

Met de configuratie voor deze waarschuwing kunt u opgeven op welk gebruikers risico niveau u de waarschuwing wilt genereren. Het e-mail adres wordt gegenereerd wanneer het risico niveau van de gebruiker de opgegeven waarde bereikt. Als u het beleid bijvoorbeeld instelt op waarschuwing bij gemiddeld gebruikers risico en de Score van de gebruiker van John van de gebruiker wordt verplaatst naar het gemiddeld risico als gevolg van een realtime-aanmeldings risico, ontvangt u de gebruikers die risico lopen. Als de gebruiker daaropvolgende risico detecties heeft die ervoor zorgen dat de berekening van het gebruikers risico niveau het opgegeven risico niveau is (of hoger), ontvangt u een extra gebruiker tegen risico gedetecteerde e-mail berichten wanneer de risico Score van de gebruiker opnieuw wordt berekend. Als een gebruiker bijvoorbeeld op 1 januari op gemiddeld risico wordt verplaatst, ontvangt u een e-mail melding als uw instellingen zijn ingesteld op waarschuwing over gemiddeld risico. Als dezelfde gebruiker vervolgens een andere risico detectie heeft op 5 januari die ook een gemiddeld risico is, en de risico Score van de gebruiker opnieuw wordt berekend en nog steeds gemiddeld is, ontvangt u een e-mail melding. 

Er wordt echter alleen een extra e-mail melding verzonden als het tijdstip waarop de risico detectie is opgetreden (die de wijziging in het risico niveau van gebruikers heeft veroorzaakt) recenter is dan wanneer de laatste e-mail is verzonden. Bijvoorbeeld: een gebruiker aanmeldingen op 1 januari om 5 uur en er is geen realtime risico (betekent dat er geen e-mail bericht zou worden gegenereerd als gevolg van die aanmelding). Tien minuten later meldt dezelfde gebruiker 5:10 zich opnieuw aan en heeft dit een hoog realtime risico, waardoor het risico niveau van de gebruiker wordt overgezet naar hoog en een e-mail bericht dat wordt verzonden. Vervolgens wordt bij 5:15 uur de offline risico score voor de oorspronkelijke aanmelding bij 5 AM gewijzigd in een hoog risico als gevolg van een offline risico verwerking. Een extra gemarkeerde gebruiker voor de risico-e-mail wordt niet verzonden, omdat het tijdstip van de eerste aanmelding vóór de tweede aanmelding was die al een e-mail melding heeft geactiveerd.

Als u een overbelasting van e-mails wilt voor komen, ontvangt u binnen een periode van vijf seconden slechts één gebruiker met een risico per seconde. Dit betekent dat als meerdere gebruikers tijdens dezelfde periode van vijf seconden naar het gespecificeerde risico niveau worden verplaatst, één e-mail bericht wordt geaggregeerd en verzonden om de wijziging van het risico niveau voor al hen te vertegenwoordigen.

![Gebruikers met een risico voor een gedetecteerd e-mail bericht](./media/howto-identity-protection-configure-notifications/01.png)

### <a name="configure-users-at-risk-detected-alerts"></a>Gebruikers configureren met gedetecteerde risico waarschuwingen

Als beheerder kunt u het volgende instellen:

- **Het risico niveau van de gebruiker waarmee het genereren van deze e-mail wordt geactiveerd** : het risico niveau is standaard ingesteld op hoog risico.
- **De ontvangers van dit e-mail bericht** bevatten standaard alle globale beheerders. Globale beheerders kunnen ook andere globale beheerders, beveiligings beheerders en beveiligings lezers toevoegen als ontvangers.
   - U kunt desgewenst **extra e-mail berichten toevoegen om waarschuwings meldingen te ontvangen** . deze functie is een preview-versie en gebruikers die zijn gedefinieerd, moeten de juiste machtigingen hebben om de gekoppelde rapporten in de Azure portal weer te geven.

Configureer het e-mail adres voor gebruikers met een risico op de **Azure Portal** onder **Azure Active Directory**  >  **Security**  >  gebruikers met beveiligings**identiteits beveiliging**van  >  **Risico's gedetecteerde waarschuwingen**.

## <a name="weekly-digest-email"></a>E-mail met wekelijkse samen vatting

Het wekelijkse overzichts bericht bevat een samen vatting van nieuwe risico detecties.  
Het bevat:

- Er zijn nieuwe Risk ante gebruikers gedetecteerd
- Er zijn nieuwe Risk ante aanmeldingen gedetecteerd (in realtime)
- Koppelingen naar de gerelateerde rapporten in identiteits beveiliging

![E-mail met wekelijkse samen vatting](./media/howto-identity-protection-configure-notifications/weekly-digest-email.png)

Ontvangers bevatten standaard alle globale beheerders. Globale beheerders kunnen ook andere globale beheerders, beveiligings beheerders en beveiligings lezers toevoegen als ontvangers.

### <a name="configure-weekly-digest-email"></a>Wekelijks overzicht van e-mail configureren

Als beheerder kunt u een wekelijks overzicht van e-mail verzenden naar of uit en de gebruikers selecteren die zijn toegewezen om het e-mail bericht te ontvangen.

Configureer de week overzichts-e-mail in de **Azure Portal** onder **Azure Active Directory**  >  wekelijkse samen vatting van**beveiligings**  >  **identiteits beveiliging**  >  **Weekly digest**.

## <a name="see-also"></a>Zie ook

- [Azure Active Directory Identity Protection](./overview-identity-protection.md)