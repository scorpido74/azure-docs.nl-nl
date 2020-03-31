---
title: Voorwaardelijke toegang - Gecombineerde beveiligingsgegevens - Azure Active Directory
description: Een aangepast beleid voor voorwaardelijke toegang maken voor registratie van beveiligingsgegevens
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
ms.openlocfilehash: 4f69a94e17155ff93510d09f666bce12f628274f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295161"
---
# <a name="conditional-access-securing-security-info-registration"></a>Voorwaardelijke toegang: registratie van beveiligingsgegevens beveiligen

Beveiligen wanneer en hoe gebruikers zich registreren voor Azure Multi-Factor Authentication en self-service wachtwoord resetten is nu mogelijk met gebruikersacties in het beleid voor voorwaardelijke toegang. Deze voorbeeldfunctie is beschikbaar voor organisaties die de [gecombineerde registratievoorbeeld](../authentication/concept-registration-mfa-sspr-combined.md)hebben ingeschakeld. Deze functionaliteit kan worden ingeschakeld in organisaties waar ze voorwaarden zoals vertrouwde netwerklocatie willen gebruiken om de toegang tot registratie voor Azure Multi-Factor Authentication en self-service password reset (SSPR) te beperken. Zie het artikel [Voorwaardelijke toegang: voorwaarden voor](concept-conditional-access-conditions.md)meer informatie over bruikbare voorwaarden.

## <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Een beleid maken om registratie vanaf een vertrouwde locatie te vereisen

Het volgende beleid is van toepassing op alle geselecteerde gebruikers, die proberen zich te registreren met behulp van de gecombineerde registratie-ervaring, en blokkeert de toegang, tenzij ze verbinding maken vanaf een locatie die is gemarkeerd als vertrouwd netwerk.

1. Blader in de **Azure-portal**naar Voorwaardelijke**toegang**voor Azure Active**Directory-beveiliging** >  **Azure Active Directory** > .
1. Selecteer **Nieuw beleid**.
1. Voer in Naam een naam in voor dit beleid. Bijvoorbeeld **gecombineerde beveiligingsgegevensregistratie op vertrouwde netwerken**.
1. Selecteer **onder Toewijzingen** **gebruikers en groepen**en selecteer de gebruikers en groepen waarop u dit beleid wilt toepassen.

   > [!WARNING]
   > Gebruikers moeten zijn ingeschakeld voor de [gecombineerde registratie preview](../authentication/howto-registration-mfa-sspr-combined.md).

1. Selecteer **onder Cloud-apps of -acties**de optie **Gebruikersacties**en schakel **Beveiligingsgegevens registreren (voorbeeld) in.**
1. Onder **voorwaarden** > **locaties**.
   1. **Configureren Ja**.
   1. Voeg **elke locatie toe.**
   1. Alle **vertrouwde locaties**uitsluiten .
   1. Selecteer **Gereed** op het blad Locaties.
   1. Selecteer **Gereed** op het ondersede voorwaarden.
1. Stel **Configureren** in op **Ja**en selecteer **Gereed**onder **Apps** > **voor client (voorbeeld)** instellen .
1. Onder **Access controls** > **Grant**.
   1. Selecteer **Bloktoegang**.
   1. Klik vervolgens op **Selecteren**.
1. Stel **Beleid inschakelen** in op **Aan**.
1. Selecteer vervolgens **Opslaan**.

Bij stap 6 in dit beleid hebben organisaties keuzes die ze kunnen maken. Het bovenstaande beleid vereist registratie vanaf een vertrouwde netwerklocatie. Organisaties kunnen ervoor kiezen om alle beschikbare voorwaarden te gebruiken in plaats van **locaties.** Vergeet niet dat dit beleid is een blok beleid, zodat alles wat inbegrepen is geblokkeerd en alles wat niet overeenkomt met de include is toegestaan. 

Sommigen kunnen ervoor kiezen om de apparaatstatus te gebruiken in plaats van locatie in stap 6 hierboven:

6. Onder de status **Apparaat van voorwaarden** > **(voorbeeld)**.
   1. **Configureren Ja**.
   1. Alle **apparaatstatus opnemen**.
   1. **Apparaat hybride Azure AD uitsluiten en/of** apparaat dat is gemarkeerd als **compatibel**
   1. Selecteer **Gereed** op het blad Locaties.
   1. Selecteer **Gereed** op het ondersede voorwaarden.

> [!WARNING]
> Als u de apparaatstatus als voorwaarde in uw beleid gebruikt, kan dit gevolgen hebben voor gastgebruikers in de map. [De modus Alleen-rapporteren](concept-conditional-access-report-only.md) kan helpen bij het bepalen van de impact van beleidsbeslissingen.

## <a name="next-steps"></a>Volgende stappen

[Gemeenschappelijk beleid voor voorwaardelijke toegang](concept-conditional-access-policy-common.md)

[Impact bepalen met de modus Alleen voor rapportvoorwaardelijke toegang](howto-conditional-access-report-only.md)

[Aanmeldingsgedrag simuleren met het gereedschap Welke als voorwaardelijke toegang](troubleshoot-conditional-access-what-if.md)
