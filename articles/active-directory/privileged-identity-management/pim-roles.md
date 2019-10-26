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
ms.date: 10/23/2019
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fac7074cf85a585c93ece60be9eea8ffb9a6345
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72895209"
---
# <a name="roles-you-cant-manage-in-privileged-identity-management"></a>Rollen die u niet kunt beheren in Privileged Identity Management

Met Azure Active Directory (Azure AD) Privileged Identity Management (PIM) kunt u alle [Azure AD-rollen](../users-groups-roles/directory-assign-admin-roles.md) en alle [Azure-resource rollen](../../role-based-access-control/built-in-roles.md)beheren. Deze rollen omvatten ook uw aangepaste rollen die zijn gekoppeld aan uw beheer groepen, abonnementen, resource groepen en resources. Er zijn echter enkele rollen die u niet kunt beheren. In dit artikel worden de functies beschreven die u niet kunt beheren in Privileged Identity Management.

## <a name="classic-subscription-administrator-roles"></a>Klassieke abonnementsbeheerdersrollen

U kunt de volgende klassieke abonnements beheerders rollen niet beheren in Privileged Identity Management:

- Accountbeheerder
- Servicebeheerder
- Medebeheerder

Zie voor meer informatie over de beheerders rollen van klassieke abonnementen [klassieke abonnements beheerders rollen, Azure RBAC-rollen en Azure AD-beheerders rollen](../../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="what-about-office-365-admin-roles"></a>Hoe zit het met Office 365-beheerders rollen?

Rollen in Exchange Online of share point online, met uitzonde ring van Exchange-beheerder en share point-beheerder, worden niet weer gegeven in azure AD en kunnen dus niet worden beheerd in Privileged Identity Management. Zie voor meer informatie over deze Office 365-Services [office 365-beheerders rollen](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles).

> [!NOTE]
> Share point-beheerder heeft beheerders toegang tot share point online via het share point online-beheer centrum en kan bijna elke taak uitvoeren in share point online. In aanmerking komende gebruikers kunnen vertragingen ondervinden bij het gebruik van deze rol in share point na het activeren van Privileged Identity Management.

## <a name="next-steps"></a>Volgende stappen

- [Azure AD-rollen toewijzen in Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Azure-resource rollen toewijzen in Privileged Identity Management](pim-resource-roles-assign-roles.md)
