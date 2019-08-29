---
title: Actieve risico detecties in Azure Active Directory Identity Protection sluiten | Microsoft Docs
description: Meer informatie over de opties die u hebt voor het sluiten van actieve risico detecties.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: d24ec94d0381fc2e79fdef97b6d525b7cec33fef
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70126477"
---
# <a name="how-to-close-active-risk-detections"></a>Procedure: Actieve risico detecties sluiten

Met [risico detecties](../reports-monitoring/concept-risk-events.md)Azure Active Directory detecteert indica toren voor mogelijk verdachte gebruikers accounts. Als beheerder wilt u alle risico detecties sluiten, zodat de betrokken gebruikers niet langer risico lopen.

In dit artikel vindt u een overzicht van de aanvullende opties die u nodig hebt om actieve risico detecties te sluiten.

## <a name="options-to-close-risk-detections"></a>Opties voor het sluiten van risico detecties 

De status van een risico detectie is **actief** of **gesloten**. Alle actieve risico detecties dragen bij aan de berekening van een waarde met de naam gebruikers risico niveau. Het risico niveau van de gebruiker is een indicator (laag, gemiddeld, hoog) voor de kans dat een account is aangetast. 

Als u actieve risico detectie wilt sluiten, hebt u de volgende opties:

- Vereisen dat wacht woord opnieuw wordt ingesteld met een beleid voor gebruikers Risico's
- Hand matig opnieuw instellen van wacht woorden
- Alle risico detecties verwijderen 
- Afzonderlijke risico detecties hand matig sluiten

## <a name="require-password-reset-with-a-user-risk-policy"></a>Vereisen dat wacht woord opnieuw wordt ingesteld met een beleid voor gebruikers Risico's

