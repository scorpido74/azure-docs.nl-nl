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
ms.date: 10/23/2019
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fac7074cf85a585c93ece60be9eea8ffb9a6345
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72895209"
---
# <a name="roles-you-cant-manage-in-privileged-identity-management"></a>Rollen die u niet beheren in Privileged Identity Management

Met Azure Active Directory (Azure AD) Privileged Identity Management (PIM) u alle [Azure AD-rollen](../users-groups-roles/directory-assign-admin-roles.md) en alle [Azure-bronrollen](../../role-based-access-control/built-in-roles.md)beheren. Deze rollen omvatten ook uw aangepaste rollen die zijn gekoppeld aan uw beheergroepen, abonnementen, resourcegroepen en resources. Er zijn echter weinig rollen die u niet beheren. In dit artikel worden de rollen beschreven die u niet beheren in Privileged Identity Management.

## <a name="classic-subscription-administrator-roles"></a>Klassieke abonnementsbeheerdersrollen

U de volgende klassieke abonnementsbeheerdersrollen niet beheren in Privileged Identity Management:

- Accountbeheerder
- Servicebeheerder
- Medebeheerder

Zie Klassieke functies voor [abonnementsbeheerders, Azure RBAC-rollen en Azure AD-beheerdersrollen](../../role-based-access-control/rbac-and-directory-admin-roles.md)voor meer informatie over de klassieke functies van abonnementsbeheerder.

## <a name="what-about-office-365-admin-roles"></a>Hoe zit het met Office 365-beheerdersrollen?

Rollen binnen Exchange Online of SharePoint Online, met uitzondering van Exchange Administrator en SharePoint Administrator, zijn niet vertegenwoordigd in Azure AD en kunnen dus niet worden beheerd in Privileged Identity Management. Zie [Office 365-beheerdersrollen](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles)voor meer informatie over deze Office 365-services.

> [!NOTE]
> SharePoint-beheerder heeft beheerderstoegang tot SharePoint Online via het SharePoint Online-beheercentrum en kan bijna elke taak uitvoeren in SharePoint Online. In aanmerking komende gebruikers kunnen vertragingoplopen bij het gebruik van deze rol binnen SharePoint nadat ze zijn geactiveerd in Privileged Identity Management.

## <a name="next-steps"></a>Volgende stappen

- [Azure AD-rollen toewijzen in Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Azure-bronrollen toewijzen in Privileged Identity Management](pim-resource-roles-assign-roles.md)
