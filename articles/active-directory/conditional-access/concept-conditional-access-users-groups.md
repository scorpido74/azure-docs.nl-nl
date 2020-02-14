---
title: Gebruikers en groepen in beleid voor voorwaardelijke toegang-Azure Active Directory
description: Wie zijn gebruikers en groepen in een beleid voor voorwaardelijke toegang van Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 36898e75680771a9cb084fa142bb635ddbf51c70
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77192127"
---
# <a name="conditional-access-users-and-groups"></a>Voorwaardelijke toegang: gebruikers en groepen

Een beleid voor voorwaardelijke toegang moet een gebruikers toewijzing bevatten als een van de signalen in het besluit proces. Gebruikers kunnen worden opgenomen in of uitgesloten van het beleid voor voorwaardelijke toegang. 

![Gebruiker als signaal in de beslissingen van voorwaardelijke toegang](./media/concept-conditional-access-users-groups/conditional-access-users-and-groups.png)

## <a name="include-users"></a>Gebruikers toevoegen

Deze lijst met gebruikers bevat doorgaans alle gebruikers die een organisatie heeft bereikt in een beleid voor voorwaardelijke toegang. 

De volgende opties zijn beschikbaar voor opname bij het maken van beleid voor voorwaardelijke toegang.

- None
   - Er zijn geen gebruikers geselecteerd
- Alle gebruikers
   - Alle gebruikers die in de directory bestaan, inclusief B2B-gasten.
- Gebruikers en groepen selecteren
   - Alle gast-en externe gebruikers (preview-versie)
      - Deze selectie omvat alle B2B-gasten en externe gebruikers, inclusief alle gebruikers met het `user type` kenmerk ingesteld op `guest`. Deze selectie geldt ook voor elke externe gebruiker die is aangemeld vanuit een andere organisatie, zoals een Cloud Solution Provider (CSP). 
   - Directory-rollen (preview-versie)
      - Hiermee kunnen beheerders specifieke Azure AD-Directory rollen selecteren die worden gebruikt voor het bepalen van de toewijzing. Bijvoorbeeld: organisaties kunnen een meer beperkend beleid maken voor gebruikers aan wie de rol van globale beheerder is toegewezen.
   - Gebruikers en groepen
      - Hiermee kunnen specifieke sets van gebruikers worden bereikt. Organisaties kunnen bijvoorbeeld een groep selecteren die alle leden van de afdeling HR bevat wanneer een HR-app is geselecteerd als de Cloud-app. Een groep kan elk wille keurig type groep in azure AD zijn, met inbegrip van dynamische of toegewezen beveiligings-en distributie groepen.

## <a name="exclude-users"></a>Gebruikers uitsluiten

Uitsluitingen worden vaak gebruikt voor nood toegang of verlopende glazen accounts. Meer informatie over accounts voor toegang in nood gevallen en waarom ze belang rijk zijn, vindt u in de volgende artikelen: 

* [Accounts voor nood toegang beheren in azure AD](../users-groups-roles/directory-emergency-access.md)
* [Maak een flexibele toegangs beheer strategie met Azure Active Directory](../authentication/concept-resilient-controls.md)

De volgende opties zijn beschikbaar om uit te sluiten bij het maken van een beleid voor voorwaardelijke toegang.

- Alle gast-en externe gebruikers (preview-versie)
   - Deze selectie omvat alle B2B-gasten en externe gebruikers, inclusief alle gebruikers met het `user type` kenmerk ingesteld op `guest`. Deze selectie geldt ook voor elke externe gebruiker die is aangemeld vanuit een andere organisatie, zoals een Cloud Solution Provider (CSP). 
- Directory-rollen (preview-versie)
   - Hiermee kunnen beheerders specifieke Azure AD-Directory rollen selecteren die worden gebruikt voor het bepalen van de toewijzing. Bijvoorbeeld: organisaties kunnen een meer beperkend beleid maken voor gebruikers aan wie de rol van globale beheerder is toegewezen.
- Gebruikers en groepen
   - Hiermee kunnen specifieke sets van gebruikers worden bereikt. Organisaties kunnen bijvoorbeeld een groep selecteren die alle leden van de afdeling HR bevat wanneer een HR-app is geselecteerd als de Cloud-app. Een groep kan elk wille keurig type groep in azure AD zijn, met inbegrip van dynamische of toegewezen beveiligings-en distributie groepen.

## <a name="next-steps"></a>Volgende stappen

- [Voorwaardelijke toegang: Cloud-apps of-acties](concept-conditional-access-cloud-apps.md)

- [Algemeen beleid voor voorwaardelijke toegang](concept-conditional-access-policy-common.md)
