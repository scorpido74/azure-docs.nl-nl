---
title: Voorwaardelijke toegang-verouderde verificatie blok keren-Azure Active Directory
description: Een aangepast beleid voor voorwaardelijke toegang maken om verouderde verificatie protocollen te blok keren
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2d6539a233fbb8038d82a8ea41da2c9e79745324
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "83995187"
---
# <a name="conditional-access-block-legacy-authentication"></a>Voorwaardelijke toegang: verouderde verificatie blok keren

Vanwege het verhoogde risico dat is gekoppeld aan verouderde verificatie protocollen, raadt micro soft aan dat organisaties verificatie aanvragen blok keren die gebruikmaken van deze protocollen en moderne verificatie vereisen.

## <a name="create-a-conditional-access-policy"></a>Beleid voor voorwaardelijke toegang maken

De volgende stappen helpen u bij het maken van een beleid voor voorwaardelijke toegang om verouderde verificatie aanvragen te blok keren. Dit beleid wordt in de [modus alleen rapport](howto-conditional-access-report-only.md) gezet om te starten, zodat beheerders kunnen bepalen wat het effect is op bestaande gebruikers. Wanneer beheerders weten dat het beleid van toepassing is op dezelfde **plaats** , kunnen ze overschakelen naar de implementatie of deze faseren door specifieke groepen toe te voegen en anderen uit te sluiten.

1. Meld u aan bij de **Azure Portal** als globale beheerder, beveiligings beheerder of beheerder van de voorwaardelijke toegang.
1. Blader naar **Azure Active Directory**  >  **beveiligings**  >  **voorwaardelijke toegang**.
1. Selecteer **Nieuw beleid**.
1. Geef uw beleid een naam. Het is raadzaam dat organisaties een zinvolle norm maken voor de namen van hun beleid.
1. Onder **toewijzingen**selecteert u **gebruikers en groepen**
   1. Onder **insluiten**selecteert u **alle gebruikers**.
   1. Onder **uitsluiten**selecteert u **gebruikers en groepen** en kiest u alle accounts die de mogelijkheid moeten hebben om verouderde verificatie te gebruiken. Sluit ten minste één account uit om te voor komen dat uzelf wordt vergrendeld. Als u geen account uitsluit, kunt u dit beleid niet maken.
   1. Selecteer **Voltooid**.
1. Selecteer onder **Cloud-apps of-acties** **alle Cloud-apps**.
   1. Selecteer **Voltooid**.
1. Stel onder **voor waarden**  >  **client-apps (preview)** **configureren** in op **Ja**.
   1. Controleer alleen de vakken **Mobile apps en desktop-clients**  >  **andere clients**.
   1. Selecteer **Voltooid**.
1. Onder **toegangs beheer**  >  **toekennen**selecteert u **toegang blok keren**.
   1. Selecteer **Selecteren**.
1. Bevestig de instellingen en stel **beleid inschakelen** in op **alleen rapport**.
1. Selecteer **maken** om uw beleid in te stellen.

## <a name="next-steps"></a>Volgende stappen

[Algemeen beleid voor voorwaardelijke toegang](concept-conditional-access-policy-common.md)

[Effect bepalen met de modus alleen rapport-alleen voor voorwaardelijke toegang](howto-conditional-access-report-only.md)

[Aanmeld gedrag simuleren met het What If hulp programma voor voorwaardelijke toegang](troubleshoot-conditional-access-what-if.md)

[Een multifunctioneel apparaat of toepassing instellen om e-mail te verzenden met Office 365 en Microsoft 365](/exchange/mail-flow-best-practices/how-to-set-up-a-multifunction-device-or-application-to-send-email-using-office-3)
