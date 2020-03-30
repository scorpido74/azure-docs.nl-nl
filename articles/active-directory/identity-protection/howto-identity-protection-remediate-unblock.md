---
title: Risico's herstellen en gebruikers deblokkeren in Azure AD-identiteitsbeveiliging
description: Meer informatie over de opties die u hebt voor bijna actieve risicodetecties.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 350e7b37d36be70cea345db52cdfb639b2f1c1a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74382100"
---
# <a name="remediate-risks-and-unblock-users"></a>Risico's herstellen en gebruikers deblokkeren

Na het voltooien van uw [onderzoek,](howto-identity-protection-investigate-risk.md)wilt u actie ondernemen om het risico te verwerkeren of gebruikers te deblokkeren. Organisaties hebben ook de mogelijkheid om geautomatiseerde herstelmogelijk te maken met behulp van hun [risicobeleid.](howto-identity-protection-configure-risk-policies.md) Organisaties moeten proberen om alle risicodetecties die ze worden gepresenteerd met in een periode van uw organisatie is comfortabel met te sluiten. Microsoft raadt aan om gebeurtenissen zo snel mogelijk te sluiten, omdat de tijd belangrijk is bij het werken met risico's.

## <a name="remediation"></a>Herstel

Alle actieve risicodetecties dragen bij aan de berekening van een waarde die gebruikersrisiconiveau wordt genoemd. Het gebruikersrisiconiveau is een indicator (laag, gemiddeld, hoog) voor de waarschijnlijkheid dat een account is gecompromitteerd. Als beheerder wilt u alle risicodetecties sluiten, zodat de getroffen gebruikers geen risico's meer lopen.

Sommige risicodetecties kunnen worden gemarkeerd door Identiteitsbescherming als "Gesloten (systeem)" omdat de gebeurtenissen niet langer als riskant werden beschouwd.

Beheerders hebben de volgende opties om te herstellen:

- Zelfherstel met risicobeleid
- Handmatige wachtwoordreset
- Gebruikersrisico's afwijzen
- Individuele risicodetecties handmatig sluiten

### <a name="self-remediation-with-risk-policy"></a>Zelfherstel met risicobeleid

Als u gebruikers toestaat om zichzelf te herstellen, kunnen ze met Azure Multi-Factor Authentication (MFA) en selfservice password reset (SSPR) in uw risicobeleid zichzelf deblokkeren wanneer risico's worden gedetecteerd. Deze detecties worden vervolgens als gesloten beschouwd. Gebruikers moeten zich eerder hebben geregistreerd voor Azure MFA en SSPR om te kunnen gebruiken wanneer risico's worden gedetecteerd.

Sommige detecties verhogen mogelijk geen risico tot het niveau waarop een gebruiker zelfherstel nodig zou zijn, maar beheerders moeten deze detecties nog steeds evalueren. Beheerders kunnen bepalen dat aanvullende maatregelen nodig zijn, zoals [het blokkeren van de toegang vanaf locaties](../conditional-access/howto-conditional-access-policy-location.md) of het verlagen van het aanvaardbare risico in hun beleid.

### <a name="manual-password-reset"></a>Handmatige wachtwoordreset

Als het vereisen van een wachtwoordreset met behulp van een gebruikersrisicobeleid geen optie is, kunnen beheerders alle risicodetecties voor een gebruiker sluiten met een handmatige wachtwoordreset.

Beheerders krijgen twee opties bij het opnieuw instellen van een wachtwoord voor hun gebruikers:

- **Een tijdelijk wachtwoord genereren** - Door een tijdelijk wachtwoord te genereren, u een identiteit onmiddellijk in een veilige staat brengen. Deze methode vereist contact opnemen met de getroffen gebruikers omdat ze moeten weten wat het tijdelijke wachtwoord is. Omdat het wachtwoord tijdelijk is, wordt de gebruiker gevraagd om het wachtwoord te wijzigen in iets nieuws tijdens de volgende aanmelding.

- **Verplicht de gebruiker om het wachtwoord opnieuw in te stellen** - De gebruikers verplichten om wachtwoorden opnieuw in te stellen, maakt zelfherstel mogelijk zonder contact op te nemen met de helpdesk of een beheerder. Deze methode is alleen van toepassing op gebruikers die zijn geregistreerd voor Azure MFA en SSPR. Voor gebruikers die niet zijn geregistreerd, is deze optie niet beschikbaar.

### <a name="dismiss-user-risk"></a>Gebruikersrisico's afwijzen

Als een wachtwoordreset geen optie voor u is, omdat bijvoorbeeld de gebruiker is verwijderd, u ervoor kiezen om gebruikersrisicodetecties te verwijderen.

