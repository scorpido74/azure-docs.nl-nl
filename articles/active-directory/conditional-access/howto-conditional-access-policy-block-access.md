---
title: Voorwaardelijke toegang - Toegang blokkeren - Azure Active Directory
description: Een aangepast beleid voor voorwaardelijke toegang maken om
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb,
ms.collection: M365-identity-device-management
ms.openlocfilehash: cdbf4f352bc7d6874cab9c12adf5f1717d4b8f9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295726"
---
# <a name="conditional-access-block-access"></a>Voorwaardelijke toegang: toegang blokkeren

Voor organisaties met een conservatieve cloudmigratiebenadering is het blokalle beleid een optie die kan worden gebruikt. 

> [!CAUTION]
> Een verkeerde configuratie van een blokbeleid kan ertoe leiden dat organisaties worden buitengesloten van de Azure-portal.

Beleid als dit kan onbedoelde bijwerkingen hebben. Goede testen en validatie zijn van vitaal belang voordat het mogelijk wordt gemaakt. Beheerders moeten tools gebruiken zoals [de modus Alleen voor rapportoverrapport voorwaardelijke toegang](concept-conditional-access-report-only.md) en het gereedschap Wat als in Voorwaardelijke [toegang](what-if-tool.md) bij het aanbrengen van wijzigingen.

## <a name="user-exclusions"></a>Gebruikersuitsluitingen

Beleid voor voorwaardelijke toegang zijn krachtige tools, we raden u aan de volgende accounts uit uw beleid uit te sluiten:

* **Noodtoegang** of **break-glass-accounts** om tenant-brede accountlock-out te voorkomen. In het onwaarschijnlijke scenario dat alle beheerders zijn uitgesloten van uw tenant, kan uw beheeraccount met noodtoegang worden gebruikt om zich aan te melden bij de tenant om de toegang te herstellen.
   * Meer informatie vindt u in het artikel, [Accounts voor noodtoegang beheren in Azure AD](../users-groups-roles/directory-emergency-access.md).
* **Serviceaccounts** en **serviceprincipals,** zoals het Azure AD Connect Sync-account. Serviceaccounts zijn niet-interactieve accounts die niet aan een bepaalde gebruiker zijn gekoppeld. Ze worden normaal gesproken gebruikt door back-endservices en bieden programmatische toegang tot toepassingen. Serviceaccounts moeten worden uitgesloten, omdat MFA niet programmatisch kan worden voltooid.
   * Als uw organisatie deze accounts in scripts of code heeft gebruikt, u overwegen deze te vervangen door [beheerde identiteiten.](../managed-identities-azure-resources/overview.md) Als tijdelijke tijdelijke oplossing u deze specifieke accounts uitsluiten van het basislijnbeleid.

## <a name="create-a-conditional-access-policy"></a>Beleid voor voorwaardelijke toegang maken

Met de volgende stappen u beleid voor voorwaardelijke toegang maken om de toegang tot alle apps te blokkeren, behalve [voor Office 365](concept-conditional-access-cloud-apps.md#office-365-preview) als gebruikers zich niet in een vertrouwd netwerk bevinden. Deze beleidsregels worden ingesteld in [de modus Alleen-rapporteren](howto-conditional-access-report-only.md) om te starten, zodat beheerders kunnen bepalen welke impact ze hebben op bestaande gebruikers. Wanneer beheerders zich op hun gemak voelen dat het beleid van toepassing is zoals ze van plan zijn, kunnen ze deze overschakelen naar **Op**.

Het eerste beleid blokkeert de toegang tot alle apps, behalve voor Office 365-toepassingen, als deze niet op een vertrouwde locatie is.

1. Meld u aan bij de **Azure-portal** als globale beheerder, beveiligingsbeheerder of beheerder van voorwaardelijke toegang.
1. Blader naar voorwaardelijke**toegang**voor Azure Active**Directory-beveiliging** >  **Azure Active Directory** > .
1. Selecteer **Nieuw beleid**.
1. Geef uw polis een naam. We raden organisaties aan een zinvolle standaard te maken voor de namen van hun beleid.
1. Onder **Toewijzingen** selecteert u **Gebruikers en groepen**.
   1. Selecteer Alle **gebruikers** **onder Opnemen**.
   1. Selecteer **onder Uitsluiten**de optie Gebruikers en **groepen** en kies de noodtoegangs- of breakglas-accounts van uw organisatie. 
   1. Selecteer **Done**.
1. Selecteer **onder Cloud-apps of -acties**de volgende opties:
   1. Selecteer **Onder Opnemen**alle **cloud-apps selecteren**.
   1. Selecteer **onder Onder Uitsluiten**Office **365 (voorbeeld)** selecteren , **Selecteer Selecteren**en selecteer **Vervolgens Gereed**.
1. Onder **voorwaarden**:
   1. Onder **voorwaarden** > **locatie**.
      1. **Configureren instellen** op **Ja**
      1. Selecteer **onder Opnemen**een locatie **selecteren**.
      1. Selecteer **onder Uitsluiten**alle vertrouwde **locaties**selecteren .
      1. Selecteer **Done**.
   1. Stel **Onder Client-apps (Voorbeeld)** **Configureren** in op **Ja**en selecteer **Gereed**, vervolgens **Gereed**.
1. Selecteer **Onder Access-besturingselementen** > **Grant**de optie **Toegang blokkeren**en selecteer vervolgens **Selecteren**.
1. Bevestig uw instellingen en stel **Beleid inschakelen** in op **Alleen rapporteren**.
1. Selecteer **Maken** om te maken om uw beleid in te schakelen.

Hieronder wordt een tweede beleid gemaakt om meervoudige verificatie of een compatibel apparaat voor gebruikers van Office 365 te vereisen.

1. Selecteer **Nieuw beleid**.
1. Geef uw polis een naam. We raden organisaties aan een zinvolle standaard te maken voor de namen van hun beleid.
1. Onder **Toewijzingen** selecteert u **Gebruikers en groepen**.
   1. Selecteer Alle **gebruikers** **onder Opnemen**.
   1. Selecteer **onder Uitsluiten**de optie Gebruikers en **groepen** en kies de noodtoegangs- of breakglas-accounts van uw organisatie. 
   1. Selecteer **Done**.
1. Selecteer onder **Cloud-apps of -acties** > **Opnemen**apps **selecteren,** kies **Office 365 (voorbeeld)** en selecteer **Selecteren**, vervolgens **Gereed**.
1. Selecteer Onder **Access-besturingselementen** > **Grant**de optie **Toegang verlenen**.
   1. Selecteer **Meervoudige verificatie vereisen** en Apparaat vereisen dat moet worden gemarkeerd als **compatibel** selecteren **selecteert**.
   1. Controleer of **Alle geselecteerde besturingselementen** zijn geselecteerd.
   1. Kies **Selecteren**.
1. Bevestig uw instellingen en stel **Beleid inschakelen** in op **Alleen rapporteren**.
1. Selecteer **Maken** om te maken om uw beleid in te schakelen.

## <a name="next-steps"></a>Volgende stappen

[Gemeenschappelijk beleid voor voorwaardelijke toegang](concept-conditional-access-policy-common.md)

[Impact bepalen met de modus Alleen voor rapportvoorwaardelijke toegang](howto-conditional-access-report-only.md)

[Aanmeldingsgedrag simuleren met het gereedschap Welke als voorwaardelijke toegang](troubleshoot-conditional-access-what-if.md)
