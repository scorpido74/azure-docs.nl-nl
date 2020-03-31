---
title: Beheerdersroldocumenten voor Microsoft 365-services - Azure AD | Microsoft Documenten
description: Inhouds- en API-verwijzingen voor beheerdersrollen voor Microsoft 365-services zoeken in Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 430b65217da33185cd3b5c235fb148f3350e1bfe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74024461"
---
# <a name="administrator-roles-for-microsoft-365-services"></a>Beheerdersrollen voor Microsoft 365-services

Alle producten in Microsoft 365 kunnen worden beheerd met administratieve rollen in Azure AD. Sommige producten bieden ook extra rollen die specifiek zijn voor dat product. Zie de onderstaande tabel voor informatie over de rollen die door elk product worden ondersteund. Algemene discussies over delegatieproblemen zijn te vinden in [Dedelegatieplanning van rollen in Azure Active Directory](roles-concept-delegation.md).

## <a name="where-to-find-content"></a>Waar vindt u inhoud

Microsoft 365-service | Rolinhoud | API-inhoud
---------------------- | ------------------ | -----------------
Beheerdersrollen in office 365- en Microsoft 365-bedrijfsabonnementen | [Office 365-beheerdersrollen](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles?view=o365-worldwide) | Niet beschikbaar
Azure Active Directory (Azure AD) en Azure AD-identiteitsbeveiliging| [Azure AD-beheerrollen](directory-assign-admin-roles.md) | [Grafische API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Roltoewijzingen ophalen](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Exchange Online| [Exchange-rolegebaseerd toegangsbeheer](https://docs.microsoft.com/exchange/understanding-role-based-access-control-exchange-2013-help) |  [PowerShell voor exchange](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps)<br>[Roltoewijzingen ophalen](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps)
SharePoint Online | [Azure AD-beheerrollen](directory-assign-admin-roles.md)<br>Ook [over de sharepoint-beheerrol in Office 365](https://docs.microsoft.com/sharepoint/sharepoint-admin-role) | [Grafische API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Roltoewijzingen ophalen](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Teams/Skype voor Bedrijven | [Azure AD-beheerrollen](directory-assign-admin-roles.md) | [Grafische API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Roltoewijzingen ophalen](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Beveiliging & Compliance Center (Office 365 Advanced Threat Protection, Exchange Online Protection, Information Protection) | [Office 365-beheerdersrollen](https://docs.microsoft.com/office365/SecurityCompliance/permissions-in-the-security-and-compliance-center) | [Exchange PowerShell](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps)<br>[Roltoewijzingen ophalen](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps)
Veilige score | [Azure AD-beheerrollen](directory-assign-admin-roles.md) | [Grafische API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Roltoewijzingen ophalen](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Compliance Manager | [Rollen compliancemanager](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud#permissions-and-role-based-access-control) | Niet beschikbaar
Azure Information Protection | [Azure AD-beheerrollen](directory-assign-admin-roles.md) | [Grafische API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Roltoewijzingen ophalen](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Microsoft Cloud App Security | [Op rollen gebaseerd toegangscontrole](https://docs.microsoft.com/cloud-app-security/manage-admins) | [API-verwijzing](https://docs.microsoft.com/cloud-app-security/api-tokens) 
Azure Advanced Threat Protection | [Azure ATP-functiegroepen](https://docs.microsoft.com/azure-advanced-threat-protection/atp-role-groups) | Niet beschikbaar
Windows Defender Advanced Threat Protection | [Windows Defender ATP-functiegebaseerd toegangscontrole](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/rbac-windows-defender-advanced-threat-protection) | Niet beschikbaar
Privileged Identity Management | [Azure AD-beheerrollen](directory-assign-admin-roles.md) | [Grafische API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Roltoewijzingen ophalen](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Intune | [Intune role-based access control](https://docs.microsoft.com/intune/role-based-access-control) | [Grafische API](https://docs.microsoft.com/graph/api/resources/intune-rbac-conceptual?view=graph-rest-beta)<br>[Roltoewijzingen ophalen](https://docs.microsoft.com/graph/api/intune-rbac-roledefinition-list?view=graph-rest-beta)
Beheerd bureaublad | [Azure AD-beheerrollen](directory-assign-admin-roles.md) | [Grafische API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Roltoewijzingen ophalen](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)

## <a name="next-steps"></a>Volgende stappen

* [Azure AD-beheerdersrollen toewijzen of verwijderen](directory-manage-roles-portal.md)
* [Naslaginformatie over Azure AD-beheerdersrollen](directory-assign-admin-roles.md)
