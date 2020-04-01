---
title: Een blokblobopslagaccount maken - Azure Storage | Microsoft Documenten
description: Hier ziet u hoe u een Azure BlockBlobStorage-account maakt met hoogwaardige prestatiekenmerken.
author: tamram
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 6303644ada5c6f093611dba94daf8006f8cc5819
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536901"
---
# <a name="create-a-blockblobstorage-account"></a>Een BlockBlobStorage-account maken

Met de BlockBlobStorage-accountsoort u blokblobs maken met hoogwaardige prestatiekenmerken. Dit type opslagaccount is geoptimaliseerd voor workloads met hoge transactiepercentages of waarvoor zeer snelle toegangstijden nodig zijn. In dit artikel ziet u hoe u een BlockBlobStorage-account maakt met behulp van de Azure-portal, de Azure CLI of Azure PowerShell.

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

Zie overzicht van [Azure-opslagaccount](https://docs.microsoft.com/azure/storage/common/storage-account-overview)voor meer informatie over BlockBlobStorage-accounts.

## <a name="prerequisites"></a>Vereisten

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Geen.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Voor dit artikel voor how-to's is de Azure PowerShell-module Az-versie 1.2.0 of hoger vereist. Voer `Get-Module -ListAvailable Az` uit om uw huidige versie te vinden. Als u PowerShell wilt installeren of upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-Az-ps).

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

U zich aanmelden bij Azure en Azure CLI-opdrachten op twee manieren uitvoeren:

- U CLI-opdrachten uitvoeren vanuit de Azure-portal in Azure Cloud Shell.
- U de CLI-opdrachten lokaal installeren en uitvoeren.

### <a name="use-azure-cloud-shell"></a>Azure Cloud Shell gebruiken

Azure Cloud Shell is een gratis Bash-shell die u rechtstreeks in Azure Portal kunt uitvoeren. De Azure CLI is vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. Klik op de knop **Cloud Shell** in het menu in het rechterbovengedeelte van de Azure-portal:

[![Cloud Shell](../common/media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

De knop lanceert een interactieve shell die u gebruiken om de stappen uit te voeren die in dit artikel worden beschreven:

[![Schermafbeelding van het venster Cloud Shell in de portal](../common/media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>De CLI lokaal installeren

U kunt Azure CLI ook lokaal installeren en gebruiken. Dit how-to-artikel vereist dat u de Azure CLI-versie 2.0.46 of hoger uitvoert. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli). 

---

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

# <a name="portal"></a>[Portal](#tab/azure-portal)

Meld u aan bij [Azure Portal](https://portal.azure.com).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Meld u met de `Connect-AzAccount` opdracht aan bij uw Azure-abonnement en volg de aanwijzingen op het scherm om te verifiëren.

```powershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u Azure Cloud Shell wilt starten, meldt u zich aan bij de [Azure-portal.](https://portal.azure.com)

Voer de opdracht [AZ-aanmelding](/cli/azure/reference-index#az-login) uit om in te loggen op uw lokale installatie van de CLI:

```azurecli
az login
```

---

## <a name="create-a-blockblobstorage-account"></a>Een BlockBlobStorage-account maken

## <a name="portal"></a>[Portal](#tab/azure-portal)
Voer de volgende stappen uit om een BlockBlobStorage-account in de Azure-portal te maken:

1. Selecteer in de Azure-portal **Alle services** > de **categorie Opslag** > **Opslagaccounts**.

1. Selecteer **Onder Opslagaccounts**de optie **Toevoegen**.

1. Selecteer **in** het veld Abonnement het abonnement waarin u het opslagaccount wilt maken.

1. Selecteer in het veld **Resourcegroep** een bestaande resourcegroep of selecteer **Nieuw maken**en voer een naam in voor de nieuwe resourcegroep.

1. Voer in het veld **Naam van het opslagaccount** een naam voor het account in. Let op de volgende richtlijnen:

   - De naam moet uniek zijn in Azure.
   - De naam moet tussen de drie en 24 tekens lang zijn.
   - De naam kan alleen nummers en kleine letters bevatten.

1. Selecteer **in** het veld Locatie een locatie voor het opslagaccount of gebruik de standaardlocatie.

1. Configureer het volgende voor de rest van de instellingen:

   |Veld     |Waarde  |
   |---------|---------|
   |**Prestaties**    |  Selecteer **Premium**.   |
   |**Accountsoort**    | Selecteer **BlockBlobStorage**.      |
   |**Replicatie**    |  Laat de standaardinstelling **van Lrs (Localredundant storage) staan.**      |

   ![Toont portal-gebruikersinterface om een blokblobopslagaccount te maken](media/storage-blob-create-account-block-blob/create-block-blob-storage-account.png)

1. Selecteer **Controleren + maken** om de instellingen van het opslagaccount te controleren.

1. Selecteer **Maken**.

## <a name="azure-powershell"></a>[Azure Powershell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

1. Open een verhoogde Windows PowerShell-sessie (Uitvoeren als beheerder).

1. Voer de volgende opdracht uit om `Az` ervoor te zorgen dat de nieuwste versie van de PowerShell-module is geïnstalleerd.

   ```powershell
   Install-Module -Name Az -AllowClobber
   ```

1. Open een nieuwe PowerShell-console en meld u aan met uw Azure-account.

   ```powershell
   Connect-AzAccount -SubscriptionId <SubscriptionID>
   ```

1. Maak indien nodig een nieuwe resourcegroep. Vervang de waarden in aanhalingstekens en voer de volgende opdracht uit.

   ```powershell
   $resourcegroup = "new_resource_group_name"
   $location = "region_name"
   New-AzResourceGroup -Name $resourceGroup -Location $location
   ```

1. Het BlockBlobStorage-account maken. Vervang de waarden in aanhalingstekens en voer de volgende opdracht uit.

   ```powershell
   $resourcegroup = "resource_group_name"
   $storageaccount = "new_storage_account_name"
   $location = "region_name"

   New-AzStorageAccount -ResourceGroupName $resourcegroup -Name $storageaccount -Location $location -Kind "BlockBlobStorage" -SkuName "Premium_LRS"
   ```

## <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u een blokblobaccount wilt maken met behulp van de Azure CLI, moet u eerst Azure CLI v installeren. 2.0.46 of een latere versie. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli).

1. Meld u aan bij uw Azure-abonnement.

   ```azurecli
   az login
   ```

1. Maak indien nodig een nieuwe resourcegroep. Vervang de waarden tussen haakjes (inclusief de haakjes) en voer de volgende opdracht uit.

   ```azurecli
   az group create \
    --name "<new_resource_group_name>" \
    --location "<location>"
   ```

1. Het BlockBlobStorage-account maken. Vervang de waarden tussen haakjes (inclusief de haakjes) en voer de volgende opdracht uit.

   ```azurecli
   az storage account create \
    --location "<location>" \
    --name "<new_storage_account_name>" \
    --resource-group "<resource_group_name>" \
    --kind "BlockBlobStorage" \
    --sku "Premium_LRS"
   ```

---

## <a name="next-steps"></a>Volgende stappen

- Zie overzicht van [Azure-opslagaccounts](https://docs.microsoft.com/azure/storage/common/storage-account-overview)voor meer informatie over opslagaccounts.

- Zie overzicht azure resource [manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)voor meer informatie over resourcegroepen.
