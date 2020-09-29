---
title: Rollen toewijzen en weer geven met het bereik van de beheer eenheid-Azure Active Directory | Microsoft Docs
description: Beheer eenheden gebruiken om het bereik van roltoewijzingen in Azure Active Directory te beperken
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 09/22/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0ae663b2c7a88e116315464c11b8d162135f0aff
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91450387"
---
# <a name="assign-scoped-roles-to-an-administrative-unit"></a>Scoped rollen toewijzen aan een beheer eenheid

In Azure Active Directory (Azure AD) kunt u gebruikers toewijzen aan een Azure AD-rol met een bereik dat beperkt is tot een of meer administratieve eenheden (AUs) voor een gedetailleerdere administratieve controle.

Zie aan de [slag](roles-admin-units-manage.md#get-started)voor de stappen voor het voorbereiden van het gebruik van Power shell en Microsoft Graph voor beheer van beheer eenheden.

## <a name="roles-available"></a>Beschik bare rollen

Rol  |  Beschrijving
----- |  -----------
Verificatie beheerder  |  Heeft toegang voor het weer geven, instellen en opnieuw instellen van verificatie methode-informatie voor een niet-beheerders gebruiker in de toegewezen beheer eenheid.
Groeps beheerder  |  Kan alle aspecten van groepen en groeps instellingen, zoals naamgeving en verloop beleid alleen in de toegewezen beheer eenheid beheren.
Helpdesk beheerder  |  Kan wacht woorden voor niet-beheerders en helpdesk beheerders opnieuw instellen in de toegewezen beheer eenheid.
Licentie beheerder  |  Kan alleen licentie toewijzingen binnen de beheer eenheid toewijzen, verwijderen en bijwerken.
Wachtwoord beheerder  |  Kan wacht woorden voor niet-beheerders en wachtwoord beheerders binnen de toegewezen beheer eenheid opnieuw instellen.
Gebruikers beheerder  |  Kan alle aspecten van gebruikers en groepen beheren, met inbegrip van het opnieuw instellen van wacht woorden voor beperkte beheerders binnen de toegewezen beheer eenheid.

## <a name="security-principals-that-can-be-assigned-to-an-au-scoped-role"></a>Beveiligings-principals die kunnen worden toegewezen aan een rollen met een AU-bereik
De volgende beveiligings-principals kunnen worden toegewezen aan een rollen met een AU-bereik:
* Gebruikers
* Toe te wijzen functie Cloud groepen (preview-versie)
* Service Principal Name (SPN)

## <a name="assign-a-scoped-role"></a>Een scoped rol toewijzen

### <a name="azure-portal"></a>Azure Portal

Ga naar **Azure AD >-beheer eenheden** in de portal. Selecteer de administratieve eenheid waarover u de rol wilt toewijzen aan een gebruiker. Selecteer in het linkerdeel venster de optie rollen en beheerders om alle beschik bare rollen weer te geven.

![Een administratieve eenheid selecteren om het gebruikersrol bereik te wijzigen](./media/roles-admin-units-assign-roles/select-role-to-scope.png)

Selecteer de rol die u wilt toewijzen en selecteer vervolgens **toewijzingen toevoegen**. Er wordt een paneel geopend aan de rechter kant waar u een of meer gebruikers kunt selecteren om aan de rol toe te wijzen.

![Selecteer de rol die u wilt bereik en selecteer vervolgens toewijzingen toevoegen](./media/roles-admin-units-assign-roles/select-add-assignment.png)

> [!Note]
>
> Volg [de stappen om](/active-directory/privileged-identity-management/pim-how-to-add-role-to-user.md#assign-a-role-with-restricted-scope)een rol toe te wijzen aan een administratieve eenheid met behulp van Pim.

### <a name="powershell"></a>PowerShell

```powershell
$AdminUser = Get-AzureADUser -ObjectId "Use the user's UPN, who would be an admin on this unit"
$Role = Get-AzureADDirectoryRole | Where-Object -Property DisplayName -EQ -Value "User Account Administrator"
$administrativeUnit = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'The display name of the unit'"
$RoleMember = New-Object -TypeName Microsoft.Open.AzureAD.Model.RoleMemberInfo
$RoleMember.ObjectId = $AdminUser.ObjectId
Add-AzureADMSScopedRoleMembership -ObjectId $administrativeUnit.ObjectId -RoleObjectId $Role.ObjectId -RoleMemberInfo $RoleMember
```

De gemarkeerde sectie kan worden gewijzigd als vereist voor de specifieke omgeving.

### <a name="microsoft-graph"></a>Microsoft Graph

```http
Http request
POST /directory/administrativeUnits/{id}/scopedRoleMembers
    
Request body
{
  "roleId": "roleId-value",
  "roleMemberInfo": {
    "id": "id-value"
  }
}
```

## <a name="list-the-scoped-admins-on-an-au"></a>De scoped Administrators op een AU weer geven

### <a name="azure-portal"></a>Azure Portal

Alle roltoewijzingen die met een bereik van een beheer eenheid worden uitgevoerd, kunnen worden weer gegeven in de [sectie administratieve eenheden van Azure AD](https://ms.portal.azure.com/?microsoft_aad_iam_adminunitprivatepreview=true&microsoft_aad_iam_rbacv2=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AdminUnit). Ga naar **Azure AD >-beheer eenheden** in de portal. Selecteer de beheer eenheid voor de roltoewijzingen die u wilt weer geven. Selecteer **rollen en beheerders** en open een rol om de toewijzingen in de beheer eenheid weer te geven.

### <a name="powershell"></a>PowerShell

```powershell
$administrativeUnit = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'The display name of the unit'"
Get-AzureADMSScopedRoleMembership -ObjectId $administrativeUnit.ObjectId | fl *
```

De gemarkeerde sectie kan worden gewijzigd als vereist voor de specifieke omgeving.

### <a name="microsoft-graph"></a>Microsoft Graph

```http
Http request
GET /directory/administrativeUnits/{id}/scopedRoleMembers
Request body
{}
```

## <a name="next-steps"></a>Volgende stappen

- [Cloud groepen gebruiken om roltoewijzingen te beheren](roles-groups-concept.md)
- [Problemen met rollen die zijn toegewezen aan cloudgroepen oplossen](roles-groups-faq-troubleshooting.md)
