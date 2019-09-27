---
title: Uw beheer groepen wijzigen, verwijderen of beheren-Azure governance
description: Meer informatie over het weer geven, onderhouden, bijwerken en verwijderen van uw beheer groeps hiërarchie.
author: rthorn17
ms.service: governance
ms.date: 05/22/2019
ms.author: rithorn
ms.topic: conceptual
ms.openlocfilehash: 28bfabd5c28fe833050551e7be690c0ea3d1e6a0
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338936"
---
# <a name="manage-your-resources-with-management-groups"></a>Uw resources beheren met beheer groepen

Als uw organisatie veel abonnementen heeft, moet u de toegang, beleidsregels en naleving voor deze abonnementen op een efficiënte manier kunnen beheren. Azure-beheergroepen bieden een scopeniveau boven abonnementen. U ordent abonnementen in containers, zogenaamde 'beheergroepen', en past uw governancevoorwaarden hierop toe. Alle abonnementen in een beheergroep nemen automatisch de voorwaarden over die op de beheergroep zijn toegepast.

Beheergroepen bieden u beheer van bedrijfskwaliteit op grote schaal, ongeacht de typen abonnementen die u hebt.  Zie [uw resources organiseren met Azure-beheer groepen](overview.md)voor meer informatie over beheer groepen.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="change-the-name-of-a-management-group"></a>De naam van een beheer groep wijzigen

U kunt de naam van de beheer groep wijzigen met behulp van de portal, Power shell of Azure CLI.

### <a name="change-the-name-in-the-portal"></a>De naam in de portal wijzigen

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Selecteer **alle services** > -**beheer groepen**.

1. Selecteer de beheer groep waarvan u de naam wilt wijzigen.

1. Selecteer **Details**.

1. Selecteer de optie **naam van groep** aan de bovenkant van de pagina.

   ![De optie groeps naam wijzigen op de pagina beheer groep](./media/detail_action_small.png)

1. Wanneer het menu wordt geopend, voert u de nieuwe naam in die u wilt weer geven.

   ![Naam van groeps deel venster wijzigen in naam van beheer groep](./media/rename_context.png)

1. Selecteer **Opslaan**.

### <a name="change-the-name-in-powershell"></a>De naam wijzigen in Power shell

Gebruik **Update-AzManagementGroup**om de weergave naam bij te werken. Als u bijvoorbeeld een weergave naam van een beheer groep wilt wijzigen van ' contoso IT ' in ' contoso Group ', voert u de volgende opdracht uit:

```azurepowershell-interactive
Update-AzManagementGroup -GroupName 'ContosoIt' -DisplayName 'Contoso Group'
```

### <a name="change-the-name-in-azure-cli"></a>De naam wijzigen in azure CLI

Gebruik de opdracht bijwerken voor Azure CLI.

```azurecli-interactive
az account management-group update --name 'Contoso' --display-name 'Contoso Group'
```

## <a name="delete-a-management-group"></a>Een beheer groep verwijderen

Als u een beheer groep wilt verwijderen, moet aan de volgende vereisten worden voldaan:

