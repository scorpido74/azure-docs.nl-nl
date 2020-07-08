---
title: Op gebruikers risico gebaseerde voorwaardelijke toegang-Azure Active Directory
description: Beleid voor voorwaardelijke toegang maken met behulp van gebruikers risico identiteits beveiliging
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 07/02/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: a015f7dd58bae328a8c18e3de3202c0ca615c9cf
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.contentlocale: nl-NL
ms.lasthandoff: 07/07/2020
ms.locfileid: "86034842"
---
# <a name="conditional-access-user-risk-based-conditional-access"></a>Voorwaardelijke toegang: voorwaardelijke toegang op basis van gebruikers risico

Micro soft werkt met onderzoekers, rechts handhaving, verschillende beveiligings teams bij micro soft en andere vertrouwde bronnen om gelekte gebruikers namen en wachtwoord paren te vinden. Organisaties met Azure AD Premium P2-licenties kunnen beleids regels voor voorwaardelijke toegang maken met [Azure AD Identity Protection gebruikers risico detectie](../identity-protection/concept-identity-protection-risks.md#user-risk). 

Er zijn twee locaties waar dit beleid kan worden toegewezen. Organisaties moeten een van de volgende opties kiezen om een beleid voor voorwaardelijke toegang op basis van gebruikers risico in te scha kelen waarvoor een beveiligd wacht woord moet worden gewijzigd.

## <a name="enable-with-conditional-access-policy"></a>Inschakelen met beleid voor voorwaardelijke toegang

1. Meld u aan bij de **Azure Portal** als globale beheerder, beveiligings beheerder of beheerder van de voorwaardelijke toegang.
1. Blader naar **Azure Active Directory**  >  **beveiligings**  >  **voorwaardelijke toegang**.
1. Selecteer **Nieuw beleid**.
1. Geef uw beleid een naam. Het is raadzaam dat organisaties een zinvolle norm maken voor de namen van hun beleid.
1. Onder **Toewijzingen** selecteert u **Gebruikers en groepen**.
   1. Onder **insluiten**selecteert u **alle gebruikers**.
   1. Onder **uitsluiten**selecteert u **gebruikers en groepen** en kiest u de accounts voor nood toegang of het afbreek glas van uw organisatie. 
   1. Selecteer **Voltooid**.
1. Onder **Cloud-apps of acties**  >  **Include**, selecteert u **alle Cloud-apps**.
1. Stel onder **voor waarden**  >  **gebruikers risico** **configureren** in op **Ja**. Onder **gebruikers risico niveaus configureren die nodig zijn om het beleid af te dwingen** , selecteert u **hoog**en selecteert u **gereed**.
1. Onder **toegangs beheer**  >  **toekennen**selecteert u **toegang verlenen**, **wachtwoord wijziging vereisen**en selecteert u **selecteren**.
1. Bevestig de instellingen en stel **beleid inschakelen** in **op aan**.
1. Selecteer **maken** om uw beleid in te stellen.

## <a name="enable-through-identity-protection"></a>Identiteits beveiliging inschakelen

1. Meld u aan bij **Azure Portal**.
1. Selecteer **alle services**en blader naar **Azure AD Identity Protection**.
1. Selecteer **beleid voor gebruikers Risico's**.
1. Onder **toewijzingen**selecteert u **gebruikers**.
   1. Onder **insluiten**selecteert u **alle gebruikers**.
   1. Onder **uitsluiten**selecteert u **uitgesloten gebruikers selecteren**, kiest u de accounts voor nood toegang of het account van uw organisatie en selecteert u **selecteren**.
   1. Selecteer **Voltooid**.
1. Selecteer onder **voor waarden** **gebruikers risico**en kies **hoog**.
   1. Selecteer **selecteren**en vervolgens **gereed**.
1. Kies onder **controle**van  >  **toegang**de optie **toegang toestaan**, en selecteer vervolgens **wachtwoord wijziging vereisen**.
   1. Selecteer **Selecteren**.
1. Stel **beleid afdwingen** in **op aan**.
1. Selecteer **Opslaan**.

## <a name="next-steps"></a>Volgende stappen

[Algemeen beleid voor voorwaardelijke toegang](concept-conditional-access-policy-common.md)

[Op Risico's gebaseerde voorwaardelijke toegang aanmelden](howto-conditional-access-policy-risk.md)

[Effect bepalen met de modus alleen rapport-alleen voor voorwaardelijke toegang](howto-conditional-access-report-only.md)

[Aanmeld gedrag simuleren met het What If hulp programma voor voorwaardelijke toegang](troubleshoot-conditional-access-what-if.md)

[Wat is Azure Active Directory Identity Protection?](../identity-protection/overview.md)
