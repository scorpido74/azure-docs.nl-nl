---
title: Het MFA-registratie beleid configureren-Azure Active Directory Identity Protection
description: Meer informatie over het configureren van het registratie beleid voor Azure AD Identity Protection multi-factor Authentication.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 06/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: d4c6b57eaa2a68906053faade48dd0e63fbf0db7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84464331"
---
# <a name="how-to-configure-the-azure-multi-factor-authentication-registration-policy"></a>Procedure: het Azure Multi-Factor Authentication-registratie beleid configureren

Azure AD Identity Protection helpt u bij het beheren van de uitrollen van de registratie van Azure Multi-Factor Authentication (MFA) door een beleid voor voorwaardelijke toegang te configureren om MFA-registratie te vereisen, ongeacht de moderne verificatie-app waarmee u zich aanmeldt.

## <a name="what-is-the-azure-multi-factor-authentication-registration-policy"></a>Wat is het Azure Multi-Factor Authentication-registratie beleid?

Azure Multi-Factor Authentication biedt een manier om te controleren wie u meer dan alleen een gebruikers naam en wacht woord gebruikt. Het biedt een tweede beveiligingslaag voor gebruikers aanmeldingen. Om gebruikers te kunnen reageren op MFA-prompts, moeten ze zich eerst registreren voor Azure Multi-Factor Authentication.

U wordt aangeraden Azure Multi-Factor Authentication te vereisen voor aanmeldingen van gebruikers omdat:

- Biedt sterke verificatie via verschillende verificatie opties.
- Speelt een belang rijke rol bij het voorbereiden van uw organisatie op zelf herstel van risico detecties in identiteits beveiliging.

Zie [Wat is azure multi-factor Authentication?](../authentication/howto-mfa-getstarted.md) voor meer informatie over Azure multi-factor Authentication.

## <a name="policy-configuration"></a>Beleidsconfiguratie

1. Navigeer naar [Azure Portal](https://portal.azure.com).
1. Blader naar **Azure Active Directory**  >  beleid voor MFA-registratie van**beveiligings**  >  **identiteits beveiliging**  >  **MFA registration policy**.
   1. Onder **toewijzingen**
      1. **Gebruikers** : Kies **alle gebruikers** of **Selecteer individuen en groepen** als u de implementatie wilt beperken.
         1. Optioneel kunt u ervoor kiezen om gebruikers uit te sluiten van het beleid.
   1. Onder **besturings elementen**
      1. Zorg ervoor dat het selectie vakje **Azure MFA-registratie vereist** is ingeschakeld en kies **selecteren**.
   1. **Beleid**  -  afdwingen **Op**
   1. **Opslaan**

## <a name="user-experience"></a>Gebruikerservaring

Azure Active Directory Identity Protection vraagt uw gebruikers om zich te registreren wanneer ze zich de volgende keer interactief aanmelden en ze hebben 14 dagen de registratie kunnen volt ooien. Tijdens deze periode van 14 dagen kunnen ze de registratie overs Laan, maar aan het einde van de periode dat ze moeten worden geregistreerd voordat ze het aanmeldings proces kunnen volt ooien.

Zie voor een overzicht van de gerelateerde gebruikers ervaring:

- [Aanmeld ervaring met Azure AD Identity Protection](concept-identity-protection-user-experience.md).  

## <a name="next-steps"></a>Volgende stappen

- [Aanmeldings-en gebruikers risico beleid inschakelen](howto-identity-protection-configure-risk-policies.md)

- [Selfservice voor wachtwoord herstel van Azure AD inschakelen](../authentication/howto-sspr-deployment.md)

- [Azure Multi-Factor Authentication inschakelen](../authentication/howto-mfa-getstarted.md)
