---
title: Groepen toevoegen, verwijderen en weergeven in een beheereenheid (voorbeeld) - Azure Active Directory | Microsoft Documenten
description: Groepen en hun rolmachtigingen beheren in een beheereenheid in Azure Active Directory
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
ms.openlocfilehash: 1f0b0c0a7b666aed56ad24625c80c0a57683b998
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683261"
---
# <a name="add-and-manage-groups-in-administrative-units-in-azure-active-directory"></a>Groepen toevoegen en beheren in beheerderseenheden in Azure Active Directory

In Azure Active Directory (Azure AD) u groepen toevoegen aan een beheereenheid (AU) voor een gedetailleerder beheerbereik.

Zie [Aan](roles-admin-units-manage.md#get-started)de slag voor stappen om PowerShell en Microsoft Graph te gebruiken voor beheer van administratieve eenheden.

## <a name="add-groups-to-an-au"></a>Groepen toevoegen aan een AU

### <a name="azure-portal"></a>Azure Portal

In het voorbeeld u groepen alleen afzonderlijk toewijzen aan een administratieve eenheid. Er is geen mogelijkheid om groepen in bulk aan een administratieve eenheid toe te zetten. U een groep toewijzen aan een administratieve eenheid op een van de twee manieren in portal:

1. Vanaf de pagina **Azure AD-> groepen**

    Open de overzichtspagina Groepen in Azure AD en selecteer de groep die moet worden toegewezen aan de beheereenheid. Selecteer aan de linkerkant **Administratieve eenheden** om de administratieve eenheden aan te geven waaraan de groep is toegewezen. Bovenaan vindt u de optie Toewijzen aan administratieve eenheid en als er op wordt geklikt, wordt een paneel aan de rechterkant aan de rechterkant de administratieve eenheid gekozen.

    ![een groep individueel toewijzen aan een administratieve eenheid](./media/roles-admin-units-add-manage-groups/assign-to-group-1.png)

1. Vanuit de pagina **Azure AD >-beheereenheden > alle groepen**

    Open het blad Alle groepen in Azure AD->-beheereenheden. Als er al groepen zijn toegewezen aan de administratieve eenheid, worden deze aan de rechterkant weergegeven. Selecteer **Toevoegen** aan de bovenkant en een rechterdeelvenster wordt weergegeven in een lijst van de groepen die beschikbaar zijn in uw Azure AD-organisatie. Selecteer een of meer groepen die aan de beheerderseenheden moeten worden toegewezen.

    ![een administratieve eenheid selecteren en vervolgens lid toevoegen selecteren](./media/roles-admin-units-add-manage-groups/assign-to-admin-unit.png)

### <a name="powershell"></a>PowerShell

    $administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    $GroupObj = Get-AzureADGroup -Filter "displayname eq 'TestGroup'"
    Add-AzureADAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId -RefObjectId $GroupObj.ObjectId

In dit voorbeeld wordt de cmdlet Add-AzureADAdministrativeUnitMember gebruikt om de groep toe te voegen aan de administratieve eenheid. De object-ID van de administratieve eenheid en de object-ID van de groep die moet worden toegevoegd, worden als argument gebruikt. De gemarkeerde sectie kan worden gewijzigd zoals vereist voor de specifieke omgeving.

### <a name="microsoft-graph"></a>Microsoft Graph

    Http request
    POST /administrativeUnits/{Admin Unit id}/members/$ref

    Request body
    {
      "@odata.id":"https://graph.microsoft.com/beta/groups/{id}"
    }

Voorbeeld:

    {
      "@odata.id":"https://graph.microsoft.com/beta/users/ 871d21ab-6b4e-4d56-b257-ba27827628f3"
    }

## <a name="list-groups-in-an-au"></a>Groepen in een AU aanbieden

### <a name="azure-portal"></a>Azure Portal

Ga naar **Azure AD->-beheereenheden** in de portal. Selecteer de administratieve eenheid waarvoor u de gebruikers wilt aanbieden. Standaard zijn **alle gebruikers** al geselecteerd in het linkerdeelvenster. Selecteer **Alle groepen** en aan de rechterkant vindt u de lijst met groepen die lid zijn van de geselecteerde administratieve eenheid.

![Een administratieve eenheid selecteren die u wilt verwijderen](./media/roles-admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="powershell"></a>PowerShell

    $administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    Get-AzureADAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId

Dit zal u helpen om alle leden van de administratieve eenheid. Als u alle groepen wilt weergeven die lid zijn van de administratieve eenheid, u het onderstaande codefragment gebruiken:

    foreach ($member in (Get-AzureADAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId)) 
    {
    if($member.ObjectType -eq "Group")
    {
    Get-AzureADGroup -ObjectId $member.ObjectId
    }
    }

### <a name="microsoft-graph"></a>Microsoft Graph

    HTTP request
    GET /administrativeUnits/{Admin id}/members/$/microsoft.graph.group
    Request body
    {}

## <a name="list-aus-for-a-group"></a>AU's voor een groep weergeven

### <a name="azure-portal"></a>Azure Portal

In de Azure AD-portal u de gegevens van een groep openen door **Groepen**te openen. Selecteer een groep om het profiel van de groep te openen. Selecteer **Administratieve eenheden** om alle administratieve eenheden weer te geven waar de groep lid van is.

![Administratieve eenheden voor een groep opsommen](./media/roles-admin-units-add-manage-groups/list-group-au.png)

### <a name="powershell"></a>PowerShell

    Get-AzureADAdministrativeUnit | where { Get-AzureADAdministrativeUnitMember -ObjectId $_.ObjectId | where {$_.ObjectId -eq $groupObjId} }

### <a name="microsoft-graph"></a>Microsoft Graph

    https://graph.microsoft.com/beta/groups/<group-id>/memberOf/$/Microsoft.Graph.AdministrativeUnit

## <a name="remove-a-group-from-an-au"></a>Een groep uit een AU verwijderen

### <a name="azure-portal"></a>Azure Portal

Er zijn twee manieren waarop u een groep verwijderen uit een beheereenheid in de Azure-portal.

Open **Azure AD-groepen** > **Groups** en open het profiel voor de groep die u uit de beheereenheid wilt verwijderen. Selecteer **Administratieve eenheden** in het linkerdeelvenster om alle administratieve eenheden weer te geven waar de groep lid van is. Selecteer de administratieve eenheid waaruit u de groep wilt verwijderen en selecteer **Vervolgens Verwijderen uit de administratieve eenheid**.

![Een groep uit een administratieve eenheid verwijderen](./media/roles-admin-units-add-manage-groups/group-au-remove.png)

U ook naar azure > **AD-beheereenheden** gaan en de administratieve eenheid selecteren waar de groep lid van is. **Azure AD** Selecteer **Groepen** in het linkerdeelvenster om de ledengroepen weer te geven. Selecteer de groep die uit de administratieve eenheid moet worden verwijderd en selecteer **vervolgens Groepen verwijderen**.

![Groepen in een administratieve eenheid vermelden](./media/roles-admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="powershell"></a>PowerShell

    Remove-AzureADAdministrativeUnitMember -ObjectId $auId -MemberId $memberGroupObjId

### <a name="microsoft-graph"></a>Microsoft Graph

    https://graph.microsoft.com/beta/administrativeUnits/<adminunit-id>/members/<group-id>/$ref

## <a name="next-steps"></a>Volgende stappen

- [Een rol toewijzen aan een administratieve eenheid](roles-admin-units-assign-roles.md)
- [Gebruikers beheren in een administratieve eenheid](roles-admin-units-add-manage-users.md)