Door het [beleid voor voorwaardelijke toegang voor gebruikers Risico's](howto-user-risk-policy.md)te configureren, kunt u een wachtwoord wijziging vereisen als een opgegeven gebruikers risico niveau automatisch is gedetecteerd. 

![Wachtwoord opnieuw instellen](./media/howto-close-active-risk-events/13.png)

Bij het opnieuw instellen van een wacht woord worden alle actieve risico gebeurtenissen van de gerelateerde gebruiker gesloten en wordt de identiteit weer ingesteld op een veilige status. Het gebruik van een gebruikers risico beleid is de voorkeurs methode voor het sluiten van actieve risico detecties omdat deze methode is geautomatiseerd. Er is geen interactie vereist tussen de betrokken gebruiker en Help Desk of beheerder.

Het gebruik van een gebruikers risico beleid is echter niet altijd van toepassing. Dit is bijvoorbeeld van toepassing op:

- Gebruikers die niet zijn geregistreerd voor multi-factor Authentication (MFA).
- Gebruikers met actieve risico detecties die zijn verwijderd.
- Een onderzoek dat laat zien dat een gerapporteerde risico detectie is uitgevoerd door de legitieme gebruiker.

## <a name="manual-password-reset"></a>Hand matig opnieuw instellen van wacht woorden

Als het vereisen van een wacht woord opnieuw instellen met behulp van een gebruikers risico beleid geen optie is, kunt u alle risico detecties voor een gebruiker sluiten met hand matig opnieuw instellen van het wacht woord.

![Wachtwoord opnieuw instellen](./media/howto-close-active-risk-events/04.png)

Het gerelateerde dialoog venster biedt twee verschillende methoden om een wacht woord opnieuw in te stellen:

![Wachtwoord opnieuw instellen](./media/howto-close-active-risk-events/05.png)

**Een tijdelijk wacht woord genereren** : door een tijdelijk wacht woord te genereren, kunt u direct een identiteit weer in een veilige staat zetten. Deze methode vereist interactie met de betrokken gebruikers omdat ze moeten weten wat het tijdelijke wacht woord is. U kunt bijvoorbeeld het nieuwe tijdelijke wacht woord verzenden naar een alternatief e-mail adres voor de gebruiker of naar de Manager van de gebruiker. Omdat het wacht woord tijdelijk is, wordt de gebruiker gevraagd het wacht woord te wijzigen tijdens de volgende aanmelding.

**De gebruiker verplichten om wacht woord opnieuw in te stellen** : als u wilt dat gebruikers hun wacht woorden opnieuw moeten instellen, kunnen ze zelf herstel zonder contact opnemen met de Help Desk of een beheerder. Net als in het geval van een beleid voor gebruikers Risico's is deze methode alleen van toepassing op gebruikers die zijn geregistreerd voor MFA. Voor gebruikers die nog niet zijn geregistreerd voor MFA, is deze optie niet beschikbaar.

## <a name="dismiss-all-risk-detections"></a>Alle risico detecties verwijderen

Als het opnieuw instellen van een wacht woord geen optie is, kunt u ook alle risico detecties negeren. 

![Wachtwoord opnieuw instellen](./media/howto-close-active-risk-events/03.png)

Wanneer u op **alle gebeurtenissen negeren**klikt, worden alle gebeurtenissen gesloten en loopt de betrokken gebruiker geen risico meer. Omdat deze methode echter geen invloed heeft op het bestaande wacht woord, wordt de bijbehorende identiteit niet opnieuw in een veilige staat gebracht. De voorkeurs use-case voor deze methode is een verwijderde gebruiker met actieve risico detecties. 

## <a name="close-individual-risk-detections-manually"></a>Afzonderlijke risico detecties hand matig sluiten

U kunt afzonderlijke risico detecties hand matig sluiten. Door risico detecties hand matig te sluiten, kunt u het risico niveau van de gebruiker verlagen. Normaal gesp roken worden risico detecties hand matig gesloten als reactie op een gerelateerd onderzoek. Als er bijvoorbeeld een gesprek met een gebruiker laat zien dat een actieve risico detectie niet meer nodig is. 
 
Wanneer u risico detecties hand matig wilt afsluiten, kunt u een van de volgende acties uitvoeren om de status van een risico detectie te wijzigen:

![Acties](./media/howto-close-active-risk-events/06.png)

- **Oplossen** : als u na het onderzoeken van een risico detectie een passende herstel actie hebt ondernomen, hebt u een andere identiteits beveiliging nodig, en u denkt dat de risico detectie als gesloten moet worden beschouwd, markeert de gebeurtenis als opgelost. Met opgeloste gebeurtenissen wordt de status van de risico detectie ingesteld op gesloten en de risico detectie wordt niet meer aan gebruikers risico bijgedragen.
- **Markeren als onjuist-positief** : in sommige gevallen kunt u een risico detectie onderzoeken en detecteren dat deze onjuist is gemarkeerd als riskant. U kunt het aantal dergelijke gevallen verminderen door de risico detectie als onwaar-positief te markeren. Dit helpt de algoritmen van machine learning om de classificatie van soort gelijke gebeurtenissen in de toekomst te verbeteren. De status van valse gebeurtenissen is om te worden gesloten en er worden geen bijdragen meer aan gebruikers Risico's.
- **Negeren** : als u geen herstel actie hebt ondernomen, maar de risico detectie wilt verwijderen uit de actieve lijst, kunt u een risico detectie negeren en de gebeurtenis status sluiten. Genegeerde gebeurtenissen dragen niet bij aan het gebruikersrisico. Deze optie dient alleen in ongebruikelijke omstandigheden te worden gebruikt.
- **Opnieuw activeren** : risico detecties die hand matig zijn gesloten (door omzetten, fout positief of negeren) kunnen opnieuw worden geactiveerd, waarbij de gebeurtenis status weer wordt ingesteld op actief. De risico detecties worden opnieuw geactiveerd en bijdragen aan de berekening van het risico niveau van de gebruiker. Risico detecties die zijn gesloten via herbemiddeling (zoals een veilig wacht woord opnieuw instellen), kunnen niet opnieuw worden geactiveerd.

## <a name="next-steps"></a>Volgende stappen

Zie overzicht van de [Azure AD Identity Protection](overview.md)voor een overzicht van Azure AD Identity Protection.
