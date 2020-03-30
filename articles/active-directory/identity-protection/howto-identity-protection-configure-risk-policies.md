---
title: Risicobeleid - Azure Active Directory Identity Protection
description: Risicobeleid inschakelen en configureren in Azure Active Directory Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ffa08f7ebf013d42d6da0589ce0f1ccc97289de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75707002"
---
# <a name="how-to-configure-and-enable-risk-policies"></a>How To: Risicobeleid configureren en inschakelen

Zoals we in het vorige artikel hebben geleerd, hebben we [beleid voor identiteitsbescherming](concept-identity-protection-policies.md) twee risicobeleidsregels die we in onze directory kunnen inschakelen. 

- Aanmeldingsrisicobeleid
- Gebruikersrisicobeleid

![Pagina met beveiligingsoverzichtom gebruikers- en aanmeldingsrisicobeleid in te schakelen](./media/howto-identity-protection-configure-risk-policies/identity-protection-security-overview.png)

Beide beleidsregels werken om de respons op risicodetecties in uw omgeving te automatiseren en gebruikers in staat te stellen zichzelf te herstellen wanneer risico's worden gedetecteerd. 

> [!VIDEO https://www.youtube.com/embed/zEsbbik-BTE]

## <a name="prerequisites"></a>Vereisten 

Als uw organisatie gebruikers wil toestaan om zichzelf te herstellen wanneer risico's worden gedetecteerd, moeten gebruikers zijn geregistreerd voor zowel selfservice wachtwoordreset als Azure Multi-Factor Authentication. We raden u [aan de gecombineerde beveiligingsinformatieregistratie-ervaring](../authentication/howto-registration-mfa-sspr-combined.md) in te schakelen voor de beste ervaring. Door gebruikers in staat te stellen zichzelf te herstellen, kunnen ze sneller weer in een productieve toestand worden teruggebracht zonder tussenkomst van de beheerder. Beheerders kunnen deze gebeurtenissen nog steeds zien en ze daarna onderzoeken. 

## <a name="choosing-acceptable-risk-levels"></a>Het kiezen van aanvaardbare risiconiveaus

Organisaties moeten beslissen welk risiconiveau ze bereid zijn te accepteren balanceren gebruikerservaring en veiligheid houding. 

De aanbeveling van Microsoft is om de drempel voor het gebruikersrisicobeleid in te stellen op **Hoog** en het aanmeldingsrisicobeleid op **Medium en hoger**.

Als u een **hoge** drempelkiest, wordt het aantal keren dat een beleid wordt geactiveerd en wordt de impact voor gebruikers geminimaliseerd. Het sluit echter **detecties** met lage en **gemiddelde** risico's uit van het beleid, waardoor een aanvaller mogelijk geen misbruik maakt van een gecompromitteerde identiteit. Als u een **lage** drempel selecteert, worden extra gebruikersonderbrekingen geïntroduceerd, maar wordt de beveiligingshouding verhoogd.

## <a name="exclusions"></a>Uitsluitingen

Met alle beleidsregels u gebruikers uitsluiten, zoals uw [account voor noodtoegang of beheerdersaccounts voor break-glass.](../users-groups-roles/directory-emergency-access.md) Organisaties kunnen bepalen dat ze andere accounts moeten uitsluiten van specifiek beleid op basis van de manier waarop de accounts worden gebruikt. Alle uitsluitingen moeten regelmatig worden herzien om te zien of ze nog steeds van toepassing zijn.

Geconfigureerde vertrouwde [netwerklocaties](../conditional-access/location-condition.md) worden door Identiteitsbeveiliging gebruikt in sommige risicodetecties om fout-positieven te verminderen.

## <a name="enable-policies"></a>Beleid inschakelen

Als u het gebruikersrisico- en aanmeldingsrisicobeleid wilt inschakelen, voert u de volgende stappen uit.

1. Navigeer naar de [Azure-portal](https://portal.azure.com).
1. Blader naar het**overzicht** **van Azure Active Directory** > **Security** > **Identity Protection** > .
1. Selecteer **Gebruikersrisicobeleid configureren**.
   1. Onder **Opdrachten**
      1. **Gebruikers** - Kies **Alle gebruikers** of Selecteer personen **en groepen** als u uw implementatie beperkt.
         1. Optioneel u ervoor kiezen om gebruikers uit te sluiten van het beleid.
      1. **Voorwaarden** - **Gebruikersrisico** De aanbeveling van Microsoft is om deze optie in te stellen op **Hoog.**
   1. Onder **Besturingselementen**
      1. **Toegang** - De aanbeveling van Microsoft is om **toegang toe** te staan en **wachtwoordwijziging vereisen.**
   1. **Beleid afdwingen** - **op**
   1. **Opslaan** - Met deze actie wordt u teruggegeven aan de pagina **Overzicht.**
1. Selecteer **Aanmeldingsrisicobeleid configureren**.
   1. Onder **Opdrachten**
      1. **Gebruikers** - Kies **Alle gebruikers** of Selecteer personen **en groepen** als u uw implementatie beperkt.
         1. Optioneel u ervoor kiezen om gebruikers uit te sluiten van het beleid.
      1. **Voorwaarden** - **Aanmeldingsrisico** De aanbeveling van Microsoft is om deze optie in te stellen op **Medium en hoger.**
   1. Onder **Besturingselementen**
      1. **Toegang** - De aanbeveling van Microsoft is om **toegang toe** te staan en meervoudige **verificatie vereisen.**
   1. **Beleid afdwingen** - **op**
   1. **Opslaan**

## <a name="next-steps"></a>Volgende stappen

- [Registratiebeleid voor Azure-verificatie met meerdere factoren inschakelen](howto-identity-protection-configure-mfa-policy.md)

- [Wat is risico](concept-identity-protection-risks.md)

- [Risicodetectie onderzoeken](howto-identity-protection-investigate-risk.md)

- [Risicodetecties simuleren](howto-identity-protection-simulate-risk.md)
