---
title: Een opslagaccount maken - Azure Storage
description: In dit artikel leert u hoe u een opslag account maakt met behulp van de Azure Portal, Azure PowerShell of de Azure CLI. Een Azure-opslagaccount biedt een unieke naamruimte in Microsoft Azure voor het opslaan en werken met de gegevensobjecten die u in Azure Storage maakt.
services: storage
author: tamram
ms.custom: mvc
ms.service: storage
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 338ba468fb1442488da78f09fc72a22186cd0a51
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/29/2019
ms.locfileid: "71673225"
---
# <a name="create-a-storage-account"></a>Create a storage account

Een Azure-opslag account bevat al uw Azure Storage gegevens objecten: blobs, bestanden, wacht rijen, tabellen en schijven. Het opslag account biedt een unieke naam ruimte voor uw Azure Storage gegevens die overal ter wereld toegankelijk zijn via HTTP of HTTPS. Gegevens in uw Azure Storage-account zijn duurzaam en Maxi maal beschikbaar, veilig en zeer schaalbaar.

In dit artikel leert u hoe u een opslag account maakt met behulp van de [Azure Portal](https://portal.azure.com/), [Azure POWERSHELL](https://docs.microsoft.com/powershell/azure/overview), [Azure cli](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)of een [Azure Resource Manager sjabloon](../../azure-resource-manager/resource-group-overview.md).  

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Geen.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Voor dit procedure-artikel is de Azure PowerShell-module AZ versie 0,7 of hoger vereist. Voer `Get-Module -ListAvailable Az` uit om uw huidige versie te vinden. Als u PowerShell wilt installeren of upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-Az-ps).

# <a name="azure-clitabazure-cli"></a>[Azure-CLI](#tab/azure-cli)

U kunt zich aanmelden bij Azure en Azure CLI-opdrachten uitvoeren op een van de volgende twee manieren:

- U kunt CLI-opdrachten uitvoeren vanuit het Azure Portal, in Azure Cloud Shell.
- U kunt de CLI installeren en CLI-opdrachten lokaal uitvoeren.

### <a name="use-azure-cloud-shell"></a>Azure Cloud Shell gebruiken

Azure Cloud Shell is een gratis Bash-shell die u rechtstreeks in Azure Portal kunt uitvoeren. De Azure CLI is vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. Klik op de knop **Cloud shell** in het menu rechtsboven in de Azure portal:

