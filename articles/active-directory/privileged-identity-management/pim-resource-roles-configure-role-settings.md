---
title: Instellingen voor Azure-resource rollen configureren in PIM-Azure Active Directory | Microsoft Docs
description: Meer informatie over het configureren van instellingen voor Azure-resource rollen in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 88645d07ff213b0b7a56f2b081f0aaed01fd2929
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804197"
---
# <a name="configure-azure-resource-role-settings-in-pim"></a>Instellingen voor Azure-resource rollen configureren in PIM

Wanneer u instellingen voor Azure-resource functies configureert, definieert u de standaard instellingen die worden toegepast op toewijzingen van Azure-resource rollen in Azure Active Directory (Azure AD) Privileged Identity Management (PIM). Gebruik de volgende procedures om de goedkeurings werk stroom te configureren en op te geven wie aanvragen kan goed keuren of weigeren.

## <a name="open-role-settings"></a>Rolinstellingen openen

Volg deze stappen om de instellingen voor een Azure-resource functie te openen.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/) met een gebruiker die lid is van de beheerdersrol [privileged Role](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) .

1. Open **Azure AD privileged Identity Management**.

1. Klik op **Azure-resources**.

1. Klik op de resource die u wilt beheren, zoals een abonnement of beheer groep.

    ![Pagina met Azure-resources met resources die kunnen worden beheerd](./media/pim-resource-roles-configure-role-settings/resources-list.png)

1. Klik op **rolinstellingen**.

    ![Pagina rolinstellingen met een lijst met Azure-resource rollen](./media/pim-resource-roles-configure-role-settings/resources-role-settings.png)

1. Klik op de rol waarvan u de instellingen wilt configureren.

    ![Pagina Details van de functie-instelling met een lijst met verschillende toewijzings-en activerings instellingen](./media/pim-resource-roles-configure-role-settings/resources-role-setting-details.png)

1. Klik op **bewerken** om het deel venster functie-instellingen te openen.

    ![Pagina Rolinstellingen bewerken met opties voor het bijwerken van de toewijzings-en activerings instellingen](./media/pim-resource-roles-configure-role-settings/resources-role-settings-edit.png)

    In het instellingen deel venster voor elke rol zijn er verschillende instellingen die u kunt configureren.

## <a name="assignment-duration"></a>Toewijzings duur

U kunt kiezen uit twee opties voor de toewijzings duur voor elk toewijzings type (in aanmerking komend en actief) wanneer u instellingen voor een rol configureert. Deze opties worden de standaard maximale duur wanneer een lid wordt toegewezen aan de rol in PIM.

U kunt kiezen uit een van **deze opties** voor de gewenste duur van de toewijzing:

| | |
| --- | --- |
| **Permanente toewijzing in aanmerking komend toestaan** | Resource beheerders kunnen permanent in aanmerking komend lidmaatschap toewijzen. |
| **In aanmerking komende toewijzing laten verlopen na** | Resource beheerders kunnen vereisen dat alle in aanmerking komende toewijzingen een opgegeven begin-en eind datum hebben. |

En u kunt een van deze **actieve** toewijzings duur opties kiezen:

| | |
| --- | --- |
| **Permanente actieve toewijzing toestaan** | Resource beheerders kunnen permanent actief lidmaatschap toewijzen. |
| **Actieve toewijzing laten verlopen na** | Resource beheerders kunnen vereisen dat alle actieve toewijzingen een opgegeven begin-en eind datum hebben. |

> [!NOTE] 
> Alle toewijzingen met een opgegeven eind datum kunnen worden vernieuwd door resource beheerders. Leden kunnen ook selfservice aanvragen initiëren om roltoewijzingen uit te [breiden of te vernieuwen](pim-resource-roles-renew-extend.md).

## <a name="require-multi-factor-authentication"></a>Multi-factor Authentication vereisen

PIM biedt een optionele afdwinging van Azure Multi-Factor Authentication (MFA) voor twee verschillende scenario's.

### <a name="require-multi-factor-authentication-on-active-assignment"></a>Multi-Factor Authentication vereisen voor actieve toewijzing

In sommige gevallen wilt u mogelijk een lid toewijzen aan een rol voor een korte duur (bijvoorbeeld één dag). In dit geval zijn de toegewezen leden niet nodig om activering aan te vragen. In dit scenario kan PIM geen MFA afdwingen wanneer het lid gebruik maakt van de roltoewijzing, omdat ze al actief zijn in de rol vanaf het moment dat ze worden toegewezen.

Als u er zeker van wilt zijn dat de resource beheerder die aan de toewijzing voldoet, weet wie ze zijn, kunt u MFA afdwingen voor actieve toewijzing door het selectie vakje **vereisen multi-factor Authentication op actieve toewijzing** in te scha kelen.

### <a name="require-multi-factor-authentication-on-activation"></a>Multi-Factor Authentication vereisen bij activering

U kunt vereisen dat in aanmerking komende leden van een rol MFA uitvoeren voordat ze kunnen activeren. Dit proces zorgt ervoor dat de gebruiker die de activering aanvraagt, een redelijke zekerheid krijgt. Het afdwingen van deze optie beschermt kritieke resources in situaties waarin het gebruikers account mogelijk is aangetast.

Als u wilt dat een in aanmerking komend lid verplicht is MFA uit te voeren vóór activering, schakelt u het selectie vakje **multi-factor Authentication vereisen bij activering** in.

Zie [multi-factor Authentication (MFA) en PIM](pim-how-to-require-mfa.md)voor meer informatie.

## <a name="activation-maximum-duration"></a>Maximale activeringsduur

Gebruik de schuif regelaar **maximale duur activering** om de maximum tijd in uren in te stellen dat een rol actief blijft voordat deze verloopt. Deze waarde kan tussen 1 en 24 uur liggen.

## <a name="require-justification"></a>Reden vereisen

U kunt vereisen dat leden een reden voor een actieve toewijzing invoeren of wanneer ze worden geactiveerd. Als u dit wilt doen, schakelt u het selectie vakje **uitvulling vereist op actieve toewijzing** of het vakje **uitvulling vereisen bij activering** in.

## <a name="require-approval-to-activate"></a>Goedkeuring vereisen om deze rol te activeren

Als u wilt dat goed keuring vereist is om een rol te activeren, voert u de volgende stappen uit.

1. Controleer het selectie vakje **goed keuring vereisen om te activeren** .

1. Klik op **fiatteurs selecteren** om het deel venster lid of groep selecteren te openen.

    ![Een lid of groeps deel venster selecteren voor het selecteren van goed keurders](./media/pim-resource-roles-configure-role-settings/resources-role-settings-select-approvers.png)

1. Selecteer ten minste één lid of groep en klik vervolgens op **selecteren**. U kunt elke wille keurige combi natie van leden en groepen toevoegen. U moet ten minste één fiatteur selecteren. Er zijn geen standaard fiatteurs.

    Uw selecties worden weer gegeven in de lijst met geselecteerde goed keurders.

1. Wanneer u de instellingen van uw rol hebt opgegeven, klikt u op **bijwerken** om uw wijzigingen op te slaan.

## <a name="next-steps"></a>Volgende stappen

- [Azure-resource rollen toewijzen in PIM](pim-resource-roles-assign-roles.md)
- [Beveiligings waarschuwingen configureren voor Azure-resource rollen in PIM](pim-resource-roles-configure-alerts.md)
