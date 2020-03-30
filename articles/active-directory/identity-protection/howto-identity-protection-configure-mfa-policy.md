---
title: Het MFA-registratiebeleid configureren - Azure Active Directory Identity Protection
description: Meer informatie over het configureren van het azure AD Identity Protection-beleid voor het registreren van meervoudige verificatie.
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
ms.openlocfilehash: 2fa6a4cf184b426355f62117ea51642127eee529
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74382145"
---
# <a name="how-to-configure-the-azure-multi-factor-authentication-registration-policy"></a>How To: Het registratiebeleid voor azure-verificatie met meerdere factoren configureren

Azure AD Identity Protection helpt u bij het beheren van de uitrol van Azure Multi-Factor Authentication (MFA) registratie door een voorwaardelijke toegangsbeleid te configureren om MFA-registratie te vereisen, ongeacht bij welke moderne verificatie-app u zich aanmeldt.

## <a name="what-is-the-azure-multi-factor-authentication-registration-policy"></a>Wat is het azure multi-factor authentication-registratiebeleid?

Azure Multi-Factor Authentication biedt een middel om te controleren wie u meer gebruikt dan alleen een gebruikersnaam en wachtwoord. Het biedt een tweede beveiligingslaag aan aanmeldingen van gebruikers. Om ervoor te doen dat gebruikers kunnen reageren op MFA-prompts, moeten ze zich eerst registreren voor Azure Multi-Factor Authentication.

We raden u aan Azure Multi-Factor Authentication te gebruiken voor aanmeldingen van gebruikers, omdat dit het zo is:

- Levert sterke verificatie via een reeks verificatieopties.
- Speelt een belangrijke rol bij het voorbereiden van uw organisatie om zelf te herstellen van risicodetecties in Identiteitsbescherming.

Zie Wat is Azure Multi-Factor Authentication voor meer informatie over Azure [Multi-Factor Authentication?](../authentication/howto-mfa-getstarted.md)

## <a name="policy-configuration"></a>Beleidsconfiguratie

1. Navigeer naar de [Azure-portal](https://portal.azure.com).
1. Blader naar **het** > **mfa-registratiebeleid**voor Azure Active Directory**Security** > **Identity Protection.** > 
   1. Onder **Opdrachten**
      1. **Gebruikers** - Kies **Alle gebruikers** of Selecteer personen **en groepen** als u uw implementatie beperkt.
         1. Optioneel u ervoor kiezen om gebruikers uit te sluiten van het beleid.
   1. Onder **Besturingselementen**
      1. Controleer of het selectievakje **Azure MFA-registratie vereisen** is ingeschakeld en kies **Selecteren**.
   1. **Beleid afdwingen** - **op**
   1. **Opslaan**

## <a name="user-experience"></a>Gebruikerservaring

Azure Active Directory Identity Protection vraagt uw gebruikers om zich te registreren wanneer ze zich de volgende keer interactief aanmelden en ze hebben 14 dagen de tijd om de registratie te voltooien. Tijdens deze periode van 14 dagen kunnen ze de registratie omzeilen, maar aan het einde van de periode moeten ze zich registreren voordat ze het aanmeldingsproces kunnen voltooien.

Zie voor een overzicht van de gerelateerde gebruikerservaring:

- [Aanmeldingservaringen met Azure AD-identiteitsbeveiliging](concept-identity-protection-user-experience.md).  

## <a name="next-steps"></a>Volgende stappen

- [Aanmeldings- en gebruikersrisicobeleid inschakelen](howto-identity-protection-configure-risk-policies.md)

- [Azure AD selfservice wachtwoord opnieuw instellen inschakelen](../authentication/howto-sspr-deployment.md)

- [Azure Multi-Factor Authentication inschakelen](../authentication/howto-mfa-getstarted.md)
