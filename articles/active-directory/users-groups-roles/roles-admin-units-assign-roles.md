---
title: Rollen toewijzen en weergeven met beheereenheidsbereik (voorbeeld) - Azure Active Directory | Microsoft Documenten
description: Beheereenheden gebruiken om het bereik van roltoewijzingen in Azure Active Directory te beperken
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 04/16/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: a518ff070ea021726ee382169e87cca55cad36d0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428249"
---
# <a name="assign-scoped-roles-to-an-administrative-unit"></a>Scoped rollen toewijzen aan een administratieve eenheid

In Azure Active Directory (Azure AD) u gebruikers toewijzen aan een Azure AD-rol met een bereik dat beperkt is tot een of meer beheereenheden (AU's) voor meer gedetailleerd beheerbeheerbeheer.

Zie [Aan](roles-admin-units-manage.md#getting-started)de slag gaan voor stappen om PowerShell en Microsoft Graph te gebruiken voor beheer van administratieve eenheden.

## <a name="roles-available"></a>Beschikbare rollen

Rol  |  Beschrijving
----- |  -----------
Verificatiebeheerder  |  Heeft alleen toegang tot het weergeven, instellen en opnieuw instellen van verificatiemethodegegevens voor elke niet-beheerdersgebruiker in de toegewezen beheereenheid.
Groepsbeheerder  |  Kan alle aspecten van groepen en groepeninstellingen beheren, zoals alleen naamgevings- en verloopbeleid in de toegewezen administratieve eenheid.
Helpdeskbeheerder  |  Kan wachtwoorden opnieuw instellen voor niet-beheerders en helpdeskbeheerders in de toegewezen administratieve eenheid.
Licentiebeheerder  |  Kan alleen licentietoewijzingen binnen de administratieve eenheid toewijzen, verwijderen en bijwerken.
Wachtwoordbeheerder  |  Kan wachtwoorden opnieuw instellen voor niet-beheerders en wachtwoordbeheerders binnen de toegewezen administratieve eenheid.
Gebruikersbeheerder  |  Kan alle aspecten van gebruikers en groepen beheren, inclusief het opnieuw instellen van wachtwoorden voor beperkte beheerders binnen de toegewezen administratieve eenheid.

## <a name="assign-a-scoped-role"></a>Een scoperol toewijzen

### <a name="azure-portal"></a>Azure Portal

Ga naar **Azure AD->-beheereenheden** in de portal. Selecteer de administratieve eenheid waarover u de rol aan een gebruiker wilt toewijzen. Selecteer in het linkerdeelvenster Rollen en beheerders om alle beschikbare rollen weer te geven.

![Een administratieve eenheid selecteren om het rolbereik te wijzigen](./media/roles-admin-units-assign-roles/select-role-to-scope.png)

Selecteer de toegewezen rol en selecteer **Toewijzingen toevoegen**. Hiermee wordt een deelvenster aan de rechterkant geopend waarin u een of meer gebruikers selecteren die aan de rol moeten worden toegewezen.

![Selecteer de rol die u wilt scopen en selecteer vervolgens Toewijzingen toevoegen](./media/roles-admin-units-assign-roles/select-add-assignment.png)

### <a name="powershell"></a>PowerShell

    $administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    $AdminUser = Get-AzureADUser -ObjectId 'janedoe@fabidentity.onmicrosoft.com'
    $uaRoleMemberInfo = New-Object -TypeName Microsoft.Open.AzureAD.Model.RoleMemberInfo -Property @{ObjectId = $AdminUser.ObjectId}
    Add-AzureADScopedRoleMembership -RoleObjectId $UserAdminRole.ObjectId -ObjectId $administrative unitObj.ObjectId -RoleMemberInfo  $uaRoleMemberInfo

De gemarkeerde sectie kan worden gewijzigd zoals vereist voor de specifieke omgeving.

### <a name="microsoft-graph"></a>Microsoft Graph

    Http request
    POST /administrativeUnits/{id}/scopedRoleMembers
    
    Request body
    {
      "roleId": "roleId-value",
      "roleMemberInfo": {
        "id": "id-value"
      }
    }

## <a name="list-the-scoped-admins-on-an-au"></a>De scoped-beheerders op een AU weergeven

### <a name="azure-portal"></a>Azure Portal

Alle roltoewijzingen die met een beheereenheid worden uitgevoerd, kunnen worden weergegeven in de [sectie Beheereenheden van Azure AD](https://ms.portal.azure.com/?microsoft_aad_iam_adminunitprivatepreview=true&microsoft_aad_iam_rbacv2=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AdminUnit). Ga naar **Azure AD->-beheereenheden** in de portal. Selecteer de beheereenheid voor de roltoewijzingen die u wilt aanbieden. Selecteer **Rollen en beheerders** en open een rol om de toewijzingen in de beheereenheid weer te geven.

### <a name="powershell"></a>PowerShell

    $administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    Get-AzureADScopedRoleMembership -ObjectId $administrative unitObj.ObjectId | fl *

De gemarkeerde sectie kan worden gewijzigd zoals vereist voor de specifieke omgeving.

### <a name="microsoft-graph"></a>Microsoft Graph

    Http request
    GET /administrativeUnits/{id}/scopedRoleMembers
    Request body
    {}

## <a name="next-steps"></a>Volgende stappen

- [Problemen met administratieve eenheden oplossen en veelgestelde vragen](roles-admin-units-faq-troubleshoot.md)
