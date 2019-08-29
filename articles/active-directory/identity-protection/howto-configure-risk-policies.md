---
title: Risico beleid configureren in Azure Active Directory identiteits beveiliging (vernieuwd) | Microsoft Docs
description: Risico beleid configureren in Azure Active Directory identiteits beveiliging (vernieuwd).
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: f244c28b99c429fef5641bb4fc399e09fd451069
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70126561"
---
# <a name="how-to-configure-risk-policies-in-azure-active-directory-identity-protection-refreshed"></a>Procedure: Risico beleid configureren in Azure Active Directory identiteits beveiliging (vernieuwd)

Azure AD detecteert risico detecties die indica toren voor mogelijk verdachte identiteiten zijn. Door risico beleid te configureren, kunt u automatische antwoorden op de detectie resultaten definiëren:

- Met het beleid voor aanmeldings Risico's kunt u een reactie configureren op real-time risico detecties die zijn gedetecteerd tijdens het aanmelden van een gebruiker. 
- Met het beleid voor gebruikers Risico's kunt u een reactie configureren op alle actieve gebruikers Risico's die gedurende een bepaalde periode zijn gedetecteerd voor een gebruiker.  

> [!VIDEO https://www.youtube.com/embed/zEsbbik-BTE]

## <a name="what-is-the-sign-in-risk-policy"></a>Wat is het aanmeldings risico beleid?

Azure AD analyseert elke aanmelding van een gebruiker. Het doel van de analyse is het detecteren van verdachte acties die samen met de aanmelding worden uitgevoerd. Is de aanmelding bijvoorbeeld voltooid met een anoniem IP-adres of is de aanmelding gestart vanaf een onbekende locatie? In azure AD worden de verdachte acties die door het systeem kunnen worden gedetecteerd ook wel risico detecties genoemd. Op basis van de risico detecties die zijn gedetecteerd tijdens een aanmelding, berekent Azure AD een waarde. De waarde vertegenwoordigt de kans (laag, gemiddeld, hoog) dat de aanmelding niet wordt uitgevoerd door de rechtmatige gebruiker. De kans is het **risico niveau voor het aanmelden**.

Het beleid voor aanmeldings Risico's is een geautomatiseerd antwoord dat u kunt configureren voor een specifiek aanmeldings risico niveau. In uw antwoord kunt u de toegang tot uw resources blok keren of vereisen dat een MFA-uitdaging (multi-factor Authentication) wordt door gegeven om toegang te krijgen.

Wanneer een gebruiker een MFA-prompt heeft uitgevoerd die wordt geactiveerd door het beleid voor aanmeldings Risico's, geeft deze feedback aan identiteits beveiliging dat de aanmelding afkomstig is van de legitieme gebruiker. De detectie van het aanmeldings risico dat de MFA-prompt heeft geactiveerd, wordt dan automatisch gesloten en de identiteits beveiliging zorgt ervoor dat deze gebeurtenis niet kan bijdragen aan de verhoging van het risico van gebruikers. Het inschakelen van het beleid voor aanmeldings Risico's kan noisiness in de weer gave Risk ante aanmeldingen verminderen door gebruikers toe te staan zichzelf op te lossen wanneer u wordt gevraagd MFA op te geven en vervolgens automatisch de bijbehorende Risk ante aanmelding af te sluiten.

## <a name="how-do-i-access-the-sign-in-risk-policy"></a>Hoe kan ik toegang tot het beleid voor aanmeldings Risico's?
   
Het beleid voor aanmeldings Risico's vindt u in de sectie **configureren** op de [pagina Azure AD Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy).
   
![Beleid voor aanmeldings Risico's](./media/howto-configure-risk-policies/1014.png "Beleid voor aanmeldings Risico's")

## <a name="sign-in-risk-policy-settings"></a>Beleids instellingen voor aanmeldings risico

Wanneer u het beleid voor aanmeldings Risico's configureert, moet u het volgende instellen:

- De gebruikers en groepen waarop het beleid van toepassing is:

   ![Gebruikers en groepen](./media/howto-configure-risk-policies/11.png)

- Het niveau van het aanmeldings risico waarmee het beleid wordt geactiveerd:

   ![Risico niveau voor aanmelden](./media/howto-configure-risk-policies/12.png)

- Het type toegang dat u wilt afdwingen wanneer aan uw aanmeldings risico niveau is voldaan:  

   ![Toegang](./media/howto-configure-risk-policies/13.png)

- De status van het beleid:

   ![Beleid afdwingen](./media/howto-configure-risk-policies/14.png)

In het dialoog venster beleids configuratie kunt u de gevolgen van het opnieuw configureren schatten.

![Verwachte impact](./media/howto-configure-risk-policies/15.png)

## <a name="what-you-should-know-about-sign-in-risk-policies"></a>Wat u moet weten over beleids regels voor aanmeldings Risico's

U kunt een beveiligings beleid voor aanmeldings Risico's configureren om MFA te vereisen:

![MFA vereisen](./media/howto-configure-risk-policies/16.png)

Uit veiligheids overwegingen werkt deze instelling echter alleen voor gebruikers die al zijn geregistreerd voor MFA. Identiteits beveiliging blokkeert gebruikers met een MFA-vereiste als ze nog niet zijn geregistreerd voor MFA.

Als u MFA wilt vereisen voor Risk ante aanmeldingen, moet u het volgende doen:

1. Schakel het registratie beleid voor multi-factor Authentication in voor de betrokken gebruikers.
2. Vereisen dat de betrokken gebruikers zich aanmelden met een niet-Risk ante sessie om een MFA-registratie uit te voeren.

Door deze stappen uit te voeren, zorgt u ervoor dat multi-factor Authentication is vereist voor een Risk ante aanmelding.

Het beleid voor aanmeldings Risico's is:

- Wordt toegepast op alle browser verkeer en-aanmeldingen met moderne verificatie.
- Niet toegepast op toepassingen met oudere beveiligings protocollen door het WS-Trust-eind punt uit te scha kelen bij de federatieve IDP, zoals ADFS.

Zie voor een overzicht van de gerelateerde gebruikers ervaring:

* [Risk ante aanmeld herstel](flows.md#risky-sign-in-recovery)
* [Risk ante aanmelding geblokkeerd](flows.md#risky-sign-in-blocked)  
* [Aanmeld ervaring met Azure AD Identity Protection](flows.md)  

## <a name="what-is-a-user-risk-policy"></a>Wat is een gebruikers risico beleid?

Azure AD analyseert elke aanmelding van een gebruiker. Het doel van de analyse is het detecteren van verdachte acties die samen met de aanmelding worden uitgevoerd. In azure AD worden de verdachte acties die door het systeem kunnen worden gedetecteerd ook wel risico detecties genoemd. Hoewel sommige risico detecties in realtime kunnen worden gedetecteerd, zijn er ook risico detecties die meer tijd vereisen. Als u bijvoorbeeld een onmogelijke reis naar ongewone locaties wilt detecteren, is voor het systeem een eerste leer periode van 14 dagen vereist om meer te weten te komen over het normale gedrag van een gebruiker. Er zijn verschillende opties voor het oplossen van gedetecteerde risico detecties. U kunt bijvoorbeeld afzonderlijke risico detecties hand matig oplossen of u kunt ze omzetten met behulp van een aanmeldings risico of een beleid voor voorwaardelijke toegang voor gebruikers Risico's.

Alle risico detecties die zijn gedetecteerd voor een gebruiker en niet zijn opgelost, worden actieve risico detecties genoemd. De actieve risico detecties die aan een gebruiker zijn gekoppeld, worden gebruikers risico genoemd. Op basis van het gebruikers risico berekent Azure AD een waarschijnlijkheid (laag, gemiddeld, hoog) die een gebruiker heeft geknoeid. De kans is het gebruikers risico niveau.

![Gebruikers Risico's](./media/howto-configure-risk-policies/11031.png)

Het beleid voor gebruikers Risico's is een geautomatiseerd antwoord dat u kunt configureren voor een specifiek gebruikers risico niveau. Met een gebruikers risico beleid kunt u de toegang tot uw resources blok keren of een wachtwoord wijziging vereisen om een gebruikers account weer in een schone staat te krijgen.

## <a name="how-do-i-access-the-user-risk-policy"></a>Hoe kan ik toegang tot het gebruikers risico beleid?
   
Het beleid voor gebruikers Risico's bevindt zich in de sectie **configureren** op de [pagina Azure AD Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy).
   
![Beleid voor gebruikersrisico's](./media/howto-configure-risk-policies/11014.png)

## <a name="user-risk-policy-settings"></a>Beleids instellingen voor gebruikers risico

Wanneer u het beleid voor gebruikers Risico's configureert, moet u het volgende instellen:

- De gebruikers en groepen waarop het beleid van toepassing is:

   ![Gebruikers en groepen](./media/howto-configure-risk-policies/111.png)

- Het niveau van het aanmeldings risico waarmee het beleid wordt geactiveerd:

   ![Niveau van gebruikersrisico](./media/howto-configure-risk-policies/112.png)

- Het type toegang dat u wilt afdwingen wanneer aan uw aanmeldings risico niveau is voldaan:  

   ![Toegang](./media/howto-configure-risk-policies/113.png)

- De status van het beleid:

   ![Beleid afdwingen](./media/howto-configure-risk-policies/114.png)

Het dialoog venster beleids configuratie biedt u een optie om de impact van uw configuratie te schatten.

![Verwachte impact](./media/howto-configure-risk-policies/115.png)

## <a name="what-you-should-know-about-user-risk-polices"></a>Wat u moet weten over gebruikers risico beleid

U kunt een beveiligings beleid voor gebruikers Risico's instellen om gebruikers te blok keren bij het aanmelden, afhankelijk van het risico niveau.

![Blokkeren](./media/howto-configure-risk-policies/116.png)

Een aanmelding blok keren:

* Hiermee wordt voor komen dat nieuwe gebruikers risico detecties worden gegenereerd voor de betrokken gebruiker
* Hiermee kunnen beheerders hand matig de risico detecties herstellen die van invloed zijn op de identiteit van de gebruiker en deze herstellen naar een veilige status

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

 [Channel 9: Azure AD en identiteits weergave: Preview van identiteits beveiliging](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)