1. Er zijn geen onderliggende beheer groepen of-abonnementen in de beheer groep.

   - Zie abonnement naar een [andere beheer groep verplaatsen](#move-subscriptions-in-the-hierarchy)als u een abonnement uit een beheer groep wilt verplaatsen.

   - Zie [beheer groepen verplaatsen in de hiërarchie](#move-management-groups-in-the-hierarchy)als u een beheer groep naar een andere beheer groep wilt verplaatsen.

1. U hebt schrijf machtigingen voor de beheer groep (eigenaar, Inzender of Inzender voor beheer groepen). Als u wilt zien welke machtigingen u hebt, selecteert u de beheer groep en selecteert u vervolgens **iam**. Zie [toegang en machtigingen beheren met RBAC](../../role-based-access-control/overview.md)voor meer informatie over RBAC-rollen.  

### <a name="delete-in-the-portal"></a>Verwijderen in de portal

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Selecteer **alle services** > -**beheer groepen**.

1. Selecteer de beheer groep die u wilt verwijderen.

1. Selecteer **Details**.

1. Selecteer **verwijderen**

    > [!TIP]
    > Als het pictogram is uitgeschakeld, ziet u de reden van het aanwijzen van de muis kiezer op het pictogram.

   ![Groeps optie verwijderen](./media/delete.png)

1. Er is een venster waarin u wordt gevraagd of u de beheer groep wilt verwijderen.

   ![Bevestigings venster voor groep verwijderen](./media/delete_confirm.png)

1. Selecteer **Ja**.

### <a name="delete-in-powershell"></a>Verwijderen in Power shell

Gebruik de opdracht **Remove-AzManagementGroup** in Power shell om beheer groepen te verwijderen.

```azurepowershell-interactive
Remove-AzManagementGroup -GroupName 'Contoso'
```

### <a name="delete-in-azure-cli"></a>Verwijderen in de Azure CLI

Met Azure CLI, gebruikt u de opdracht AZ Account Management-Group Delete.

```azurecli-interactive
az account management-group delete --name 'Contoso'
```

## <a name="view-management-groups"></a>Beheer groepen weer geven

U kunt elke beheer groep weer geven waarop u een directe of overgenomen RBAC-rol hebt.  

### <a name="view-in-the-portal"></a>Weer geven in de portal

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Selecteer **alle services** > -**beheer groepen**.

1. De pagina beheer groeps hiërarchie wordt geladen. Op deze pagina vindt u een onderzoek van alle beheer groepen en abonnementen waartoe u toegang hebt. Als u de groeps naam selecteert, wordt een niveau in de hiërarchie omlaag genoteerd. De navigatie werkt hetzelfde als een Verkenner.

1. Als u de details van de beheer groep wilt weer geven, selecteert u de koppeling **(details)** naast de titel van de beheer groep. Als deze koppeling niet beschikbaar is, bent u niet gemachtigd om die beheer groep weer te geven.

   ![Algemeen](./media/main.png)

### <a name="view-in-powershell"></a>Weer geven in Power shell

U gebruikt de opdracht Get-AzManagementGroup voor het ophalen van alle groepen.  Zie [AZ. resources](/powershell/module/az.resources/Get-AzManagementGroup) -modules voor de volledige lijst van beheer groepen Get Power shell-opdrachten.  

```azurepowershell-interactive
Get-AzManagementGroup
```

Voor de informatie van één beheer groep gebruikt u de para meter-GroupName

```azurepowershell-interactive
Get-AzManagementGroup -GroupName 'Contoso'
```

Als u een specifieke beheer groep en alle niveaus van de onderliggende hiërarchie wilt retour neren, gebruikt u de para meters **-expand** en **-recursief** .  

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

### <a name="view-in-azure-cli"></a>Weer geven in azure CLI

U gebruikt de lijst opdracht om alle groepen op te halen.  

```azurecli-interactive
az account management-group list
```

Voor de informatie van één beheer groep gebruikt u de opdracht weer geven

```azurecli-interactive
az account management-group show --name 'Contoso'
```

Als u een specifieke beheer groep en alle niveaus van de onderliggende hiërarchie wilt retour neren, gebruikt u de para meters **-expand** en **-recursief** .

```azurecli-interactive
az account management-group show --name 'Contoso' -e -r
```

## <a name="move-subscriptions-in-the-hierarchy"></a>Abonnementen verplaatsen in de hiërarchie

Een van de redenen om een beheer groep te maken, is om abonnementen samen te bundelen. Alleen beheer groepen en abonnementen kunnen onderliggende items van een andere beheer groep worden gemaakt. Een abonnement dat naar een beheer groep verplaatst, neemt alle gebruikers toegang en-beleid over van de bovenliggende beheer groep.

Als u het abonnement wilt verplaatsen, moeten alle volgende RBAC-machtigingen waar zijn:

- De rol ' eigenaar ' van het onderliggende abonnement.
- Rol ' eigenaar ', ' Inzender ' of ' Inzender beheerder ' op de bovenliggende beheer groep van het doel.
- Rol ' eigenaar ', ' Inzender ' of ' Inzender beheerder ' op de bestaande bovenliggende beheer groep.

Als het doel of de bestaande bovenliggende beheer groep de hoofd beheer groep is, zijn de machtigingen vereisten niet van toepassing. Omdat de hoofd beheer groep de standaard overgangs plaats voor alle nieuwe beheer groepen en abonnementen is, hebt u geen machtigingen nodig om een item te verplaatsen.

Als de rol van eigenaar van het abonnement wordt overgenomen van de huidige beheer groep, zijn de verplaatsings doelen beperkt. U kunt het abonnement alleen verplaatsen naar een andere beheer groep waar u de rol eigenaar hebt. U kunt het niet verplaatsen naar een beheer groep waar u mede werker bent, omdat u de eigenaar van het abonnement kwijtraakt. Als u direct wordt toegewezen aan de rol van eigenaar van het abonnement (niet overgenomen van de beheer groep), kunt u deze verplaatsen naar een beheer groep waar u een bijdrager bent.

Als u wilt zien welke machtigingen u hebt in de Azure Portal, selecteert u de beheer groep en selecteert u vervolgens **iam**. Zie [toegang en machtigingen beheren met RBAC](../../role-based-access-control/overview.md)voor meer informatie over RBAC-rollen.

### <a name="move-subscriptions-in-the-portal"></a>Abonnementen verplaatsen in de portal

#### <a name="add-an-existing-subscription-to-a-management-group"></a>Een bestaand abonnement toevoegen aan een beheer groep

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Selecteer **alle services** > -**beheer groepen**.

1. Selecteer de beheer groep die u van plan bent het bovenliggende te zijn.

1. Selecteer aan de bovenkant van de pagina **abonnement toevoegen**.

1. Selecteer het abonnement in de lijst met de juiste ID.

   ![Beschik bare abonnementen om toe te voegen aan een beheer groep](./media/add_context_sub.png)

1. Selecteer opslaan.

#### <a name="remove-a-subscription-from-a-management-group"></a>Een abonnement verwijderen uit een beheer groep

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Selecteer **alle services** > -**beheer groepen**.

1. Selecteer de beheer groep die u wilt plannen die het huidige bovenliggende item is.  

1. Selecteer de ovaal aan het einde van de rij voor het abonnement in de lijst die u wilt verplaatsen.

   ![Optie verplaatsen in een beheer groep](./media/move_small.png)

1. Selecteer **verplaatsen**.

1. Selecteer in het menu dat wordt geopend de **bovenliggende beheer groep**.

   ![Deel venster verplaatsen om bovenliggende groep te wijzigen](./media/move_small_context.png)

1. Selecteer **Opslaan**.

### <a name="move-subscriptions-in-powershell"></a>Abonnementen verplaatsen in Power shell

Als u een abonnement in Power shell wilt verplaatsen, gebruikt u de opdracht New-AzManagementGroupSubscription.  

```azurepowershell-interactive
New-AzManagementGroupSubscription -GroupName 'Contoso' -SubscriptionId '12345678-1234-1234-1234-123456789012'
```

Als u de koppeling tussen en-abonnement en de beheer groep wilt verwijderen, gebruikt u de opdracht Remove-AzManagementGroupSubscription.

```azurepowershell-interactive
Remove-AzManagementGroupSubscription -GroupName 'Contoso' -SubscriptionId '12345678-1234-1234-1234-123456789012'
```

### <a name="move-subscriptions-in-azure-cli"></a>Abonnementen verplaatsen in azure CLI

Als u een abonnement in CLI wilt verplaatsen, gebruikt u de opdracht toevoegen.

```azurecli-interactive
az account management-group subscription add --name 'Contoso' --subscription '12345678-1234-1234-1234-123456789012'
```

Als u het abonnement wilt verwijderen uit de beheer groep, gebruikt u de opdracht voor het verwijderen van het abonnement.  

```azurecli-interactive
az account management-group subscription remove --name 'Contoso' --subscription '12345678-1234-1234-1234-123456789012'
```

## <a name="move-management-groups-in-the-hierarchy"></a>Beheer groepen verplaatsen in de hiërarchie  

Wanneer u een bovenliggende beheer groep verplaatst, wordt de hiërarchie onder die groep verplaatst. Zie [toegang tot de beheer groep](overview.md#management-group-access)voor de toegang die u nodig hebt om beheer groepen te verplaatsen.

### <a name="move-management-groups-in-the-portal"></a>Beheer groepen verplaatsen in de portal

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Selecteer **alle services** > -**beheer groepen**.

1. Selecteer de beheer groep die u van plan bent het bovenliggende te zijn.

1. Selecteer boven aan de pagina **beheer groep toevoegen**.

1. In het menu dat wordt geopend, selecteert u of u een bestaande beheer groep wilt gebruiken.

   - Als u nieuw selecteert, wordt er een nieuwe beheer groep gemaakt.
   - Als u een bestaand selecteert, wordt er een vervolg keuzelijst weer gegeven van alle beheer groepen die u naar deze beheer groep kunt verplaatsen.  

   ![Een beheer groep verplaatsen naar een nieuwe of bestaande groep](./media/add_context_MG.png)

1. Selecteer **Opslaan**.

### <a name="move-management-groups-in-powershell"></a>Beheer groepen verplaatsen in Power shell

Gebruik de opdracht update-AzManagementGroup in Power shell om een beheer groep te verplaatsen naar een andere groep.

```azurepowershell-interactive
$parentGroup = Get-AzManagementGroup -GroupName ContosoIT
Update-AzManagementGroup -GroupName 'Contoso' -ParentId $parentGroup.id
```  

### <a name="move-management-groups-in-azure-cli"></a>Beheer groepen verplaatsen in azure CLI

Gebruik de opdracht bijwerken om een beheer groep te verplaatsen met Azure CLI.

```azurecli-interactive
az account management-group update --name 'Contoso' --parent ContosoIT
```

## <a name="audit-management-groups-using-activity-logs"></a>Beheergroepen controleren met behulp van activiteitenlogboeken

Beheergroepen worden ondersteund door het [Azure-activiteitenlogboek](../../azure-monitor/platform/activity-logs-overview.md). U kunt een query uitvoeren op alle gebeurtenissen die plaatsvinden naar een beheer groep op dezelfde centrale locatie als andere Azure-resources.  Zo kunt u alle gewijzigde rol- of beleidstoewijzingen binnen een bepaalde beheergroep bekijken.

![Activiteiten logboeken met beheer groepen](media/al-mg.png)

Bij het uitvoeren van query's op beheergroepen buiten de Azure-portal, ziet het doelbereik voor beheergroepen er als volgt uit: **/ providers/Microsoft.Management/managementGroups/{yourMgID}** .

## <a name="referencing-management-groups-from-other-resource-providers"></a>Verwijzen naar beheer groepen van andere bron providers

Gebruik het volgende pad als bereik bij het verwijzen naar beheer groepen vanuit de acties van een andere bron provider. Dit pad wordt gebruikt bij het gebruik van Power shell, Azure CLI en REST Api's.  

>"/providers/Microsoft.Management/managementGroups/{yourMgID}"

Een voor beeld van het gebruik van dit pad is wanneer u een nieuwe roltoewijzing toewijst aan een beheer groep in Power shell

```azurepowershell-interactive
New-AzRoleAssignment -Scope "/providers/Microsoft.Management/managementGroups/Contoso"
```

Het pad naar de scope wordt gebruikt bij het ophalen van een beleids definitie in een beheer groep.

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