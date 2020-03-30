---
title: Voorwaardelijke toegang - Verouderde verificatie blokkeren - Azure Active Directory
description: Een aangepast beleid voor voorwaardelijke toegang maken om verouderde verificatieprotocollen te blokkeren
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a868c8199ac34a498a280e2522d6b1e4c7ec370
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295218"
---
# <a name="conditional-access-block-legacy-authentication"></a>Voorwaardelijke toegang: verouderde verificatie blokkeren

Vanwege het verhoogde risico in verband met verouderde verificatieprotocollen raadt Microsoft organisaties aan verificatieaanvragen te blokkeren met behulp van deze protocollen en moderne verificatie te vereisen.

## <a name="create-a-conditional-access-policy"></a>Beleid voor voorwaardelijke toegang maken

Met de volgende stappen u een beleid voor voorwaardelijke toegang maken om verouderde verificatieaanvragen te blokkeren. Dit beleid wordt ingesteld in [de modus Alleen-rapporteren](howto-conditional-access-report-only.md) om te starten, zodat beheerders kunnen bepalen welke impact ze hebben op bestaande gebruikers. Wanneer beheerders zich op hun gemak voelen dat het beleid van toepassing is zoals ze van plan zijn, kunnen ze overschakelen naar **Aan** of de implementatie fasen door specifieke groepen toe te voegen en anderen uit te sluiten.

1. Meld u aan bij de **Azure-portal** als globale beheerder, beveiligingsbeheerder of beheerder van voorwaardelijke toegang.
1. Blader naar voorwaardelijke**toegang**voor Azure Active**Directory-beveiliging** >  **Azure Active Directory** > .
1. Selecteer **Nieuw beleid**.
1. Geef uw polis een naam. We raden organisaties aan een zinvolle standaard te maken voor de namen van hun beleid.
1. Selecteer **Gebruikers en groepen** onder **Toewijzingen**
   1. Selecteer Alle **gebruikers** **onder Opnemen**.
   1. Selecteer **onder Uitsluiten**de optie Gebruikers en **groepen** en kies alle accounts die de mogelijkheid moeten behouden om verouderde verificatie te gebruiken. Sluit ten minste één account uit om te voorkomen dat u wordt buitengesloten. Als u geen account uitsluit, u dit beleid niet maken.
   1. Selecteer **Done**.
1. Selecteer **Onder Cloud-apps of -acties** **alle cloud-apps**.
   1. Selecteer **Done**.
1. Stel **Configureren** in op **Ja**onder **Voorwaarden** > **client-apps (voorbeeld)** instellen op Ja .
   1. Schakel alleen de vakken **Mobiele apps en desktopclients** > **in Andere clients**in.
   1. Selecteer **Done**.
1. Selecteer **Toegang blokkeren**onder **Access-besturingselementen** > **Verlenen**.
   1. Kies **Selecteren**.
1. Bevestig uw instellingen en stel **Beleid inschakelen** in op **Alleen rapporteren**.
1. Selecteer **Maken** om te maken om uw beleid in te schakelen.

## <a name="next-steps"></a>Volgende stappen

[Gemeenschappelijk beleid voor voorwaardelijke toegang](concept-conditional-access-policy-common.md)

[Impact bepalen met de modus Alleen voor rapportvoorwaardelijke toegang](howto-conditional-access-report-only.md)

[Aanmeldingsgedrag simuleren met het gereedschap Welke als voorwaardelijke toegang](troubleshoot-conditional-access-what-if.md)
