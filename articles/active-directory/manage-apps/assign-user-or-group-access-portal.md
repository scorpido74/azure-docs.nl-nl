---
title: Een gebruiker of groep toewijzen aan een bedrijfsapp in Azure AD
description: Een bedrijfsapp selecteren om een gebruiker of groep toe te wijzen in Azure Active Directory
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: mimart
ms.reviewer: luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 186e36e4625a60362c54972b16b53f0f3e6753fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409189"
---
# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory"></a>Een gebruiker of groep toewijzen aan een bedrijfsapp in Azure Active Directory

In dit artikel ziet u hoe u gebruikers of groepen toewijzen aan bedrijfstoepassingen in Azure Active Directory (Azure AD), vanuit de Azure-portal of met PowerShell. Wanneer u een gebruiker aan een toepassing toewijst, wordt de toepassing weergegeven in het [toegangspaneel Mijn apps](https://myapps.microsoft.com/) van de gebruiker voor eenvoudige toegang. Als de toepassing rollen blootlegt, u ook een specifieke rol aan de gebruiker toewijzen.

Voor meer controle kunnen bepaalde typen bedrijfstoepassingen worden geconfigureerd om [gebruikerstoewijzing te vereisen.](#configure-an-application-to-require-user-assignment) 

Als [u een gebruiker of groep wilt toewijzen aan een bedrijfsapp,](#assign-users-or-groups-to-an-app-via-the-azure-portal)moet u zich aanmelden als globale beheerder, toepassingsbeheerder, beheerder van een cloudtoepassing of de toegewezen eigenaar van de bedrijfsapp.

> [!NOTE]
> Voor groepstoewijzing is Azure Active Directory Premium P1 of P2-editie vereist. Groepstoewijzing wordt alleen ondersteund voor beveiligingsgroepen. Geneste groepslidmaatschappen en Office 365-groepen worden momenteel niet ondersteund. Zie de [prijspagina](https://azure.microsoft.com/pricing/details/active-directory)van Azure Active Directory voor meer licentievereisten voor de functies die in dit artikel worden besproken. 

## <a name="configure-an-application-to-require-user-assignment"></a>Een toepassing configureren om gebruikerstoewijzing te vereisen

Bij de volgende typen toepassingen u ervoor kiezen dat gebruikers aan de toepassing worden toegewezen voordat ze er toegang toe hebben:

- Toepassingen die zijn geconfigureerd voor federatieve single sign-on (SSO) met SAML-gebaseerde verificatie
- Toepassingsproxytoepassingen die Azure Active Directory Pre-Authentication gebruiken
- Toepassingen die zijn gebouwd op het Azure AD-toepassingsplatform en die OAuth 2.0 / OpenID Connect-verificatie gebruiken nadat een gebruiker of beheerder toestemming heeft gegeven voor die toepassing.

Wanneer gebruikerstoewijzing vereist is, kunnen alleen de gebruikers die u expliciet aan de toepassing toewijst, zich aanmelden. Ze hebben toegang tot de app op hun Pagina Mijn apps of via een directe link. 

Wanneer toewijzing *niet vereist*is, omdat u deze optie hebt ingesteld op **Nee** of omdat de toepassing een andere SSO-modus gebruikt, heeft elke gebruiker toegang tot de toepassing als hij een directe koppeling naar de toepassing of de URL **voor gebruikerstoegang** op de pagina **Eigenschappen** van de toepassing heeft. 

Deze instelling heeft geen invloed op het al dan niet verschijnen van een toepassing in het deelvenster Mijn apps. Toepassingen worden weergegeven op de toegangspanelen van Mijn apps van gebruikers zodra u een gebruiker of groep aan de toepassing hebt toegewezen. Zie Toegang [tot apps beheren](what-is-access-management.md)voor achtergrondinformatie.


Gebruikstoewijzing voor een toepassing vereisen:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een beheerdersaccount of als eigenaar van de toepassing.

2. Selecteer **Azure Active Directory**. Selecteer **Enterprise-toepassingen**in het linkernavigatiemenu .

3. Selecteer de toepassing in de lijst. Als u de toepassing niet ziet, typt u de naam in het zoekvak. Of gebruik de filterbesturingselementen om het toepassingstype, de status of de zichtbaarheid te selecteren en selecteer **Vervolgens Toepassen**.

4. Selecteer **Eigenschappen**in het linkernavigatiemenu .

5. Zorg ervoor dat de **vereiste gebruikerstoewijzing?** **Yes**

   > [!NOTE]
   > Als de **vereiste toewijzing voor gebruiker?** Niet beschikbaar is, u PowerShell gebruiken om de eigenschap appRoleAssignmentRequired in te stellen op de serviceprincipal.

6. Selecteer de knop **Opslaan** boven aan het scherm.

## <a name="assign-users-or-groups-to-an-app-via-the-azure-portal"></a>Gebruikers of groepen toewijzen aan een app via de Azure-portal

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een globale beheerder, toepassingsbeheerder of cloudtoepassingsbeheerdersaccount of als de toegewezen eigenaar van de bedrijfsapp.
2. Selecteer **Azure Active Directory**. Selecteer **Enterprise-toepassingen**in het linkernavigatiemenu .
3. Selecteer de toepassing in de lijst. Als u de toepassing niet ziet, typt u de naam in het zoekvak. Of gebruik de filterbesturingselementen om het toepassingstype, de status of de zichtbaarheid te selecteren en selecteer **Vervolgens Toepassen**.
4. Selecteer **gebruikers en groepen**in het linkernavigatiemenu .
   > [!NOTE]
   > Als u gebruikers wilt toewijzen aan Microsoft-toepassingen, zoals Office 365-apps, gebruiken sommige van deze apps PowerShell. 
5. Selecteer de **knop Gebruiker toevoegen.**
6. Selecteer **gebruikers en groepen**in het deelvenster Toewijzing **toevoegen** .
7. Selecteer de gebruiker of groep die u aan de toepassing wilt toewijzen of typ de naam van de gebruiker of groep in het zoekvak. U meerdere gebruikers en groepen kiezen en uw selecties worden weergegeven onder **Geselecteerde items**.
8. Klik op **Selecteren**als u klaar bent.

   ![Een gebruiker of groep aan de app toewijzen](./media/assign-user-or-group-access-portal/assign-users.png)

9. Selecteer **in** het deelvenster Gebruikers en groepen een of meer gebruikers of groepen in de lijst en kies de knop **Selecteren** onder aan het deelvenster.
10. Als de toepassing dit ondersteunt, u een rol toewijzen aan de gebruiker of groep. Kies **Rol selecteren**in het deelvenster **Toewijzing toevoegen** . Kies vervolgens in het deelvenster **Rol selecteren** een rol die u wilt toepassen op de geselecteerde gebruikers of groepen en selecteer **VERVOLGENS OK** onder aan het deelvenster. 

    > [!NOTE]
    > Als de toepassing geen ondersteuning biedt voor rolselectie, wordt de standaardtoegangsrol toegewezen. In dit geval beheert de toepassing het toegangsniveau dat gebruikers hebben.

2. Selecteer in het deelvenster **Toewijzing toevoegen** de knop **Toewijzen** onder aan het deelvenster toewijzen.

## <a name="assign-users-or-groups-to-an-app-via-powershell"></a>Gebruikers of groepen toewijzen aan een app via PowerShell

1. Open een verhoogde Windows PowerShell-opdrachtprompt.

   > [!NOTE]
   > U moet de AzureAD-module installeren `Install-Module -Name AzureAD`(de opdracht gebruiken). Als u wordt gevraagd een NuGet-module of de nieuwe Azure Active Directory V2 PowerShell-module te installeren, typt u Y en drukt u op ENTER.

1. Uitvoeren `Connect-AzureAD` en aanmelden met een gebruikersaccount van globale beheerders.
1. Gebruik het volgende script om een gebruiker en rol aan een toepassing toe te wijzen:

    ```powershell
    # Assign the values to the variables
    $username = "<You user's UPN>"
    $app_name = "<Your App's display name>"
    $app_role_name = "<App role display name>"

    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }

    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```

Ga voor meer informatie over het toewijzen van een gebruiker aan een toepassingsrol naar de documentatie voor [Nieuw-AzureADUserAppRoleAssignment](https://docs.microsoft.com/powershell/module/azuread/new-azureaduserapproleassignment?view=azureadps-2.0)

Als u een groep wilt toewijzen aan `Get-AzureADUser` `Get-AzureADGroup`een bedrijfsapp, moet u deze vervangen door .

### <a name="example"></a>Voorbeeld

In dit voorbeeld wordt de gebruiker Britta Simon met PowerShell aan de [Microsoft Workplace Analytics-toepassing](https://products.office.com/business/workplace-analytics) toegedeeld.

1. Wijs in PowerShell de bijbehorende waarden toe aan de variabelen $username, $app_name en $app_role_name.

    ```powershell
    # Assign the values to the variables
    $username = "britta.simon@contoso.com"
    $app_name = "Workplace Analytics"
    ```

1. In dit voorbeeld weten we niet wat de exacte naam is van de toepassingsrol die we aan Britta Simon willen toewijzen. Voer de volgende opdrachten uit om de gebruiker ($user) en de serviceprincipal ($sp) te krijgen met behulp van de gebruiker UPN en de hoofdweergavenamen van de service.

    ```powershell
    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    ```

1. Voer de `$sp.AppRoles` opdracht uit om de rollen weer te geven die beschikbaar zijn voor de Workplace Analytics-toepassing. In dit voorbeeld willen we Britta Simon de rol van analist (beperkte toegang) toewijzen.

   ![Toont de rollen die beschikbaar zijn voor een gebruiker met behulp van Workplace Analytics Role](./media/assign-user-or-group-access-portal/workplace-analytics-role.png)

1. Wijs de rolnaam toe aan de `$app_role_name` variabele.

    ```powershell
    # Assign the values to the variables
    $app_role_name = "Analyst (Limited access)"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }
    ```

1. Voer de volgende opdracht uit om de gebruiker aan de app-rol toe te wijzen:

    ```powershell
    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```

## <a name="related-articles"></a>Verwante artikelen:

- [Meer informatie over de toegang van eindgebruikers tot toepassingen](end-user-experiences.md)
- [Een Azure AD-toegangspaneelimplementatie plannen](access-panel-deployment-plan.md)
- [Toegang tot apps beheren](what-is-access-management.md)
 
## <a name="next-steps"></a>Volgende stappen

- [Bekijk al mijn groepen](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Een gebruiker of groepstoewijzing verwijderen uit een bedrijfsapp](remove-user-or-group-access-portal.md)
- [Aanmeldingen van gebruikers voor een bedrijfsapp uitschakelen](disable-user-sign-in-portal.md)
- [De naam of het logo van een bedrijfsapp wijzigen](change-name-or-logo-portal.md)
