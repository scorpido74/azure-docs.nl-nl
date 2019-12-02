---
title: Bepaal welk versleutelings sleutel model wordt gebruikt voor het opslag account-Azure Storage
description: Gebruik Azure Portal, Power shell of Azure CLI om te controleren hoe de versleutelings sleutels voor het opslag account worden beheerd. Sleutels kunnen worden beheerd door micro soft (de standaard instelling) of door de klant. Door de klant beheerde sleutels moeten worden opgeslagen in Azure Key Vault.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/26/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 967e6f278008a59721d8d0c74e34c0252eeb1138
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2019
ms.locfileid: "74666596"
---
# <a name="determine-which-azure-storage-encryption-key-model-is-in-use-for-the-storage-account"></a>Bepalen welk Azure Storage versleutelings sleutel model wordt gebruikt voor het opslag account

De gegevens in uw opslag account worden automatisch versleuteld door Azure Storage. Azure Storage versleuteling biedt twee opties voor het beheren van versleutelings sleutels op het niveau van het opslag account:

- **Door micro soft beheerde sleutels.** Standaard beheert micro soft de sleutels die worden gebruikt voor het versleutelen van uw opslag account.
- **Door de klant beheerde sleutels.** Desgewenst kunt u ervoor kiezen om versleutelings sleutels voor uw opslag account te beheren. Door de klant beheerde sleutels moeten worden opgeslagen in Azure Key Vault.

Daarnaast kunt u een versleutelings sleutel opgeven op het niveau van een afzonderlijke aanvraag voor bepaalde Blob Storage-bewerkingen. Wanneer een versleutelings sleutel is opgegeven voor de aanvraag, overschrijft die sleutel de versleutelings sleutel die actief is op het opslag account. Zie voor meer informatie [een door de klant opgegeven sleutel opgeven voor een aanvraag voor Blob-opslag](../blobs/storage-blob-customer-provided-key.md).

Zie [Azure Storage versleuteling voor Data-at-rest](storage-service-encryption.md)voor meer informatie over versleutelings sleutels.

## <a name="check-the-encryption-key-model-for-the-storage-account"></a>Het versleutelings sleutel model voor het opslag account controleren

Gebruik een van de volgende benaderingen om te bepalen of een opslag account gebruikmaakt van door micro soft beheerde sleutels of door de klant beheerde sleutels voor versleuteling.

# <a name="azure-portaltabportal"></a>[Azure-portal](#tab/portal)

Voer de volgende stappen uit om het versleutelings model voor het opslag account te controleren met behulp van de Azure Portal:

1. Ga in Azure Portal naar uw opslagaccount.
1. Selecteer de **versleutelings** instelling en noteer de instelling.

In de volgende afbeelding ziet u een opslag account waarin door de klant beheerde sleutels worden gebruikt voor versleuteling:

![Scherm opname met de instelling voor de versleutelings sleutel in Azure Portal](media/storage-encryption-key-model-get/customer-managed-encryption-key-setting-portal.png)

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

Als u het versleutelings model voor het opslag account wilt controleren met behulp van Power shell, roept u de opdracht [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) aan en controleert u vervolgens de eigenschap sleutel **bron** voor het account.

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account>
$account.Encryption.KeySource
```

Als de waarde van de eigenschap sleutel **bron** is `Microsoft.Storage`, wordt het account versleuteld met door micro soft beheerde sleutels. Als de waarde van de eigenschap sleutel **bron** is `Microsoft.Keyvault`, wordt het account versleuteld met door de klant beheerde sleutels.

# <a name="azure-clitabcli"></a>[Azure CLI](#tab/cli)

Als u het versleutelings model voor het opslag account wilt controleren met behulp van Azure CLI, roept u de opdracht [AZ Storage account show](/cli/azure/storage/account#az-storage-account-show) aan en controleert u vervolgens de eigenschap sleutel **bron** voor het account.

```azurecli-interactive
key_source=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query encryption.keySource \
    --output tsv)
```

Als de waarde van de eigenschap sleutel **bron** is `Microsoft.Storage`, wordt het account versleuteld met door micro soft beheerde sleutels. Als de waarde van de eigenschap sleutel **bron** is `Microsoft.Keyvault`, wordt het account versleuteld met door de klant beheerde sleutels.

---

## <a name="next-steps"></a>Volgende stappen

[Azure Storage versleuteling voor Data-at-rest](storage-service-encryption.md)
