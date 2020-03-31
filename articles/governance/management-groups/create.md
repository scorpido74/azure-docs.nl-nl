---
title: Beheergroepen maken om resources te ordenen - Azure Governance
description: Meer informatie over het maken van Azure-beheergroepen om meerdere resources te beheren met behulp van de portal, Azure PowerShell en Azure CLI.
ms.date: 12/18/2019
ms.topic: conceptual
ms.openlocfilehash: d9bb2e82404c0188094298f40da3346ee132eec3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75436532"
---
# <a name="create-management-groups-for-resource-organization-and-management"></a>Beheergroepen maken voor het organiseren en beheren van resources

Beheergroepen zijn containers waarmee u toegang, beleid en naleving voor meerdere abonnementen beheren. Maak deze containers om een effectieve en efficiënte hiërarchie op te bouwen die kan worden gebruikt met [Azure Policy](../policy/overview.md) en Azure Role Based [Access Controls](../../role-based-access-control/overview.md). Zie [Uw resources ordenen met Azure-beheergroepen](overview.md)voor meer informatie over beheergroepen.

De eerste beheergroep die in de map is gemaakt, kan tot 15 minuten in beslag nemen. Er zijn processen die de eerste keer worden uitgevoerd om de beheergroepenservice binnen Azure voor uw directory in te stellen. U ontvangt een melding wanneer het proces is voltooid. Zie [de eerste installatie van beheergroepen](./overview.md#initial-setup-of-management-groups)voor meer informatie . 

## <a name="create-a-management-group"></a>Een beheergroep maken

Elke Azure AD-gebruiker in de tenant kan een beheergroep maken zonder dat de schrijfmachtiging voor de beheergroep aan die gebruiker is toegewezen.  Deze nieuwe beheergroep wordt een kind van de Root Management Group en de maker krijgt een "Eigenaar" rolopdracht. Beheergroep service maakt dit mogelijk, zodat roltoewijzingen niet nodig zijn op het basisniveau. Geen gebruikers hebben toegang tot de Root Management Group wanneer deze wordt gemaakt.  Om te voorkomen dat de Azure AD Global Administrators worden gevonden om beheergroepen te gaan gebruiken, staan we de creatie van de eerste beheergroepen op het hoofdniveau toe.      

U de beheergroep maken met behulp van de portal, PowerShell of Azure CLI. Momenteel u resourcebeheersjablonen niet gebruiken om beheergroepen te maken.

### <a name="create-in-portal"></a>Maken in portal

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Selecteer **Alle services** > **Beheer + governance**.

1. Selecteer **Kostenbeheer + Facturering**

1. Selecteer op de pagina Kostenbeheer + Facturering - Beheergroepen de optie **Beheergroepen**

1. Selecteer **+ Beheergroep toevoegen**.

   ![Pagina voor het werken met beheergroepen](./media/main.png)

1. Vul het veld beheergroep-id in.

   - De **managementgroep-id** is de unieke id van de map die wordt gebruikt om opdrachten in te dienen voor deze beheergroep. Deze id is niet bewerkbaar na het maken, omdat deze in het hele Azure-systeem wordt gebruikt om deze groep te identificeren. De [hoofdbeheergroep](overview.md#root-management-group-for-each-directory) wordt automatisch gemaakt met een id die de Azure Active Directory ID is. Wijs voor alle andere beheergroepen een unieke ID toe.
   - Het veld weergavenaam is de naam die wordt weergegeven in de Azure-portal. Een aparte weergavenaam is een optioneel veld bij het maken van de beheergroep en kan op elk gewenst moment worden gewijzigd.  

   ![Deelvenster Opties voor het maken van een nieuwe beheergroep](./media/create_context_menu.png)  

1. Selecteer **Opslaan**.

### <a name="create-in-powershell"></a>Maken in PowerShell

Gebruik voor PowerShell de cmdlet [New-AzManagementGroup](/powershell/module/az.resources/new-azmanagementgroup) om een nieuwe beheergroep te creëren.

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso'
```

De **GroupName** is een unieke id die wordt gemaakt. Deze ID wordt gebruikt door andere opdrachten om naar deze groep te verwijzen en kan later niet worden gewijzigd.

Als u wilt dat de beheergroep een andere naam binnen de Azure-portal weergeeft, voegt u de parameter **DisplayName** toe. Als u bijvoorbeeld een beheergroep wilt maken met de GroupName van Contoso en de weergavenaam 'Contoso-groep', gebruikt u de volgende cmdlet:

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso' -DisplayName 'Contoso Group'
```

In de voorgaande voorbeelden wordt de nieuwe beheergroep gemaakt onder de hoofdbeheergroep. Als u een andere beheergroep als bovenliggende groep wilt opgeven, gebruikt u de parameter **ParentId.**

```azurepowershell-interactive
$parentGroup = Get-AzManagementGroup -GroupName Contoso
New-AzManagementGroup -GroupName 'ContosoSubGroup' -ParentId $parentGroup.id
```

### <a name="create-in-azure-cli"></a>Maken in Azure CLI

Gebruik voor Azure CLI de opdracht [Beheer-groep AZ-accountbeheer](/cli/azure/account/management-group?view=azure-cli-latest#az-account-management-group-create) om een nieuwe beheergroep te maken.

```azurecli-interactive
az account management-group create --name Contoso
```

De **naam** is een unieke id die wordt gemaakt. Deze ID wordt gebruikt door andere opdrachten om naar deze groep te verwijzen en kan later niet worden gewijzigd.

Als u wilt dat de beheergroep een andere naam in de Azure-portal weergeeft, voegt u de **parameter display-name** toe. Als u bijvoorbeeld een beheergroep wilt maken met de GroupName van Contoso en de weergavenaam 'Contoso-groep', gebruikt u de volgende opdracht:

```azurecli-interactive
az account management-group create --name Contoso --display-name 'Contoso Group'
```

In de voorgaande voorbeelden wordt de nieuwe beheergroep gemaakt onder de hoofdbeheergroep. Als u een andere beheergroep als bovenliggende groep wilt opgeven, gebruikt u de **bovenliggende** parameter en geeft u de naam van de bovenliggende groep op.

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
