---
title: Roltoewijzingen uit een groep in Azure Active Directory verwijderen | Microsoft Docs
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
ms.openlocfilehash: de09fddf6b02cfd57504994b4b8d3f7dc4254870
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/31/2020
ms.locfileid: "87476015"
---
# <a name="remove-role-assignments-from-a-group-in-azure-active-directory"></a>Roltoewijzingen uit een groep in Azure Active Directory verwijderen

In dit artikel wordt beschreven hoe een IT-beheerder Azure AD-rollen kan verwijderen die aan groepen zijn toegewezen. In de Azure Portal kunt u nu zowel directe als indirecte roltoewijzingen voor een gebruiker verwijderen. Als aan een gebruiker een rol is toegewezen door een groepslid maatschap, verwijdert u de gebruiker uit de groep om de roltoewijzing te verwijderen.

## <a name="using-azure-admin-center"></a>Azure-beheer centrum gebruiken

1. Meld u aan bij het [Azure AD-beheer centrum](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) met privileged Role Administrator of Global Administrator Permissions in de Azure AD-organisatie.

1. Selecteer **rollen en beheerders**  >  ***naam van rol***.

1. Selecteer de groep waaruit u de roltoewijzing wilt verwijderen en selecteer **toewijzing verwijderen**.

   ![Een roltoewijzing verwijderen uit een geselecteerde groep.](./media/roles-groups-remove-assignment/remove-assignment.png)

1. Wanneer u wordt gevraagd om uw actie te bevestigen, selecteert u **Ja**.

## <a name="using-powershell"></a>PowerShell gebruiken

### <a name="create-a-group-that-can-be-assigned-to-role"></a>Een groep maken die kan worden toegewezen aan een rol

```powershell
$group = New-AzureADMSGroup -DisplayName "Contoso_Helpdesk_Administrators" -Description "This group is assigned to Helpdesk Administrator built-in role in Azure AD." -MailEnabled $true -SecurityEnabled $true -MailNickName "contosohelpdeskadministrators" -IsAssignableToRole $true
```

### <a name="get-the-role-definition-you-want-to-assign-the-group-to"></a>Haal de roldefinitie op waaraan u de groep wilt toewijzen

```powershell
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Helpdesk Administrator'"
```

### <a name="create-a-role-assignment"></a>Een roltoewijzing maken

```powershell
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope '/' -RoleDefinitionId $roleDefinition.Id -PrincipalId $group.objectId
```

### <a name="remove-the-role-assignment"></a>De roltoewijzing verwijderen

```powershell
Remove-AzureAdMSRoleAssignment -Id $roleAssignment.Id 
```

## <a name="using-microsoft-graph-api"></a>Microsoft Graph-API gebruiken

### <a name="create-a-group-that-can-be-assigned-an-azure-ad-role"></a>Een groep maken waaraan een Azure AD-rol kan worden toegewezen

```powershell
POST https://graph.microsoft.com/beta/groups

{
"description": "This group is assigned to Helpdesk Administrator built-in role of Azure AD",
"displayName": "Contoso_Helpdesk_Administrators",
"groupTypes": [
"Unified"
],
"mailEnabled": true,
"securityEnabled": true
"mailNickname": "contosohelpdeskadministrators",
"isAssignableToRole": true,
}
```

### <a name="get-the-role-definition"></a>De roldefinitie ophalen

```powershell
GET https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions?$filter = displayName eq ‘Helpdesk Administrator’
```

### <a name="create-the-role-assignment"></a>De roltoewijzing maken

```powershell
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
{
"principalId":"<Object Id of Group>",
"roleDefinitionId":"<Id of role definition>",
"resourceScope":"/"
}
```

### <a name="delete-role-assignment"></a>Roltoewijzing verwijderen

```powershell
DELETE https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments/<Id of role assignment>
```

## <a name="next-steps"></a>Volgende stappen

- [Cloud groepen gebruiken om roltoewijzingen te beheren](roles-groups-concept.md)
- [Problemen met rollen die zijn toegewezen aan Cloud groepen oplossen](roles-groups-faq-troubleshooting.md)
