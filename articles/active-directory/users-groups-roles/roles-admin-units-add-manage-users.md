---
title: Gebruikers toevoegen, verwijderen en weergeven in een beheereenheid (voorbeeld) - Azure Active Directory | Microsoft Documenten
description: Gebruikers en hun rolmachtigingen beheren in een beheereenheid in Azure Active Directory
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
ms.openlocfilehash: 2b2b901f652564c47ca35cb0f75a69f26fa2fa71
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81533221"
---
# <a name="add-and-manage-users-in-an-administrative-unit-in-azure-active-directory"></a>Gebruikers toevoegen en beheren in een beheereenheid in Azure Active Directory

In Azure Active Directory (Azure AD) u gebruikers toevoegen aan een beheereenheid (AU) voor een gedetailleerder beheerbereik.

Zie [Aan](roles-admin-units-manage.md#getting-started)de slag gaan voor stappen om PowerShell en Microsoft Graph te gebruiken voor beheer van administratieve eenheden.

## <a name="add-users-to-an-au"></a>Gebruikers toevoegen aan een AU

### <a name="azure-portal"></a>Azure Portal

U gebruikers op twee manieren toewijzen aan administratieve eenheden.

1. Individuele opdracht

    1. U naar het Azure AD in de portal gaan en Gebruikers selecteren en de gebruiker selecteren die aan een beheereenheid moet worden toegewezen. Vervolgens u Administratieve eenheden selecteren in het linkerdeelvenster. De gebruiker kan aan een of meer administratieve eenheden worden toegewezen door op Toewijzen aan de administratieve eenheid te klikken en de administratieve eenheden te selecteren waar de gebruiker moet worden toegewezen.

       ![selecteer Toevoegen en voer vervolgens een naam in voor de administratieve eenheid](./media/roles-admin-units-add-manage-users/assign-users-individually.png)

    1. U naar Azure AD in de portal gaan en Beheereenheden in het linkerdeelvenster selecteren en vervolgens de administratieve eenheid selecteren waar de gebruikers moeten worden toegewezen. Selecteer Alle gebruikers in het linkerdeelvenster en selecteer Vervolgens Lid toevoegen. U vervolgens een of meer gebruikers selecteren die in het rechterdeelvenster aan de administratieve eenheid moeten worden toegewezen.

        ![een administratieve eenheid selecteren en vervolgens lid toevoegen selecteren](./media/roles-admin-units-add-manage-users/assign-to-admin-unit.png)

1. Bulktoewijzing

    Ga naar Azure AD in de portal en selecteer Beheereenheden. Selecteer de administratieve eenheid waar gebruikers moeten worden toegevoegd. Ga verder door te klikken op Alle gebruikers -> Voeg leden toe uit het CSV-bestand. U vervolgens de CSV-sjabloon downloaden en het bestand bewerken. Het formaat is eenvoudig en heeft een enkele UPN nodig om in elke regel te worden toegevoegd. Zodra het bestand klaar is, slaat u het op een geschikte locatie op en uploadt u het vervolgens in stap 3, zoals gemarkeerd in de momentopname.

    ![bulk gebruikers toewijzen aan een administratieve eenheid](./media/roles-admin-units-add-manage-users/bulk-assign-to-admin-unit.png)

### <a name="powershell"></a>PowerShell

    $administrativeunitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    $UserObj = Get-AzureADUser -Filter "UserPrincipalName eq 'billjohn@fabidentity.onmicrosoft.com'"
    Add-AzureADAdministrativeUnitMember -ObjectId $administrativeunitObj.ObjectId -RefObjectId $UserObj.ObjectId

In het bovenstaande voorbeeld wordt de cmdlet Add-AzureADAdministrativeUnitMember gebruikt om de gebruiker toe te voegen aan de administratieve eenheid. De object-ID van de administratieve eenheid waar de gebruiker moet worden toegevoegd en de object-id van de gebruiker die moet worden toegevoegd, worden als argument gebruikt. De gemarkeerde sectie kan worden gewijzigd zoals vereist voor de specifieke omgeving.

### <a name="microsoft-graph"></a>Microsoft Graph

    Http request
    POST /administrativeUnits/{Admin Unit id}/members/$ref
    Request body
    {
      "@odata.id":"https://graph.microsoft.com/beta/users/{id}"
    }

Voorbeeld:

    {
      "@odata.id":"https://graph.microsoft.com/beta/users/johndoe@fabidentity.com"
    }

## <a name="list-administrative-units-for-a-user"></a>Beheerderseenheden voor een gebruiker aanbieden

### <a name="azure-portal"></a>Azure Portal

In de Azure-portal u het profiel van een gebruiker openen door naar Azure AD->-gebruikers te gaan. Klik op de gebruiker om het profiel van de gebruiker te openen.

![Het profiel van een gebruiker openen in Azure Active Directory](./media/roles-admin-units-add-manage-users/user-profile-admin-units.png)

Selecteer **Beheereenheden** in het linkerdeelvenster om de lijst met beheerderseenheden te zien waaraan de gebruiker is toegewezen.

![De beheerderseenheden voor een gebruiker weergeven](./media/roles-admin-units-add-manage-users/list-user-admin-units.png)

### <a name="powershell"></a>PowerShell

    Get-AzureADAdministrativeUnit | where { Get-AzureADAdministrativeUnitMember -ObjectId $_.ObjectId | where {$_.ObjectId -eq $userObjId} }

### <a name="microsoft-graph"></a>Microsoft Graph

    https://graph.microsoft.com/beta/users//memberOf/$/Microsoft.Graph.AdministrativeUnit

## <a name="remove-a-single-user-from-an-au"></a>Eén gebruiker uit een AU verwijderen

### <a name="azure-portal"></a>Azure Portal

Er zijn twee manieren waarop u een gebruiker uit een administratieve eenheid verwijderen. In de Azure-portal u het profiel van een gebruiker openen door naar **Azure AD-gebruikers** > **Users**te gaan. Selecteer de gebruiker om het profiel van de gebruiker te openen. Selecteer de administratieve eenheid waaruit de gebruiker moet worden verwijderd en selecteer **Verwijderen uit de administratieve eenheid**.

![Een gebruiker uit een beheereenheid verwijderen uit het gebruikersprofiel](./media/roles-admin-units-add-manage-users/user-remove-admin-units.png)

U een gebruiker ook verwijderen in Azure > **AD-beheereenheden** door de beheereenheid te selecteren waaruit u gebruikers wilt verwijderen. **Azure AD** Selecteer de gebruiker en selecteer **Lid verwijderen**.
  
![Een gebruiker verwijderen op het niveau van de administratieve eenheid](./media/roles-admin-units-add-manage-users/admin-units-remove-user.png)

### <a name="powershell"></a>PowerShell

    Remove-AzureADAdministrativeUnitMember -ObjectId $auId -MemberId $memberUserObjId

### <a name="microsoft-graph"></a>Microsoft Graph

   https://graph.microsoft.com/beta/administrativeUnits/<adminunit-id>/members/<user-id>/$ref

## <a name="bulk-remove-more-than-one-user"></a>Bulk verwijdert meer dan één gebruiker

U naar Azure AD->-beheereenheden gaan en de beheereenheid selecteren waaruit u gebruikers wilt verwijderen. Klik op Bulk lid verwijderen. Download de CSV-sjabloon voor het verstrekken van de lijst met gebruikers die moeten worden verwijderd.

Bewerk de gedownloade CSV-sjabloon met de relevante gebruikersvermeldingen. Verwijder de eerste twee rijen van de sjabloon niet. Voeg in elke rij één gebruiker UPN toe.

![Het CSV-bestand bewerken voor het verwijderen van bulkgebruikers uit administratieve eenheden](./media/roles-admin-units-add-manage-users/bulk-user-entries.png)

Zodra u de vermeldingen in het bestand hebt opgeslagen, uploadt u het bestand en selecteert **u Verzenden**.

![Het bestand voor het uploaden van bulk verzenden](./media/roles-admin-units-add-manage-users/bulk-user-remove.png)

## <a name="next-steps"></a>Volgende stappen

- [Een rol toewijzen aan een administratieve eenheid](roles-admin-units-assign-roles.md)
- [Groepen toevoegen aan een administratieve eenheid](roles-admin-units-add-manage-groups.md)
