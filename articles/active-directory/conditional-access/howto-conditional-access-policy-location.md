---
title: 'Voorwaardelijke toegang: toegang blok keren op locatie-Azure Active Directory'
description: Een aangepast beleid voor voorwaardelijke toegang maken om de toegang tot bronnen op basis van de IP-locatie te blok keren
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
ms.openlocfilehash: 34b29ceadaaf85e69d1214039fa1b563ed21a77d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80295190"
---
# <a name="conditional-access-block-access-by-location"></a>Voorwaardelijke toegang: toegang blok keren per locatie

Met de voor waarde voor de locatie in voorwaardelijke toegang kunt u de toegang tot uw Cloud-apps beheren op basis van de netwerk locatie van een gebruiker. De locatie voorwaarde wordt doorgaans gebruikt om toegang te blok keren van landen waar uw organisatie geen verkeer mag zijn.

## <a name="define-locations"></a>Locaties definiëren

1. Meld u aan bij de **Azure Portal** als globale beheerder, beveiligings beheerder of beheerder van de voorwaardelijke toegang.
1. Blader naar **Azure Active Directory** > **beveiligings** > **voorwaardelijke toegang** > met de**naam locaties**.
1. Kies **nieuwe locatie**.
1. Geef een naam op voor uw locatie.
1. Kies **IP-adresbereiken** als u de specifieke, extern toegankelijke IPv4-adresbereiken die deze locatie of **landen/regio's**vormen, kent.
   1. Geef het **IP-bereik** op of selecteer de **landen/regio's** voor de locatie die u opgeeft.
      * Als u landen/regio's kiest, kunt u desgewenst ook onbekende gebieden opgeven.
1. Kies **Opslaan**

Meer informatie over de locatie voorwaarde in voorwaardelijke toegang vindt u in het artikel, [Wat is de voor waarde voor de locatie in azure Active Directory voorwaardelijke toegang](location-condition.md)

## <a name="create-a-conditional-access-policy"></a>Beleid voor voorwaardelijke toegang maken

1. Meld u aan bij de **Azure Portal** als globale beheerder, beveiligings beheerder of beheerder van de voorwaardelijke toegang.
1. Blader naar **Azure Active Directory** > **beveiligings** > **voorwaardelijke toegang**.
1. Selecteer **Nieuw beleid**.
1. Geef uw beleid een naam. Het is raadzaam dat organisaties een zinvolle norm maken voor de namen van hun beleid.
1. Onder **toewijzingen**selecteert u **gebruikers en groepen**
   1. Onder **insluiten**selecteert u **alle gebruikers**.
   1. Selecteer **Done**.
1. Onder **Cloud-apps of acties** > **, selecteert**u **alle Cloud-apps**en selecteert u **gereed**.
1. Onder **voor waarden** > **locatie**.
   1. Stel **configureren** op **Ja** in
   1. Selecteer **geselecteerde locaties** **toevoegen**
   1. Selecteer de geblokkeerde locatie die u hebt gemaakt voor uw organisatie.
   1.  > **Done****Done**Klik op **gereed.** > 
1. Onder **voor waarden** > **client-apps (preview)** stelt u **configureren** op **Ja**in en selecteert u **gereed**.
1. Onder **toegangscontrole** > **blok**en selecteer **selecteren**.
1. Bevestig de instellingen en stel **beleid inschakelen** in **op aan**.
1. Selecteer **maken** om uw beleid in te stellen.

## <a name="next-steps"></a>Volgende stappen

[Algemeen beleid voor voorwaardelijke toegang](concept-conditional-access-policy-common.md)

[Effect bepalen met de modus alleen rapport-alleen voor voorwaardelijke toegang](howto-conditional-access-report-only.md)

[Aanmeld gedrag simuleren met het What If hulp programma voor voorwaardelijke toegang](troubleshoot-conditional-access-what-if.md)
