---
title: Een Azure Data Lake Storage Gen2-opslagaccount maken | Microsoft Docs
description: Leer snel hoe u een nieuw opslag account maakt met toegang tot Data Lake Storage Gen2 met behulp van de Azure Portal, Azure PowerShell of de Azure CLI.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 1c9cdfa54494cd6d77edcd13110a79e5265e5032
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72817849"
---
# <a name="create-an-azure-data-lake-storage-gen2-storage-account"></a>Een Azure Data Lake Storage Gen2-opslagaccount maken

Azure Data Lake Storage Gen2 [ondersteunt een hiërarchische naam ruimte](data-lake-storage-introduction.md) die een op een native Directory gebaseerde container bevat die is afgestemd op het gebruik van de HADOOP DISTRIBUTED File System (HDFS). Toegang tot Data Lake Storage Gen2-gegevens uit HDFS is beschikbaar via het [ABFS-stuurprogramma](data-lake-storage-abfs-driver.md).

In dit artikel wordt beschreven hoe u een account maakt met behulp van de Azure Portal, Azure PowerShell of via de Azure CLI.

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint. 

|           | Vereisten |
|-----------|--------------|
|Portal     | Geen         |
|PowerShell | Voor dit artikel is de Power shell-module AZ. Storage versie **0,7** of hoger vereist. Voer de opdracht `Get-Module -ListAvailable Az.Storage` uit om uw huidige versie te vinden. Als u na het uitvoeren van deze opdracht geen resultaten ziet of als er een versie lager dan **0,7** wordt weer gegeven, moet u de Power shell-module bijwerken. Zie de sectie [Upgrade uitvoeren van uw powershell-module](#upgrade-your-powershell-module) van deze handleiding.
|CLI        | U kunt zich aanmelden bij Azure en Azure CLI-opdrachten uitvoeren op een van de volgende twee manieren: <ul><li>U kunt CLI-opdrachten uitvoeren vanuit Azure Portal, in Azure Cloud Shell </li><li>U kunt de CLI installeren en CLI-opdrachten lokaal uitvoeren</li></ul>|

Wanneer u werkt met de opdrachtregel, hebt u de mogelijkheid om de Azure Cloud-shell of de CLI lokaal te installeren.

### <a name="use-azure-cloud-shell"></a>Azure Cloud Shell gebruiken

Azure Cloud Shell is een gratis Bash-shell die u rechtstreeks in Azure Portal kunt uitvoeren. In deze shell is de Azure CLI vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. Klik op de knop **Cloud Shell** in het menu rechtsboven in Azure Portal:

[![Cloud Shell](./media/data-lake-storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

Met de knop start u een interactieve shell die u kunt gebruiken om de stappen in dit artikel uit te voeren:

[![Schermopname van het Cloud Shell-venster in de portal](./media/data-lake-storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>De CLI lokaal installeren

U kunt Azure CLI ook lokaal installeren en gebruiken. Voor dit artikel moet u de Azure CLI-versie 2.0.38 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli).

## <a name="create-a-storage-account-with-azure-data-lake-storage-gen2-enabled"></a>Een opslagaccount maken waarvoor Azure Data Lake Storage Gen2 is ingeschakeld

Een Azure-opslag account bevat al uw Azure Storage gegevens objecten: blobs, bestanden, wacht rijen, tabellen en schijven. Het opslag account biedt een unieke naam ruimte voor uw Azure Storage gegevens die overal ter wereld toegankelijk zijn via HTTP of HTTPS. Gegevens in uw Azure Storage-account zijn duurzaam en Maxi maal beschikbaar, veilig en zeer schaalbaar.

> [!NOTE]
> U moet nieuwe opslagaccounts maken van het type **StorageV2 (algemeen gebruik v2)** om te profiteren van de functies van Data Lake Storage Gen2.  

Zie [Overzicht van Azure-opslagaccount](../common/storage-account-overview.md) voor meer informatie over opslagaccounts.

## <a name="create-an-account-using-the-azure-portal"></a>Een account maken in Azure Portal

Meld u aan bij de [Azure-portal](https://portal.azure.com).

### <a name="create-a-storage-account"></a>Maak een opslagaccount

Elk opslagaccount moet behoren tot een Azure-resourcegroep. Een resourcegroep is een logische container voor het groeperen van uw Azure-services. Wanneer u een opslagaccount maakt, kunt u een nieuwe resourcegroep maken of een bestaande resourcegroep gebruiken. In dit artikel wordt beschreven hoe u een nieuwe resource groep maakt.

Als u een v2-opslagaccount voor algemeen gebruik wilt maken in de Azure Portal, volgt u deze stappen:

> [!NOTE]
> De hiërarchische naamruimte is momenteel beschikbaar in alle openbare regio's.

1. Kies het abonnement waarin u het opslagaccount wilt maken.
2. Klik in de Azure Portal op de knop **een resource maken** en kies vervolgens **opslag account**.
3. Selecteer **Nieuwe maken** onder het veld **Resourcegroep**. Voer een naam in voor de nieuwe resource groep.
   
   Een resourcegroep is een logische container voor het groeperen van uw Azure-services. Wanneer u een opslagaccount maakt, kunt u een nieuwe resourcegroep maken of een bestaande resourcegroep gebruiken.

4. Voer vervolgens een naam in voor het opslagaccount. De naam die u kiest, moet uniek zijn binnen Azure. Verder moet de naam 3 tot 24 tekens lang zijn en mag alleen cijfers en kleine letters bevatten.
5. Kies een locatie.
6. Zorg ervoor dat **StorageV2 (algemeen gebruik v2)** als geselecteerd wordt weer gegeven in de vervolg keuzelijst **account type** .
7. Wijzig desgewenst de waarden in elk van deze velden: **prestaties**, **replicatie**, **toegangs niveau**. Zie [Inleiding tot Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-introduction#introducing-the-azure-storage-services)voor meer informatie over deze opties.
8. Kies het tabblad **Geavanceerd** .
10. In de sectie **Data Lake Storage Gen2** stelt u **Hiërarchische naamruimte** in op **Ingeschakeld**.
11. Klik op **Beoordelen en maken** om het opslagaccount te maken.

Uw opslagaccount is nu gemaakt via de portal.

### <a name="clean-up-resources"></a>Resources opschonen

Ga als volgt te werk om een resourcegroep te verwijderen in Azure Portal:

1. Vouw het menu aan de linkerkant in Azure Portal uit om het menu met services te openen en kies **Resourcegroepen** om de lijst met resourcegroepen weer te geven.
2. Zoek de resourcegroep die u wilt verwijderen, en klik met de rechtermuisknop op de knop **Meer** ( **...** ) aan de rechterkant van de lijst.
3. Selecteer **Resourcegroep verwijderen** en bevestig dit.

## <a name="create-an-account-using-powershell"></a>Een account maken met PowerShell

Installeer eerst de meest recente versie van de [ PowerShellGet](https://docs.microsoft.com/powershell/gallery/installing-psget)-module.

Vervolgens voert u een upgrade uit van uw Power shell-module, meldt u zich aan bij uw Azure-abonnement, maakt u een resource groep en maakt u vervolgens een opslag account.

### <a name="upgrade-your-powershell-module"></a>De PowerShell-module upgraden

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Voor de interactie met Data Lake Storage Gen2 met behulp van PowerShell moet u moduleversie Az.Storage **0,7** of hoger installeren.

Begin door een PowerShell-sessie met verhoogde bevoegdheden te openen.

De module Az.Storage installeren

```powershell
Install-Module Az.Storage -Repository PSGallery -AllowClobber -Force
```

### <a name="sign-in-to-your-azure-subscription"></a>Meld u aan bij uw Azure-abonnement

Gebruik de opdracht `Login-AzAccount` en volg de instructies op het scherm om te verifiëren.

```powershell
Login-AzAccount
```

### <a name="create-a-resource-group"></a>Een resourcegroep maken

Gebruik de opdracht [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) om een nieuwe resourcegroep met PowerShell te maken: 

> [!NOTE]
> De hiërarchische naamruimte is momenteel beschikbaar in alle openbare regio's.

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hardcoding it repeatedly
$resourceGroup = "storage-quickstart-resource-group"
$location = "westus2"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

### <a name="create-a-general-purpose-v2-storage-account"></a>Een v2-opslagaccount voor algemeen gebruik maken

Gebruik de opdracht [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount) om een v2-opslagaccount voor algemeen gebruik met lokaal redundante opslag (LRS) te maken vanuit PowerShell:

```powershell
$location = "westus2"

New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name "storagequickstart" `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind StorageV2 `
  -EnableHierarchicalNamespace $True
```

### <a name="clean-up-resources"></a>Resources opschonen

Gebruik de opdracht [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) om de resourcegroep en alle bijbehorende resources te verwijderen, inclusief het nieuwe opslagaccount: 

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="create-an-account-using-azure-cli"></a>Een account maken met Azure CLI

Meld u aan bij de [Azure Portal](https://portal.azure.com)om Azure Cloud shell te starten.

Als u zich wilt aanmelden bij de lokale installatie van de CLI, voert u de aanmeldings opdracht uit:

```cli
az login
```

### <a name="add-the-cli-extension-for-azure-data-lake-gen-2"></a>De CLI-extensie voor Azure Data Lake Gen 2 toevoegen

Als u via CLI wilt communiceren met Data Lake Storage Gen2, moet u een extensie toevoegen aan de shell.

Dit doet u als volgt: voer met behulp van Cloud Shell of een lokale shell de volgende opdracht in: `az extension add --name storage-preview`

### <a name="create-a-resource-group"></a>Een resourcegroep maken

Gebruik de opdracht [az group create](/cli/azure/group) om een nieuwe resourcegroep te maken met Azure CLI.

```azurecli-interactive
az group create `
    --name storage-quickstart-resource-group `
    --location westus2
```

> [!NOTE]
> > De hiërarchische naamruimte is momenteel beschikbaar in alle openbare regio's.

### <a name="create-a-general-purpose-v2-storage-account"></a>Een v2-opslagaccount voor algemeen gebruik maken

Gebruik de opdracht [az storage account create](/cli/azure/storage/account) om een v2-opslagaccount voor algemeen gebruik met lokaal redundante opslag te maken vanuit de Azure CLI.

```azurecli-interactive
az storage account create `
    --name storagequickstart `
    --resource-group storage-quickstart-resource-group `
    --location westus2 `
    --sku Standard_LRS `
    --kind StorageV2 `
    --hierarchical-namespace true
```

### <a name="clean-up-resources"></a>Resources opschonen

Gebruik de opdracht [az group delete](/cli/azure/group) om de resourcegroep en alle bijbehorende resources te verwijderen, inclusief het nieuwe opslagaccount.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een opslag account gemaakt met Data Lake Storage Gen2 mogelijkheden. Zie het volgende onderwerp voor informatie over het uploaden en downloaden van blobs naar en uit uw opslagaccount.

* [AzCopy V10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
