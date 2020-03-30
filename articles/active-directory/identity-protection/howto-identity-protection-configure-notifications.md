---
title: Azure Active Directory-meldingen voor identiteitsbeveiliging
description: Meer informatie over hoe meldingen uw onderzoeksactiviteiten ondersteunen.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c83aa6e476bbd898999fb6efe490c7847a809ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77120124"
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Azure Active Directory-meldingen voor identiteitsbeveiliging

Azure AD Identity Protection verzendt twee soorten geautomatiseerde meldingen om u te helpen bij het beheren van gebruikersrisico- en risicodetecties:

- Gebruikers met risico's gedetecteerde e-mail
- Wekelijkse samenvatting e-mail

Dit artikel geeft u een overzicht van beide e-mails met meldingen.

## <a name="users-at-risk-detected-email"></a>Gebruikers met risico's gedetecteerde e-mail

In reactie op een gedetecteerd account met risico genereert Azure AD Identity Protection een e-mailwaarschuwing waarbij gebruikers die risico lopen als onderwerp **worden gedetecteerd.** De e-mail bevat een link naar de **[gebruikers gemarkeerd voor risicorapport.](../reports-monitoring/concept-user-at-risk.md)** Als een best practice, moet u onmiddellijk onderzoeken van de gebruikers in gevaar.

Met de configuratie voor deze waarschuwing u opgeven op welk gebruikersrisiconiveau u de waarschuwing wilt genereren. De e-mail wordt gegenereerd wanneer het risiconiveau van de gebruiker bereikt wat u hebt opgegeven; u ontvangt echter geen nieuwe gebruikers met risico's gedetecteerde e-mailwaarschuwingen voor deze gebruiker nadat ze naar dit gebruikersrisiconiveau zijn verhuisd. Als u bijvoorbeeld het beleid instelt op waarschuwing voor het gemiddelde gebruikersrisico en uw gebruiker John overgaat op gemiddeld risico, ontvangt u de gebruikers die risico's lopen en e-mail voor John. U ontvangt echter geen tweede gebruiker met een risicomelding als John vervolgens overgaat op een hoog risico of extra risicodetecties heeft.

![Gebruikers met risico's gedetecteerde e-mail](./media/howto-identity-protection-configure-notifications/01.png)

### <a name="configure-users-at-risk-detected-alerts"></a>Gebruikers met risicomeldingen configureren

Als beheerder u het:

- **Het gebruikersrisiconiveau dat de generatie van deze e-mail activeert** - Standaard is het risiconiveau ingesteld op "Hoog" risico.
- **De ontvangers van deze e-mail** - Standaard bevatten ontvangers alle globale beheerders. Globale beheerders kunnen ook andere globale beheerders, beveiligingsbeheerders, beveiligingslezers als ontvangers toevoegen.
   - Optioneel u **extra e-mails toevoegen om waarschuwingsmeldingen te ontvangen** deze functie is een voorbeeld en gebruikers gedefinieerd moeten de juiste machtigingen om de gekoppelde rapporten in de Azure-portal te bekijken.

Configureer de gebruikers met risico-e-mail in de **Azure-portal** onder **Azure Active Directory** > **Security** > **Identity Protection** > **Gebruikers met risicogedetecteerde waarschuwingen**.

## <a name="weekly-digest-email"></a>Wekelijkse samenvatting e-mail

De wekelijkse samenvatting e-mail bevat een samenvatting van nieuwe risicodetecties.  
Het omvat:

- Gebruikers die risico lopen
- Verdachte activiteiten
- Gedetecteerde kwetsbaarheden
- Links naar de gerelateerde rapporten in Identiteitsbescherming

![Wekelijkse samenvatting e-mail](./media/howto-identity-protection-configure-notifications/400.png)

Standaard bevatten ontvangers alle globale beheerders. Globale beheerders kunnen ook andere globale beheerders, beveiligingsbeheerders, beveiligingslezers als ontvangers toevoegen.

### <a name="configure-weekly-digest-email"></a>E-mail met wekelijkse samenvatting configureren

Als beheerder u een wekelijkse samenvattingse-mail in- of uitschakelen en de gebruikers kiezen die zijn toegewezen om de e-mail te ontvangen.

Configureer de wekelijkse samenvattingse-e-mail in de **Azure-portal** onder **Azure Active Directory** > **Security** > **Identity Protection** > **Weekly digest**.

## <a name="see-also"></a>Zie ook

- [Azure Active Directory-identiteitsbeveiliging](../active-directory-identityprotection.md)
