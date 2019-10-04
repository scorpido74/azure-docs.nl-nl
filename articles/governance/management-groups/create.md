---
title: Beheer groepen maken om Azure-resources te organiseren-Azure governance
description: Meer informatie over het maken van Azure-beheer groepen voor het beheren van meerdere resources met behulp van de portal, Azure PowerShell en Azure CLI.
author: rthorn17
manager: rithorn
ms.service: governance
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/05/2019
ms.author: rithorn
ms.topic: conceptual
ms.openlocfilehash: 1b53d8a15e6ca1d4fc5a8ed6d58e526fadd12466
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/04/2019
ms.locfileid: "71949006"
---
# <a name="create-management-groups-for-resource-organization-and-management"></a>Beheer groepen maken voor resource organisatie en-beheer

Beheer groepen zijn containers die u helpen bij het beheren van de toegang, het beleid en de naleving van meerdere abonnementen. Maak deze containers om een effectieve en efficiënte hiërarchie te maken die kan worden gebruikt met [Azure Policy](../policy/overview.md) en [toegangs beheer op basis van rollen in azure](../../role-based-access-control/overview.md). Zie [uw resources organiseren met Azure-beheer groepen](overview.md)voor meer informatie over beheer groepen.

Het kan tot vijf tien minuten duren voordat de eerste beheer groep die in de Directory is gemaakt, is voltooid. Er zijn processen die de eerste keer worden uitgevoerd om de service voor beheer groepen in te stellen in azure voor uw Directory. U ontvangt een melding wanneer het proces is voltooid.

## <a name="create-a-management-group"></a>Een beheergroep maken

U kunt de beheer groep maken met behulp van de portal, Power shell of Azure CLI. Op dit moment kunt u geen Resource Manager-sjablonen gebruiken om beheer groepen te maken.

### <a name="create-in-portal"></a>Maken in Portal

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Selecteer **alle services** > **Management + governance**.

1. **Cost Management en facturering** selecteren

1. Selecteer op de pagina Cost Management + facturering-beheer groepen **beheergroepen**

1. Selecteer **+ beheer groep toevoegen**.

   ![Pagina voor het werken met beheer groepen](./media/main.png)

1. Vul het veld beheer groep-ID in.

   - De **beheer groep-ID** is de unieke id van de map die wordt gebruikt voor het verzenden van opdrachten in deze beheer groep. Deze id kan niet worden bewerkt nadat deze is gemaakt, omdat deze wordt gebruikt in het Azure-systeem om deze groep te identificeren. De [hoofd beheer groep](overview.md#root-management-group-for-each-directory) wordt automatisch gemaakt met een id die de Azure Active Directory-id is. Wijs voor alle andere beheer groepen een unieke ID toe.
   - Het veld weergave naam is de naam die wordt weer gegeven in de Azure Portal. Een afzonderlijke weergave naam is een optioneel veld bij het maken van de beheer groep en kan op elk gewenst moment worden gewijzigd.  

   ![Deel venster Opties voor het maken van een nieuwe beheer groep](./media/create_context_menu.png)  

1. Selecteer **Opslaan**.

### <a name="create-in-powershell"></a>Maken in Power shell

Voor Power shell gebruikt u de cmdlet [New-AzManagementGroup](/powershell/module/az.resources/new-azmanagementgroup) om een nieuwe beheer groep te maken.

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso'
```

De **GroupName** is een unieke id die wordt gemaakt. Deze ID wordt gebruikt door andere opdrachten om te verwijzen naar deze groep en kan later niet worden gewijzigd.

Als u wilt dat de beheer groep een andere naam weergeeft in de Azure Portal, voegt u de para meter **DisplayName** toe. Gebruik bijvoorbeeld de volgende cmdlet om een beheer groep te maken met de GroupName van Contoso en de weergave naam ' contoso Group ':

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso' -DisplayName 'Contoso Group'
```

In de voor gaande voor beelden wordt de nieuwe beheer groep gemaakt in de hoofd beheer groep. Als u een andere beheer groep als bovenliggende wilt opgeven, gebruikt u de para meter **ParentId** .

```azurepowershell-interactive
$parentGroup = Get-AzManagementGroup -GroupName Contoso
New-AzManagementGroup -GroupName 'ContosoSubGroup' -ParentId $parentGroup.id
```

### <a name="create-in-azure-cli"></a>Maken in azure CLI

Voor Azure CLI gebruikt u de opdracht [AZ Account Management-Group Create](/cli/azure/account/management-group?view=azure-cli-latest#az-account-management-group-create) om een nieuwe beheer groep te maken.

```azurecli-interactive
az account management-group create --name Contoso
```

De **naam** is een unieke id die wordt gemaakt. Deze ID wordt gebruikt door andere opdrachten om te verwijzen naar deze groep en kan later niet worden gewijzigd.

Als u wilt dat de beheer groep een andere naam weergeeft in de Azure Portal, voegt u de para meter voor de **weergave naam** toe. Gebruik bijvoorbeeld de volgende opdracht om een beheer groep te maken met de GroupName van Contoso en de weergave naam ' contoso-groep ':

```azurecli-interactive
az account management-group create --name Contoso --display-name 'Contoso Group'
```

In de voor gaande voor beelden wordt de nieuwe beheer groep gemaakt in de hoofd beheer groep. Als u een andere beheer groep als bovenliggende wilt opgeven, gebruikt u de **bovenliggende** para meter en geeft u de naam van de bovenliggende groep op.

```azurecli-interactive
az account management-group create --name ContosoSubGroup --parent Contoso
```

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over beheergroepen gaat u naar:

- [Beheergroepen maken om Azure-resources te ordenen](create.md)
- [Uw beheergroepen wijzigen, verwijderen of beheren](manage.md)
- [Beheergroepen bekijken via de module voor Azure PowerShell-resources](/powershell/module/az.resources#resources)
- [Beheergroepen bekijken via de REST-API](/rest/api/resources/managementgroups)
- [Beheergroepen bekijken via de Azure-CLI](/cli/azure/account/management-group)
