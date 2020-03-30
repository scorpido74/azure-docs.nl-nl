---
title: MFA of 2FA en Privileged Identity Management - Azure AD | Microsoft Documenten
description: Ontdek hoe Azure AD Privileged Identity Management (PIM) multi-factor authenticatie (MFA) valideert.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6df593909c3ae5962e413eb84e64196fade0326b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022140"
---
# <a name="multi-factor-authentication-and-privileged-identity-management"></a>Meervoudige verificatie en privileged identity management

We raden u aan multi-factor authenticatie (MFA) voor al uw beheerders te vereisen. Dit vermindert het risico op een aanval als gevolg van een gecompromitteerd wachtwoord.

U vereisen dat gebruikers een multi-factor authenticatie uitdaging voltooien wanneer ze zich aanmelden. U ook eisen dat gebruikers een multi-factor authenticatieuitdaging voltooien wanneer ze een rol activeren in Azure Active Directory (Azure AD) Privileged Identity Management (PIM). Op deze manier wordt de gebruiker, als hij een multi-factor authenticatieuitdaging niet heeft voltooid toen hij zich aanmeldde, gevraagd dit te doen door Privileged Identity Management.

> [!IMPORTANT]
> Op dit moment werkt Azure Multi-Factor Authentication alleen met werk- of schoolaccounts, niet met persoonlijke microsoft-accounts (meestal een persoonlijk account dat wordt gebruikt om u aan te melden bij Microsoft-services zoals Skype, Xbox of Outlook.com). Hierdoor kan iedereen die een persoonlijk account gebruikt geen in aanmerking komende beheerder zijn omdat ze geen multi-factor authenticatie kunnen gebruiken om hun rollen te activeren. Als deze gebruikers workloads moeten blijven beheren met een Microsoft-account, u deze voorlopig verhogen tot permanente beheerders.

## <a name="how-pim-validates-mfa"></a>Hoe PIM MFA valideert

Er zijn twee opties voor het valideren van meervoudige verificatie wanneer een gebruiker een rol activeert.

De eenvoudigste optie is om te vertrouwen op Azure Multi-Factor Authentication voor gebruikers die een bevoorrechte rol activeren. Controleer hiervoor eerst of deze gebruikers een licentie hebben en zich hebben geregistreerd voor Azure Multi-Factor Authentication. Zie Azure Multi-Factor Authentication implementeren in de cloud voor meer informatie over het implementeren van Azure [Multi-Factor Authentication.](../authentication/howto-mfa-getstarted.md) Het wordt aanbevolen, maar niet vereist, dat u Azure AD configureert om meervoudige verificatie af te dwingen voor deze gebruikers wanneer ze zich aanmelden. Dit komt omdat de multi-factor authenticatie controles zullen worden uitgevoerd door Privileged Identity Management zelf.

Als gebruikers on-premises verifiëren, u uw identiteitsprovider ook verantwoordelijk maken voor meervoudige verificatie. Als u bijvoorbeeld AD Federation Services hebt geconfigureerd om verificatie op basis van smartcard te vereisen voordat u toegang krijgt tot Azure AD, bevat [het beveiligen van cloudresources met Azure Multi-Factor Authentication en AD FS](../authentication/howto-mfa-adfs.md) instructies voor het configureren van AD FS om claims naar Azure AD te verzenden. Wanneer een gebruiker een rol probeert te activeren, accepteert Privileged Identity Management dat meervoudige verificatie al is gevalideerd voor de gebruiker zodra deze de juiste claims ontvangt.

## <a name="next-steps"></a>Volgende stappen

- [Azure AD-rolinstellingen configureren in Privileged Identity Management](pim-how-to-change-default-settings.md)
- [Azure-bronrolinstellingen configureren in Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
