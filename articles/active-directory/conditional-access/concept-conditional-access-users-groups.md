---
title: Gebruikers en groepen in beleid voor voorwaardelijke toegang-Azure Active Directory
description: Wie zijn gebruikers en groepen in een beleid voor voorwaardelijke toegang van Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/03/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: ba1fc856ee9093b628bd86b9847f8fc70b7189c2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87552897"
---
# <a name="conditional-access-users-and-groups"></a>Voorwaardelijke toegang: gebruikers en groepen

Een beleid voor voorwaardelijke toegang moet een gebruikers toewijzing bevatten als een van de signalen in het besluit proces. Gebruikers kunnen worden opgenomen in of uitgesloten van het beleid voor voorwaardelijke toegang. 

![Gebruiker als signaal in de beslissingen van voorwaardelijke toegang](./media/concept-conditional-access-users-groups/conditional-access-users-and-groups.png)

## <a name="include-users"></a>Gebruikers toevoegen

Deze lijst met gebruikers bevat doorgaans alle gebruikers die een organisatie heeft bereikt in een beleid voor voorwaardelijke toegang. 

De volgende opties zijn beschikbaar voor opname bij het maken van beleid voor voorwaardelijke toegang.

- Geen
   - Er zijn geen gebruikers geselecteerd
- Alle gebruikers
   - Alle gebruikers die in de directory bestaan, inclusief B2B-gasten.
- Gebruikers en groepen selecteren
   - Alle gast-en externe gebruikers
      - Deze selectie omvat alle B2B-gasten en externe gebruikers, inclusief alle gebruikers waarvoor het `user type` kenmerk is ingesteld op `guest` . Deze selectie geldt ook voor elke externe gebruiker die is aangemeld vanuit een andere organisatie, zoals een Cloud Solution Provider (CSP). 
   - Directory-rollen
      - Hiermee kunnen beheerders specifieke Azure AD-Directory rollen selecteren die worden gebruikt voor het bepalen van de toewijzing. Bijvoorbeeld: organisaties kunnen een meer beperkend beleid maken voor gebruikers aan wie de rol van globale beheerder is toegewezen.
   - Gebruikers en groepen
      - Hiermee kunnen specifieke sets van gebruikers worden bereikt. Organisaties kunnen bijvoorbeeld een groep selecteren die alle leden van de afdeling HR bevat wanneer een HR-app is geselecteerd als de Cloud-app. Een groep kan elk wille keurig type groep in azure AD zijn, met inbegrip van dynamische of toegewezen beveiligings-en distributie groepen. Het beleid wordt toegepast op geneste gebruikers en groepen.

> [!WARNING]
> Als gebruikers of groepen lid zijn van meer dan 2048 groepen, kan hun toegang worden geblokkeerd. Deze limiet is van toepassing op het directe en geneste groepslid maatschap.

> [!WARNING]
> Het beleid voor voorwaardelijke toegang biedt geen ondersteuning voor gebruikers die zijn toegewezen aan een directory-rol [binnen een administratieve eenheid](../users-groups-roles/roles-admin-units-assign-roles.md) of Directory-rollen die rechtstreeks aan een object zijn gekoppeld, zoals via [aangepaste rollen](../users-groups-roles/roles-create-custom.md).

## <a name="exclude-users"></a>Gebruikers uitsluiten

Wanneer organisaties een gebruiker of groep opnemen en uitsluiten, wordt de gebruiker of groep uitgesloten van het beleid, omdat een actie uitsluiten een insluiting in het beleid overschrijft. Uitsluitingen worden vaak gebruikt voor nood toegang of verlopende glazen accounts. Meer informatie over accounts voor toegang in nood gevallen en waarom ze belang rijk zijn, vindt u in de volgende artikelen: 

* [Accounts voor nood toegang beheren in azure AD](../users-groups-roles/directory-emergency-access.md)
* [Maak een flexibele toegangs beheer strategie met Azure Active Directory](../authentication/concept-resilient-controls.md)

De volgende opties zijn beschikbaar om uit te sluiten bij het maken van een beleid voor voorwaardelijke toegang.

- Alle gast-en externe gebruikers
   - Deze selectie omvat alle B2B-gasten en externe gebruikers, inclusief alle gebruikers waarvoor het `user type` kenmerk is ingesteld op `guest` . Deze selectie geldt ook voor elke externe gebruiker die is aangemeld vanuit een andere organisatie, zoals een Cloud Solution Provider (CSP). 
- Directory-rollen
   - Hiermee kunnen beheerders specifieke Azure AD-Directory rollen selecteren die worden gebruikt voor het bepalen van de toewijzing. Bijvoorbeeld: organisaties kunnen een meer beperkend beleid maken voor gebruikers aan wie de rol van globale beheerder is toegewezen.
- Gebruikers en groepen
   - Hiermee kunnen specifieke sets van gebruikers worden bereikt. Organisaties kunnen bijvoorbeeld een groep selecteren die alle leden van de afdeling HR bevat wanneer een HR-app is geselecteerd als de Cloud-app. Een groep kan elk wille keurig type groep in azure AD zijn, met inbegrip van dynamische of toegewezen beveiligings-en distributie groepen.

### <a name="preventing-administrator-lockout"></a>Vergren deling door beheerder voor komen

Om te voor komen dat een beheerder zichzelf kan vergren delen bij het maken van een beleid dat wordt toegepast op **alle gebruikers** en **alle apps**, wordt de volgende waarschuwing weer gegeven.

> Vergren delen niet. We raden u aan om eerst een beleid toe te passen op een kleine groep gebruikers om te controleren of het werkt zoals verwacht. We raden u ook aan ten minste één beheerder van dit beleid uit te sluiten. Dit zorgt ervoor dat u nog steeds toegang hebt en een beleid kunt bijwerken als er een wijziging is vereist. Controleer de betrokken gebruikers en apps.

Het beleid biedt standaard een optie om de huidige gebruiker uit te sluiten van het beleid, maar deze standaard instelling kan worden overschreven door de beheerder zoals wordt weer gegeven in de volgende afbeelding. 

![Waarschuwing, niet zelf vergren delen.](./media/concept-conditional-access-users-groups/conditional-access-users-and-groups-lockout-warning.png)

## <a name="next-steps"></a>Volgende stappen

- [Voorwaardelijke toegang: Cloud-apps of-acties](concept-conditional-access-cloud-apps.md)

- [Algemeen beleid voor voorwaardelijke toegang](concept-conditional-access-policy-common.md)
