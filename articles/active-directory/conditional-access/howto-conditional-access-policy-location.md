---
title: Voorwaardelijke toegang - Toegang blokkeren op locatie - Azure Active Directory
description: Een aangepast beleid voor voorwaardelijke toegang maken om de toegang tot bronnen per IP-locatie te blokkeren
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295190"
---
# <a name="conditional-access-block-access-by-location"></a>Voorwaardelijke toegang: toegang per locatie blokkeren

Met de locatievoorwaarde in Voorwaardelijke toegang u de toegang tot uw cloud-apps beheren op basis van de netwerklocatie van een gebruiker. De locatievoorwaarde wordt vaak gebruikt om toegang te blokkeren vanuit landen waarvan uw organisatie weet dat verkeer niet afkomstig mag zijn.

## <a name="define-locations"></a>Locaties definiëren

1. Meld u aan bij de **Azure-portal** als globale beheerder, beveiligingsbeheerder of beheerder van voorwaardelijke toegang.
1. Blader naar**locaties met**voorwaardelijke toegang met voorwaardelijke toegang voor Azure Active**Directory-beveiliging** > **Conditional Access** >  **Azure Active Directory** > .
1. Kies **Nieuwe locatie**.
1. Geef uw locatie een naam.
1. Kies **IP-bereiken** als u de specifieke extern toegankelijke IPv4-adresbereiken kent die deel uitmaken van die locatie of **landen/regio's.**
   1. Geef de **IP-bereiken op** of selecteer de **landen/regio's** voor de locatie die u opgeeft.
      * Als u Landen/regio's kiest, u er optioneel voor kiezen om onbekende gebieden op te nemen.
1. Opslaan **kiezen**

Meer informatie over de locatievoorwaarde in Voorwaardelijke toegang vindt u in het artikel, [Wat is de locatievoorwaarde in Voorwaardelijke toegang van Azure Active Directory](location-condition.md)

## <a name="create-a-conditional-access-policy"></a>Beleid voor voorwaardelijke toegang maken

1. Meld u aan bij de **Azure-portal** als globale beheerder, beveiligingsbeheerder of beheerder van voorwaardelijke toegang.
1. Blader naar voorwaardelijke**toegang**voor Azure Active**Directory-beveiliging** >  **Azure Active Directory** > .
1. Selecteer **Nieuw beleid**.
1. Geef uw polis een naam. We raden organisaties aan een zinvolle standaard te maken voor de namen van hun beleid.
1. Selecteer **Gebruikers en groepen** onder **Toewijzingen**
   1. Selecteer Alle **gebruikers** **onder Opnemen**.
   1. Selecteer **Done**.
1. Selecteer Onder **Cloud-apps of -acties** > **Opnemen** **alle cloud-apps**en selecteer **Gereed**.
1. Onder **voorwaarden** > **locatie**.
   1. **Configureren instellen** op **Ja**
   1. **Geselecteerde** **locaties** opnemen
   1. Selecteer de geblokkeerde locatie die u voor uw organisatie hebt gemaakt.
   1. Klik **op Gereed** > **selecteren** > **Done**.
1. Stel **Configureren** in op **Ja**en selecteer **Gereed**onder **Apps** > **voor client (voorbeeld)** instellen .
1. Selecteer onder **Toegangsbesturingselementen** > **Blok**en selecteer **Selecteren**.
1. Bevestig uw instellingen en stel **Beleid inschakelen** in **op Aan**.
1. Selecteer **Maken** om te maken om uw beleid in te schakelen.

## <a name="next-steps"></a>Volgende stappen

[Gemeenschappelijk beleid voor voorwaardelijke toegang](concept-conditional-access-policy-common.md)

[Impact bepalen met de modus Alleen voor rapportvoorwaardelijke toegang](howto-conditional-access-report-only.md)

[Aanmeldingsgedrag simuleren met het gereedschap Welke als voorwaardelijke toegang](troubleshoot-conditional-access-what-if.md)
