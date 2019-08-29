---
title: Het beleid voor aanmeldings Risico's configureren in Azure Active Directory Identity Protection | Microsoft Docs
description: Meer informatie over het configureren van het beleid voor Azure AD Identity Protection-aanmeldings Risico's.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: d00376c6689b6be773f24e8acd09c3697fb6a799
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70126299"
---
# <a name="how-to-configure-the-sign-in-risk-policy"></a>Procedure: Het beleid voor aanmeldingsrisico’s configureren

Azure Active Directory detecteert [typen risico detectie](../reports-monitoring/concept-risk-events.md#risk-detection-types) in realtime en offline. Elk risico detectie dat is gedetecteerd voor een aanmelding van een gebruiker draagt bij aan een logisch concept met de naam Risk ante aanmelding. Een Risk ante aanmelding is een indicator voor een aanmeldings poging die mogelijk niet is uitgevoerd door de rechtmatige eigenaar van een gebruikers account.

## <a name="what-is-the-sign-in-risk-policy"></a>Wat is het aanmeldings risico beleid?

Azure AD analyseert elke aanmelding van een gebruiker. Het doel van de analyse is het detecteren van verdachte acties die samen met de aanmelding worden uitgevoerd. Is de aanmelding bijvoorbeeld voltooid met een anoniem IP-adres of is de aanmelding gestart vanaf een onbekende locatie? In azure AD worden de verdachte acties die door het systeem kunnen worden gedetecteerd ook wel risico detecties genoemd. Op basis van de risico detecties die zijn gedetecteerd tijdens een aanmelding, berekent Azure AD een waarde. De waarde vertegenwoordigt de kans (laag, gemiddeld, hoog) dat de aanmelding niet wordt uitgevoerd door de rechtmatige gebruiker. De kans is het **risico niveau voor het aanmelden**.

Het beleid voor aanmeldings Risico's is een geautomatiseerd antwoord dat u kunt configureren voor een specifiek aanmeldings risico niveau. In uw antwoord kunt u de toegang tot uw resources blok keren of vereisen dat een MFA-uitdaging (multi-factor Authentication) wordt door gegeven om toegang te krijgen.
   
## <a name="how-do-i-access-the-sign-in-risk-policy"></a>Hoe kan ik toegang tot het beleid voor aanmeldings Risico's?
   
Het beleid voor aanmeldings Risico's vindt u in de sectie **configureren** op de [pagina Azure AD Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy).
   
![Beleid voor aanmeldings Risico's](./media/howto-sign-in-risk-policy/1014.png "Beleid voor aanmeldings Risico's")

## <a name="policy-settings"></a>Beleidsinstellingen

Wanneer u het beleid voor aanmeldings Risico's configureert, moet u het volgende instellen:

- De gebruikers en groepen waarop het beleid van toepassing is:

    ![Gebruikers en groepen](./media/howto-sign-in-risk-policy/11.png)

- Het niveau van het aanmeldings risico waarmee het beleid wordt geactiveerd:

    ![Risico niveau voor aanmelden](./media/howto-sign-in-risk-policy/12.png)

- Het type toegang dat u wilt afdwingen wanneer aan uw aanmeldings risico niveau is voldaan:  

    ![Toegang](./media/howto-sign-in-risk-policy/13.png)

- De status van het beleid:

    ![Beleid afdwingen](./media/howto-sign-in-risk-policy/14.png)

In het dialoog venster beleids configuratie kunt u de gevolgen van het opnieuw configureren schatten.

![Verwachte impact](./media/howto-sign-in-risk-policy/15.png)

## <a name="what-you-should-know"></a>Wat u moet weten

U kunt een beveiligings beleid voor aanmeldings Risico's configureren om MFA te vereisen:

![MFA vereisen](./media/howto-sign-in-risk-policy/16.png)

Uit veiligheids overwegingen werkt deze instelling echter alleen voor gebruikers die al zijn geregistreerd voor MFA. Identiteits beveiliging blokkeert gebruikers met een MFA-vereiste als ze nog niet zijn geregistreerd voor MFA.

Als u MFA wilt vereisen voor Risk ante aanmeldingen, moet u het volgende doen:

1. Schakel het [registratie beleid voor multi-factor Authentication](howto-mfa-policy.md) in voor de betrokken gebruikers.
2. Vereisen dat de betrokken gebruikers zich aanmelden bij een niet-Risk ante sessie om een MFA-registratie uit te voeren.

Door deze stappen uit te voeren, zorgt u ervoor dat multi-factor Authentication is vereist voor een Risk ante aanmelding.

Het beleid voor aanmeldings Risico's is:

- Wordt toegepast op alle browser verkeer en-aanmeldingen met moderne verificatie.
- Niet toegepast op toepassingen met oudere beveiligings protocollen door het WS-Trust-eind punt uit te scha kelen bij de federatieve IDP, zoals ADFS.

Zie voor een overzicht van de gerelateerde gebruikers ervaring:

* [Risk ante aanmeld herstel](flows.md#risky-sign-in-recovery)
* [Risk ante aanmelding geblokkeerd](flows.md#risky-sign-in-blocked)  
* [Aanmeld ervaring met Azure AD Identity Protection](flows.md)  

## <a name="best-practices"></a>Aanbevolen procedures

Het kiezen van een **hoge** drempel waarde vermindert het aantal keren dat een beleid wordt geactiveerd en minimaliseert de gevolgen voor gebruikers.  

Er zijn echter **lage** en **middel grote** aanmeldingen uitgesloten die zijn gemarkeerd voor risico van het beleid, waardoor een aanvaller niet kan worden misbruikt van een aangetaste identiteit.

Wanneer het beleid wordt ingesteld,

- Gebruikers uitsluiten waarvoor multi-factor Authentication niet/niet kan worden uitgevoerd
- Gebruikers in landen uitsluiten waarbij het inschakelen van het beleid niet praktisch is (bijvoorbeeld geen toegang tot de Help Desk)
- Gebruikers uitsluiten die waarschijnlijk veel valse-positieven genereren (ontwikkel aars, beveiligings analisten)
- Gebruik een **hoge** drempel waarde tijdens het initialiseren van het beleid of als u de uitdagingen moet minimaliseren die door eind gebruikers worden gezien.
- Gebruik een **lage** drempel waarde als uw organisatie betere beveiliging vereist. Als u een **lage** drempel waarde selecteert, worden extra aanmeldings uitdagingen voor gebruikers geïntroduceerd, maar een betere beveiliging.

De aanbevolen standaard instelling voor de meeste organisaties is het configureren van een regel voor een **gemiddelde** drempel waarde om een evenwicht tussen de bruikbaarheid en de beveiliging te halen.

## <a name="next-steps"></a>Volgende stappen

Zie overzicht van de [Azure AD Identity Protection](overview.md)voor een overzicht van Azure AD Identity Protection.
