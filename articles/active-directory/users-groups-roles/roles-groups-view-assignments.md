---
title: Rollen weer geven die zijn toegewezen aan een groep in Azure Active Directory | Microsoft Docs
description: Preview van aangepaste Azure AD-rollen voor het delegeren van identiteits beheer. Azure-rollen beheren in de Azure Portal, Power shell of Graph API.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 07/27/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3b7613fb70299a70e4389b97c2647a26cb7c3374
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/31/2020
ms.locfileid: "87476014"
---
# <a name="view-roles-assigned-to-a-group-in-azure-active-directory"></a>Rollen weer geven die zijn toegewezen aan een groep in Azure Active Directory

In deze sectie wordt beschreven hoe de functies die aan een groep zijn toegewezen, kunnen worden weer gegeven met behulp van het Azure AD-beheer centrum. Het weer geven van groepen en toegewezen rollen zijn standaard gebruikers machtigingen.

1. Meld u aan bij het [Azure AD-beheer centrum](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) met alle niet-beheerders-of beheerders referenties.

1. Selecteer de groep waarin u geïnteresseerd bent.

1. Selecteer **toegewezen rollen**. U kunt nu alle Azure AD-rollen zien die aan deze groep zijn toegewezen.

   ![Alle rollen weer geven die zijn toegewezen aan een geselecteerde groep](./media/roles-groups-view-assignments/view-assignments.png)

## <a name="using-powershell"></a>PowerShell gebruiken

### <a name="get-object-id-of-the-group"></a>Object-ID van de groep ophalen

```powershell
Get-AzureADMSGroup -SearchString “Contoso_Helpdesk_Administrators”
```

### <a name="view-role-assignment-to-a-group"></a>Roltoewijzing weer geven aan een groep

```powershell
Get-AzureADMSRoleAssignment -Filter "principalId eq '<object id of group>" 
```

## <a name="using-microsoft-graph-api"></a>Microsoft Graph-API gebruiken

### <a name="get-object-id-of-the-group"></a>Object-ID van de groep ophalen

```powershell
GET https://graph.microsoft.com/beta/groups?$filter displayName eq ‘Contoso_Helpdesk_Administrator’ 
```

### <a name="get-role-assignments-to-a-group"></a>Roltoewijzingen voor een groep ophalen

```powershell
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments?$filter=principalId eq
```

## <a name="next-steps"></a>Volgende stappen

- [Cloud groepen gebruiken om roltoewijzingen te beheren](roles-groups-concept.md)
- [Problemen met rollen die zijn toegewezen aan Cloud groepen oplossen](roles-groups-faq-troubleshooting.md)
