---
title: Rollen die u niet beheren in Privileged Identity Management - Azure Active Directory | Microsoft Documenten
description: Beschrijft de rollen die u niet beheren in Azure AD Privileged Identity Management (PIM).
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
ms.sourcegitcommit: 515482c6348d5bef78bb5def9b71c01bb469ed80
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80607526"
---
# <a name="roles-you-cant-manage-in-privileged-identity-management"></a>Rollen die u niet beheren in Privileged Identity Management

Met Azure Active Directory (Azure AD) Privileged Identity Management (PIM) u alle [Azure AD-rollen](../users-groups-roles/directory-assign-admin-roles.md) en alle [Azure-rollen](../../role-based-access-control/built-in-roles.md)beheren. Azure-rollen kunnen ook uw aangepaste rollen bevatten die zijn gekoppeld aan uw beheergroepen, abonnementen, brongroepen en resources. Er zijn echter weinig rollen die u niet beheren. In dit artikel worden de rollen beschreven die u niet beheren in Privileged Identity Management.

## <a name="classic-subscription-administrator-roles"></a>Klassieke abonnementsbeheerdersrollen

U de volgende klassieke abonnementsbeheerdersrollen niet beheren in Privileged Identity Management:

- Accountbeheerder
- Servicebeheerder
- Medebeheerder

Zie Klassieke functies voor [abonnementsbeheerders, Azure RBAC-rollen en Azure AD-beheerdersrollen](../../role-based-access-control/rbac-and-directory-admin-roles.md)voor meer informatie over de klassieke functies van abonnementsbeheerder.

## <a name="what-about-office-365-admin-roles"></a>Hoe zit het met Office 365-beheerdersrollen?

We ondersteunen alle Office365-rollen in de azure AD-rollen- en beheerdersportalervaring, zoals Exchange Administrator en SharePoint Administrator, maar we ondersteunen geen specifieke rollen binnen Exchange RBAC of SharePoint RBAC. Zie [Office 365-beheerdersrollen](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles)voor meer informatie over deze Office 365-services.

> [!NOTE]
> In aanmerking komende gebruikers voor de Rol van SharePoint-beheerder en alle rollen die toegang proberen te krijgen tot het Microsoft Security and Compliance Center, kunnen vertragingen oplopen tot enkele uren na het activeren van hun rol. We werken samen met die teams om de problemen op te lossen.

## <a name="next-steps"></a>Volgende stappen

- [Azure AD-rollen toewijzen in Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Azure-bronrollen toewijzen in Privileged Identity Management](pim-resource-roles-assign-roles.md)
