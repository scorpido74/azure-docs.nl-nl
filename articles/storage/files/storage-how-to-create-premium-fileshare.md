---
title: Een premium Azure-bestandsshare maken
description: In dit artikel leert u hoe u een premium Azure-bestandsshare maakt.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 05/05/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 7680a28b165dc252159cf95311439508d3c867e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79529104"
---
# <a name="how-to-create-an-premium-azure-file-share"></a>Een premium Azure-bestandsshare maken
Premium bestandsshares worden aangeboden op SSD-opslagmedia (solid-state disk) en zijn handig voor IO-intensieve workloads, waaronder hostingdatabases en high-performance computing (HPC). Premium bestandsaandelen worden gehost in een speciale opslagaccount, een Bestandsopslagaccount genoemd. Premium bestandsshares zijn ontworpen voor toepassingen op hoge prestaties en bedrijfsschaal, die consistente lage latentie, hoge IOPS en hoge doorvoeraandelen bieden.

In dit artikel ziet u hoe u dit nieuwe accounttype maakt met [Azure-portal,](https://portal.azure.com/)Azure PowerShell en Azure CLI.

## <a name="prerequisites"></a>Vereisten

Als u toegang wilt krijgen tot Azure-bronnen, waaronder premium Azure-bestandsshares, hebt u een Azure-abonnement nodig. Als u nog geen abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="create-a-premium-file-share-using-the-azure-portal"></a>Een premium bestandsshare maken met de Azure-portal

### <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij [Azure Portal](https://portal.azure.com/).

### <a name="create-a-filestorage-storage-account"></a>Een opslagaccount voor bestandsopslag maken

Nu bent u klaar om uw opslagaccount aan te maken.

Elk opslagaccount moet behoren tot een Azure-resourcegroep. Een resourcegroep is een logische container voor het groeperen van uw Azure-services. Wanneer u een opslagaccount maakt, kunt u een nieuwe resourcegroep maken of een bestaande resourcegroep gebruiken. In dit artikel ziet u hoe u een nieuwe resourcegroep maakt.

1. Selecteer **opslagaccounts** in het linkermenu in de Azure-portal.

    ![Azure portal hoofdpagina selecteren opslagaccount](media/storage-how-to-create-premium-fileshare/azure-portal-storage-accounts.png)

1. Kies in het venster **Opslagaccounts** dat wordt weergegeven de optie **Toevoegen**.
1. Selecteer het abonnement waarin u het opslagaccount wilt maken.
1. Selecteer **Nieuwe maken** onder het veld **Resourcegroep**. Voer een naam in voor de nieuwe resourcegroep, zoals in de volgende afbeelding wordt weergegeven.

1. Voer vervolgens een naam in voor het opslagaccount. De naam die u kiest, moet uniek zijn binnen Azure. Verder moet de naam 3 tot 24 tekens lang zijn en mag alleen cijfers en kleine letters bevatten.
1. Selecteer een locatie voor uw opslagaccount of gebruik de standaardlocatie.
1. Selecteer **Premium**voor **Prestaties** .
1. Selecteer **Accountsoort** en kies **FileStorage**.
1. Replicatie **ingesteld** op de standaardwaarde **van LRS (Localredundante opslag)**.

    ![Een opslagaccount maken voor een premium bestandsshare](media/storage-how-to-create-premium-fileshare/create-filestorage-account.png)

1. Selecteer **Beoordelen en maken** om uw opslagaccountinstellingen te bekijken en het account te maken.
1. Selecteer **Maken**.

Zodra uw opslagaccountbron is gemaakt, navigeert u naar de bron.

### <a name="create-a-premium-file-share"></a>Een Premium-bestandsshare maken

1. Schuif in het linkermenu voor het opslagaccount naar de sectie **Bestandsservice** en selecteer **Vervolgens Bestanden**.
1. Selecteer **Bestandsshare** om een premium bestandsshare te maken.
1. Voer een naam en een gewenst quotum in voor uw bestandsshare en selecteer **Vervolgens Maken**.

> [!NOTE]
> Ingerichte aandelengroottes worden gespecificeerd door het aandelenquotum, bestandsaandelen worden gefactureerd op de ingerichte grootte, verwijzen naar de [prijspagina](https://azure.microsoft.com/pricing/details/storage/files/) voor meer details.

   ![Een Premium-bestandsshare maken](media/storage-how-to-create-premium-fileshare/create-premium-file-share.png)

### <a name="clean-up-resources"></a>Resources opschonen

Als u de bronnen die in dit artikel zijn gemaakt wilt opruimen, u de brongroep gewoon verwijderen. Als u de resourcegroep verwijdert, wordt ook het bijbehorende opslagaccount en alle andere bronnen die aan de resourcegroep zijn gekoppeld, verwijderd.

## <a name="create-a-premium-file-share-using-powershell"></a>Een premium bestandsshare maken met PowerShell

### <a name="create-an-account-using-powershell"></a>Een account maken met PowerShell

Installeer eerst de meest recente versie van de [ PowerShellGet](/powershell/scripting/gallery/installing-psget)-module.

Upgrade vervolgens uw PowerShell-module, meld u aan bij uw Azure-abonnement, maakt een brongroep en maak vervolgens een opslagaccount.

### <a name="upgrade-your-powershell-module"></a>Uw PowerShell-module upgraden

Als u wilt communiceren met een premium bestandsshare van PowerShell, moet u een Az.Storage-module versie 1.4.0 of de nieuwste Az.Storage-module installeren.

Begin door een PowerShell-sessie met verhoogde bevoegdheden te openen.

Installeer de az.storage-module:

```powershell
Install-Module Az.Storage -Repository PSGallery -AllowClobber -Force
```

### <a name="sign-in-to-your-azure-subscription"></a>Aanmelden bij uw Azure-abonnement

Gebruik de opdracht `Connect-AzAccount` en volg de instructies op het scherm om te verifiëren.

```powershell
Connect-AzAccount
```

### <a name="create-a-resource-group"></a>Een resourcegroep maken

Gebruik de opdracht [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) om een nieuwe resourcegroep met PowerShell te maken:

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hardcoding it repeatedly
$resourceGroup = "storage-how-to-resource-group"
$location = "westus2"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

### <a name="create-a-filestorage-storage-account"></a>Een FileStorage-opslagaccount maken

Als u een FileStorage-opslagaccount wilt maken vanuit PowerShell, gebruikt u de opdracht [Nieuw-AzStorageAccount:](/powershell/module/az.storage/New-azStorageAccount)

```powershell
$storageAcct = New-AzStorageAccount -ResourceGroupName $resourceGroup -Name "fileshowto" -SkuName "Premium_LRS" -Location "westus2" -Kind "FileStorage"
```

### <a name="create-a-premium-file-share"></a>Een Premium-bestandsshare maken

Nu u een FileStorage-account hebt, u een premium bestandsshare maken. Gebruik de [cmdlet Nieuw-AzStorageShare](/powershell/module/az.storage/New-AzStorageShare) om er een te maken.

> [!NOTE]
> Ingerichte aandelengroottes worden gespecificeerd door het aandelenquotum, bestandsaandelen worden gefactureerd op de ingerichte grootte, verwijzen naar de [prijspagina](https://azure.microsoft.com/pricing/details/storage/files/) voor meer details.

```powershell
New-AzStorageShare `
   -Name myshare `
   -Context $storageAcct.Context
```

### <a name="clean-up-resources"></a>Resources opschonen

Gebruik de opdracht [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) om de resourcegroep en alle bijbehorende resources te verwijderen, inclusief het nieuwe opslagaccount: 

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="create-a-premium-file-share-using-azure-cli"></a>Een premium bestandsshare maken met Azure CLI

Als u Azure Cloud Shell wilt starten, meldt u zich aan bij de [Azure-portal.](https://portal.azure.com)

Als u wilt inloggen op uw lokale installatie van de CLI, zorg er dan eerst voor dat u de nieuwste versie hebt en voer vervolgens de aanmeldingsopdracht uit:

```azurecli
az login
```

### <a name="create-a-resource-group"></a>Een resourcegroep maken

Gebruik de opdracht [az group create](/cli/azure/group) om een nieuwe resourcegroep te maken met Azure CLI.

```azurecli-interactive
az group create `
    --name files-howto-resource-group `
    --location westus2
```

### <a name="create-a-filestorage-storage-account"></a>Een FileStorage-opslagaccount maken

Als u een FileStorage-opslagaccount wilt maken vanuit de Azure CLI, gebruikt u de opdracht [az-opslagaccount maken.](/cli/azure/storage/account)

```azurecli-interactive
az storage account create `
    --name fileshowto `
    --resource-group files-howto-resource-group `
    --location westus `
    --sku Premium_LRS `
    --kind FileStorage
```

### <a name="get-the-storage-account-key"></a>Opslagaccountsleutel opvragen

Opslagaccountsleutels bepalen de toegang tot bronnen in een opslagaccount, in dit artikel gebruiken we de sleutel om een premium bestandsshare te maken. Wanneer u een opslagaccount maakt, worden de sleutels automatisch gemaakt. U kunt de opslagaccountsleutels voor uw opslagaccount opvragen met de opdracht [az storage account keys list](/cli/azure/storage/account/keys):

```azurecli-interactive
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

### <a name="create-a-premium-file-share"></a>Een Premium-bestandsshare maken

Nu u een filestorage-account hebt, u een premium bestandsshare maken. Gebruik de opdracht [az-opslagdelen om er](/cli/azure/storage/share) een te maken.

> [!NOTE]
> Ingerichte aandelengroottes worden gespecificeerd door het aandelenquotum, bestandsaandelen worden gefactureerd op de ingerichte grootte, verwijzen naar de [prijspagina](https://azure.microsoft.com/pricing/details/storage/files/) voor meer details.

```azurecli-interactive
az storage share create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" 
```

### <a name="clean-up-resources"></a>Resources opschonen

Gebruik de opdracht [az group delete](/cli/azure/group) om de resourcegroep en alle bijbehorende resources te verwijderen, inclusief het nieuwe opslagaccount.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een premium bestandsshare gemaakt. Ga voor meer informatie over de prestaties die dit account biedt verder naar het gedeelte prestatielaag van de planningshandleiding.

> [!div class="nextstepaction"]
> [Lagen voor bestandsshare](storage-files-planning.md#storage-tiers)