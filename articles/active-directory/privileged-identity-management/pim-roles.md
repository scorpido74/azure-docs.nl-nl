---
title: Rollen die u niet kunt beheren in Privileged Identity Management-Azure Active Directory | Microsoft Docs
description: Hierin worden de functies beschreven die u niet kunt beheren in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 03/31/2020
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: b6eaa50f57dd8037ef0ad96b69284f565bd3558f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80607526"
---
# <a name="roles-you-cant-manage-in-privileged-identity-management"></a>Rollen die u niet kunt beheren in Privileged Identity Management

Met Azure Active Directory (Azure AD) Privileged Identity Management (PIM) kunt u alle [Azure AD-rollen](../users-groups-roles/directory-assign-admin-roles.md) en alle [Azure-rollen](../../role-based-access-control/built-in-roles.md)beheren. Azure-rollen kunnen ook uw aangepaste rollen bevatten die zijn gekoppeld aan uw beheer groepen, abonnementen, resource groepen en resources. Er zijn echter enkele rollen die u niet kunt beheren. In dit artikel worden de functies beschreven die u niet kunt beheren in Privileged Identity Management.

## <a name="classic-subscription-administrator-roles"></a>Klassieke abonnementsbeheerdersrollen

U kunt de volgende klassieke abonnements beheerders rollen niet beheren in Privileged Identity Management:

- Accountbeheerder
- Servicebeheerder
- Medebeheerder

Zie voor meer informatie over de beheerders rollen van klassieke abonnementen [klassieke abonnements beheerders rollen, Azure RBAC-rollen en Azure AD-beheerders rollen](../../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="what-about-office-365-admin-roles"></a>Hoe zit het met Office 365-beheerders rollen?

We ondersteunen alle Office365-functies in de Azure AD-functies en-Portal-ervaring, zoals Exchange-beheerder en share point-beheerder, maar ondersteunen geen specifieke rollen binnen Exchange RBAC of share point RBAC. Zie voor meer informatie over deze Office 365-Services [office 365-beheerders rollen](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles).

> [!NOTE]
> Gebruikers die in aanmerking komen voor de share point-beheerdersrol en de rollen die toegang proberen te krijgen tot het micro soft Security and Compliance Center kunnen vertragingen van een paar uur in beslag nemen na het activeren van hun rol. We werken samen met die teams om de problemen op te lossen.

## <a name="next-steps"></a>Volgende stappen

- [Azure AD-rollen toewijzen in Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Azure-resource rollen toewijzen in Privileged Identity Management](pim-resource-roles-assign-roles.md)
