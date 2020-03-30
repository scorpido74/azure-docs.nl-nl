---
title: Gebruikers en groepen in beleid voor voorwaardelijke toegang - Azure Active Directory
description: Gebruikers en groepen in een Azure AD Conditional Access-beleid
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77192127"
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

Uitsluitingen worden vaak gebruikt voor noodtoegang of break-glass accounts. Meer informatie over noodtoegangsaccounts en waarom deze belangrijk zijn, vindt u in de volgende artikelen: 

* [Noodtoegangsaccounts beheren in Azure AD](../users-groups-roles/directory-emergency-access.md)
* [Een beheerstrategie voor veerkrachtigtoegangsbeheer maken met Azure Active Directory](../authentication/concept-resilient-controls.md)

De volgende opties zijn beschikbaar om uit te sluiten bij het maken van een beleid voor voorwaardelijke toegang.

- Alle gast- en externe gebruikers (preview)
   - Deze selectie omvat alle B2B-gasten en `user type` externe gebruikers, inclusief elke gebruiker met het kenmerk ingesteld op `guest`. Deze selectie is ook van toepassing op externe gebruikers die zijn aangemeld bij een andere organisatie, zoals een Cloud Solution Provider (CSP). 
- Directoryrollen (voorbeeld)
   - Hiermee kunnen beheerders specifieke Azure AD-maprollen selecteren die worden gebruikt om toewijzing te bepalen. Organisaties kunnen bijvoorbeeld een restrictiever beleid maken voor gebruikers die de globale beheerdersrol toegewezen hebben.
- Gebruikers en groepen
   - Hiermee u specifieke sets gebruikers targeten. Organisaties kunnen bijvoorbeeld een groep selecteren die alle leden van de HR-afdeling bevat wanneer een HR-app is geselecteerd als de cloud-app. Een groep kan elk type groep in Azure AD zijn, inclusief dynamische of toegewezen beveiligings- en distributiegroepen.

## <a name="next-steps"></a>Volgende stappen

- [Voorwaardelijke toegang: cloud-apps of -acties](concept-conditional-access-cloud-apps.md)

- [Gemeenschappelijk beleid voor voorwaardelijke toegang](concept-conditional-access-policy-common.md)
