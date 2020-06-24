---
title: Groepen toevoegen, verwijderen en weer geven in een beheer eenheid (preview)-Azure Active Directory | Microsoft Docs
description: Groepen en machtigingen voor rollen beheren in een beheer eenheid in Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 04/16/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: d549d64dc2729b5d6280089e42d456043fb18393
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/12/2020
ms.locfileid: "84732494"
---
# <a name="add-and-manage-groups-in-administrative-units-in-azure-active-directory"></a>Groepen toevoegen en beheren in beheer eenheden in Azure Active Directory

In Azure Active Directory (Azure AD) kunt u groepen toevoegen aan een beheer eenheid (AU) voor een gedetailleerdere administratieve controle mogelijkheden.

Zie aan de [slag](roles-admin-units-manage.md#get-started)voor de stappen voor het voorbereiden van het gebruik van Power shell en Microsoft Graph voor beheer van beheer eenheden.

## <a name="add-groups-to-an-au"></a>Groepen toevoegen aan een AU

### <a name="azure-portal"></a>Azure Portal

In het voor beeld kunt u groepen alleen afzonderlijk toewijzen aan een beheer eenheid. Er is geen optie voor het bulksgewijs toewijzen van groepen aan een beheer eenheid. U kunt een groep toewijzen aan een beheer eenheid op een van de twee manieren in de portal:

1. Op de pagina **Azure AD >-groepen**

    Open de pagina overzicht van groepen in azure AD en selecteer de groep die moet worden toegewezen aan de beheer eenheid. Selecteer aan de linkerkant **beheer eenheden** om de administratieve eenheden die aan de groep zijn toegewezen, weer te geven. Aan de bovenkant ziet u de optie toewijzen aan beheer eenheid en klikt u op de knop aan de rechter kant om de beheer eenheid te kiezen.

    ![een groep afzonderlijk toewijzen aan een beheer eenheid](./media/roles-admin-units-add-manage-groups/assign-to-group-1.png)

1. Van de pagina **Azure AD >-beheer eenheden > alle groepen**

    Open de Blade alle groepen in azure AD >-beheer eenheden. Als er al groepen zijn toegewezen aan de beheer eenheid, worden ze weer gegeven aan de rechter kant. Selecteer **toevoegen** bovenaan en een venster aan de rechter kant om de groepen weer te geven die beschikbaar zijn in uw Azure AD-organisatie. Selecteer een of meer groepen die moeten worden toegewezen aan de beheer eenheden.

    ![Selecteer een administratieve eenheid en selecteer lid toevoegen](./media/roles-admin-units-add-manage-groups/assign-to-admin-unit.png)

### <a name="powershell"></a>PowerShell

    $administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    $GroupObj = Get-AzureADGroup -Filter "displayname eq 'TestGroup'"
    Add-AzureADAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId -RefObjectId $GroupObj.ObjectId

In dit voor beeld wordt de cmdlet Add-AzureADAdministrativeUnitMember gebruikt om de groep toe te voegen aan de beheer eenheid. De object-ID van de beheer eenheid en de object-ID van de groep die moet worden toegevoegd, worden als argument beschouwd. De gemarkeerde sectie kan worden gewijzigd als vereist voor de specifieke omgeving.

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

## <a name="list-groups-in-an-au"></a>Groepen in een AU weer geven

### <a name="azure-portal"></a>Azure Portal

Ga naar **Azure AD >-beheer eenheden** in de portal. Selecteer de administratieve eenheid waarvoor u de gebruikers wilt weer geven. Standaard wordt **alle gebruikers** al geselecteerd in het linkerdeel venster. Selecteer **alle groepen** en aan de rechter kant ziet u de lijst met groepen die lid zijn van de geselecteerde beheer eenheid.

![Lijst groepen in een beheer eenheid](./media/roles-admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="powershell"></a>PowerShell

    $administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    Get-AzureADAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId

Dit helpt u alle leden van de beheer eenheid op te halen. Als u alle groepen wilt weer geven die lid van de beheer eenheid zijn, kunt u het onderstaande code fragment gebruiken:

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

## <a name="list-aus-for-a-group"></a>AUs voor een groep vermelden

### <a name="azure-portal"></a>Azure Portal

In de Azure AD-Portal kunt u de details van een groep openen door **groepen**te openen. Selecteer een groep om het profiel van de groep te openen. Selecteer **beheer eenheden** om alle administratieve eenheden weer te geven waarvan de groep lid is.

![Beheer eenheden voor een groep weer geven](./media/roles-admin-units-add-manage-groups/list-group-au.png)

### <a name="powershell"></a>PowerShell

    Get-AzureADAdministrativeUnit | where { Get-AzureADAdministrativeUnitMember -ObjectId $_.ObjectId | where {$_.ObjectId -eq $groupObjId} }

### <a name="microsoft-graph"></a>Microsoft Graph

    https://graph.microsoft.com/beta/groups/<group-id>/memberOf/$/Microsoft.Graph.AdministrativeUnit

## <a name="remove-a-group-from-an-au"></a>Een groep uit een AU verwijderen

### <a name="azure-portal"></a>Azure Portal

Er zijn twee manieren waarop u een groep kunt verwijderen uit een administratieve eenheid in de Azure Portal.

Open **Azure AD**-  >  **groepen** en open het profiel voor de groep die u wilt verwijderen uit de beheer eenheid. Selecteer **beheer eenheden** in het linkerdeel venster om een lijst weer te geven met alle administratieve eenheden waarvan de groep lid is. Selecteer de beheer eenheid waarvan u de groep wilt verwijderen en selecteer vervolgens **verwijderen uit beheer eenheid**.

![Een groep verwijderen uit een beheer eenheid](./media/roles-admin-units-add-manage-groups/group-au-remove.png)

U kunt ook naar **Azure AD**-  >  **beheer eenheden** gaan en de administratieve eenheid selecteren waarvan de groep lid is. Selecteer **groepen** in het linkerdeel venster om de leden groepen weer te geven. Selecteer de groep die u wilt verwijderen uit de beheer eenheid en selecteer vervolgens **groepen verwijderen**.

![Lijst groepen in een beheer eenheid](./media/roles-admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="powershell"></a>PowerShell

    Remove-AzureADAdministrativeUnitMember -ObjectId $auId -MemberId $memberGroupObjId

### <a name="microsoft-graph"></a>Microsoft Graph

    https://graph.microsoft.com/beta/administrativeUnits/<adminunit-id>/members/<group-id>/$ref

## <a name="next-steps"></a>Volgende stappen

- [Een rol toewijzen aan een beheer eenheid](roles-admin-units-assign-roles.md)
- [Gebruikers beheren in een beheer eenheid](roles-admin-units-add-manage-users.md)
