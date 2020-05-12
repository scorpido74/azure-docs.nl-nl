---
title: Een Block Blob Storage-account maken-Azure Storage | Microsoft Docs
description: Laat zien hoe u een Azure BlockBlobStorage-account met Premium-prestatie kenmerken maakt.
author: tamram
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 05/10/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 4767c0310783e7e2cc51b4caa7d6e6a052d0a05a
ms.sourcegitcommit: 801a551e047e933e5e844ea4e735d044d170d99a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/11/2020
ms.locfileid: "83007306"
---
# <a name="create-a-blockblobstorage-account"></a>Een BlockBlobStorage-account maken

Met het soort BlockBlobStorage-account kunt u blok-blobs maken met Premium-prestatie kenmerken. Dit type opslag account is geoptimaliseerd voor workloads met hoge transactie tarieven of voor zeer snelle toegangs tijden. In dit artikel wordt beschreven hoe u een BlockBlobStorage-account maakt met behulp van de Azure Portal, de Azure CLI of Azure PowerShell.

> [!NOTE]
> De functie voor hiërarchische naam ruimte in een blok-Blob-opslag account is in open bare preview en is beschikbaar in de Verenigde Staten Oost, VS Oost 2, VS centraal, VS Zuid-Centraal, VS West 2, UK-zuid, Canada-centraal en Australië-oost regio's. Zie [Blob Storage-functies die beschikbaar zijn in azure data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md) en [bekende problemen](data-lake-storage-known-issues.md)als u beperkingen wilt bekijken. Zie [dit formulier](https://aka.ms/adlspremiumonboard)als u zich wilt inschrijven voor de preview-versie.

Zie [overzicht van Azure-opslag accounts](https://docs.microsoft.com/azure/storage/common/storage-account-overview)voor meer informatie over BlockBlobStorage-accounts.

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Geen.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Voor dit procedure-artikel is de Azure PowerShell AZ versie 1.2.0 of hoger vereist. Voer `Get-Module -ListAvailable Az` uit om uw huidige versie te vinden. Als u PowerShell wilt installeren of upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-Az-ps).

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

U kunt zich aanmelden bij Azure en Azure CLI-opdrachten uitvoeren op een van de volgende twee manieren:

- U kunt CLI-opdrachten uitvoeren vanuit het Azure Portal, in Azure Cloud Shell.
- U kunt de CLI installeren en CLI-opdrachten lokaal uitvoeren.

### <a name="use-azure-cloud-shell"></a>Azure Cloud Shell gebruiken

Azure Cloud Shell is een gratis Bash-shell die u rechtstreeks in Azure Portal kunt uitvoeren. De Azure CLI is vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. Klik op de knop **Cloud shell** in het menu rechtsboven in de Azure portal:

