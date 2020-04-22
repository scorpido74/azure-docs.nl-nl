---
title: Administratieve eenheden toevoegen en verwijderen (voorbeeld) - Azure Active Directory | Microsoft Documenten
description: Gebruik beheereenheden om het bereik van rolmachtigingen in Azure Active Directory te beperken.
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
ms.openlocfilehash: 7a4a0dfaeda18b3f68ddc3c7cc7333b8c994d174
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684921"
---
# <a name="manage-administrative-units-in-azure-active-directory"></a>Beheervan beheereenheden in Azure Active Directory

Voor meer gedetailleerd beheerbeheerbeheer in Azure Active Directory (Azure AD) u gebruikers toewijzen aan een Azure AD-rol met een bereik dat is beperkt tot een of meer beheereenheden (AU's).

## <a name="get-started"></a>Aan de slag

1. Ga als volgt te werk om query's uit de volgende instructies via [Graph Explorer](https://aka.ms/ge)uit te voeren:

    a. Ga in de Azure-portal naar Azure AD. Selecteer **Grafiekverkenner**in de lijst met toepassingen en selecteer **vervolgens Toestemming voor beheerders verlenen aan Graph Explorer**.

    ![Schermafbeelding van koppeling naar 'Toestemming verlenen van beheerders'](./media/roles-admin-units-manage/select-graph-explorer.png)

    b. Selecteer in Grafiekverkenner de **bètaversie.**

    ![Schermafbeelding van de geselecteerde bètaversie](./media/roles-admin-units-manage/select-beta-version.png)

1. Gebruik de preview-versie van Azure AD PowerShell.

## <a name="add-an-administrative-unit"></a>Een administratieve eenheid toevoegen

### <a name="use-the-azure-portal"></a>Azure Portal gebruiken

1. Ga in de Azure-portal naar Azure AD en selecteer vervolgens in het linkerdeelvenster **Beheereenheden**.

    ![Schermafbeelding van de koppeling Voorvertoningseenheden (Preview) in Azure AD](./media/roles-admin-units-manage/nav-to-admin-units.png)

1. Selecteer **Toevoegen** en voer de naam van de administratieve eenheid in. Voeg eventueel een beschrijving van de administratieve eenheid toe.

    ![Schermafbeelding van de knop Toevoegen en het tekstvak voor het invoeren van de naam van de administratieve eenheid](./media/roles-admin-units-manage/add-new-admin-unit.png)

1. Selecteer **Toevoegen** om de administratieve eenheid af te ronden.

### <a name="use-powershell"></a>PowerShell gebruiken

Installeer Azure AD PowerShell (voorbeeld) voordat u de volgende opdrachten probeert uit te voeren:

    Connect-AzureAD
    New-AzureADAdministrativeUnit -Description "West Coast region" -DisplayName "West Coast"

U de waarden die zijn ingesloten tussen aanhalingstekens, naar behoefte wijzigen.

### <a name="use-microsoft-graph"></a>Microsoft Graph gebruiken

    Http Request
    POST /administrativeUnits
    Request body
    {
        "displayName": "North America Operations",
        "description": "North America Operations administration"
    }

## <a name="remove-an-administrative-unit"></a>Een administratieve eenheid verwijderen

In Azure AD u een administratieve eenheid verwijderen die u niet meer nodig hebt als eenheid van ruimte voor beheerfuncties.

### <a name="use-the-azure-portal"></a>Azure Portal gebruiken

1. Ga in de **Azure-portal**naar Azure > **AD-beheereenheden**. 
1. Selecteer de administratieve eenheid die moet worden verwijderd en selecteer **Verwijderen**. 
1. Als u wilt bevestigen dat u de administratieve eenheid wilt verwijderen, selecteert u **Ja**. De administratieve eenheid wordt verwijderd.

![Schermafbeelding van de knop Verwijderen van de administratieve eenheid en het bevestigingsvenster](./media/roles-admin-units-manage/select-admin-unit-to-delete.png)

### <a name="use-powershell"></a>PowerShell gebruiken

    $delau = Get-AzureADAdministrativeUnit -Filter "displayname eq 'DeleteMe Admin Unit'"
    Remove-AzureADAdministrativeUnit -ObjectId $delau.ObjectId

U de waarden wijzigen die zijn ingesloten tussen aanhalingstekens, zoals vereist voor de specifieke omgeving.

### <a name="use-the-graph-api"></a>De Graph-API gebruiken

    HTTP request
    DELETE /administrativeUnits/{Admin id}
    Request body
    {}

## <a name="next-steps"></a>Volgende stappen

* [Gebruikers beheren in een administratieve eenheid](roles-admin-units-add-manage-users.md)
* [Groepen beheren in een administratieve eenheid](roles-admin-units-add-manage-groups.md)
