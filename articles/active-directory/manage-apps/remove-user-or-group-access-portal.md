---
title: Gebruikers- of groepstoewijzingen verwijderen uit een app in Azure AD
description: De toegangstoewijzing van een gebruiker of groep verwijderen uit een bedrijfsapp in Azure Active Directory
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74275866"
---
# <a name="remove-a-user-or-group-assignment-from-an-enterprise-app-in-azure-active-directory"></a>Een gebruiker of groepstoewijzing verwijderen uit een bedrijfsapp in Azure Active Directory

Het is eenvoudig om een gebruiker of een groep te verwijderen uit toegewezen toegang tot een van uw bedrijfstoepassingen in Azure Active Directory (Azure AD). U hebt de juiste machtigingen nodig om de bedrijfsapp te beheren. En u moet globale beheerder voor de directory zijn.

> [!NOTE]
> Voor Microsoft-toepassingen (zoals Office 365-apps) gebruikt u PowerShell om gebruikers naar een bedrijfsapp te verwijderen.

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-in-the-azure-portal"></a>Hoe verwijder ik een gebruiker of groepstoewijzing naar een bedrijfsapp in de Azure-portal?

1. Meld u aan bij [Azure Portal](https://portal.azure.com) met een account van een globale beheerder voor de directory.
1. Selecteer **Alle services,** voer **Azure Active Directory** in het tekstvak in en selecteer **Enter**.
1. Selecteer op de pagina **Azure Active Directory - *directoryname* ** (dat wil zeggen de Azure AD-pagina voor de map die u beheert), de optie **Enterprise-toepassingen**.
1. Op de pagina **Enterprise-toepassingen - Alle toepassingen** ziet u een lijst met de apps die u beheren. Selecteer een app.
1. Selecteer op de pagina ***met appnaam*** (dat wil zeggen de pagina met de naam van de geselecteerde app in de titel) de optie **Gebruikers & groepen**.
1. Selecteer op de pagina ***Appname*** **- User & Group Assignment** een van de meer gebruikers of groepen en selecteer vervolgens de opdracht **Verwijderen.** Bevestig uw beslissing op de prompt.

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-using-powershell"></a>Hoe verwijder ik een gebruiker of groepstoewijzing naar een bedrijfsapp met PowerShell?

1. Open een verhoogde Windows PowerShell-opdrachtprompt.

   > [!NOTE]
   > U moet de AzureAD-module installeren `Install-Module -Name AzureAD`(de opdracht gebruiken). Als u wordt gevraagd een NuGet-module of de nieuwe Azure Active Directory V2 PowerShell-module te installeren, typt u Y en drukt u op ENTER.

1. Uitvoeren `Connect-AzureAD` en aanmelden met een gebruikersaccount van globale beheerders.
1. Gebruik het volgende script om een gebruiker en rol uit een toepassing te verwijderen:

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

- [Bekijk al mijn groepen](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Een gebruiker of groep toewijzen aan een bedrijfsapp](assign-user-or-group-access-portal.md)
- [Aanmeldingen van gebruikers voor een bedrijfsapp uitschakelen](disable-user-sign-in-portal.md)
- [De naam of het logo van een bedrijfsapp wijzigen](change-name-or-logo-portal.md)
