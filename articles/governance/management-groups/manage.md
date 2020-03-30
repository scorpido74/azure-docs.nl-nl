---
title: Werken met uw beheergroepen - Azure Governance
description: Meer informatie over het weergeven, onderhouden, bijwerken en verwijderen van de hiërarchie van uw beheergroep.
ms.date: 12/18/2019
ms.topic: conceptual
ms.openlocfilehash: 94df67888c0ed0ea532844a92a362a181621d3d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78267945"
---
# <a name="manage-your-resources-with-management-groups"></a>Uw resources beheren met beheergroepen

Als uw organisatie veel abonnementen heeft, moet u de toegang, beleidsregels en naleving voor deze abonnementen op een efficiënte manier kunnen beheren. Azure-beheergroepen bieden een scopeniveau boven abonnementen. U ordent abonnementen in containers, zogenaamde 'beheergroepen', en past uw governancevoorwaarden hierop toe. Alle abonnementen in een beheergroep nemen automatisch de voorwaarden over die op de beheergroep zijn toegepast.

Beheergroepen bieden u beheer van bedrijfskwaliteit op grote schaal, ongeacht de typen abonnementen die u hebt.  Zie Uw [resources ordenen met Azure-beheergroepen](overview.md)voor meer informatie over beheergroepen.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

>[!IMPORTANT]
>Azure Resource Manager-gebruikerstokens en beheergroepcache duurt 30 minuten voordat ze worden vernieuwd.  Nadat u een actie hebt gedaan, zoals het verplaatsen van een beheergroep of een abonnement, kan het tot 30 minuten duren voordat deze worden weergegeven.  
>Als u de updates sneller wilt bekijken, moet u uw token bijwerken door de browser te vernieuwen, in- en uit te melden of een nieuw token aan te vragen.  



## <a name="change-the-name-of-a-management-group"></a>De naam van een beheergroep wijzigen

U de naam van de beheergroep wijzigen met behulp van de portal, PowerShell of Azure CLI.

### <a name="change-the-name-in-the-portal"></a>De naam in de portal wijzigen

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Selecteer **Alle services** > **beheergroepen**.

1. Selecteer de beheergroep die u de naam wilt wijzigen.

1. Selecteer **details**.

1. Selecteer de optie **Naam wijzigen** boven aan de pagina.

   ![De optie Groep wijzigen op de pagina beheergroep](./media/detail_action_small.png)

1. Wanneer het menu wordt geopend, voert u de nieuwe naam in die u wilt weergeven.

   ![De naam van het deelvenster Groep wijzigen om de naam van beheergroep te wijzigen](./media/rename_context.png)

1. Selecteer **Opslaan**.

### <a name="change-the-name-in-powershell"></a>De naam wijzigen in PowerShell

Als u de weergavenaam wilt bijwerken, gebruikt **u Update-AzManagementGroup**. Als u bijvoorbeeld een beheergroep wilt wijzigen, wordt de naam van 'Contoso IT' in 'Contoso-groep' weergegeven, voert u de volgende opdracht uit:

```azurepowershell-interactive
Update-AzManagementGroup -GroupName 'ContosoIt' -DisplayName 'Contoso Group'
```

### <a name="change-the-name-in-azure-cli"></a>De naam wijzigen in Azure CLI

Gebruik voor Azure CLI de opdracht Bijwerken.

```azurecli-interactive
az account management-group update --name 'Contoso' --display-name 'Contoso Group'
```

## <a name="delete-a-management-group"></a>Een beheergroep verwijderen

Om een beheergroep te verwijderen, moet aan de volgende vereisten worden voldaan:

