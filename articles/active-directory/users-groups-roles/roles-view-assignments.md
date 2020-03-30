---
title: Aangepaste roltoewijzingen weergeven in de Azure AD-portal | Microsoft Documenten
description: U nu leden van een Azure AD-beheerdersrol zien en beheren in het Azure AD-beheercentrum.
services: active-directory
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
ms.openlocfilehash: f04bd7788a9cc9657e14aedfb153182d6e53eb95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259706"
---
# <a name="view-custom-role-assignments-in-azure-active-directory"></a>Aangepaste roltoewijzingen weergeven in Azure Active Directory

In dit artikel wordt beschreven hoe u aangepaste rollen weergeven die u hebt toegewezen in Azure Active Directory (Azure AD). In Azure Active Directory (Azure AD) kunnen rollen worden toegewezen op een organisatiebreed bereik of met één toepassingsbereik.

- Roltoewijzingen op het organisatiebrede bereik worden toegevoegd aan en zijn te zien in de lijst met enkele toepassingsrollentoewijzingen.
- Roltoewijzingen in het enkele toepassingsbereik worden niet toegevoegd aan en kunnen niet worden weergegeven in de lijst met toewijzingen met een hele scope.

## <a name="view-role-assignments-in-the-azure-portal"></a>Roltoewijzingen weergeven in de Azure-portal

Deze procedure beschrijft het bekijken van opdrachten van een rol met organisatiebreed bereik.

1. Meld u aan bij het [Azure AD-beheercentrum](https://aad.portal.azure.com) met bevoegde rolbeheerder of machtigingen voor globale beheerders in de Azure AD-organisatie.
1. Selecteer **Azure Active Directory,** selecteer **Rollen en beheerders**en selecteer vervolgens een rol om deze te openen en de eigenschappen ervan weer te geven.
1. Selecteer **Toewijzingen** om de toewijzingen voor de rol weer te geven.

    ![Roltoewijzingen en machtigingen weergeven wanneer u een rol opent in de lijst](./media/roles-view-assignments/role-assignments.png)

## <a name="view-role-assignments-using-azure-ad-powershell"></a>Roltoewijzingen weergeven met Azure AD PowerShell

In deze sectie worden weergavetoewijzingen beschreven van een rol met een organisatiebreed bereik. In dit artikel wordt de [Azure Active Directory PowerShell-module voor versie 2](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#directory_roles) gebruikt. Als u scopetoewijzingen met één toepassing wilt weergeven met PowerShell, u de cmdlets gebruiken in [Aangepaste rollen toewijzen met PowerShell](roles-assign-powershell.md).

### <a name="prepare-powershell"></a>PowerShell voorbereiden

Eerst moet u [de PowerShell-module voor Azure AD-voorbeeld downloaden.](https://www.powershellgallery.com/packages/AzureAD/)

Als u de Azure AD PowerShell-module wilt installeren, gebruikt u de volgende opdrachten:

``` PowerShell
install-module azureadpreview
import-module azureadpreview
```

Als u wilt controleren of de module klaar is voor gebruik, gebruikt u de volgende opdracht:

``` PowerShell
get-module azuread
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azuread                      {Add-AzureADAdministrati...}
```

### <a name="view-the-assignments-of-a-role"></a>De opdrachten van een rol weergeven

Voorbeeld van het bekijken van de opdrachten van een rol.

``` PowerShell
# Fetch list of all directory roles with object ID
Get-AzureADDirectoryRole

# Fetch a specific directory role by ID
$role = Get-AzureADDirectoryRole -ObjectId "5b3fe201-fa8b-4144-b6f1-875829ff7543"

# Fetch role membership for a role
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId | Get-AzureADUser
```

## <a name="view-role-assignments-using-microsoft-graph-api"></a>Roltoewijzingen weergeven met Microsoft Graph API

In deze sectie worden weergavetoewijzingen beschreven van een rol met een organisatiebreed bereik.  Als u scopetoewijzingen met één toepassing wilt weergeven met behulp van Graph API, u de bewerkingen gebruiken in [Aangepaste rollen toewijzen met Graph API](roles-assign-graph.md).

HTTP-aanvraag om een roltoewijzing voor een bepaalde roldefinitie te krijgen.

GET

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments&$filter=roleDefinitionId eq ‘<object-id-or-template-id-of-role-definition>’
```

Antwoord

``` HTTP
HTTP/1.1 200 OK
{
    "id":"CtRxNqwabEKgwaOCHr2CGJIiSDKQoTVJrLE9etXyrY0-1"
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"3671d40a-1aac-426c-a0c1-a3821ebd8218",
    "resourceScopes":["/"]
}
```

## <a name="view-assignments-of-single-application-scope"></a>Toewijzingen van één toepassingsbereik weergeven

In deze sectie worden weergavetoewijzingen beschreven van een rol met één toepassingsbereik. Deze functie is momenteel beschikbaar als openbare preview-versie.

1. Meld u aan bij het [Azure AD-beheercentrum](https://aad.portal.azure.com) met bevoegde rolbeheerder of machtigingen voor globale beheerders in de Azure AD-organisatie.
1. Selecteer **App-registraties**en selecteer vervolgens de app-registratie om de eigenschappen ervan weer te geven. Mogelijk moet u **Alle toepassingen** selecteren om de volledige lijst met app-registraties in uw Azure AD-organisatie te bekijken.

    ![App-registraties maken of bewerken op de pagina App-registraties](./media/roles-create-custom/appreg-all-apps.png)

1. Selecteer in de app-registratie **Rollen en beheerders**en selecteer vervolgens een rol om de eigenschappen ervan weer te geven.

    ![Toewijzingen van app-registratierollen weergeven op de pagina App-registraties](./media/roles-view-assignments/appreg-assignments.png)

1. Selecteer **Toewijzingen** om de toewijzingen voor de rol weer te geven. Als u de toewijzingsweergave opent vanuit de app-registratie, ziet u de toewijzingen die zijn ondergebracht bij deze Azure AD-bron.

    ![Toewijzingen van app-registratierollen weergeven vanuit de eigenschappen van een app-registratie](./media/roles-view-assignments/appreg-assignments-2.png)

## <a name="next-steps"></a>Volgende stappen

* Voel je vrij om met ons te delen op het [Azure AD-forum voor beheerfuncties](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* Zie [Beheerdersrollen toewijzen](directory-assign-admin-roles.md)voor meer informatie over rollen en toewijzing van beheerders.
* Zie een vergelijking van [standaardmachtigingen voor gasten en leden](../fundamentals/users-default-permissions.md)voor standaardgebruikersmachtigingen voor standaardgebruikers.
