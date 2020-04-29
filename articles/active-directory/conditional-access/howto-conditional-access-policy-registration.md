---
title: Voorwaardelijke toegang-gecombineerde beveiligings informatie-Azure Active Directory
description: Een aangepast beleid voor voorwaardelijke toegang maken voor de registratie van beveiligings gegevens
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
ms.openlocfilehash: 9c8081bb8145a6654c168fb2d664e1666b32dc18
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81457906"
---
# <a name="conditional-access-securing-security-info-registration"></a>Voorwaardelijke toegang: registratie van beveiligings gegevens beveiligen

Beveiligen wanneer en hoe gebruikers zich registreren voor Azure Multi-Factor Authentication en self-service voor wacht woord opnieuw instellen is nu mogelijk met gebruikers acties in het beleid voor voorwaardelijke toegang. Deze preview-functie is beschikbaar voor organisaties die de [gecombineerde registratie preview](../authentication/concept-registration-mfa-sspr-combined.md)hebben ingeschakeld. Deze functionaliteit kan worden ingeschakeld in organisaties waar ze voor waarden zoals een vertrouwde netwerk locatie willen gebruiken om de toegang te beperken tot registratie voor Azure Multi-Factor Authentication en self-service voor wachtwoord herstel (SSPR). Zie het artikel [voorwaardelijke toegang: voor waarden](concept-conditional-access-conditions.md)voor meer informatie over bruikbare omstandigheden.

## <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Een beleid maken om registratie van een vertrouwde locatie te vereisen

Het volgende beleid is van toepassing op alle geselecteerde gebruikers die zich willen registreren met de gecombineerde registratie-ervaring en blokkeert de toegang tenzij ze verbinding maken vanaf een locatie die is gemarkeerd als vertrouwd netwerk.

1. Blader in het **Azure Portal**naar **Azure Active Directory** > **voorwaardelijke toegang**voor**beveiliging** > .
1. Selecteer **Nieuw beleid**.
1. Voer bij naam een naam in voor dit beleid. Bijvoorbeeld **registratie van gegevens over gecombineerde beveiliging op vertrouwde netwerken**.
1. Onder **toewijzingen**selecteert u **gebruikers en groepen**en selecteert u de gebruikers en groepen waarop u dit beleid wilt Toep assen.

   > [!WARNING]
   > Gebruikers moeten zijn ingeschakeld voor de [gecombineerde registratie](../authentication/howto-registration-mfa-sspr-combined.md).

1. Onder **Cloud-apps of-acties**selecteert u **gebruikers acties**, check **Security Information registreren**.
1. Onder **voor waarden** > **locaties**.
   1. Configureer **Ja**.
   1. **Een wille keurige locatie**bevatten.
   1. **Alle vertrouwde locaties**uitsluiten.
   1. Selecteer **gereed** op de Blade locaties.
   1. Selecteer **gereed** op de Blade voor waarden.
1. Onder **voor waarden** > **client-apps (preview)** stelt u **configureren** op **Ja**in en selecteert u **gereed**.
1. Onder **toegangs beheer** > **verlenen**.
   1. Selecteer **toegang blok keren**.
   1. Klik vervolgens op **Selecteren**.
1. Stel **Beleid inschakelen** in op **Aan**.
1. Selecteer vervolgens **Opslaan**.

Bij stap 6 in dit beleid hebben organisaties keuzes die ze kunnen maken. Voor het bovenstaande beleid is registratie van een vertrouwde netwerk locatie vereist. Organisaties kunnen kiezen voor het gebruik van alle beschik bare voor waarden in plaats van **locaties**. Houd er rekening mee dat dit beleid een blok beleid is, zodat alle opgenomen items worden geblokkeerd en alles wat niet overeenkomt met het include is toegestaan. 

Sommige kunnen ervoor kiezen om de apparaatstatus te gebruiken in plaats van locatie in stap 6 hierboven:

6. Onder **voor waarden** > **Apparaatstatus (preview-versie)**.
   1. Configureer **Ja**.
   1. **Alle Apparaatstatus**toevoegen.
   1. **Hybride Azure AD-join** en/of **apparaat dat is gemarkeerd als compatibel** uitsluiten voor apparaat
   1. Selecteer **gereed** op de Blade locaties.
   1. Selecteer **gereed** op de Blade voor waarden.

> [!WARNING]
> Als u Apparaatstatus als een voor waarde in uw beleid gebruikt, kan dit van invloed zijn op gast gebruikers in de Directory. De [modus alleen rapport](concept-conditional-access-report-only.md) kan helpen bij het bepalen van de impact van beleids beslissingen.

## <a name="next-steps"></a>Volgende stappen

[Algemeen beleid voor voorwaardelijke toegang](concept-conditional-access-policy-common.md)

[Effect bepalen met de modus alleen rapport-alleen voor voorwaardelijke toegang](howto-conditional-access-report-only.md)

[Aanmeld gedrag simuleren met het What If hulp programma voor voorwaardelijke toegang](troubleshoot-conditional-access-what-if.md)
