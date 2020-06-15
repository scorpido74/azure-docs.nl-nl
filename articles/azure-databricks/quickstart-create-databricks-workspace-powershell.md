---
title: Quickstart - Een Azure Databricks-werkruimte maken met behulp van PowerShell
description: In deze quickstart wordt uitgelegd hoe u PowerShell kunt gebruiken om een Azure Databricks-werkruimte te maken.
services: azure-databricks
ms.service: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.workload: big-data
ms.topic: quickstart
ms.date: 06/02/2020
ms.custom: mvc
ms.openlocfilehash: 445832119a113f92dc1da51eb5b5e77dff176fa3
ms.sourcegitcommit: f57fa5f3ce40647eda93f8be4b0ab0726d479bca
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/07/2020
ms.locfileid: "84485686"
---
# <a name="quickstart-create-an-azure-databricks-workspace-using-powershell"></a>Quickstart: Een Azure Databricks-werkruimte maken met behulp van PowerShell

In deze quickstart wordt beschreven hoe u PowerShell kunt gebruiken om een Azure Databricks-werkruimte te maken. U kunt PowerShell gebruiken om Azure-resources interactief of in scripts te maken en te beheren.

## <a name="prerequisites"></a>Vereisten

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

Als u PowerShell lokaal wilt gebruiken, moet u voor dit artikel de AZ Powershell-module installeren en verbinding maken met uw Azure-account met behulp van de cmdlet [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount). Zie [Azure PowerShell installeren](/powershell/azure/install-az-ps) voor meer informatie over het installeren van de AZ PowerShell-module.

> [!IMPORTANT]
> Hoewel de PowerShell-module Az.Databricks in preview is, moet u deze afzonderlijk van de Az-module van PowerShell installeren met behulp van de volgende opdracht: `Install-Module -Name Az.Databricks -AllowPrerelease`.
> Zodra de PowerShell-module Az.Databricks algemeen beschikbaar is, wordt het onderdeel van toekomstige releases van Az PowerShell en is de module systeemeigen beschikbaar vanuit Azure Cloud Shell.

Als dit de eerste keer is dat u Azure Databricks gebruikt, moet u de resourceprovider van **Microsoft.Databricks** registreren.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Databricks
```

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Als u meerdere Azure-abonnementen hebt, kiest u het juiste abonnement waarin de resource moet worden gefactureerd. Selecteer een specifieke abonnements-id met behulp van de cmdlet [set-AzContext](/powershell/module/az.accounts/set-azcontext).

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een [Azure-resourcegroep](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) met de cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en groepsgewijs worden beheerd.

In het volgende voorbeeld wordt een resourcegroep met de naam **myresourcegroup** gemaakt in de regio **VS - west 2**.

```azurepowershell-interactive
New-AzResourceGroup -Name myresourcegroup -Location westus2
```

## <a name="create-an-azure-databricks-workspace"></a>Een Azure Databricks-werkruimte maken

In deze sectie gaat u een Azure Databricks-werkruimte maken met behulp van PowerShell.

```azurepowershell-interactive
New-AzDatabricksWorkspace -Name mydatabricksws -ResourceGroupName myresourcegroup -Location westus2 -ManagedResourceGroupName databricks-group -Sku standard
```

Geef de volgende waarden op:

|       **Eigenschap**       |                                                                                **Beschrijving**                                                                                 |
| ------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Naam                     | Geef een naam op voor uw Databricks-werkruimte.                                                                                                                                   |
| ResourceGroupName        | Geef een bestaande resourcegroepnaam op                                                                                                                                        |
| Locatie                 | Selecteer **VS - west 2**. Zie [Producten beschikbaar per regio](https://azure.microsoft.com/regions/services/) voor andere beschikbare regio's                                     |
| ManagedResourceGroupName | Geef aan of u een nieuwe beheerde resourcegroep wilt maken of een bestaande groep wilt gebruiken.                                                                                        |
| Sku                      | U kunt kiezen tussen **Standard**, **Premium** en **Trial**. Zie [Prijzen voor Databricks](https://azure.microsoft.com/pricing/details/databricks/) voor meer informatie over deze categorieën |

Het maken van de werkruimte duurt enkele minuten. Zodra dit proces is voltooid, wordt uw gebruikersaccount automatisch als een gebruiker met beheerdersrechten toegevoegd in de werkruimte.

Wanneer de implementatie van een werkruimte mislukt, wordt de werkruimte nog steeds gemaakt, maar krijgt deze de status Mislukt. Verwijder de mislukte werkruimte en maak een nieuwe werkruimte waarin de implementatiefouten zijn opgelost. Wanneer u de mislukte werkruimte verwijdert, worden de beheerde resourcegroep en de resources die wel zijn geïmplementeerd ook verwijderd.

## <a name="determine-the-provisioning-state-of-a-databricks-workspace"></a>De inrichtingsstatus van een Databricks-werkruimte bepalen

U kunt de cmdlet `Get-AzDatabricksWorkspace` gebruiken om te bepalen of de inrichting van een Databricks-werkruimte is gelukt.

```azurepowershell-interactive
Get-AzDatabricksWorkspace -Name mydatabricksws -ResourceGroupName myresourcegroup |
  Select-Object -Property Name, SkuName, Location, ProvisioningState
```

```Output
Name            SkuName   Location  ProvisioningState
----            -------   --------  -----------------
mydatabricksws  standard  westus2   Succeeded
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze resources uit deze quickstart niet voor een andere quickstart of zelfstudie nodig hebt, kunt u ze verwijderen door het volgende voorbeeld uit te voeren.

> [!CAUTION]
> In het volgende voorbeeld worden de opgegeven resourcegroep en alle resources erin verwijderd.
> Als resources buiten het bereik van deze quickstart in de opgegeven resourcegroep bestaan, worden ze ook verwijderd.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myresourcegroup
```

Als u alleen de server wilt verwijderen die u in deze quickstart hebt gemaakt zonder de resourcegroep te verwijderen, gebruikt u de cmdlet `Remove-AzDatabricksWorkspace`.

```azurepowershell-interactive
Remove-AzDatabricksWorkspace -Name mydatabricksws -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een Spark-cluster maken in Databricks](quickstart-create-databricks-workspace-portal.md#create-a-spark-cluster-in-databricks)
