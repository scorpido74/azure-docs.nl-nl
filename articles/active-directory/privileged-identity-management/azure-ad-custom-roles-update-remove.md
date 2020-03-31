---
title: Aangepaste azure-functie voor AD bijwerken of verwijderen - Pim voor privileged identity management (PIM)
description: Een aangepaste Azure-roltoewijzing privileged Identity Management (PIM) bijwerken of verwijderen
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/06/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad5747be47e250cf9c623cc40d21d12c91ee16f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77499110"
---
# <a name="update-or-remove-an-assigned-azure-ad-custom-role-in-privileged-identity-management"></a>Een toegewezen aangepaste Azure AD-rol in Privileged Identity Management bijwerken of verwijderen

In dit artikel vindt u hoe u Privileged Identity Management (PIM) gebruiken om just-in-time- en tijdgebonden toewijzing bij te werken of te verwijderen naar aangepaste rollen die zijn gemaakt voor toepassingsbeheer in de beheerervaring azure active directory (Azure AD). 

- Zie [Aangepaste beheerdersrollen in Azure Active Directory (preview)](../users-groups-roles/roles-custom-overview.md)voor meer informatie over het maken van aangepaste rollen om toepassingsbeheer in Azure AD te delegeren. 
- Als u Privileged Identity Management nog niet hebt gebruikt, krijgt u meer informatie bij [Start met Privileged Identity Management](pim-getting-started.md).

> [!NOTE]
> Aangepaste Azure AD-rollen zijn niet geïntegreerd met de ingebouwde maprollen tijdens de preview. Zodra de capaciteit algemeen beschikbaar is, zal het rolbeheer plaatsvinden in de ingebouwde rollenervaring. Als u de volgende banner ziet, moeten deze rollen worden beheerd [in de ervaring met ingebouwde rollen](pim-how-to-add-role-to-user.md) en is dit artikel niet van toepassing:
>
> [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

## <a name="update-or-remove-an-assignment"></a>Een toewijzing bijwerken of verwijderen

Volg deze stappen om een bestaande aangepaste roltoewijzing bij te werken of te verwijderen.

1. Meld u aan bij [Privileged Identity Management](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart) in de Azure-portal met een gebruikersaccount dat is toegewezen aan de rol van privileged role administrator.
1. Selecteer **aangepaste azure-rollen voor AD (voorbeeld)**.

    ![De aangepaste rollenvoorA Voorrol van Azure AD selecteren om in aanmerking komende roltoewijzingen te bekijken](./media/azure-ad-custom-roles-assign/view-custom.png)

1. Selecteer **Rollen** om een lijst **met toewijzingen** met aangepaste rollen voor Azure AD-toepassingen weer te geven.

    ![In rollen selecteren ziet u de lijst met in aanmerking komende roltoewijzingen](./media/azure-ad-custom-roles-update-remove/assignments-list.png)

1. Selecteer de rol die u wilt bijwerken of verwijderen.
1. Zoek de roltoewijzing op de tabbladen **In aanmerking komende rollen** of Actieve **rollen.**
1. Selecteer **Bijwerken** of **Verwijderen** om de roltoewijzing bij te werken of te verwijderen.

    ![Verwijderen of bijwerken selecteren in de in aanmerking komende roltoewijzing](./media/azure-ad-custom-roles-update-remove/remove-update.png)

## <a name="next-steps"></a>Volgende stappen

- [Een aangepaste Azure AD-rol activeren](azure-ad-custom-roles-assign.md)
- [Een aangepaste Azure AD-rol toewijzen](azure-ad-custom-roles-assign.md)
- [Een aangepaste azure-functietoewijzing configureren](azure-ad-custom-roles-configure.md)
