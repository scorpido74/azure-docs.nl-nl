---
title: Een gebruiker of groep toewijzen aan een bedrijfs-app in azure AD
description: Een bedrijfs-app selecteren waaraan u een gebruiker of groep wilt toewijzen in Azure Active Directory
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79409189"
---
# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory"></a>Een gebruiker of groep toewijzen aan een bedrijfs-app in Azure Active Directory

In dit artikel wordt beschreven hoe u gebruikers of groepen toewijst aan bedrijfs toepassingen in Azure Active Directory (Azure AD), hetzij vanuit de Azure Portal of met behulp van Power shell. Wanneer u een gebruiker toewijst aan een toepassing, wordt de toepassing weer gegeven in het [toegangs venster voor mijn apps](https://myapps.microsoft.com/) van de gebruiker voor eenvoudige toegang. Als de toepassing rollen beschikbaar maakt, kunt u ook een specifieke rol aan de gebruiker toewijzen.

Voor meer controle kunnen bepaalde typen bedrijfs toepassingen worden geconfigureerd voor het vereisen van een [gebruikers toewijzing](#configure-an-application-to-require-user-assignment). 

Als u [een gebruiker of groep aan een bedrijfs-app wilt toewijzen](#assign-users-or-groups-to-an-app-via-the-azure-portal), moet u zich aanmelden als een globale beheerder, toepassings beheerder, Cloud toepassings beheerder of de toegewezen eigenaar van de Enter prise-app.

> [!NOTE]
> Voor een toewijzing op basis van een groep is Azure Active Directory Premium P1 of P2 Edition vereist. Toewijzing op basis van een groep wordt alleen ondersteund voor beveiligings groepen. Geneste groepslid maatschappen en Office 365-groepen worden momenteel niet ondersteund. Zie de [pagina met prijzen voor Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory)voor meer informatie over de licentie vereisten voor de functies die in dit artikel worden besproken. 

## <a name="configure-an-application-to-require-user-assignment"></a>Een toepassing configureren voor het vereisen van gebruikers toewijzing

Met de volgende typen toepassingen kunt u vereisen dat gebruikers aan de toepassing worden toegewezen voordat ze er toegang toe hebben:

- Toepassingen die zijn geconfigureerd voor federatieve eenmalige aanmelding (SSO) met verificatie op basis van SAML
- Toepassings proxy toepassingen die gebruikmaken van Azure Active Directory verificatie vooraf
- Toepassingen die zijn gebouwd op het Azure AD-toepassings platform die gebruikmaken van OAuth 2,0/OpenID Connect Connect-verificatie nadat een gebruiker of beheerder heeft ingestemd met die toepassing.

Wanneer de gebruikers toewijzing is vereist, kunnen alleen gebruikers die u expliciet aan de toepassing toewijst zich aanmelden. Ze hebben toegang tot de app op hun pagina mijn apps of met behulp van een directe koppeling. 

Wanneer de toewijzing *niet is vereist*, omdat u deze optie hebt ingesteld op **Nee** of omdat de toepassing een andere SSO-modus gebruikt, kan elke gebruiker toegang krijgen tot de toepassing als deze een rechtstreekse koppeling naar de toepassing of de **gebruikers toegangs-URL** hebben op de **Eigenschappen** pagina van de toepassing. 

Deze instelling heeft geen invloed op de vraag of een toepassing wordt weer gegeven in het toegangs venster mijn apps. Toepassingen worden weer gegeven op de toegangs Vensters van mijn apps van gebruikers zodra u een gebruiker of groep aan de toepassing hebt toegewezen. Zie [toegang tot apps beheren](what-is-access-management.md)voor achtergrond informatie.


Gebruikers toewijzing voor een toepassing vereisen:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met een beheerders account of als eigenaar van de toepassing.

2. Selecteer **Azure Active Directory**. Selecteer in het navigatie menu links de optie **bedrijfs toepassingen**.

3. Selecteer de toepassing in de lijst. Als u de toepassing niet ziet, begint u met het invoeren van de naam in het zoekvak. Of gebruik de filter besturings elementen om het toepassings type, de status of de zicht baarheid te selecteren en selecteer vervolgens **Toep assen**.

4. Selecteer **Eigenschappen**in het navigatie menu links.

5. Zorg ervoor dat de **gebruikers toewijzing vereist is?** wissel knop is ingesteld op **Ja**.

   > [!NOTE]
   > Als de **gebruikers toewijzing is vereist?** wissel knop niet beschikbaar is, kunt u Power shell gebruiken om de eigenschap appRoleAssignmentRequired in te stellen voor de Service-Principal.

6. Selecteer de knop **Opslaan** boven aan het scherm.

## <a name="assign-users-or-groups-to-an-app-via-the-azure-portal"></a>Gebruikers of groepen toewijzen aan een app via de Azure Portal

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met een globale beheerder, toepassings beheerder of account van de beheerder van de Cloud toepassing, of als de toegewezen eigenaar van de Enter prise-app.
2. Selecteer **Azure Active Directory**. Selecteer in het navigatie menu links de optie **bedrijfs toepassingen**.
3. Selecteer de toepassing in de lijst. Als u de toepassing niet ziet, begint u met het invoeren van de naam in het zoekvak. Of gebruik de filter besturings elementen om het toepassings type, de status of de zicht baarheid te selecteren en selecteer vervolgens **Toep assen**.
4. Selecteer **gebruikers en groepen**in het navigatie menu links.
   > [!NOTE]
   > Als u gebruikers wilt toewijzen aan micro soft-toepassingen, zoals Office 365-apps, gebruiken sommige van deze apps Power shell. 
5. Selecteer de knop **gebruiker toevoegen** .
6. Selecteer **gebruikers en groepen**in het deel venster **toewijzing toevoegen** .
7. Selecteer de gebruiker of groep die u aan de toepassing wilt toewijzen of begin met het typen van de naam van de gebruiker of groep in het zoekvak. U kunt meerdere gebruikers en groepen kiezen en uw selecties worden weer gegeven onder **geselecteerde items**.
8. Wanneer u klaar bent, klikt **u op selecteren**.

   ![Een gebruiker of groep toewijzen aan de app](./media/assign-user-or-group-access-portal/assign-users.png)

9. Selecteer in het deel venster **gebruikers en groepen** een of meer gebruikers of groepen in de lijst en kies vervolgens de knop **selecteren** onder aan het deel venster.
10. Als de toepassing dit ondersteunt, kunt u een rol toewijzen aan de gebruiker of groep. Kies in het deel venster **toewijzing toevoegen** de optie **rol selecteren**. Kies vervolgens in het deel venster **rol selecteren** een rol die u wilt Toep assen op de geselecteerde gebruikers of groepen en selecteer vervolgens **OK** onder aan het deel venster. 

    > [!NOTE]
    > Als de toepassing geen rollen electie ondersteunt, wordt de standaard-Access-rol toegewezen. In dit geval beheert de toepassing het toegangs niveau voor gebruikers.

2. Selecteer in het deel venster **toewijzing toevoegen** de knop **toewijzen** onder aan het deel venster.

## <a name="assign-users-or-groups-to-an-app-via-powershell"></a>Gebruikers of groepen toewijzen aan een app via Power shell

1. Open een verhoogde Windows Power shell-opdracht prompt.

   > [!NOTE]
   > U moet de AzureAD-module installeren (gebruik de opdracht `Install-Module -Name AzureAD`). Als u wordt gevraagd een NuGet-module of de nieuwe Azure Active Directory v2 Power shell-module te installeren, typt u j en drukt u op ENTER.

1. Voer `Connect-AzureAD` uit en meld u aan met een gebruikers account voor globale beheerders.
1. Gebruik het volgende script om een gebruiker en een rol toe te wijzen aan een toepassing:

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

Raadpleeg de documentatie voor [New-AzureADUserAppRoleAssignment](https://docs.microsoft.com/powershell/module/azuread/new-azureaduserapproleassignment?view=azureadps-2.0) voor meer informatie over het toewijzen van een gebruiker aan een toepassingsrol.

Als u een groep aan een bedrijfs-app wilt toewijzen, moet `Get-AzureADUser` u `Get-AzureADGroup`vervangen door.

### <a name="example"></a>Voorbeeld

In dit voor beeld wordt de gebruiker Julia Simon toegewezen aan de [micro soft Workplace Analytics](https://products.office.com/business/workplace-analytics) -toepassing met behulp van Power shell.

1. Wijs in Power shell de overeenkomende waarden toe aan de variabelen $username, $app _name en $app _role_name.

    ```powershell
    # Assign the values to the variables
    $username = "britta.simon@contoso.com"
    $app_name = "Workplace Analytics"
    ```

1. In dit voor beeld weten we niet wat de exacte naam is van de toepassingsrol die we aan Julia Simon willen toewijzen. Voer de volgende opdrachten uit om de gebruiker ($user) en de Service-Principal ($sp) op te halen met behulp van de UPN van de gebruiker en de weer gegeven namen van de Service-Principal.

    ```powershell
    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    ```

1. Voer de opdracht `$sp.AppRoles` uit om de beschik bare functies voor de Application Analytics-toepassing weer te geven. In dit voor beeld willen we de rol analist (beperkte toegang) Julia Simon toewijzen.

   ![Toont de functies die beschikbaar zijn voor een gebruiker met behulp van de rol werk plek Analytics](./media/assign-user-or-group-access-portal/workplace-analytics-role.png)

1. Wijs de rolnaam toe aan de `$app_role_name` variabele.

    ```powershell
    # Assign the values to the variables
    $app_role_name = "Analyst (Limited access)"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }
    ```

1. Voer de volgende opdracht uit om de gebruiker toe te wijzen aan de rol van de app:

    ```powershell
    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```

## <a name="related-articles"></a>Verwante artikelen:

- [Meer informatie over toegang door eind gebruikers tot toepassingen](end-user-experiences.md)
- [Een Azure AD-toegangs venster implementatie plannen](access-panel-deployment-plan.md)
- [Toegang tot apps beheren](what-is-access-management.md)
 
## <a name="next-steps"></a>Volgende stappen

- [Alle groepen weer geven](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Een gebruiker of groeps toewijzing verwijderen uit een bedrijfs-app](remove-user-or-group-access-portal.md)
- [Aanmeldingen van gebruikers voor een bedrijfs-app uitschakelen](disable-user-sign-in-portal.md)
- [De naam of het logo van een zakelijke app wijzigen](change-name-or-logo-portal.md)
