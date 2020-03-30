---
title: Zichtbaarheid voor tenant voor Azure Security Center | Microsoft Documenten
description: In dit artikel wordt uitgelegd hoe u uw beveiligingshouding op schaal beheren door beleid toe te passen op alle abonnementen die zijn gekoppeld aan uw Azure Active Directory-tenant.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: b85c0e93-9982-48ad-b23f-53b367f22b10
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2018
ms.author: memildin
ms.openlocfilehash: 734876380d22f5d4d6dae0dd375b238fd5f6ffed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74559350"
---
# <a name="gain-tenant-wide-visibility-for-azure-security-center"></a>Zichtbaarheid voor tenant voor Azure Security Center
In dit artikel wordt uitgelegd hoe u de beveiligingshouding van uw organisatie op schaal beheren door beveiligingsbeleid toe te passen op alle Azure-abonnementen die zijn gekoppeld aan uw Azure Active Directory-tenant.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="management-groups"></a>Beheergroepen
Azure-beheergroepen bieden de mogelijkheid om toegang, beleid en rapportage over groepen abonnementen efficiënt te beheren en effectief de gehele Azure-afmeting te beheren door acties uit te voeren op de hoofdbeheergroep. Elke Azure AD-tenant krijgt één beheergroep op het hoogste niveau, de hoofdbeheergroep. Deze hoofdbeheergroep is zo in de hiërarchie ingebouwd dat alle beheergroepen en abonnementen hierin zijn opgevouwen. Met deze groep kunnen globale beleidsregels en RBAC-toewijzingen worden toegepast op directoryniveau. 

