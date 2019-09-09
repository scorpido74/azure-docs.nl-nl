---
title: Multi-factor Authentication (MFA) en PIM-Azure Active Directory | Microsoft Docs
description: Meer informatie over hoe Azure AD Privileged Identity Management (PIM) multi-factor Authentication (MFA) valideert.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 08/31/2018
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 686c39c9fb1f1ff7c0ecf068a6612f530620d5dc
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804296"
---
# <a name="multi-factor-authentication-mfa-and-pim"></a>Multi-factor Authentication (MFA) en PIM

U wordt aangeraden multi-factor Authentication (MFA) te vereisen voor al uw beheerders. Dit vermindert het risico van een aanval vanwege een wacht woord dat is aangetast.

U kunt vereisen dat gebruikers een MFA-Challenge volt ooien wanneer ze zich aanmelden. U kunt ook vereisen dat gebruikers een MFA-Challenge volt ooien wanneer ze een functie activeren in Azure Active Directory (Azure AD) Privileged Identity Management (PIM). Op deze manier wordt, als de gebruiker een MFA-uitdaging niet heeft voltooid bij het aanmelden, dit door PIM gevraagd.

> [!IMPORTANT]
> Azure MFA werkt nu alleen met werk-of school accounts, niet van micro soft-accounts (meestal een persoonlijk account dat wordt gebruikt om u aan te melden bij micro soft-services zoals Skype, Xbox, Outlook.com, enzovoort). Als gevolg hiervan kan iedereen die gebruikmaakt van een Microsoft-account geen geschikte beheerder zijn omdat ze MFA niet kunnen gebruiken om hun rollen te activeren. Als deze gebruikers de werk belasting met behulp van een Microsoft-account blijven beheren, moet u deze nu voor permanente beheerders verhogen.

## <a name="how-pim-validates-mfa"></a>Hoe PIM MFA valideert

Er zijn twee opties voor het valideren van MFA wanneer een gebruiker een rol activeert.

De eenvoudigste optie is afhankelijk van Azure MFA voor gebruikers die een bevoorrechte rol activeren. Als u dit wilt doen, controleert u eerst of deze gebruikers een licentie hebben, indien nodig, en zijn geregistreerd voor Azure MFA. Zie [Cloud azure multi-factor Authentication implementeren](../authentication/howto-mfa-getstarted.md)voor meer informatie over het implementeren van Azure MFA. Het wordt aanbevolen, maar niet vereist, dat u Azure AD configureert om MFA af te dwingen voor deze gebruikers wanneer ze zich aanmelden. Dit komt doordat de MFA-controles door PIM zelf worden uitgevoerd.

Als gebruikers on-premises verifiëren, kunt u ook uw ID-provider verantwoordelijk maken voor MFA. Als u bijvoorbeeld AD Federation Services hebt geconfigureerd om verificatie op basis van Smart Cards te vereisen voordat u toegang krijgt tot Azure AD, kunt u [cloud resources beveiligen met azure multi-factor Authentication en AD FS](../authentication/howto-mfa-adfs.md) bevat instructies voor het configureren van AD FS claims verzenden naar Azure AD. Wanneer een gebruiker probeert een rol te activeren, wordt door PIM geaccepteerd dat MFA al is gevalideerd voor de gebruiker nadat het de juiste claims heeft ontvangen.

## <a name="next-steps"></a>Volgende stappen

- [Instellingen voor Azure AD-rollen configureren in PIM](pim-how-to-change-default-settings.md)
- [Instellingen voor Azure-resource rollen configureren in PIM](pim-resource-roles-configure-role-settings.md)