1. Er zijn geen onderliggende beheergroepen of abonnementen onder de beheergroep.

   - Als u een abonnements- of beheergroep naar een andere beheergroep wilt verplaatsen, ziet [U beheergroepen en abonnementen verplaatsen in de hiërarchie](#moving-management-groups-and-subscriptions).

1. U hebt schrijfmachtigingen nodig voor de beheergroep ('Eigenaar', 'Bijdrager' of 'Bijdrager van de beheergroep'). Als u wilt zien welke machtigingen u hebt, selecteert u de beheergroep en selecteert u **IAM**. Zie [Toegang en machtigingen beheren met RBAC voor](../../role-based-access-control/overview.md)meer informatie over RBAC-rollen.  

### <a name="delete-in-the-portal"></a>Verwijderen in de portal

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Selecteer **Alle services** > **beheergroepen**.

1. Selecteer de beheergroep die u wilt verwijderen.

1. Selecteer **details**.

1. Selecteren **Verwijderen**

    > [!TIP]
    > Als het pictogram is uitgeschakeld, geeft u de reden van de muiskiezer boven het pictogram.

   ![Groepsoptie verwijderen](./media/delete.png)

1. Er wordt een venster geopend waarin wordt bevestigd dat u de beheergroep wilt verwijderen.

   ![Venster groepsbevestiging verwijderen](./media/delete_confirm.png)

1. Selecteer **Ja**.

### <a name="delete-in-powershell"></a>Verwijderen in PowerShell

Gebruik de opdracht **Verwijderen-AzManagementGroup** in PowerShell om beheergroepen te verwijderen.

```azurepowershell-interactive
Remove-AzManagementGroup -GroupName 'Contoso'
```

### <a name="delete-in-azure-cli"></a>Verwijderen in de Azure CLI

Gebruik met Azure CLI de opdracht az-accountbeheergroep verwijderen.

```azurecli-interactive
az account management-group delete --name 'Contoso'
```

## <a name="view-management-groups"></a>Beheergroepen weergeven

U elke beheergroep bekijken waarop u een directe of overgeërfde RBAC-rol hebt.  

### <a name="view-in-the-portal"></a>Bekijk in de portal

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Selecteer **Alle services** > **beheergroepen**.

1. De pagina hiërarchie van de beheergroep wordt geladen. Op deze pagina u alle beheergroepen en abonnementen bekijken waartoe u toegang hebt. Als u de groepsnaam selecteert, wordt u een niveau in de hiërarchie omlaag. De navigatie werkt hetzelfde als een verkenner doet.

1. Als u de details van de beheergroep wilt zien, selecteert u de koppeling **(details)** naast de titel van de beheergroep. Als deze koppeling niet beschikbaar is, hebt u geen machtigingen om die beheergroep te bekijken.

   ![Hoofdformulier](./media/main.png)

### <a name="view-in-powershell"></a>Bekijk in PowerShell

U gebruikt de opdracht Get-AzManagementGroup om alle groepen op te halen.  Zie [Az.Resources-modules](/powershell/module/az.resources/Get-AzManagementGroup) voor de volledige lijst met beheergroep GET PowerShell-opdrachten.  

```azurepowershell-interactive
Get-AzManagementGroup
```

Gebruik de parameter GroepNaam voor de informatie van één beheergroep

```azurepowershell-interactive
Get-AzManagementGroup -GroupName 'Contoso'
```

Als u een specifieke beheergroep en alle niveaus van de hiërarchie eronder wilt retourneren, gebruikt u **parameters -Uitvouwen** en **opnieuw verbreken.**  

```azurepowershell-interactive
PS C:\> $response = Get-AzManagementGroup -GroupName TestGroupParent -Expand -Recurse
PS C:\> $response

Id                : /providers/Microsoft.Management/managementGroups/TestGroupParent
Type              : /providers/Microsoft.Management/managementGroups
Name              : TestGroupParent
TenantId          : 00000000-0000-0000-0000-000000000000
DisplayName       : TestGroupParent
UpdatedTime       : 2/1/2018 11:15:46 AM
UpdatedBy         : 00000000-0000-0000-0000-000000000000
ParentId          : /providers/Microsoft.Management/managementGroups/00000000-0000-0000-0000-000000000000
ParentName        : 00000000-0000-0000-0000-000000000000
ParentDisplayName : 00000000-0000-0000-0000-000000000000
Children          : {TestGroup1DisplayName, TestGroup2DisplayName}

PS C:\> $response.Children[0]

Type        : /managementGroup
Id          : /providers/Microsoft.Management/managementGroups/TestGroup1
Name        : TestGroup1
DisplayName : TestGroup1DisplayName
Children    : {TestRecurseChild}

PS C:\> $response.Children[0].Children[0]

Type        : /managementGroup
Id          : /providers/Microsoft.Management/managementGroups/TestRecurseChild
Name        : TestRecurseChild
DisplayName : TestRecurseChild
Children    :
```

### <a name="view-in-azure-cli"></a>Weergave in Azure CLI

U gebruikt de opdracht lijst om alle groepen op te halen.  

```azurecli-interactive
az account management-group list
```

Gebruik de opdracht Toon

```azurecli-interactive
az account management-group show --name 'Contoso'
```

Als u een specifieke beheergroep en alle niveaus van de hiërarchie eronder wilt retourneren, gebruikt u **parameters -Uitvouwen** en **opnieuw verbreken.**

```azurecli-interactive
az account management-group show --name 'Contoso' -e -r
```

## <a name="moving-management-groups-and-subscriptions"></a>Beheergroepen en abonnementen verplaatsen   

Een van de redenen om een beheergroep te maken is om abonnementen samen te bundelen. Alleen managementgroepen en abonnementen kunnen kinderen van een andere managementgroep worden gemaakt. Een abonnement dat naar een beheergroep wordt verplaatst, neemt alle gebruikerstoegang en -beleidsregels over van de bovenliggende beheergroep

Bij het verplaatsen van een beheergroep of abonnement om een kind van een andere beheergroep te zijn, moeten drie regels als waar worden beoordeeld.

Als u de verhuisactie doet, moet u het zelfde 

-  Managementgroep schrijven en Roltoewijzing schrijven machtigingen voor het onderliggende abonnement of beheergroep.
    - **Voorbeeld-eigenaar** van een ingebouwde rol
- Managementgroep schrijft toegang op de doelgroep management groep.
    - Voorbeeld van ingebouwde rollen: **eigenaar**, **bijdrager**, **managementteambijdrager**
- Beheergroep schrijft toegang op de bestaande bovenliggende beheergroep.
    - Voorbeeld van ingebouwde rollen: **eigenaar**, **bijdrager**, **managementteambijdrager**

**Uitzondering:** Als de doelgroep of de bestaande bovenliggende beheergroep de hoofdbeheergroep is, zijn de machtigingenvereisten niet van toepassing. Aangezien de hoofdbeheergroep de standaardbestemmingsplek is voor alle nieuwe beheergroepen en abonnementen, hebt u geen machtigingen nodig om een item te verplaatsen.

Als de rol Eigenaar van het abonnement is overgenomen van de huidige beheergroep, zijn uw verplaatsingsdoelen beperkt. U het abonnement alleen verplaatsen naar een andere beheergroep waar u de rol Eigenaar hebt. U het niet verplaatsen naar een beheergroep waar u een bijdrager levert omdat u het eigendom van het abonnement verliest. Als u rechtstreeks bent toegewezen aan de rol Eigenaar voor het abonnement (niet overgenomen van de beheergroep), u deze verplaatsen naar een beheergroep waar u een bijdrager levert. 

Als u wilt zien welke machtigingen u in de Azure-portal hebt, selecteert u de beheergroep en selecteert u **IAM**. Zie [Toegang en machtigingen beheren met RBAC voor](../../role-based-access-control/overview.md)meer informatie over RBAC-rollen.


## <a name="move-subscriptions"></a>Abonnementen verplaatsen 

#### <a name="add-an-existing-subscription-to-a-management-group-in-the-portal"></a>Een bestaand abonnement toevoegen aan een beheergroep in de portal

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Selecteer **Alle services** > **beheergroepen**.

1. Selecteer de beheergroep die u van plan bent de bovenliggende te zijn.

1. Selecteer boven aan de pagina **Abonnement toevoegen**.

1. Selecteer het abonnement in de lijst met de juiste id.

   ![Beschikbare abonnementen om toe te voegen aan een beheergroep](./media/add_context_sub.png)

1. Selecteer 'Opslaan'.

#### <a name="remove-a-subscription-from-a-management-group-in-the-portal"></a>Een abonnement verwijderen uit een beheergroep in de portal

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Selecteer **Alle services** > **beheergroepen**.

1. Selecteer de beheergroep die u van plan bent als de huidige bovenliggende groep.  

1. Selecteer de ellips aan het einde van de rij voor het abonnement in de lijst die u wilt verplaatsen.

   ![Optie Verplaatsen op een beheergroep](./media/move_small.png)

1. Selecteer **Verplaatsen**selecteren .

1. Selecteer in het menu dat wordt geopend de **bovenliggende beheergroep**.

   ![Deelvenster verplaatsen om bovenliggende groep te wijzigen](./media/move_small_context.png)

1. Selecteer **Opslaan**.

### <a name="move-subscriptions-in-powershell"></a>Abonnementen verplaatsen in PowerShell

Als u een abonnement in PowerShell wilt verplaatsen, gebruikt u de opdracht Nieuw-AzManagementGroupAbonnement.  

```azurepowershell-interactive
New-AzManagementGroupSubscription -GroupName 'Contoso' -SubscriptionId '12345678-1234-1234-1234-123456789012'
```

Als u de koppeling tussen en het abonnement en de beheergroep wilt verwijderen, gebruikt u de opdracht Verwijderen-AzManagementGroupAbonnement.

```azurepowershell-interactive
Remove-AzManagementGroupSubscription -GroupName 'Contoso' -SubscriptionId '12345678-1234-1234-1234-123456789012'
```

### <a name="move-subscriptions-in-azure-cli"></a>Abonnementen verplaatsen in Azure CLI

Als u een abonnement wilt verplaatsen in CLI, gebruikt u de opdracht Toevoegen.

```azurecli-interactive
az account management-group subscription add --name 'Contoso' --subscription '12345678-1234-1234-1234-123456789012'
```

Als u het abonnement uit de beheergroep wilt verwijderen, gebruikt u de opdracht Abonnement verwijderen.  

```azurecli-interactive
az account management-group subscription remove --name 'Contoso' --subscription '12345678-1234-1234-1234-123456789012'
```

## <a name="move-management-groups"></a>Beheergroepen verplaatsen 

### <a name="move-management-groups-in-the-portal"></a>Beheergroepen verplaatsen in de portal

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Selecteer **Alle services** > **beheergroepen**.

1. Selecteer de beheergroep die u van plan bent de bovenliggende te zijn.

1. Selecteer boven aan de pagina **Beheergroep toevoegen**.

1. Selecteer in het menu dat wordt geopend of u een nieuwe wilt of gebruik een bestaande beheergroep.

   - Als u nieuw selecteert, wordt een nieuwe beheergroep gemaakt.
   - Als u een bestaande bestaande groep selecteert, krijgt u een vervolgkeuzelijst van alle beheergroepen die u naar deze beheergroep verplaatsen.  

   ![Een beheergroep verplaatsen naar een nieuwe of bestaande groep](./media/add_context_MG.png)

1. Selecteer **Opslaan**.

### <a name="move-management-groups-in-powershell"></a>Beheergroepen verplaatsen in PowerShell

Gebruik de opdracht Update-AzManagementGroup in PowerShell om een beheergroep onder een andere groep te verplaatsen.

```azurepowershell-interactive
$parentGroup = Get-AzManagementGroup -GroupName ContosoIT
Update-AzManagementGroup -GroupName 'Contoso' -ParentId $parentGroup.id
```  

### <a name="move-management-groups-in-azure-cli"></a>Beheergroepen verplaatsen in Azure CLI

Gebruik de opdracht Bijwerken om een beheergroep te verplaatsen met Azure CLI.

```azurecli-interactive
az account management-group update --name 'Contoso' --parent ContosoIT
```

## <a name="audit-management-groups-using-activity-logs"></a>Beheergroepen controleren met behulp van activiteitenlogboeken

Beheergroepen worden ondersteund door het [Azure-activiteitenlogboek](../../azure-monitor/platform/platform-logs-overview.md). U alle gebeurtenissen opvragen die gebeuren met een beheergroep op dezelfde centrale locatie als andere Azure-resources.  Zo kunt u alle gewijzigde rol- of beleidstoewijzingen binnen een bepaalde beheergroep bekijken.

![Activiteitslogboeken met beheergroepen](media/al-mg.png)

Bij het uitvoeren van query's op beheergroepen buiten de Azure-portal, ziet het doelbereik voor beheergroepen er als volgt uit: **/ providers/Microsoft.Management/managementGroups/{yourMgID}**.

## <a name="referencing-management-groups-from-other-resource-providers"></a>Verwijzingen naar beheergroepen van andere resourceproviders

Wanneer u beheergroepen verwijst uit de acties van andere Resource Provider, gebruikt u het volgende pad als bereik. Dit pad wordt gebruikt bij het gebruik van PowerShell-, Azure CLI- en REST-API's.  

>"/providers/Microsoft.Management/managementGroups/{yourMgID}"

Een voorbeeld van het gebruik van dit pad is bij het toewijzen van een nieuwe roltoewijzing aan een beheergroep in PowerShell

```azurepowershell-interactive
New-AzRoleAssignment -Scope "/providers/Microsoft.Management/managementGroups/Contoso"
```

Hetzelfde bereikpad wordt gebruikt bij het ophalen van een beleidsdefinitie bij een beheergroep.

```http
GET https://management.azure.com/providers/Microsoft.Management/managementgroups/MyManagementGroup/providers/Microsoft.Authorization/policyDefinitions/ResourceNaming?api-version=2018-05-01
```

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over beheergroepen gaat u naar:

- [Beheergroepen maken om Azure-resources te ordenen](create.md)
- [Uw beheergroepen wijzigen, verwijderen of beheren](manage.md)
- [Beheergroepen bekijken via de module voor Azure PowerShell-resources](/powershell/module/az.resources#resources)
- [Beheergroepen bekijken via de REST-API](/rest/api/resources/managementgroups)
- [Beheergroepen bekijken via de Azure-CLI](/cli/azure/account/management-group)