[![Cloud Shell](../common/media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

Met de knop start u een interactieve shell die u kunt gebruiken om de stappen uit te voeren die worden beschreven in dit artikel:

[![Scherm opname van het Cloud Shell-venster in de portal](../common/media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>De CLI lokaal installeren

U kunt Azure CLI ook lokaal installeren en gebruiken. Voor dit procedure-artikel moet u de Azure CLI-versie 2.0.46 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli). 

---

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

# <a name="portal"></a>[Portal](#tab/azure-portal)

Meld u aan bij de [Azure-portal](https://portal.azure.com).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Meld u aan bij uw Azure-abonnement met de `Connect-AzAccount` opdracht en volg de instructies op het scherm om te verifiëren.

```powershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Meld u aan bij de [Azure Portal](https://portal.azure.com)om Azure Cloud shell te starten.

Als u zich wilt aanmelden bij de lokale installatie van de CLI, voert u de opdracht [AZ login](/cli/azure/reference-index#az-login) :

```azurecli
az login
```

---

## <a name="create-a-blockblobstorage-account"></a>Een BlockBlobStorage-account maken

## <a name="portal"></a>[Portal](#tab/azure-portal)
Voer de volgende stappen uit om een BlockBlobStorage-account te maken in de Azure Portal:

1. Selecteer in de Azure Portal **alle services** > de **opslag** categorie > **opslag accounts**.

2. Selecteer onder **opslag accounts**de optie **toevoegen**.

3. Selecteer in het veld **abonnement** het abonnement waarin u het opslag account wilt maken.

4. Selecteer in het veld **resource groep** een bestaande resource groep of selecteer **nieuwe maken**en voer een naam in voor de nieuwe resource groep.

5. Voer in het veld **naam van opslag account** een naam in voor het account. Houd rekening met de volgende richt lijnen:

   - De naam moet uniek zijn binnen Azure.
   - De naam moet tussen de 3 en 24 tekens lang zijn.
   - De naam mag alleen cijfers en kleine letters bevatten.

6. Selecteer in het veld **locatie** een locatie voor het opslag account of gebruik de standaard locatie.

7. Configureer voor de rest van de instellingen het volgende:

   |Veld     |Waarde  |
   |---------|---------|
   |**Nemen**    |  Selecteer **Premium**.   |
   |**Soort account**    | Selecteer **BlockBlobStorage**.      |
   |**Replicatie**    |  Behoud de standaard instelling van **lokaal redundante opslag (LRS)**.      |

   ![Geeft een portal-gebruikers interface weer voor het maken van een blok-Blob-opslag account](media/storage-blob-create-account-block-blob/create-block-blob-storage-account.png)

8. Kies het tabblad **Geavanceerd** .

9. Als u uw opslag account voor gegevens analyse wilt optimaliseren, stelt u **hiërarchische naam ruimte** in op **ingeschakeld**. Als dat niet het geval is, moet u deze optie instellen op de standaard waarde.

   Zie [Inleiding tot Azure data Lake Storage Gen2](data-lake-storage-introduction.md)voor meer informatie.

   > [!NOTE]
   > De functie voor hiërarchische naam ruimte in een blok-Blob-opslag account is in open bare preview en is beschikbaar in de Verenigde Staten Oost, VS Oost 2, VS centraal, VS Zuid-Centraal, VS West 2, UK-zuid, Canada-centraal en Australië-oost regio's. Zie [Blob Storage-functies die beschikbaar zijn in azure data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md) en [bekende problemen](data-lake-storage-known-issues.md)als u beperkingen wilt bekijken. Zie [dit formulier](https://aka.ms/adlspremiumonboard)als u zich wilt inschrijven voor de preview-versie.

8. Selecteer **controleren + maken** om de instellingen voor het opslag account te controleren.

9. Selecteer **Maken**.

## <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

1. Open een Windows Power shell-sessie met verhoogde bevoegdheden (als administrator uitvoeren).

2. Voer de volgende opdracht uit om te controleren of de meest recente versie van de `Az` Power shell-module is geïnstalleerd.

   ```powershell
   Install-Module -Name Az -AllowClobber
   ```

3. Open een nieuwe Power shell-console en meld u aan met uw Azure-account.

   ```powershell
   Connect-AzAccount -SubscriptionId <SubscriptionID>
   ```

4. Maak indien nodig een nieuwe resource groep. Vervang de waarden in aanhalings tekens en voer de volgende opdracht uit.

   ```powershell
   $resourcegroup = "new_resource_group_name"
   $location = "region_name"
   New-AzResourceGroup -Name $resourceGroup -Location $location
   ```

5. Maak het BlockBlobStorage-account. Vervang de waarden in aanhalings tekens en voer de volgende opdracht uit.

   ```powershell
   $resourcegroup = "resource_group_name"
   $storageaccount = "new_storage_account_name"
   $location = "region_name"

   New-AzStorageAccount -ResourceGroupName $resourcegroup -Name $storageaccount -Location $location -Kind "BlockBlobStorage" -SkuName "Premium_LRS"
   ```
   Als u uw opslag account wilt optimaliseren voor gegevens analyse, voegt u toe `-EnableHierarchicalNamespace $True` aan de opdracht. Zie [Inleiding tot Azure data Lake Storage Gen2](data-lake-storage-introduction.md)voor meer informatie.

   > [!NOTE]
   > De functie voor hiërarchische naam ruimte in een blok-Blob-opslag account is in open bare preview en is beschikbaar in de Verenigde Staten Oost, VS Oost 2, VS centraal, VS Zuid-Centraal, VS West 2, UK-zuid, Canada-centraal en Australië-oost regio's. Zie [Blob Storage-functies die beschikbaar zijn in azure data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md) en [bekende problemen](data-lake-storage-known-issues.md)als u beperkingen wilt bekijken. Zie [dit formulier](https://aka.ms/adlspremiumonboard)als u zich wilt inschrijven voor de preview-versie.

## <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u een account voor een blok-BLOB wilt maken met behulp van de Azure CLI, moet u eerst Azure CLI v installeren. 2.0.46 of een latere versie. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli).

1. Meld u aan bij uw Azure-abonnement.

   ```azurecli
   az login
   ```

2. Maak indien nodig een nieuwe resource groep. Vervang de waarden tussen vier Kante haken (inclusief de accolades) en voer de volgende opdracht uit.

   ```azurecli
   az group create \
    --name "<new_resource_group_name>" \
    --location "<location>"
   ```

3. Maak het BlockBlobStorage-account. Vervang de waarden tussen vier Kante haken (inclusief de accolades) en voer de volgende opdracht uit.

   ```azurecli
   az storage account create \
    --location "<location>" \
    --name "<new_storage_account_name>" \
    --resource-group "<resource_group_name>" \
    --kind "BlockBlobStorage" \
    --sku "Premium_LRS"
   ```

   Als u uw opslag account wilt optimaliseren voor gegevens analyse, voegt u toe `--hierarchical-namespace true` aan de opdracht. Zie [Inleiding tot Azure data Lake Storage Gen2](data-lake-storage-introduction.md)voor meer informatie.

   > [!NOTE]
   > De functie voor hiërarchische naam ruimte in een blok-Blob-opslag account is in open bare preview en is beschikbaar in de Verenigde Staten Oost, VS Oost 2, VS centraal, VS Zuid-Centraal, VS West 2, UK-zuid, Canada-centraal en Australië-oost regio's. Zie [Blob Storage-functies die beschikbaar zijn in azure data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md) en [bekende problemen](data-lake-storage-known-issues.md)als u beperkingen wilt bekijken. Zie [dit formulier](https://aka.ms/adlspremiumonboard)als u zich wilt inschrijven voor de preview-versie.
   
---

## <a name="next-steps"></a>Volgende stappen

- Zie [overzicht van Azure Storage-account](https://docs.microsoft.com/azure/storage/common/storage-account-overview)voor meer informatie over opslag accounts.

- Zie [Azure Resource Manager Overview](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)voor meer informatie over resource groepen.
