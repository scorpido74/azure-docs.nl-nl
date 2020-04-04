---
title: Gebruikers en groepen in beleid voor voorwaardelijke toegang - Azure Active Directory
description: Gebruikers en groepen in een Azure AD Conditional Access-beleid
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 04/02/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 43ae866959dd2112bacbb6b56e5683e7b3b851a0
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631924"
---
# <a name="conditional-access-users-and-groups"></a>Voorwaardelijke toegang: gebruikers en groepen

Een beleid voor voorwaardelijke toegang moet een gebruikerstoewijzing bevatten als een van de signalen in het beslissingsproces. Gebruikers kunnen worden opgenomen of uitgesloten van beleid voor voorwaardelijke toegang. 

![Gebruiker als signaal in de beslissingen van Voorwaardelijke Toegang](./media/concept-conditional-access-users-groups/conditional-access-users-and-groups.png)

## <a name="include-users"></a>Gebruikers opnemen

Deze lijst met gebruikers bevat doorgaans alle gebruikers die een organisatie target in een beleid voor voorwaardelijke toegang. 

De volgende opties zijn beschikbaar om op te nemen bij het maken van een beleid voor voorwaardelijke toegang.

- Geen
   - Geen gebruikers geselecteerd
- Alle gebruikers
   - Alle gebruikers die in de directory aanwezig zijn, inclusief B2B-gasten.
- Gebruikers en groepen selecteren
   - Alle gast- en externe gebruikers (preview)
      - Deze selectie omvat alle B2B-gasten en `user type` externe gebruikers, inclusief elke gebruiker met het kenmerk ingesteld op `guest`. Deze selectie is ook van toepassing op externe gebruikers die zijn aangemeld bij een andere organisatie, zoals een Cloud Solution Provider (CSP). 
   - Directoryrollen (voorbeeld)
      - Hiermee kunnen beheerders specifieke Azure AD-maprollen selecteren die worden gebruikt om toewijzing te bepalen. Organisaties kunnen bijvoorbeeld een restrictiever beleid maken voor gebruikers die de globale beheerdersrol toegewezen hebben.
   - Gebruikers en groepen
      - Hiermee u specifieke sets gebruikers targeten. Organisaties kunnen bijvoorbeeld een groep selecteren die alle leden van de HR-afdeling bevat wanneer een HR-app is geselecteerd als de cloud-app. Een groep kan elk type groep in Azure AD zijn, inclusief dynamische of toegewezen beveiligings- en distributiegroepen.

## <a name="exclude-users"></a>Gebruikers uitsluiten

Wanneer organisaties zowel een gebruiker of groep opnemen als uitsluiten, wordt de gebruiker of groep uitgesloten van het beleid, omdat een actie voor uitsluiten een include in het beleid overschrijft. Uitsluitingen worden vaak gebruikt voor noodtoegang of break-glass accounts. Meer informatie over noodtoegangsaccounts en waarom deze belangrijk zijn, vindt u in de volgende artikelen: 

* [Noodtoegangsaccounts beheren in Azure AD](../users-groups-roles/directory-emergency-access.md)
* [Een beheerstrategie voor veerkrachtigtoegangsbeheer maken met Azure Active Directory](../authentication/concept-resilient-controls.md)

De volgende opties zijn beschikbaar om uit te sluiten bij het maken van een beleid voor voorwaardelijke toegang.

- Alle gast- en externe gebruikers (preview)
   - Deze selectie omvat alle B2B-gasten en `user type` externe gebruikers, inclusief elke gebruiker met het kenmerk ingesteld op `guest`. Deze selectie is ook van toepassing op externe gebruikers die zijn aangemeld bij een andere organisatie, zoals een Cloud Solution Provider (CSP). 
- Directoryrollen (voorbeeld)
   - Hiermee kunnen beheerders specifieke Azure AD-maprollen selecteren die worden gebruikt om toewijzing te bepalen. Organisaties kunnen bijvoorbeeld een restrictiever beleid maken voor gebruikers die de globale beheerdersrol toegewezen hebben.
- Gebruikers en groepen
   - Hiermee u specifieke sets gebruikers targeten. Organisaties kunnen bijvoorbeeld een groep selecteren die alle leden van de HR-afdeling bevat wanneer een HR-app is geselecteerd als de cloud-app. Een groep kan elk type groep in Azure AD zijn, inclusief dynamische of toegewezen beveiligings- en distributiegroepen.

### <a name="preventing-administrator-lockout"></a>Uitsluiting van beheerders voorkomen

Om te voorkomen dat een beheerder zichzelf uit zijn map sluit bij het maken van een beleid dat is toegepast op **Alle gebruikers** en **Alle apps,** ziet deze de volgende waarschuwing.

> Sluit jezelf niet buiten. We raden u aan een beleid toe te passen op een kleine groep gebruikers om te controleren of het zich gedraagt zoals verwacht. We raden ook aan om ten minste één beheerder uit te sluiten van dit beleid. Dit zorgt ervoor dat u nog steeds toegang hebt en een beleid bijwerken als een wijziging vereist is. Bekijk de getroffen gebruikers en apps.

Standaard biedt het beleid een optie om de huidige gebruiker uit te sluiten van het beleid, maar deze standaardinstelling kan worden overschreven door de beheerder, zoals weergegeven in de volgende afbeelding. 

![Let op, sluit jezelf niet buiten!](./media/concept-conditional-access-users-groups/conditional-access-users-and-groups-lockout-warning.png)

## <a name="next-steps"></a>Volgende stappen

- [Voorwaardelijke toegang: cloud-apps of -acties](concept-conditional-access-cloud-apps.md)

- [Gemeenschappelijk beleid voor voorwaardelijke toegang](concept-conditional-access-policy-common.md)
