---
title: Gebruikers-of groeps toewijzingen verwijderen uit een app in azure AD
description: De toegangs toewijzing van een gebruiker of groep verwijderen uit een bedrijfs-app in Azure Active Directory
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: mimart
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: edf918b57212cf2adfbffb358a0257d9dbea85e9
ms.sourcegitcommit: b1e25a8a442656e98343463aca706f4fde629867
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/27/2020
ms.locfileid: "74275866"
---
# <a name="remove-a-user-or-group-assignment-from-an-enterprise-app-in-azure-active-directory"></a>Een toewijzing van een gebruiker of groep uit een bedrijfs-app in Azure Active Directory verwijderen

Het is eenvoudig om een gebruiker of een groep te verwijderen uit toegewezen toegang tot een van uw bedrijfs toepassingen in Azure Active Directory (Azure AD). U hebt de juiste machtigingen nodig om de Enter prise-app te beheren. En u moet globale beheerder zijn voor de Directory.

> [!NOTE]
> Voor micro soft-toepassingen (zoals Office 365-apps) gebruikt u Power shell om gebruikers te verwijderen voor een bedrijfs-app.

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-in-the-azure-portal"></a>Hoe kan ik een gebruikers-of groeps toewijzing voor een bedrijfs-app in de Azure Portal verwijderen?

1. Meld u aan bij [Azure Portal](https://portal.azure.com) met een account van een globale beheerder voor de directory.
1. Selecteer **alle services**, Voer **Azure Active Directory** in het tekstvak in en selecteer vervolgens **Enter**.
1. Selecteer op de pagina **Azure Active Directory- *map* ** (dat wil zeggen, de pagina van Azure AD voor de map die u beheert) **bedrijfs toepassingen**.
1. Op de pagina **bedrijfs toepassingen-alle toepassingen** ziet u een lijst met de apps die u kunt beheren. Selecteer een app.
1. Selecteer op de pagina ***AppName*** -overzicht (dat wil zeggen, de pagina met de naam van de geselecteerde app in de titel) de optie **gebruikers & groepen**.
1. Selecteer op de pagina ***AppName*** **-gebruiker & groeps toewijzing** een of meer gebruikers of groepen en selecteer vervolgens de opdracht **verwijderen** . Bevestig uw beslissing bij de prompt.

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-using-powershell"></a>Een toewijzing van een gebruiker of groep Hoe kan ik verwijderen uit een bedrijfs-app met behulp van Power shell?

1. Open een verhoogde Windows Power shell-opdracht prompt.

   > [!NOTE]
   > U moet de AzureAD-module installeren (gebruik de opdracht `Install-Module -Name AzureAD`). Als u wordt gevraagd een NuGet-module of de nieuwe Azure Active Directory v2 Power shell-module te installeren, typt u j en drukt u op ENTER.

1. Voer `Connect-AzureAD` uit en meld u aan met een gebruikers account voor globale beheerders.
1. Gebruik het volgende script om een gebruiker en een rol uit een toepassing te verwijderen:

    ```powershell
    # Store the proper parameters
    $user = get-azureaduser -ObjectId <objectId>
    $spo = Get-AzureADServicePrincipal -ObjectId <objectId>

    #Get the ID of role assignment 
    $assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $spo.ObjectId | Where {$_.PrincipalDisplayName -eq $user.DisplayName}

    #if you run the following, it will show you what is assigned what
    $assignments | Select *

    #To remove the App role assignment run the following command.
    Remove-AzureADServiceAppRoleAssignment -ObjectId $spo.ObjectId -AppRoleAssignmentId $assignments[assignment #].ObjectId
    ```

## <a name="next-steps"></a>Volgende stappen

- [Alle groepen weer geven](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Een gebruiker of groep toewijzen aan een bedrijfs-app](assign-user-or-group-access-portal.md)
- [Aanmeldingen van gebruikers voor een bedrijfs-app uitschakelen](disable-user-sign-in-portal.md)
- [De naam of het logo van een zakelijke app wijzigen](change-name-or-logo-portal.md)
