---
title: Voorwaardelijke toegang-verouderde verificatie blok keren-Azure Active Directory
description: Een aangepast beleid voor voorwaardelijke toegang maken om verouderde verificatie protocollen te blok keren
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: b992973beb7cb132075e47e104733d812dc06ca0
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73151078"
---
# <a name="conditional-access-block-legacy-authentication"></a>Voorwaardelijke toegang: verouderde verificatie blok keren

Vanwege het verhoogde risico dat is gekoppeld aan verouderde verificatie protocollen, raadt micro soft aan dat organisaties verificatie aanvragen blok keren die gebruikmaken van deze protocollen en moderne verificatie vereisen.

## <a name="create-a-conditional-access-policy"></a>Beleid voor voorwaardelijke toegang maken

De volgende stappen helpen u bij het maken van een beleid voor voorwaardelijke toegang om verouderde verificatie aanvragen te blok keren.

1. Meld u aan bij de **Azure Portal** als globale beheerder, beveiligings beheerder of beheerder van de voorwaardelijke toegang.
1. Blader naar **Azure Active Directory** > **voorwaardelijke toegang**.
1. Selecteer **Nieuw beleid**.
1. Geef uw beleid een naam. Het is raadzaam dat organisaties een zinvolle norm maken voor de namen van hun beleid.
1. Onder **toewijzingen**selecteert u **gebruikers en groepen**
   1. Onder **insluiten**selecteert u **alle gebruikers**.
   1. Onder **uitsluiten**selecteert u **gebruikers en groepen** en kiest u alle accounts die de mogelijkheid moeten hebben om verouderde verificatie te gebruiken. 
   1. Selecteer **Done**.
1. Selecteer onder **Cloud-apps of acties** > **include** **alle Cloud-apps**.
   1. Als u specifieke toepassingen van uw beleid moet uitsluiten, kunt u ze kiezen op het tabblad **uitsluiten** onder **Selecteer uitgesloten Cloud-apps** en kiest u **selecteren**.
   1. Selecteer **Done**.
1. Stel onder **voor waarden** > -**client-apps (preview-versie)** **configureren** in op **Ja**.
   1. Controleer alleen de vakken **Mobile apps en desktop-clients** > **andere clients**.
   2. Selecteer **Done**.
1. Onder **toegangs beheer** > **toekenning**selecteert u **toegang blok keren**.
   1. Kies **Selecteren**.
1. Bevestig de instellingen en stel **beleid inschakelen** in **op aan**.
1. Selecteer **maken** om uw beleid in te stellen.

## <a name="next-steps"></a>Volgende stappen

[Algemeen beleid voor voorwaardelijke toegang](concept-conditional-access-policy-common.md)

[Aanmeld gedrag simuleren met het What If hulp programma voor voorwaardelijke toegang](troubleshoot-conditional-access-what-if.md)