[![Cloud Shell](./media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

Met de knop start u een interactieve shell die u kunt gebruiken om de stappen uit te voeren die worden beschreven in dit artikel:

[![Schermopname van het Cloud Shell-venster in de portal](./media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>De CLI lokaal installeren

U kunt Azure CLI ook lokaal installeren en gebruiken. Voor dit procedure-artikel moet u de Azure CLI-versie 2.0.4 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli). 

# <a name="templatetabtemplate"></a>[Sjabloon](#tab/template)

Geen.

---

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Meld u aan bij [Azure Portal](https://portal.azure.com).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Aanmelden bij uw Azure-abonnement met de `Connect-AzAccount` opdracht en volg de aanwijzingen aanwijzingen om te verifiëren.

```powershell
Connect-AzAccount
```

# <a name="azure-clitabazure-cli"></a>[Azure-CLI](#tab/azure-cli)

Meld u aan bij de [Azure Portal](https://portal.azure.com)om Azure Cloud shell te starten.

Als u zich wilt aanmelden bij de lokale installatie van de CLI, voert u de opdracht [AZ login](/cli/azure/reference-index#az-login) :

```cli
az login
```

# <a name="templatetabtemplate"></a>[Sjabloon](#tab/template)

N/A

---

## <a name="create-a-storage-account"></a>Create a storage account

U kunt nu uw opslagaccount maken.

Elk opslagaccount moet behoren tot een Azure-resourcegroep. Een resourcegroep is een logische container voor het groeperen van uw Azure-services. Wanneer u een opslagaccount maakt, kunt u een nieuwe resourcegroep maken of een bestaande resourcegroep gebruiken. In dit artikel wordt beschreven hoe u een nieuwe resource groep maakt.

Een v2-opslagaccount **voor algemeen gebruik** biedt toegang tot alle services van Azure Storage: blobs, bestanden, wachtrijen, tabellen en schijven. De stappen die hier worden beschreven, maken een v2-opslag account voor algemeen gebruik, maar de stappen voor het maken van elk type opslag account zijn vergelijkbaar.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Maak eerst een nieuwe resourcegroep met PowerShell met behulp van de opdracht [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup):

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hard-coding it repeatedly
$resourceGroup = "storage-resource-group"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

Als u niet zeker weet welke regio u moet opgeven voor de parameter `-Location`, kunt u een lijst met ondersteunde regio's voor uw abonnement ophalen met de opdracht [Get-AzLocation](/powershell/module/az.resources/get-azlocation):

```powershell
Get-AzLocation | select Location
$location = "westus"
```

Maak vervolgens een voor algemeen gebruik v2-opslag account met geografisch redundante opslag met lees toegang (RA-GRS) met behulp van de opdracht [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount) . Houd er rekening mee dat de naam van uw opslag account uniek moet zijn in azure, dus Vervang de waarde van de tijdelijke aanduiding tussen vier Kante haken door uw eigen unieke waarde:

```powershell
New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name <account-name> `
  -Location $location `
  -SkuName Standard_RAGRS `
  -Kind StorageV2 
```

Als u een v2-opslag account voor algemeen gebruik met een andere replicatie optie wilt maken, vervangt u de gewenste waarde in de onderstaande tabel voor de para meter **SkuName** .

|Replicatie-optie  |De parameter SkuName  |
|---------|---------|
|Lokaal redundante opslag (LRS)     |Standard_LRS         |
|Zone-redundante opslag (ZRS)     |Standard_ZRS         |
|Geografisch redundante opslag (GRS)     |Standard_GRS         |
|Geografisch redundante opslag met leestoegang (GRS)     |Standard_RAGRS         |
|Geo-zone-redundante opslag (GZRS) (preview)    |Standard_GZRS         |
|Geo-zone-redundante opslag met lees toegang (RA-GZRS) (preview)    |Standard_RAGZRS         |

# <a name="azure-clitabazure-cli"></a>[Azure-CLI](#tab/azure-cli)

Gebruik eerst de opdracht [az group create](/cli/azure/group#az_group_create) om een nieuwe resourcegroep te maken met Azure CLI.

```azurecli-interactive
az group create \
    --name storage-resource-group \
    --location westus
```

Als u niet zeker weet welke regio u moet opgeven voor de parameter `--location`, kunt u een lijst met ondersteunde regio's voor uw abonnement ophalen met de opdracht [az account list-locations](/cli/azure/account#az_account_list).

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

Maak vervolgens een voor algemeen gebruik v2-opslag account met geografisch redundante opslag met lees toegang met behulp van de opdracht [AZ Storage account create](/cli/azure/storage/account#az_storage_account_create) . Houd er rekening mee dat de naam van uw opslag account uniek moet zijn in azure, dus Vervang de waarde van de tijdelijke aanduiding tussen vier Kante haken door uw eigen unieke waarde:

```azurecli-interactive
az storage account create \
    --name <account-name> \
    --resource-group storage-resource-group \
    --location westus \
    --sku Standard_RAGRS \
    --kind StorageV2
```

Als u een v2-opslag account voor algemeen gebruik met een andere replicatie optie wilt maken, vervangt u de gewenste waarde in de onderstaande tabel voor de **SKU** -para meter.

|Replicatie-optie  |sku-parameter  |
|---------|---------|
|Lokaal redundante opslag (LRS)     |Standard_LRS         |
|Zone-redundante opslag (ZRS)     |Standard_ZRS         |
|Geografisch redundante opslag (GRS)     |Standard_GRS         |
|Geografisch redundante opslag met leestoegang (GRS)     |Standard_RAGRS         |
|Geo-zone-redundante opslag (GZRS) (preview)    |Standard_GZRS         |
|Geo-zone-redundante opslag met lees toegang (RA-GZRS) (preview)    |Standard_RAGZRS         |

# <a name="templatetabtemplate"></a>[Sjabloon](#tab/template)

U kunt Azure Power shell of Azure CLI gebruiken voor het implementeren van een resource manager-sjabloon voor het maken van een opslag account. De sjabloon die in dit artikel wordt gebruikt, is van [Azure Resource Manager Quick](https://azure.microsoft.com/resources/templates/101-storage-account-create/)start-sjablonen. Als u de scripts wilt uitvoeren, selecteert u **proberen** om de Azure Cloud shell te openen. Als u het script wilt plakken, klikt u met de rechter muisknop op de shell en selecteert u vervolgens **Plakken**.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group create --name $resourceGroupName --location "$location" &&
az group deployment create --resource-group $resourceGroupName --template-file "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

Zie voor meer informatie over het maken van sjablonen:

- [Azure Resource Manager documentatie](/azure/azure-resource-manager/).
- [Verwijzing naar opslag account sjabloon](/azure/templates/microsoft.storage/allversions).
- [Extra opslag account sjabloon voorbeelden](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Storage).

---

Zie [Storage-replicatieopties](storage-redundancy.md) voor meer informatie over beschikbare replicatieopties.

## <a name="clean-up-resources"></a>Resources opschonen

Als u de resources wilt opschonen die zijn gemaakt in dit artikel, kunt u de resource groep verwijderen. Als u de resourcegroep verwijdert, worden ook het bijbehorende opslagaccount en eventuele andere resources die zijn gekoppeld aan de resourcegroep, verwijderd.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Ga als volgt te werk om een resourcegroep te verwijderen in Azure Portal:

1. Vouw het menu aan de linkerkant in Azure Portal uit om het menu met services te openen en kies **Resourcegroepen** om de lijst met resourcegroepen weer te geven.
2. Zoek de resourcegroep die u wilt verwijderen, en klik met de rechtermuisknop op de knop **Meer** ( **...** ) aan de rechterkant van de lijst.
3. Selecteer **Resourcegroep verwijderen** en bevestig dit.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Gebruik de opdracht [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) om de resourcegroep en alle bijbehorende resources te verwijderen, inclusief het nieuwe opslagaccount:

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

# <a name="azure-clitabazure-cli"></a>[Azure-CLI](#tab/azure-cli)

Gebruik de opdracht [az group delete](/cli/azure/group#az_group_delete) om de resourcegroep en alle bijbehorende resources te verwijderen, inclusief het nieuwe opslagaccount.

```azurecli-interactive
az group delete --name storage-resource-group
```

# <a name="templatetabtemplate"></a>[Sjabloon](#tab/template)

Als u de resource groep en de bijbehorende resources wilt verwijderen, met inbegrip van het nieuwe opslag account, gebruikt u Azure PowerShell of Azure CLI.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
```

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

---

## <a name="next-steps"></a>Volgende stappen

In dit procedure-artikel hebt u een standaard v2-opslag account voor algemeen gebruik gemaakt. Ga door naar een van de Snelstartgids voor Blob-opslag voor meer informatie over het uploaden en downloaden van blobs naar en van uw opslag account.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

> [!div class="nextstepaction"]
> [Werken met blobs met behulp van de Azure-portal](../blobs/storage-quickstart-blobs-portal.md)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

> [!div class="nextstepaction"]
> [Werken met blobs met behulp van PowerShell](../blobs/storage-quickstart-blobs-powershell.md)

# <a name="azure-clitabazure-cli"></a>[Azure-CLI](#tab/azure-cli)

> [!div class="nextstepaction"]
> [Werken met blobs met behulp van Azure CLI](../blobs/storage-quickstart-blobs-cli.md)

# <a name="templatetabtemplate"></a>[Sjabloon](#tab/template)

> [!div class="nextstepaction"]
> [Werken met blobs met behulp van de Azure-portal](../blobs/storage-quickstart-blobs-portal.md)

---
