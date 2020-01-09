---
title: Voorwaardelijke toegang-gecombineerde beveiligings informatie-Azure Active Directory
description: Een aangepast beleid voor voorwaardelijke toegang maken voor het vereisen van een vertrouwde locatie voor de registratie van beveiligings gegevens
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c9b01cc06b3d0ef8f47b34e9ef86bec9adac03f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75424856"
---
# <a name="conditional-access-require-trusted-location-for-mfa-registration"></a>Voorwaardelijke toegang: vertrouwde locatie vereisen voor MFA-registratie

Beveiligen wanneer en hoe gebruikers zich registreren voor Azure Multi-Factor Authentication en self-service voor wacht woord opnieuw instellen is nu mogelijk met gebruikers acties in het beleid voor voorwaardelijke toegang. Deze preview-functie is beschikbaar voor organisaties die de [gecombineerde registratie preview](../authentication/concept-registration-mfa-sspr-combined.md)hebben ingeschakeld. Deze functionaliteit kan worden ingeschakeld in organisaties waar ze voor waarden zoals een vertrouwde netwerk locatie willen gebruiken om de toegang te beperken tot registratie voor Azure Multi-Factor Authentication en SSPR. Zie het artikel [Wat is de locatie voorwaarde in azure Active Directory voorwaardelijke toegang?](../conditional-access/location-condition.md#named-locations) voor meer informatie over het maken van vertrouwde locaties in voorwaardelijke toegang.

## <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Een beleid maken om registratie van een vertrouwde locatie te vereisen

Het volgende beleid is van toepassing op alle geselecteerde gebruikers die zich willen registreren met de gecombineerde registratie-ervaring en blokkeert de toegang tenzij ze verbinding maken vanaf een locatie die is gemarkeerd als vertrouwd netwerk.

1. Ga in het **Azure Portal**naar **Azure Active Directory** > **beveiliging** > **voorwaardelijke toegang**.
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
1. Onder **Access controls** > **Grant**.
   1. Klik op **toegang blok keren**.
   1. Klik vervolgens op **Selecteren**.
1. Stel **Beleid inschakelen** in op **Aan**.
1. Klik vervolgens op **Opslaan**.

## <a name="next-steps"></a>Volgende stappen

[Algemeen beleid voor voorwaardelijke toegang](concept-conditional-access-policy-common.md)

[Effect bepalen met de modus alleen rapport-alleen voor voorwaardelijke toegang](howto-conditional-access-report-only.md)

[Aanmeld gedrag simuleren met het What If hulp programma voor voorwaardelijke toegang](troubleshoot-conditional-access-what-if.md)
