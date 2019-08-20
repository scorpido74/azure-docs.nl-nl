---
title: Voorwaardelijke toegang-gecombineerde beveiligings informatie-Azure Active Directory
description: Een aangepast beleid voor voorwaardelijke toegang maken voor het vereisen van een vertrouwde locatie voor de registratie van beveiligings gegevens
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: f54ad6de21f05c76ca021e172a041563e3d688a8
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69576561"
---
# <a name="conditional-access-require-trusted-location-for-mfa-registration"></a>Voorwaardelijke toegang: Vertrouwde locatie vereisen voor MFA-registratie

Beveiligen wanneer en hoe gebruikers zich registreren voor Azure multi-factor Authentication en self-service voor het opnieuw instellen van wacht woorden is nu mogelijk met gebruikers acties in het beleid voor voorwaardelijke toegang. Deze preview-functie is beschikbaar voor organisaties die de [gecombineerde registratie preview](../authentication/concept-registration-mfa-sspr-combined.md)hebben ingeschakeld. Deze functionaliteit kan worden ingeschakeld in organisaties waar gebruikers moeten registreren voor Azure multi-factor Authentication en SSPR vanaf een centrale locatie, zoals een vertrouwde netwerk locatie tijdens HR-onboarding. Zie het artikel [Wat is de locatie voorwaarde in azure Active Directory voorwaardelijke toegang?](../conditional-access/location-condition.md#named-locations) voor meer informatie over het maken van vertrouwde locaties in voorwaardelijke toegang.

## <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Een beleid maken om registratie van een vertrouwde locatie te vereisen

Het volgende beleid is van toepassing op alle geselecteerde gebruikers die zich willen registreren met de gecombineerde registratie-ervaring en blokkeert de toegang tenzij ze verbinding maken vanaf een locatie die is gemarkeerd als vertrouwd netwerk.

1. Blader in het **Azure Portal**naar **Azure Active Directory** > **voorwaardelijke toegang**.
1. Selecteer **Nieuw beleid**.
1. Voer bij naam een naam in voor dit beleid. Bijvoorbeeld **registratie van gegevens over gecombineerde beveiliging op vertrouwde netwerken**.
1. Klik onder **toewijzingen**op **gebruikers en groepen**en selecteer de gebruikers en groepen waarop u dit beleid wilt Toep assen.

   > [!WARNING]
   > Gebruikers moeten zijn ingeschakeld voor de [gecombineerde registratie preview](../authentication/howto-registration-mfa-sspr-combined.md).

1. Selecteer onder **Cloud-apps of-acties** **gebruikers acties**, Controleer de **beveiligings gegevens registreren (preview-versie)** .
1. Onder **voor waarden** > **locaties**.
   1. Configureer **Ja**.
   1. **Een wille keurige locatie**bevatten.
   1. **Alle vertrouwde locaties**uitsluiten.
   1. Klik op **gereed** op de Blade locaties.
   1. Klik op **gereed** op de Blade voor waarden.
1. Onder **toegangs beheer** > **verlenen**.
   1. Klik op **toegang blok keren**.
   1. Klik vervolgens op **Selecteren**.
1. Stel **beleid inschakelen** op aan **in**.
1. Klik vervolgens op **Maken**.

## <a name="next-steps"></a>Volgende stappen

[Algemeen beleid voor voorwaardelijke toegang](concept-conditional-access-policy-common.md)

[Aanmeld gedrag simuleren met het What If hulp programma voor voorwaardelijke toegang](troubleshoot-conditional-access-what-if.md)
