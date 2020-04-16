---
title: Administratieve eenheden toevoegen en verwijderen (voorbeeld) - Azure Active Directory | Microsoft Documenten
description: Beheereenheden gebruiken om het bereik van functiemachtigingen in Azure Active Directory te beperken
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
ms.openlocfilehash: 351cbee9cbc7cc02c7f20553b6fc8c3f861541df
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428158"
---
# <a name="manage-administrative-units-in-azure-active-directory"></a>Beheervan beheereenheden in Azure Active Directory

Voor meer gedetailleerd beheerbeheerbeheer in Azure Active Directory (Azure AD) u gebruikers toewijzen aan een Azure AD-rol met een bereik dat beperkt is tot een of meer beheereenheden (AU's).

## <a name="getting-started"></a>Aan de slag

1. Als u query's wilt uitvoeren via de volgende instructies via [Graph Explorer,](https://aka.ms/ge)moet u het volgende controleren:

    1. Ga naar Azure AD in de portal en selecteer vervolgens in de toepassingen Graph Explorer en geef beheerderstoestemming voor Graph Explorer.

        ![selecteer Grafiekverkenner en geef beheerderstoestemming op deze pagina](./media/roles-admin-units-manage/select-graph-explorer.png)

    1. Controleer in de Grafiekverkenner of u de bètaversie selecteert.

        ![de bètaversie selecteren vóór de postbewerking](./media/roles-admin-units-manage/select-beta-version.png)

1. Gebruik de preview-versie van Azure AD PowerShell. Gedetailleerde instructies zijn hier.

## <a name="add-an-administrative-unit"></a>Een administratieve eenheid toevoegen

### <a name="azure-portal"></a>Azure Portal

1. Ga naar Active Directory in de portal en selecteer Beheereenheden in het linkerdeelvenster.

    ![navigeren naar beheereenheden in Azure Active Directory](./media/roles-admin-units-manage/nav-to-admin-units.png)

1. Selecteer **Toevoegen*** en geef de naam van de administratieve eenheid op en kan optioneel een beschrijving voor de administratieve eenheid toevoegen.

    ![selecteer Toevoegen en voer vervolgens een naam in voor de administratieve eenheid](./media/roles-admin-units-manage/add-new-admin-unit.png)

1. Selecteer **Toevoegen** om de administratieve eenheid af te ronden.

### <a name="powershell"></a>PowerShell

Installeer Azure AD PowerShell (preview-versie) voordat u de onderstaande acties probeert uit te voeren:

    Connect-AzureAD
    New-AzureADAdministrativeUnit -Description "West Coast region" -DisplayName "West Coast"

De hierboven gemarkeerde waarden kunnen naar behoefte worden gewijzigd.

### <a name="microsoft-graph"></a>Microsoft Graph

    Http Request
    POST /administrativeUnits
    Request body
    {
        "displayName": "North America Operations",
        "description": "North America Operations administration"
    }

## <a name="remove-an-administrative-unit"></a>Een administratieve eenheid verwijderen

In Azure Active Directory (Azure AD) u een beheereenheid verwijderen die u niet meer nodig hebt als eenheid van bereik voor beheerrollen.

### <a name="azure-portal"></a>Azure Portal

Ga naar **Azure AD->-beheereenheden** in de portal. Selecteer de administratieve eenheid die moet worden verwijderd en selecteer **Verwijderen**. Na het bevestigen van **Ja,** wordt de administratieve eenheid verwijderd.

![Een administratieve eenheid selecteren die u wilt verwijderen](./media/roles-admin-units-manage/select-admin-unit-to-delete.png)

### <a name="powershell"></a>PowerShell

    $delau = Get-AzureADAdministrativeUnit -Filter "displayname eq 'DeleteMe Admin Unit'"
    Remove-AzureADAdministrativeUnit -ObjectId $delau.ObjectId

De gemarkeerde sectie kan worden gewijzigd zoals vereist voor de specifieke omgeving.

### <a name="graph-api"></a>Graph API

    HTTP request
    DELETE /administrativeUnits/{Admin id}
    Request body
    {}

## <a name="next-steps"></a>Volgende stappen

[Gebruikers beheren in administratieve eenheid](roles-admin-units-add-manage-users.md)
[Groepen beheren in administratieve eenheid](roles-admin-units-add-manage-groups.md)
