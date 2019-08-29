---
title: Het registratie beleid voor multi-factor Authentication configureren in Azure Active Directory Identity Protection | Microsoft Docs
description: Meer informatie over het configureren van het registratie beleid voor Azure AD Identity Protection multi-factor Authentication.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 05/01/2019
author: MicrosoftGuyJFlo
manager: daveba
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 939f08fd16cf27e641cf6436a00396ad2db8e6c3
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70126397"
---
# <a name="how-to-configure-the-azure-multi-factor-authentication-registration-policy"></a>Procedure: Het registratie beleid voor Azure Multi-Factor Authentication configureren

Azure AD Identity Protection helpt u bij het beheren van de implementatie van multi-factor Authentication (MFA) door het configureren van een beleid voor voorwaardelijke toegang om MFA-registratie te vereisen, ongeacht de moderne verificatie-app waarmee u zich aanmeldt. In dit artikel wordt uitgelegd wat het beleid kan gebruiken en hoe het kan worden geconfigureerd.



## <a name="what-is-the-azure-multi-factor-authentication-registration-policy"></a>Wat is het Azure Multi-Factor Authentication-registratie beleid?

Azure Multi-Factor Authentication biedt een manier om te controleren wie u meer dan alleen een gebruikers naam en wacht woord gebruikt. Het biedt een tweede beveiligingslaag voor gebruikers aanmeldingen. Om gebruikers te kunnen reageren op MFA-prompts, moeten ze zich eerst registreren voor Azure Multi-Factor Authentication.

U wordt aangeraden Azure Multi-Factor Authentication te vereisen voor aanmeldingen van gebruikers omdat:

- Biedt sterke verificatie met een scala aan eenvoudige verificatie opties
- Speelt een belang rijke rol bij het voorbereiden van uw organisatie voor het beveiligen en herstellen van risico detecties in identiteits beveiliging

Zie [Wat is Azure multi-factor Authentication?](../authentication/howto-mfa-getstarted.md) voor meer informatie over MFA.

## <a name="how-do-i-access-the-registration-policy"></a>Hoe kan ik toegang tot het registratie beleid?

Het MFA-registratie beleid bevindt zich in de sectie **configureren** op de [pagina Azure AD Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy).

![MFA-beleid](./media/howto-mfa-policy/1014.png)

## <a name="policy-settings"></a>Beleidsinstellingen

Wanneer u het MFA-registratie beleid configureert, moet u de volgende configuratie wijzigingen aanbrengen:

- De gebruikers en groepen waarop het beleid van toepassing is. Vergeet niet om de accounts voor [nood toegang](../users-groups-roles/directory-emergency-access.md)van uw organisatie uit te sluiten.

    ![Gebruikers en groepen](./media/howto-mfa-policy/11.png)

- Het besturings element dat u wilt afdwingen- **Azure MFA-registratie vereisen**

    ![Toegang](./media/howto-mfa-policy/12.png)

- Het beleid voor afdwingen moet worden ingesteld op **on**.

    ![Beleid afdwingen](./media/howto-mfa-policy/14.png)

- Uw beleid **Opslaan**

## <a name="user-experience"></a>Gebruikerservaring

Azure Active Directory Identity Protection vraagt uw gebruikers de volgende keer dat ze zich interactief aanmelden, zich aan te melden.

Zie voor een overzicht van de gerelateerde gebruikers ervaring:

- [Registratie stroom multi-factor Authentication](flows.md#multi-factor-authentication-registration).  
- [Aanmeld ervaring met Azure AD Identity Protection](flows.md).  

## <a name="next-steps"></a>Volgende stappen

Zie overzicht van de [Azure AD Identity Protection](overview.md)voor een overzicht van Azure AD Identity Protection.
