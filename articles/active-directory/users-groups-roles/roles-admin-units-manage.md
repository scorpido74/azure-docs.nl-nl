---
title: Beheer eenheden toevoegen en verwijderen (preview)-Azure Active Directory | Microsoft Docs
description: Beheer eenheden gebruiken om het bereik van rolmachtigingen te beperken in Azure Active Directory.
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
ms.openlocfilehash: 00ed10ece042446e941832b27c58b0032f852ad9
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/12/2020
ms.locfileid: "84732256"
---
# <a name="manage-administrative-units-in-azure-active-directory"></a>Beheer eenheden in Azure Active Directory beheren

Voor gedetailleerdere administratieve controle in Azure Active Directory (Azure AD) kunt u gebruikers toewijzen aan een Azure AD-rol met een bereik dat beperkt is tot een of meer administratieve eenheden (AUs).

## <a name="get-started"></a>Aan de slag

1. Ga als volgt te werk om query's uit te voeren vanuit de volgende instructies via [Graph Explorer](https://aka.ms/ge):

    a. Ga in de Azure Portal naar Azure AD. Selecteer in de lijst toepassingen de optie **Graph Explorer**en selecteer vervolgens **toestemming geven voor de beheerder voor Graph Explorer**.

    ![Scherm afbeelding met een koppeling naar ' toestemming van beheerder verlenen '](./media/roles-admin-units-manage/select-graph-explorer.png)

    b. Selecteer in Graph Explorer de **bèta** versie.

    ![Scherm opname van de geselecteerde bèta versie](./media/roles-admin-units-manage/select-beta-version.png)

1. De preview-versie van Azure AD Power shell gebruiken.

## <a name="add-an-administrative-unit"></a>Een beheer eenheid toevoegen

### <a name="use-the-azure-portal"></a>Azure Portal gebruiken

1. Ga in de Azure Portal naar Azure AD en selecteer vervolgens in het linkerdeel venster **beheer eenheden**.

    ![Scherm afbeelding van de koppeling administratieve eenheden (preview) in azure AD](./media/roles-admin-units-manage/nav-to-admin-units.png)

1. Selecteer **toevoegen** en voer vervolgens de naam van de beheer eenheid in. U kunt desgewenst een beschrijving van de beheer eenheid toevoegen.

    ![Scherm afbeelding van de knop toevoegen en het tekstvak voor het invoeren van de naam van de beheer eenheid](./media/roles-admin-units-manage/add-new-admin-unit.png)

1. Selecteer **toevoegen** om de beheer eenheid te volt ooien.

### <a name="use-powershell"></a>PowerShell gebruiken

Installeer Azure AD Power shell (preview) voordat u probeert de volgende opdrachten uit te voeren:

    Connect-AzureAD
    New-AzureADAdministrativeUnit -Description "West Coast region" -DisplayName "West Coast"

U kunt de waarden die zijn Inge sloten tussen aanhalings tekens, indien nodig, wijzigen.

### <a name="use-microsoft-graph"></a>Microsoft Graph gebruiken

    Http Request
    POST /administrativeUnits
    Request body
    {
        "displayName": "North America Operations",
        "description": "North America Operations administration"
    }

## <a name="remove-an-administrative-unit"></a>Een beheer eenheid verwijderen

In azure AD kunt u een administratieve eenheid verwijderen die u niet meer nodig hebt als een eenheid van het bereik voor beheerders rollen.

### <a name="use-the-azure-portal"></a>Azure Portal gebruiken

1. Ga in de Azure Portal naar **Azure AD**-  >  **beheer eenheden**. 
1. Selecteer de beheer eenheid die u wilt verwijderen en selecteer vervolgens **verwijderen**. 
1. Selecteer **Ja**om te bevestigen dat u de beheer eenheid wilt verwijderen. De administratieve eenheid wordt verwijderd.

![Scherm afbeelding van de knop voor het verwijderen van de beheer eenheid en het bevestigings venster](./media/roles-admin-units-manage/select-admin-unit-to-delete.png)

### <a name="use-powershell"></a>PowerShell gebruiken

    $delau = Get-AzureADAdministrativeUnit -Filter "displayname eq 'DeleteMe Admin Unit'"
    Remove-AzureADAdministrativeUnit -ObjectId $delau.ObjectId

U kunt de waarden tussen aanhalings tekens, zoals vereist voor de specifieke omgeving, wijzigen.

### <a name="use-the-graph-api"></a>De Graph API gebruiken

    HTTP request
    DELETE /administrativeUnits/{Admin id}
    Request body
    {}

## <a name="next-steps"></a>Volgende stappen

* [Gebruikers beheren in een beheer eenheid](roles-admin-units-add-manage-users.md)
* [Groepen beheren in een beheer eenheid](roles-admin-units-add-manage-groups.md)
