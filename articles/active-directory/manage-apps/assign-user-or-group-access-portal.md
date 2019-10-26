---
title: Een gebruiker of groep toewijzen aan een bedrijfs-app in Azure Active Directory | Microsoft Docs
description: Een bedrijfs-app selecteren waaraan u een gebruiker of groep wilt toewijzen in Azure Active Directory
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: mimart
ms.reviewer: luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6d28b9c31b8fbad8a565ff8cbdf717bfb3bc1309
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72896472"
---
# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory"></a>Een gebruiker of groep toewijzen aan een bedrijfs-app in Azure Active Directory

Als u een gebruiker of groep aan een bedrijfs-app wilt toewijzen, moet u een van deze beheerders rollen hebben toegewezen: globale beheerder, toepassings beheerder, Cloud toepassings beheerder of worden toegewezen als de eigenaar van de Enter prise-app.  Voor micro soft-toepassingen (zoals Office 365-apps) gebruikt u Power shell om gebruikers toe te wijzen aan een bedrijfs-app.

> [!NOTE]
> Zie de [pagina met Azure Active Directory prijzen](https://azure.microsoft.com/pricing/details/active-directory)voor licentie vereisten voor de functies die in dit artikel worden besproken.

## <a name="assign-a-user-to-an-app---portal"></a>Een gebruiker toewijzen aan een app-Portal

1. Meld u aan bij [Azure Portal](https://portal.azure.com) met een account van een globale beheerder voor de directory.
1. Selecteer **alle services**, voer Azure Active Directory in het tekstvak in en selecteer vervolgens **Enter**.
1. Selecteer **bedrijfs toepassingen**.
1. In het deel venster **bedrijfs toepassingen-alle toepassingen** ziet u een lijst met de apps die u kunt beheren. Selecteer een app.
1. Selecteer in het deel venster ***AppName*** (dat wil zeggen het deel venster met de naam van de geselecteerde app in de titel) de optie **gebruikers & groepen**.
1. Selecteer **gebruiker toevoegen**in het deel venster ***AppName*** **-gebruiker en groepen** .
1. Selecteer **gebruikers en groepen**in het deel venster **toewijzing toevoegen** .

   ![Een gebruiker of groep toewijzen aan de app](./media/assign-user-or-group-access-portal/assign-users.png)

1. Selecteer in het deel venster **gebruikers en groepen** een of meer gebruikers of groepen in de lijst en kies vervolgens de knop **selecteren** onder aan het deel venster.
1. Selecteer in het deel venster **toewijzing toevoegen** de optie **rol**. Selecteer vervolgens in het deel venster **rol selecteren** een rol die u wilt Toep assen op de geselecteerde gebruikers of groepen en selecteer vervolgens **OK** onder aan het deel venster.
1. Selecteer in het deel venster **toewijzing toevoegen** de knop **toewijzen** onder aan het deel venster. De toegewezen gebruikers of groepen hebben de machtigingen die zijn gedefinieerd door de geselecteerde rol voor deze zakelijke app.

## <a name="allow-all-users-to-access-an-app---portal"></a>Alle gebruikers toestaan om toegang te krijgen tot een app-Portal

1. Meld u aan bij [Azure Portal](https://portal.azure.com) met een account van een globale beheerder voor de directory.
1. Selecteer **alle services**, voer Azure Active Directory in het tekstvak in en selecteer vervolgens **Enter**.
1. Selecteer **bedrijfs toepassingen**.
1. Selecteer **alle toepassingen**in het deel venster **bedrijfs toepassingen** . Hier vindt u een lijst met de apps die u kunt beheren.
1. Selecteer een app in het deel venster **bedrijfs toepassingen-alle toepassingen** .
1. Selecteer **Eigenschappen**in het deel venster ***AppName*** .
1. Stel in het deel venster  ***AppName* -eigenschappen** de **gebruikers toewijzing vereist** in op **Nee**.

De **gebruikers toewijzing is vereist?** optie:

- Als deze optie is ingesteld op Ja, moeten gebruikers eerst worden toegewezen aan deze toepassing voordat ze toegang kunnen krijgen.
- Als deze optie is ingesteld op Nee, worden gebruikers die naar de uitgebreide URL van de toepassing of de URL van de toepassing verwijzen, rechtstreeks toegang verleend
- Heeft geen invloed op het al dan niet weer geven van een toepassing in het toegangs venster van de toepassing. Als u de toepassing wilt weer geven in het toegangs venster, moet u een geschikte gebruiker of groep toewijzen aan de toepassing.
- Werkt alleen met de Cloud toepassingen die zijn geconfigureerd voor eenmalige SAML-aanmelding, toepassings proxy toepassingen die gebruikmaken van Azure Active Directory vooraf-verificatie of toepassingen die rechtstreeks zijn gebouwd op het Azure AD-toepassings platform dat gebruikmaakt van OAuth 2,0/ OpenID Connect Connect-verificatie nadat een gebruiker of beheerder heeft ingestemd met die toepassing. Zie [eenmalige aanmelding voor toepassingen](what-is-single-sign-on.md). Zie [configureren hoe eind gebruikers toestemming geven voor een toepassing](configure-user-consent.md).
- Deze optie heeft geen effect wanneer een toepassing is geconfigureerd voor een van de andere modus voor eenmalige aanmelding.

## <a name="assign-a-user-to-an-app---powershell"></a>Een gebruiker toewijzen aan een app-Power shell

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

Als u een groep aan een bedrijfs-app wilt toewijzen, moet u `Get-AzureADUser` vervangen door `Get-AzureADGroup`.

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

1. Voer de opdracht `$sp.AppRoles` uit om de beschik bare functies voor de toepassing voor het Toep assen van de app weer te geven. In dit voor beeld willen we de rol analist (beperkte toegang) Julia Simon toewijzen.

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

## <a name="next-steps"></a>Volgende stappen

- [Alle groepen weer geven](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Een gebruiker of groeps toewijzing verwijderen uit een bedrijfs-app](remove-user-or-group-access-portal.md)
- [Aanmeldingen van gebruikers voor een bedrijfs-app uitschakelen](disable-user-sign-in-portal.md)
- [De naam of het logo van een zakelijke app wijzigen](change-name-or-logo-portal.md)