De hoofdbeheergroep wordt automatisch gemaakt wanneer u een van de volgende acties doet: 
1. U zich aanmelden voor azure-beheergroepen door te navigeren naar **beheergroepen** in de [Azure-portal.](https://portal.azure.com)
2. Maak een beheergroep via een API-aanroep.
3. Maak een beheergroep met PowerShell.

Zie Het artikel Uw resources [ordenen met Azure-beheergroepen](../azure-resource-manager/management-groups-overview.md) voor een gedetailleerd overzicht van beheergroepen.

## <a name="create-a-management-group-in-the-azure-portal"></a>Een beheergroep maken in de Azure-portal
U abonnementen organiseren in beheergroepen en uw governancebeleid toepassen op de beheergroepen. Alle abonnementen in een beheergroep nemen automatisch het beleid over dat op de beheergroep is toegepast. Hoewel beheergroepen niet aan boord van het Security Center hoeven te worden aanboord, is het ten zeerste aan te raden om ten minste één beheergroep te maken, zodat de hoofdbeheergroep wordt gemaakt. Nadat de groep is gemaakt, worden alle abonnementen onder uw Azure AD-tenant eraan gekoppeld. Zie [Beheergroepen maken voor resource- en organisatiebeheer](../azure-resource-manager/management-groups-create.md)voor instructies voor PowerShell en meer informatie.

 
1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer **Alle services** > **beheergroepen**.
3. Selecteer op de hoofdpagina de optie **Nieuwe beheergroep.** 

    ![Hoofdgroep](./media/security-center-management-groups/main.png) 
4.  Vul het veld beheergroep-id in. 
    - De **managementgroep-id** is de unieke id van de map die wordt gebruikt om opdrachten in te dienen voor deze beheergroep. Deze id is niet bewerkbaar na het maken, omdat deze in het hele Azure-systeem wordt gebruikt om deze groep te identificeren. 
    - Het veld weergavenaam is de naam die wordt weergegeven in de Azure-portal. Een aparte weergavenaam is een optioneel veld bij het maken van de beheergroep en kan op elk gewenst moment worden gewijzigd.  

      ![Maken](./media/security-center-management-groups/create_context_menu.png)  
5.  Selecteer **Opslaan**

### <a name="view-management-groups-in-the-azure-portal"></a>Beheergroepen weergeven in de Azure-portal
1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Als u beheergroepen wilt weergeven, selecteert u **Alle services** onder het hoofdmenu van Azure.
3. Selecteer **onder Algemeen** **beheergroepen**.

    ![Een beheergroep maken](./media/security-center-management-groups/all-services.png)

## <a name="grant-tenant-level-visibility-and-the-ability-to-assign-policies"></a>Zichtbaarheid op tenantniveau en de mogelijkheid om beleid toe te wijzen

Om inzicht te krijgen in de beveiligingshouding van alle abonnementen die zijn geregistreerd in de Azure AD-tenant, moet een RBAC-rol met voldoende leesmachtigingen worden toegewezen aan de hoofdbeheergroep.

### <a name="elevate-access-for-a-global-administrator-in-azure-active-directory"></a>Toegang voor een globale beheerder in Azure Active Directory verhogen
Een Azure Active Directory-tenantbeheerder heeft geen directe toegang tot Azure-abonnementen. Als directorybeheerder hebben ze echter het recht om zichzelf te verheffen tot een rol die wel toegang heeft. Een Azure AD-tenantbeheerder moet zichzelf verheffen tot gebruikerstoegangsbeheerder op het niveau van de rootbeheergroep, zodat deze RBAC-rollen kan toewijzen. Zie [Toegang voor een globale beheerder in Azure Active Directory voor](../role-based-access-control/elevate-access-global-admin.md)PowerShell-instructies en aanvullende informatie. 


1. Meld u aan bij de [Azure-portal](https://portal.azure.com) of het [Azure Active Directory-beheercentrum](https://aad.portal.azure.com).

2. Klik in de navigatielijst op **Azure Active Directory** en klik vervolgens op **Eigenschappen**.

   ![Azure AD-eigenschappen - schermafbeelding](./media/security-center-management-groups/aad-properties.png)

3. Stel **onder Access-beheer voor Azure-resources**de overstap in op **Ja**.

   ![Globale beheerder kan Azure-abonnementen en beheergroepen beheren - schermafbeelding](./media/security-center-management-groups/aad-properties-global-admin-setting.png)

   - Wanneer u de switch instelt op Ja, krijgt u de rol Gebruikerstoegangsbeheerder in Azure RBAC toegewezen aan het hoofdbereik (/). Hiermee u rollen toewijzen in alle Azure-abonnementen en beheergroepen die zijn gekoppeld aan deze Azure AD-map. Deze switch is alleen beschikbaar voor gebruikers die de rol Globale beheerder in Azure AD hebben toegewezen.

   - Wanneer u de switch instelt op Nee, wordt de rol Gebruikerstoegangsbeheer in Azure RBAC uit uw gebruikersaccount verwijderd. U geen rollen meer toewijzen in alle Azure-abonnementen en beheergroepen die zijn gekoppeld aan deze Azure AD-map. U alleen de Azure-abonnementen en beheergroepen waartoe u toegang hebt gekregen, bekijken en beheren.

4. Klik **op Opslaan** om de instelling op te slaan.

    - Deze instelling is geen globale eigenschap en is alleen van toepassing op de momenteel ingelogde gebruiker.

5. Voer de taken uit die u moet uitvoeren bij de verhoogde toegang. Als je klaar bent, zet je de schakelaar terug op **Nee.**


### <a name="assign-rbac-roles-to-users"></a>RBAC-rollen toewijzen aan gebruikers
Om inzicht te krijgen in alle abonnementen, moeten tenantbeheerders de juiste RBAC-rol toewijzen aan gebruikers die ze tenantbrede zichtbaarheid willen geven, inclusief zichzelf, op het niveau van de root-beheergroep. De aanbevolen rollen die u wilt toewijzen zijn **beveiligingsbeheerder** of **beveiligingslezer.** Over het algemeen is de functie Beveiligingsbeheer vereist om beleid op basisniveau toe te passen, terwijl Beveiligingslezer voldoende is om zichtbaarheid op tenantniveau te bieden. Zie de beschrijving van de [ingebouwde rolbeschrijving beveiligingsbeheerder](../role-based-access-control/built-in-roles.md#security-admin) of de [ingebouwde rolbeschrijving van beveiligingslezer](../role-based-access-control/built-in-roles.md#security-reader)voor meer informatie over de machtigingen die door deze rollen worden verleend.


#### <a name="assign-rbac-roles-to-users-through-the-azure-portal"></a>RBAC-rollen toewijzen aan gebruikers via de Azure-portal: 

1. Meld u aan bij [Azure Portal](https://portal.azure.com). 
1. Als u beheergroepen wilt weergeven, selecteert u **Alle services** onder het hoofdmenu van Azure en selecteert u **Beheergroepen**.
1.  Selecteer een beheergroep en klik op **details**.

    ![Schermafbeelding van details van beheergroepen](./media/security-center-management-groups/management-group-details.PNG)
 
1. Klik **op Toegangsbesturingselement (IAM)** en **vervolgens op Toewijzingen van rollen**.

1. Klik **op Roltoewijzing toevoegen**.

1. Selecteer de rol die u wilt toewijzen en de gebruiker en klik op **Opslaan**.  
   
   ![Schermafbeelding van de rolschermafbeelding Beveiligingslezer toevoegen](./media/security-center-management-groups/asc-security-reader.png)


#### <a name="assign-rbac-roles-to-users-with-powershell"></a>RBAC-rollen toewijzen aan gebruikers met PowerShell: 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Azure [PowerShell](/powershell/azure/install-az-ps)installeren .
2. Voer de volgende opdrachten uit: 

    ```azurepowershell
    # Login to Azure as a Global Administrator user
    Connect-AzAccount
    ```

3. Meld u aan met algemene beheerdersreferenties wanneer u daarom wordt gevraagd. 

    ![Schermafbeelding van aanmeldingsprompt](./media/security-center-management-groups/azurerm-sign-in.PNG)

4. Machtigingen voor lezersrollen verlenen door de volgende opdracht uit te voeren:

    ```azurepowershell
    # Add Reader role to the required user on the Root Management Group
    # Replace "user@domian.com” with the user to grant access to
    New-AzRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/"
    ```
5. Als u de rol wilt verwijderen, gebruikt u de volgende opdracht: 

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/" 
    ```

### <a name="open-or-refresh-security-center"></a>Beveiligingscentrum openen of vernieuwen
Zodra u de toegang hebt verhoogd, opent of vernieuwt u Azure Security Center om te controleren of u inzicht hebt in alle abonnementen onder uw Azure AD-tenant. 

1. Meld u aan bij [Azure Portal](https://portal.azure.com). 
2. Zorg ervoor dat u alle abonnementen in de abonnementskiezer selecteert die u in Beveiligingscentrum wilt weergeven.

    ![Schermafbeelding van abonnementskiezer](./media/security-center-management-groups/subscription-selector.png)

1. Selecteer **Alle services** onder het hoofdmenu van Azure selecteert u **Beveiligingscentrum**.
2. In het **overzicht**is er een overzichtsoverzicht.

    ![Schermafbeelding van de overzichtsgrafiek van abonnementen](./media/security-center-management-groups/security-center-subscription-coverage.png)

3. Klik op **Dekking** om de lijst met abonnementen te bekijken. 

    ![Schermafbeelding van de lijst met abonnementsdekking](./media/security-center-management-groups/security-center-coverage.png)

### <a name="remove-elevated-access"></a>Verhoogde toegang verwijderen 
Zodra de RBAC-rollen aan de gebruikers zijn toegewezen, moet de tenantbeheerder zichzelf verwijderen uit de rol van gebruikerstoegangsbeheerder.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) of het [Azure Active Directory-beheercentrum](https://aad.portal.azure.com).

2. Klik in de navigatielijst op **Azure Active Directory** en klik vervolgens op **Eigenschappen**.

3. Stel **onder Globale beheerder Azure-abonnementen en beheergroepen beheren**en stelt u de overstap in op **Nee**.

4. Klik **op Opslaan** om de instelling op te slaan.



## <a name="adding-subscriptions-to-a-management-group"></a>Abonnementen toevoegen aan een beheergroep
U abonnementen toevoegen aan de beheergroep die u hebt gemaakt. Deze stappen zijn niet verplicht voor het verkrijgen van tenantbrede zichtbaarheid en wereldwijd beleid en toegangsbeheer.

1. Selecteer **onder Beheergroepen**een beheergroep waaraan u uw abonnement wilt toevoegen.

    ![Selecteer een beheergroep waaraan u een abonnement wilt toevoegen](./media/security-center-management-groups/management-group-subscriptions.png)

2. Selecteer **Bestaande toevoegen**.

    ![Bestaande toevoegen](./media/security-center-management-groups/add-existing.png)

3. Voer een abonnement in onder **Bestaande resource toevoegen** en klik op **Opslaan**.

4. Herhaal stap 1 tot en met 3 totdat u alle abonnementen in het bereik hebt toegevoegd.

   > [!NOTE]
   > Beheergroepen kunnen zowel abonnementen als onderliggende beheergroepen bevatten. Wanneer u een gebruiker een RBAC-rol toewijst aan de bovenliggende beheergroep, wordt de toegang overgenomen door de abonnementen van de onderliggende beheergroep. Beleid dat is ingesteld bij de bovenliggende beheergroep wordt ook overgenomen door de kinderen. 

## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u geleerd hoe u tenantbrede zichtbaarheid krijgen voor Azure Security Center. Zie de volgende artikelen voor meer informatie over Security Center:

> [!div class="nextstepaction"]
> [Beveiligingsstatus bewaken in Azure Security Center](security-center-monitoring.md)

> [!div class="nextstepaction"]
> [Beveiligingswaarschuwingen beheren en erop reageren in Azure Security Center](security-center-managing-and-responding-alerts.md)

