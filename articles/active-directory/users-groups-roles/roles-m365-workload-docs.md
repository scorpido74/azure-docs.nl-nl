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
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 430b65217da33185cd3b5c235fb148f3350e1bfe
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74024461"
---
# <a name="administrator-roles-for-microsoft-365-services"></a>Beheerders rollen voor Microsoft 365 Services

Alle producten in Microsoft 365 kunnen worden beheerd met beheerders rollen in azure AD. Sommige producten bieden ook aanvullende rollen die specifiek zijn voor dat product. Zie de onderstaande tabel voor informatie over de rollen die door elk product worden ondersteund. Algemene discussies over overdrachts problemen zijn te vinden in het [plannen van rollen delegering in azure Active Directory](roles-concept-delegation.md).

## <a name="where-to-find-content"></a>Waar u inhoud kunt vinden

Microsoft 365-service | Inhoud van rol | API-inhoud
---------------------- | ------------------ | -----------------
Beheerders rollen in Office 365 en Microsoft 365 Business-abonnementen | [Office 365-beheerdersrollen](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles?view=o365-worldwide) | Niet beschikbaar
Azure Active Directory (Azure AD) en Azure AD Identity Protection| [Azure AD-beheerders rollen](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Roltoewijzingen ophalen](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Exchange Online| [Op rollen gebaseerd toegangs beheer op basis van Exchange](https://docs.microsoft.com/exchange/understanding-role-based-access-control-exchange-2013-help) |  [Power shell voor Exchange](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps)<br>[Roltoewijzingen ophalen](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps)
SharePoint Online | [Azure AD-beheerders rollen](directory-assign-admin-roles.md)<br>Ook [over de share point-beheerdersrol in Office 365](https://docs.microsoft.com/sharepoint/sharepoint-admin-role) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Roltoewijzingen ophalen](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Teams/Skype voor bedrijven | [Azure AD-beheerders rollen](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Roltoewijzingen ophalen](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Beveiligings-en compliancecentrum (Office 365 Advanced Threat Protection, beveiliging van Exchange Online, Information Protection) | [Office 365-beheerdersrollen](https://docs.microsoft.com/office365/SecurityCompliance/permissions-in-the-security-and-compliance-center) | [Exchange PowerShell](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps)<br>[Roltoewijzingen ophalen](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps)
Beveiligde Score | [Azure AD-beheerders rollen](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Roltoewijzingen ophalen](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Nalevings beheerder | [Functies van de nalevings beheerder](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud#permissions-and-role-based-access-control) | Niet beschikbaar
Azure Information Protection | [Azure AD-beheerders rollen](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Roltoewijzingen ophalen](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Microsoft Cloud App Security | [Op rollen gebaseerd toegangsbeheer](https://docs.microsoft.com/cloud-app-security/manage-admins) | [API-referentie](https://docs.microsoft.com/cloud-app-security/api-tokens) 
Azure Advanced Threat Protection | [Azure ATP-Rolgroepen](https://docs.microsoft.com/azure-advanced-threat-protection/atp-role-groups) | Niet beschikbaar
Windows Defender Advanced Threat Protection | [Windows Defender ATP op rollen gebaseerd toegangs beheer](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/rbac-windows-defender-advanced-threat-protection) | Niet beschikbaar
Privileged Identity Management | [Azure AD-beheerders rollen](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Roltoewijzingen ophalen](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Intune | [InTune-op rollen gebaseerd toegangs beheer](https://docs.microsoft.com/intune/role-based-access-control) | [Graph API](https://docs.microsoft.com/graph/api/resources/intune-rbac-conceptual?view=graph-rest-beta)<br>[Roltoewijzingen ophalen](https://docs.microsoft.com/graph/api/intune-rbac-roledefinition-list?view=graph-rest-beta)
Beheerd bureau blad | [Azure AD-beheerders rollen](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Roltoewijzingen ophalen](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)

## <a name="next-steps"></a>Volgende stappen

* [Azure AD-beheerders rollen toewijzen of verwijderen](directory-manage-roles-portal.md)
* [Naslag informatie over Azure AD-beheerders rollen](directory-assign-admin-roles.md)
