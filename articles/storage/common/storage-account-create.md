---
title: Een opslagaccount maken
titleSuffix: Azure Storage
description: Meer informatie over het maken van een opslagaccount met de Azure-portal, Azure PowerShell of azure CLI. Een Azure-opslagaccount biedt een unieke naamruimte in Microsoft Azure om uw gegevens op te slaan en te openen.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/07/2020
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: c2d1e8b4975be0657983192df00cc434da00a6f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255403"
---
# <a name="create-an-azure-storage-account"></a>Een Azure Storage-account maken

Een Azure-opslagaccount bevat al uw Azure Storage-gegevensobjecten: blobs, bestanden, wachtrijen, tabellen en schijven. Het opslagaccount biedt een unieke naamruimte voor uw Azure Storage-gegevens die overal ter wereld toegankelijk is via HTTP of HTTPS. Gegevens in uw Azure-opslagaccount zijn duurzaam en zeer beschikbaar, veilig en enorm schaalbaar.

In dit artikel leert u een opslagaccount maken met de [Azure-portal](https://portal.azure.com/), [Azure PowerShell,](https://docs.microsoft.com/powershell/azure/overview) [Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)of een Azure [Resource Manager-sjabloon](../../azure-resource-manager/management/overview.md).  

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Geen.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Als u een Azure-opslagaccount wilt maken met PowerShell, controleert u of u Azure PowerShell-module Az-versie 0.7 of hoger hebt geïnstalleerd. Zie [De Azure PowerShell Az-module introduceren](/powershell/azure/new-azureps-module-az)voor meer informatie.

Voer de volgende opdracht uit om uw huidige versie te vinden:

```powershell
Get-InstalledModule -Name "Az"
```

Zie [Azure PowerShell-module installeren](/powershell/azure/install-Az-ps)om Azure PowerShell te installeren of te upgraden.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

U zich aanmelden bij Azure en Azure CLI-opdrachten op twee manieren uitvoeren:

- U CLI-opdrachten uitvoeren vanuit de Azure-portal in Azure Cloud Shell.
- U de CLI-opdrachten lokaal installeren en uitvoeren.

### <a name="use-azure-cloud-shell"></a>Azure Cloud Shell gebruiken

Azure Cloud Shell is een gratis Bash-shell die u rechtstreeks in Azure Portal kunt uitvoeren. De Azure CLI is vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. Klik op de knop **Cloud Shell** in het menu in het rechterbovengedeelte van de Azure-portal:

[![Cloud Shell](./media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

De knop lanceert een interactieve shell die u gebruiken om de stappen uit te voeren die in dit artikel worden beschreven:

[![Schermafbeelding van het venster Cloud Shell in de portal](./media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>De CLI lokaal installeren

U kunt Azure CLI ook lokaal installeren en gebruiken. Dit how-to-artikel vereist dat u de Azure CLI-versie 2.0.4 of hoger uitvoert. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli). 

# <a name="template"></a>[Sjabloon](#tab/template)

Geen.

---

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

# <a name="portal"></a>[Portal](#tab/azure-portal)

Meld u aan bij [Azure Portal](https://portal.azure.com).

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Meld u met de `Connect-AzAccount` opdracht aan bij uw Azure-abonnement en volg de aanwijzingen op het scherm om te verifiëren.

```powershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u Azure Cloud Shell wilt starten, meldt u zich aan bij de [Azure-portal.](https://portal.azure.com)

Voer de opdracht [AZ-aanmelding](/cli/azure/reference-index#az-login) uit om in te loggen op uw lokale installatie van de CLI:

```azurecli-interactive
az login
```

# <a name="template"></a>[Sjabloon](#tab/template)

N.v.t.

---

## <a name="create-a-storage-account"></a>Een opslagaccount maken

Nu bent u klaar om een opslagaccount aan te maken.

Elk opslagaccount moet behoren tot een Azure-resourcegroep. Een resourcegroep is een logische container voor het groeperen van uw Azure-services. Wanneer u een opslagaccount maakt, kunt u een nieuwe resourcegroep maken of een bestaande resourcegroep gebruiken. In dit artikel ziet u hoe u een nieuwe resourcegroep maakt.

Een v2-opslagaccount **voor algemeen gebruik** biedt toegang tot alle services van Azure Storage: blobs, bestanden, wachtrijen, tabellen en schijven. De stappen die hier worden beschreven, maken een v2-opslagaccount voor algemene doeleinden, maar de stappen om elk type opslagaccount te maken zijn vergelijkbaar.

# <a name="portal"></a>[Portal](#tab/azure-portal)

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Maak eerst een nieuwe resourcegroep met PowerShell met behulp van de opdracht [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup):

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hard-coding it repeatedly
$resourceGroup = "storage-resource-group"
$location = "westus"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

Als u niet zeker weet welke regio u moet opgeven voor de parameter `-Location`, kunt u een lijst met ondersteunde regio's voor uw abonnement ophalen met de opdracht [Get-AzLocation](/powershell/module/az.resources/get-azlocation):

```powershell
Get-AzLocation | select Location
```

Maak vervolgens een v2-opslagaccount voor algemene doeleinden met georedundante opslag (READ-Access) met behulp van de opdracht [Nieuw-AzStorageAccount.](/powershell/module/az.storage/New-azStorageAccount) Houd er rekening mee dat de naam van uw opslagaccount uniek moet zijn in Azure, dus vervang de tijdelijke aanduidingswaarde tussen haakjes door uw eigen unieke waarde:

```powershell
New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name <account-name> `
  -Location $location `
  -SkuName Standard_RAGRS `
  -Kind StorageV2
```

> [!IMPORTANT]
> Als u [Azure Data Lake](https://azure.microsoft.com/services/storage/data-lake-storage/)Storage `-EnableHierarchicalNamespace $True` wilt gebruiken, neemt u dit op in deze lijst met parameters.

Als u een v2-opslagaccount voor algemene doeleinden wilt maken met een andere replicatieoptie, vervangt u de gewenste waarde in de onderstaande tabel voor de parameter **SkuName.**

|Replicatie-optie  |De parameter SkuName  |
|---------|---------|
|Lokaal redundante opslag (LRS)     |Standard_LRS         |
|Zone-redundante opslag (ZRS)     |Standard_ZRS         |
|Geografisch redundante opslag (GRS)     |Standard_GRS         |
|Geografisch redundante opslag met leestoegang (GRS)     |Standard_RAGRS         |
|Geo-zoneredundante opslag (GZRS) (voorbeeld)    |Standard_GZRS         |
|Leestoegang geozoneredundante opslag (RA-GZRS) (voorbeeld)    |Standard_RAGZRS         |

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

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

Maak vervolgens een v2-opslagaccount voor algemene doeleinden met georedundante opslag met leestoegang met behulp van de opdracht [AZ-opslagaccount](/cli/azure/storage/account#az_storage_account_create) maken. Houd er rekening mee dat de naam van uw opslagaccount uniek moet zijn in Azure, dus vervang de tijdelijke aanduidingswaarde tussen haakjes door uw eigen unieke waarde:

```azurecli-interactive
az storage account create \
    --name <account-name> \
    --resource-group storage-resource-group \
    --location westus \
    --sku Standard_RAGRS \
    --kind StorageV2
```

> [!IMPORTANT]
> Als u [Azure Data Lake](https://azure.microsoft.com/services/storage/data-lake-storage/)Storage `--enable-hierarchical-namespace true` wilt gebruiken, neemt u dit op in deze lijst met parameters. 

Als u een v2-opslagaccount voor algemene doeleinden wilt maken met een andere replicatieoptie, vervangt u de gewenste waarde in de onderstaande tabel voor de **parameter sku.**

|Replicatie-optie  |sku-parameter  |
|---------|---------|
|Lokaal redundante opslag (LRS)     |Standard_LRS         |
|Zone-redundante opslag (ZRS)     |Standard_ZRS         |
|Geografisch redundante opslag (GRS)     |Standard_GRS         |
|Geografisch redundante opslag met leestoegang (GRS)     |Standard_RAGRS         |
|Geo-zoneredundante opslag (GZRS) (voorbeeld)    |Standard_GZRS         |
|Leestoegang geozoneredundante opslag (RA-GZRS) (voorbeeld)    |Standard_RAGZRS         |

# <a name="template"></a>[Sjabloon](#tab/template)

U Azure Powershell of Azure CLI gebruiken om een Resource Manager-sjabloon te implementeren om een opslagaccount te maken. De sjabloon die in dit handleidingartikel wordt gebruikt, is afkomstig van [snelstartsjablonen](https://azure.microsoft.com/resources/templates/101-storage-account-create/)van Azure Resource Manager . Als u de scripts wilt uitvoeren, selecteert **u Proberen** om de Azure Cloud-shell te openen. Als u het script wilt plakken, klikt u met de rechtermuisknop op de shell en selecteert u **Plakken**.

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

> [!NOTE]
> Deze sjabloon dient alleen als voorbeeld. Er zijn veel instellingen voor opslagaccount die niet zijn geconfigureerd als onderdeel van deze sjabloon. Als u bijvoorbeeld Azure [Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/)wilt gebruiken, wijzigt `isHnsEnabledad` u `StorageAccountPropertiesCreateParameters` deze `true`sjabloon door de eigenschap van het object in te stellen op . 

Zie voor meer informatie over het wijzigen van deze sjabloon of het maken van nieuwe sjabloon:

- [Azure Resource Manager-documentatie](/azure/azure-resource-manager/).
- [Verwijzing naar de sjabloon voor opslagaccount](/azure/templates/microsoft.storage/allversions).
- [Voorbeelden van extra opslagaccountsjablonen](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Storage).

---

Zie [Storage-replicatieopties](storage-redundancy.md) voor meer informatie over beschikbare replicatieopties.

## <a name="delete-a-storage-account"></a>Een opslagaccount verwijderen

Als u een opslagaccount verwijdert, wordt het hele account verwijderd, inclusief alle gegevens in het account, en kan het niet ongedaan worden gemaakt.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Navigeer naar het opslagaccount in de [Azure-portal.](https://portal.azure.com)
1. Klik **op Verwijderen**.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Als u het opslagaccount wilt verwijderen, gebruikt u de opdracht [Verwijderen-AzStorageAccount:](/powershell/module/az.storage/remove-azstorageaccount)

```powershell
Remove-AzStorageAccount -Name <storage-account> -ResourceGroupName <resource-group>
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u het opslagaccount wilt verwijderen, gebruikt u de opdracht [voor het verwijderen van het AZ-opslagaccount:](/cli/azure/storage/account#az-storage-account-delete)

```azurecli-interactive
az storage account delete --name <storage-account> --resource-group <resource-group>
```

# <a name="template"></a>[Sjabloon](#tab/template)

Als u het opslagaccount wilt verwijderen, gebruikt u Azure PowerShell of Azure CLI.

```azurepowershell-interactive
$storageResourceGroupName = Read-Host -Prompt "Enter the resource group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"
Remove-AzStorageAccount -Name $storageAccountName -ResourceGroupName $storageResourceGroupName
```

```azurecli-interactive
echo "Enter the resource group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az storage account delete --name storageAccountName --resource-group resourceGroupName
```

---

U ook de brongroep verwijderen, die het opslagaccount en andere bronnen in die brongroep verwijdert. Zie [Brongroep en resources verwijderen](../../azure-resource-manager/management/delete-resource-group.md)voor meer informatie over het verwijderen van een resourcegroep.

> [!WARNING]
> Het is niet mogelijk om een verwijderd opslagaccount te herstellen of om inhoud terug te halen die het account vóór verwijdering bevatte. Zorg ervoor dat u een back-up maakt van alles dat u wilt opslaan, voordat u het account verwijdert. Dit geldt ook voor alle resources in het account: als u blobs, tabellen, wachtrijen of bestanden verwijdert, worden deze permanent verwijderd.
>
> Als u probeert een opslagaccount te verwijderen dat is gekoppeld aan een virtuele machine van Azure, ziet u mogelijk een foutbericht dat het account nog in gebruik is. Zie [Fouten oplossen wanneer u opslagaccounts verwijdert](../common/storage-resource-manager-cannot-delete-storage-account-container-vhd.md)voor hulp bij het oplossen van deze fout.

## <a name="next-steps"></a>Volgende stappen

In dit artikel voor how-to hebt u een v2-standaardopslagaccount voor algemene doeleinden gemaakt. Ga door naar een van de snel gestarte Blob-opslagvoor meer informatie over het uploaden en downloaden van blobs van en naar uw opslagaccount.

# <a name="portal"></a>[Portal](#tab/azure-portal)

> [!div class="nextstepaction"]
> [Werken met blobs met behulp van de Azure-portal](../blobs/storage-quickstart-blobs-portal.md)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

> [!div class="nextstepaction"]
> [Werken met blobs met behulp van PowerShell](../blobs/storage-quickstart-blobs-powershell.md)

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

> [!div class="nextstepaction"]
> [Werken met blobs met behulp van Azure CLI](../blobs/storage-quickstart-blobs-cli.md)

# <a name="template"></a>[Sjabloon](#tab/template)

> [!div class="nextstepaction"]
> [Werken met blobs met behulp van de Azure-portal](../blobs/storage-quickstart-blobs-portal.md)

---
