---
title: Het beleid voor gebruikers Risico's configureren in Azure Active Directory Identity Protection | Microsoft Docs
description: Meer informatie over het configureren van het beleid voor Azure AD Identity Protection gebruikers risico.
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
ms.openlocfilehash: 92bfb921833d99a3538ffa8c4c5d16a9f0cd3acd
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70126290"
---
# <a name="how-to-configure-the-user-risk-policy"></a>Procedure: Het beleid voor gebruikersrisico’s configureren

Met het gebruikers risico detecteert Azure AD de waarschijnlijkheid dat een gebruikers account is aangetast. Als beheerder kunt u een beleid voor voorwaardelijke toegang voor gebruikers Risico's configureren, zodat automatisch wordt gereageerd op een specifiek risico niveau van de gebruiker.
 
In dit artikel vindt u de informatie die u nodig hebt om een beleid voor gebruikers Risico's te configureren.

## <a name="what-is-a-user-risk-policy"></a>Wat is een gebruikers risico beleid?

Azure AD analyseert elke aanmelding van een gebruiker. Het doel van de analyse is het detecteren van verdachte acties die samen met de aanmelding worden uitgevoerd. In azure AD worden de verdachte acties die door het systeem kunnen worden gedetecteerd ook wel risico detecties genoemd. Hoewel sommige risico detecties in realtime kunnen worden gedetecteerd, zijn er ook risico detecties die meer tijd vereisen. Als u bijvoorbeeld een onmogelijke reis naar ongewone locaties wilt detecteren, is voor het systeem een eerste leer periode van 14 dagen vereist om meer te weten te komen over het normale gedrag van een gebruiker. Er zijn verschillende opties voor het oplossen van gedetecteerde risico detecties. U kunt bijvoorbeeld afzonderlijke risico detecties hand matig oplossen of u kunt ze omzetten met behulp van een aanmeldings risico of een beleid voor voorwaardelijke toegang voor gebruikers Risico's.

Alle risico detecties die zijn gedetecteerd voor een gebruiker en niet zijn opgelost, worden actieve risico detecties genoemd. De actieve risico detecties die aan een gebruiker zijn gekoppeld, worden gebruikers risico genoemd. Op basis van het gebruikers risico berekent Azure AD een waarschijnlijkheid (laag, gemiddeld, hoog) die een gebruiker heeft geknoeid. De kans is het gebruikers risico niveau.

![Gebruikers Risico's](./media/howto-user-risk-policy/1031.png)

Het beleid voor gebruikers Risico's is een geautomatiseerd antwoord dat u kunt configureren voor een specifiek gebruikers risico niveau. Met een gebruikers risico beleid kunt u de toegang tot uw resources blok keren of een wachtwoord wijziging vereisen om een gebruikers account weer in een schone staat te krijgen.

## <a name="how-do-i-access-the-user-risk-policy"></a>Hoe kan ik toegang tot het gebruikers risico beleid?
   
Het beleid voor aanmeldings Risico's vindt u in de sectie **configureren** op de [pagina Azure AD Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy).
   
![Beleid voor gebruikersrisico's](./media/howto-user-risk-policy/1014.png)

## <a name="policy-settings"></a>Beleidsinstellingen

Wanneer u het beleid voor aanmeldings Risico's configureert, moet u het volgende instellen:

- De gebruikers en groepen waarop het beleid van toepassing is:

    ![Gebruikers en groepen](./media/howto-user-risk-policy/11.png)

- Het niveau van het aanmeldings risico waarmee het beleid wordt geactiveerd:

    ![Niveau van gebruikersrisico](./media/howto-user-risk-policy/12.png)

- Het type toegang dat u wilt afdwingen wanneer aan uw aanmeldings risico niveau is voldaan:  

    ![Toegang](./media/howto-user-risk-policy/13.png)

- De status van het beleid:

    ![Beleid afdwingen](./media/howto-user-risk-policy/14.png)

Het dialoog venster beleids configuratie biedt u een optie om de impact van uw configuratie te schatten.

![Verwachte impact](./media/howto-user-risk-policy/15.png)

## <a name="what-you-should-know"></a>Wat u moet weten

U kunt een beveiligings beleid voor gebruikers Risico's instellen om gebruikers te blok keren bij het aanmelden, afhankelijk van het risico niveau.

![Blokkeren](./media/howto-user-risk-policy/16.png)

Een aanmelding blok keren:

* Hiermee wordt voor komen dat nieuwe gebruikers risico detecties worden gegenereerd voor de betrokken gebruiker
* Hiermee kunnen beheerders hand matig de risico detecties herstellen die van invloed zijn op de identiteit van de gebruiker en deze herstellen naar een veilige status

## <a name="best-practices"></a>Aanbevolen procedures

Het kiezen van een **hoge** drempel waarde vermindert het aantal keren dat een beleid wordt geactiveerd en minimaliseert de gevolgen voor gebruikers.
Het biedt echter geen **kleine** en **gemiddelde** gebruikers die zijn gemarkeerd voor risico van het beleid, die mogelijk geen beveiligde identiteiten of apparaten beveiligen die eerder zijn vermoed of waarvan bekend is dat ze worden aangetast.

Wanneer het beleid wordt ingesteld,

* Gebruikers uitsluiten die waarschijnlijk veel valse-positieven genereren (ontwikkel aars, beveiligings analisten)
* Gebruikers in landen uitsluiten waarbij het inschakelen van het beleid niet praktisch is (bijvoorbeeld geen toegang tot de Help Desk)
* Gebruik een **hoge** drempel waarde tijdens de eerste implementatie van het beleid of als u de uitdagingen die door eind gebruikers moeten worden beperkt, wilt minimaliseren.
* Gebruik een **lage** drempel waarde als uw organisatie betere beveiliging vereist. Als u een **lage** drempel waarde selecteert, worden extra aanmeldings uitdagingen voor gebruikers geïntroduceerd, maar een betere beveiliging.

De aanbevolen standaard instelling voor de meeste organisaties is het configureren van een regel voor een **gemiddelde** drempel waarde om een evenwicht tussen de bruikbaarheid en de beveiliging te halen.

Zie voor een overzicht van de gerelateerde gebruikers ervaring:

* [Beschadigde account herstel stroom](flows.md#compromised-account-recovery).  
* [Geblokkeerde stroom van beschadigd account](flows.md#compromised-account-blocked).  

**Het dialoog venster gerelateerde configuratie openen**:

- Klik op de Blade **Azure AD Identity Protection** in de sectie **configureren** op **beleid voor gebruikers Risico's**.

    ![Beleid voor gebruikers Risico's](./media/howto-user-risk-policy/1009.png "Beleid voor gebruikers Risico's")

## <a name="next-steps"></a>Volgende stappen

Zie overzicht van de [Azure AD Identity Protection](overview.md)voor een overzicht van Azure AD Identity Protection.
