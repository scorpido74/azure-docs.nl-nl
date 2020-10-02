---
title: Gebruikers toevoegen, verwijderen en weer geven in een beheer eenheid-Azure Active Directory | Microsoft Docs
description: Gebruikers en machtigingen voor rollen beheren in een beheer eenheid in Azure Active Directory
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
ms.openlocfilehash: f3e223dd46806551fbba05127b70a9119a5d9c75
ms.sourcegitcommit: 487a9f5272300d60df2622c3d13e794d54680f90
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/02/2020
ms.locfileid: "91661139"
---
# <a name="add-and-manage-users-in-an-administrative-unit-in-azure-active-directory"></a>Gebruikers toevoegen en beheren in een beheer eenheid in Azure Active Directory

In Azure Active Directory (Azure AD) kunt u gebruikers toevoegen aan een beheer eenheid (AU) voor een gedetailleerdere administratieve controle mogelijkheden.

Zie aan de [slag](roles-admin-units-manage.md#get-started)voor de stappen voor het voorbereiden van het gebruik van Power shell en Microsoft Graph voor beheer van beheer eenheden.

## <a name="add-users-to-an-au"></a>Gebruikers toevoegen aan een AU

### <a name="azure-portal"></a>Azure Portal

U kunt op twee manieren gebruikers aan beheer eenheden toewijzen.

1. Afzonderlijke toewijzing

    1. U kunt in de portal naar Azure AD gaan en gebruikers selecteren en de gebruiker selecteren die aan een beheer eenheid moet worden toegewezen. U kunt vervolgens beheer eenheden selecteren in het linkerdeel venster. De gebruiker kan worden toegewezen aan een of meer beheer eenheden door te klikken op * * toewijzen aan beheer eenheid en de beheer eenheden te selecteren waaraan de gebruiker moet worden toegewezen.

       ![Selecteer toevoegen en voer een naam in voor de beheer eenheid](./media/roles-admin-units-add-manage-users/assign-users-individually.png)

    1. U kunt in de portal naar Azure AD gaan en administratieve eenheden selecteren in het linkerdeel venster en vervolgens de administratieve eenheid selecteren waaraan de gebruikers moeten worden toegewezen. Selecteer **alle gebruikers** en selecteer vervolgens **lid toevoegen**. U kunt vervolgens door gaan en een of meer gebruikers selecteren die moeten worden toegewezen aan de beheer eenheid in het rechterdeel venster.

        ![Selecteer een administratieve eenheid en selecteer lid toevoegen](./media/roles-admin-units-add-manage-users/assign-to-admin-unit.png)

1. Bulk toewijzing

    Ga naar Azure AD in de portal en selecteer beheer eenheden. Selecteer de administratieve eenheid waar gebruikers moeten worden toegevoegd. Klik op alle gebruikers > leden uit het CSV-bestand toe te voegen. U kunt vervolgens de CSV-sjabloon downloaden en het bestand bewerken. De indeling is eenvoudig en er moet één UPN worden toegevoegd aan elke regel. Zodra het bestand klaar is, slaat u het op een geschikte locatie op en uploadt u het in stap 3 als gemarkeerd in de moment opname.

    ![gebruikers bulksgewijs toewijzen aan een beheer eenheid](./media/roles-admin-units-add-manage-users/bulk-assign-to-admin-unit.png)

### <a name="powershell"></a>PowerShell

```powershell
$administrativeunitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
$UserObj = Get-AzureADUser -Filter "UserPrincipalName eq 'billjohn@fabidentity.onmicrosoft.com'"
Add-AzureADMSAdministrativeUnitMember -Id $administrativeunitObj.ObjectId -RefObjectId $UserObj.ObjectId
```

In het bovenstaande voor beeld wordt de cmdlet Add-AzureADAdministrativeUnitMember gebruikt om de gebruiker toe te voegen aan de beheer eenheid. De object-ID van de beheer eenheid waaraan de gebruiker moet worden toegevoegd en de object-ID van de gebruiker die moet worden toegevoegd, wordt als argument beschouwd. De gemarkeerde sectie kan worden gewijzigd als vereist voor de specifieke omgeving.

### <a name="microsoft-graph"></a>Microsoft Graph

```http
Http request
POST /administrativeUnits/{Admin Unit id}/members/$ref
Request body
{
  "@odata.id":"https://graph.microsoft.com/v1.0/users/{id}"
}
```

Voorbeeld:

```http
{
  "@odata.id":"https://graph.microsoft.com/v1.0/users/johndoe@fabidentity.com"
}
```

## <a name="list-administrative-units-for-a-user"></a>Beheer eenheden voor een gebruiker weer geven

### <a name="azure-portal"></a>Azure Portal

In de Azure Portal kunt u een gebruikers profiel openen door:

1. **Azure AD**-  >  **gebruikers**openen.

1. Selecteer de gebruiker om het profiel van de gebruiker te openen.

1. Selecteer **beheer eenheden** om de lijst met beheer eenheden te bekijken waaraan de gebruiker is toegewezen.

   ![De beheer eenheden voor een gebruiker weer geven](./media/roles-admin-units-add-manage-users/list-user-admin-units.png)

### <a name="powershell"></a>PowerShell

```powershell
Get-AzureADMSAdministrativeUnit | where { Get-AzureADMSAdministrativeUnitMember -Id $_.ObjectId | where {$_.RefObjectId -eq $userObjId} }
```
Opmerking: standaard retourneert Get-AzureADAdministrativeUnitMember alleen 100 leden, kunt u '-alle $true ' toevoegen om meer leden op te halen.

### <a name="microsoft-graph"></a>Microsoft Graph

```http
https://graph.microsoft.com/v1.0/users/{id}/memberOf/$/Microsoft.Graph.AdministrativeUnit
```

## <a name="remove-a-single-user-from-an-au"></a>Eén gebruiker uit een AU verwijderen

### <a name="azure-portal"></a>Azure Portal

Er zijn twee manieren om een gebruiker uit een beheer eenheid te verwijderen. In de Azure Portal kunt u een gebruikers profiel openen door naar **Azure AD**-  >  **gebruikers**te gaan. Selecteer de gebruiker om het profiel van de gebruiker te openen. Selecteer de beheer eenheid waaruit u de gebruiker wilt verwijderen en selecteer **verwijderen uit beheer eenheid**.

![Een gebruiker uit een beheer eenheid verwijderen uit het gebruikers profiel](./media/roles-admin-units-add-manage-users/user-remove-admin-units.png)

U kunt een gebruiker ook verwijderen in **Azure AD**-  >  **administratieve eenheden** door de beheer eenheid te selecteren waarvan u gebruikers wilt verwijderen. Selecteer de gebruiker en selecteer **lid verwijderen**.
  
![Een gebruiker op het niveau van de beheer eenheid verwijderen](./media/roles-admin-units-add-manage-users/admin-units-remove-user.png)

### <a name="powershell"></a>PowerShell

```powershell
Remove-AzureADMSAdministrativeUnitMember -Id $auId -MemberId $memberUserObjId
```

### <a name="microsoft-graph"></a>Microsoft Graph

   https://graph.microsoft.com/v1.0/directory/administrativeUnits/{adminunit-id}/members/{user-id}/$ref

## <a name="bulk-remove-more-than-one-user"></a>Meerdere gebruikers tegelijk verwijderen

U kunt naar Azure AD >-beheer eenheden gaan en de beheer eenheid selecteren waarvan u gebruikers wilt verwijderen. Klik op lid van bulk verwijdering. Down load de CSV-sjabloon voor het opgeven van de lijst met gebruikers die moeten worden verwijderd.

Bewerk de gedownloade CSV-sjabloon met de relevante gebruikers vermeldingen. Verwijder de eerste twee rijen van de sjabloon niet. Voeg één gebruikers-UPN toe aan elke rij.

![Bewerk het CSV-bestand voor het verwijderen van bulk gebruikers uit administratieve eenheden](./media/roles-admin-units-add-manage-users/bulk-user-entries.png)

Wanneer u de vermeldingen in het bestand hebt opgeslagen, uploadt u het bestand en selecteert u **verzenden**.

![Het bulk upload bestand verzenden](./media/roles-admin-units-add-manage-users/bulk-user-remove.png)

## <a name="next-steps"></a>Volgende stappen

- [Een rol toewijzen aan een beheer eenheid](roles-admin-units-assign-roles.md)
- [Groepen toevoegen aan een beheer eenheid](roles-admin-units-add-manage-groups.md)
