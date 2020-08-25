---
title: 'Documenten van beheerdersrol in Microsoft 365 Services: Azure AD | Microsoft Docs'
description: Zoeken naar inhoud en API-verwijzingen voor beheerders rollen voor Microsoft 365 Services in Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: reference
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 14d5b458aeccd2587f24b30548c4b5e76912bce1
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/25/2020
ms.locfileid: "88798275"
---
# <a name="administrator-roles-for-microsoft-365-services"></a>Beheerders rollen voor Microsoft 365 Services

Alle producten in Microsoft 365 kunnen worden beheerd met beheerders rollen in azure AD. Sommige producten bieden ook aanvullende rollen die specifiek zijn voor dat product. Zie de onderstaande tabel voor informatie over de rollen die door elk product worden ondersteund. Algemene discussies over overdrachts problemen zijn te vinden in het [plannen van rollen delegering in azure Active Directory](roles-concept-delegation.md).

## <a name="where-to-find-content"></a>Waar u inhoud kunt vinden

Microsoft 365-service | Inhoud van rol | API-inhoud
---------------------- | ------------------ | -----------------
Beheerders rollen in Office 365 en Microsoft 365 Business-abonnementen | [Office 365-beheerdersrollen](/office365/admin/add-users/about-admin-roles?view=o365-worldwide) | Niet beschikbaar
Azure Active Directory (Azure AD) en Azure AD Identity Protection| [Azure AD-beheerders rollen](directory-assign-admin-roles.md) | [Graph API](/graph/api/overview?view=graph-rest-1.0)<br>[Roltoewijzingen ophalen](/graph/api/directoryrole-list?view=graph-rest-1.0)
Exchange Online| [Op rollen gebaseerd toegangs beheer op basis van Exchange](/exchange/understanding-role-based-access-control-exchange-2013-help) |  [Power shell voor Exchange](/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps)<br>[Roltoewijzingen ophalen](/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps)
SharePoint Online | [Azure AD-beheerders rollen](directory-assign-admin-roles.md)<br>Ook [over de share point-beheerdersrol in Office 365](/sharepoint/sharepoint-admin-role) | [Graph API](/graph/api/overview?view=graph-rest-1.0)<br>[Roltoewijzingen ophalen](/graph/api/directoryrole-list?view=graph-rest-1.0)
Teams/Skype voor bedrijven | [Azure AD-beheerders rollen](directory-assign-admin-roles.md) | [Graph API](/graph/api/overview?view=graph-rest-1.0)<br>[Roltoewijzingen ophalen](/graph/api/directoryrole-list?view=graph-rest-1.0)
Security & compliance Center (Office 365 Advanced Threat Protection, Exchange Online Protection, Information Protection) | [Office 365-beheerdersrollen](/office365/SecurityCompliance/permissions-in-the-security-and-compliance-center) | [Exchange Power shell](/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps)<br>[Roltoewijzingen ophalen](/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps)
Beveiligde Score | [Azure AD-beheerders rollen](directory-assign-admin-roles.md) | [Graph API](/graph/api/overview?view=graph-rest-1.0)<br>[Roltoewijzingen ophalen](/graph/api/directoryrole-list?view=graph-rest-1.0)
Compliance Manager | [Functies van de nalevings beheerder](/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud#permissions-and-role-based-access-control) | Niet beschikbaar
Azure Information Protection | [Azure AD-beheerders rollen](directory-assign-admin-roles.md) | [Graph API](/graph/api/overview?view=graph-rest-1.0)<br>[Roltoewijzingen ophalen](/graph/api/directoryrole-list?view=graph-rest-1.0)
Microsoft Cloud App Security | [Op rollen gebaseerd toegangsbeheer](/cloud-app-security/manage-admins) | [API-naslaginformatie](/cloud-app-security/api-tokens) 
Azure Advanced Threat Protection | [Azure ATP-functiegroepen](/azure-advanced-threat-protection/atp-role-groups) | Niet beschikbaar
Windows Defender Advanced Threat Protection | [Windows Defender ATP op rollen gebaseerd toegangs beheer](/windows/security/threat-protection/windows-defender-atp/rbac-windows-defender-advanced-threat-protection) | Niet beschikbaar
Privileged Identity Management | [Azure AD-beheerders rollen](directory-assign-admin-roles.md) | [Graph API](/graph/api/overview?view=graph-rest-1.0)<br>[Roltoewijzingen ophalen](/graph/api/directoryrole-list?view=graph-rest-1.0)
Intune | [InTune-op rollen gebaseerd toegangs beheer](/intune/role-based-access-control) | [Graph API](/graph/api/resources/intune-rbac-conceptual?view=graph-rest-beta)<br>[Roltoewijzingen ophalen](/graph/api/intune-rbac-roledefinition-list?view=graph-rest-beta)
Beheerd bureau blad | [Azure AD-beheerders rollen](directory-assign-admin-roles.md) | [Graph API](/graph/api/overview?view=graph-rest-1.0)<br>[Roltoewijzingen ophalen](/graph/api/directoryrole-list?view=graph-rest-1.0)

## <a name="next-steps"></a>Volgende stappen

* [Azure AD-beheerders rollen toewijzen of verwijderen](directory-manage-roles-portal.md)
* [Naslag informatie over Azure AD-beheerders rollen](directory-assign-admin-roles.md)