Wanneer u op **Gebruikersrisico afwijzen**klikt, worden alle gebeurtenissen gesloten en loopt de betrokken gebruiker geen risico meer. Omdat deze methode echter geen invloed heeft op het bestaande wachtwoord, brengt deze de gerelateerde identiteit niet terug in een veilige staat. 

### <a name="close-individual-risk-detections-manually"></a>Individuele risicodetecties handmatig sluiten

U individuele risicodetecties handmatig afsluiten. Door risicodetecties handmatig te sluiten, u het risiconiveau van de gebruiker verlagen. Risicodetecties worden meestal handmatig gesloten als reactie op een gerelateerd onderzoek. Bijvoorbeeld, wanneer het spreken aan een gebruiker aantoont dat een actieve risicoopsporing niet meer vereist is. 
 
Wanneer u risicodetecties handmatig afsluit, u ervoor kiezen een van de volgende acties uit te voeren om de status van een risicodetectie te wijzigen:

- Gebruiker gecompromitteerd bevestigen
- Gebruikersrisico's afwijzen
- Aanmelden veilig bevestigen
- Aanmelden sluiten bevestigen

## <a name="unblocking-users"></a>Gebruikers deblokkeren

Een beheerder kan ervoor kiezen om een aanmelding te blokkeren op basis van zijn of haar risicobeleid of -onderzoeken. Er kan een blok optreden op basis van aanmeldings- of gebruikersrisico.

### <a name="unblocking-based-on-user-risk"></a>Deblokkering op basis van gebruikersrisico opheffen

Als u een geblokkeerd account wilt deblokkeren vanwege gebruikersrisico, hebben beheerders de volgende opties:

1. **Wachtwoord opnieuw instellen** : u het wachtwoord van de gebruiker opnieuw instellen.
1. **Gebruikersrisico's afwijzen** - Het gebruikersrisicobeleid blokkeert een gebruiker als het geconfigureerde gebruikersrisiconiveau voor het blokkeren van de toegang is bereikt. U het risiconiveau van een gebruiker verlagen door gebruikersrisico's te verwijderen of gerapporteerde risicodetecties handmatig te sluiten.
1. **De gebruiker uitsluiten van beleid** - Als u denkt dat de huidige configuratie van uw aanmeldingsbeleid problemen veroorzaakt voor specifieke gebruikers, u de gebruikers hiervan uitsluiten. Zie voor meer informatie de sectie Uitsluitingen in het artikel [Hoe u: risicobeleid configureren en inschakelen.](howto-identity-protection-configure-risk-policies.md#exclusions)
1. **Beleid uitschakelen** : als u denkt dat uw beleidsconfiguratie problemen veroorzaakt voor al uw gebruikers, u het beleid uitschakelen. Zie voor meer informatie het artikel [Hoe u: risicobeleid configureren en inschakelen .](howto-identity-protection-configure-risk-policies.md)

### <a name="unblocking-based-on-sign-in-risk"></a>Deblokkering op basis van aanmeldingsrisico opheffen

Beheerders hebben de volgende opties om een account te deblokkeren op basis van aanmeldingsrisico's:

1. **Aanmelden vanaf een vertrouwde locatie of apparaat** - Een veelvoorkomende reden voor geblokkeerde verdachte aanmeldingen zijn aanmeldingspogingen vanaf onbekende locaties of apparaten. Uw gebruikers kunnen snel bepalen of deze reden de blokkeringsreden is door te proberen zich aan te melden vanaf een vertrouwde locatie of apparaat.
1. **De gebruiker uitsluiten van beleid** - Als u denkt dat de huidige configuratie van uw aanmeldingsbeleid problemen veroorzaakt voor specifieke gebruikers, u de gebruikers hiervan uitsluiten. Zie voor meer informatie de sectie Uitsluitingen in het artikel [Hoe u: risicobeleid configureren en inschakelen.](howto-identity-protection-configure-risk-policies.md#exclusions)
1. **Beleid uitschakelen** : als u denkt dat uw beleidsconfiguratie problemen veroorzaakt voor al uw gebruikers, u het beleid uitschakelen. Zie voor meer informatie het artikel [Hoe u: risicobeleid configureren en inschakelen .](howto-identity-protection-configure-risk-policies.md)

## <a name="next-steps"></a>Volgende stappen

Zie het overzicht Azure AD-identiteitsbeveiliging voor een overzicht van Azure AD-identiteitsbeveiliging voor een overzicht van Azure [AD-identiteitsbeveiliging.](overview-identity-protection.md)
