---
title: Bepalen welk versleutelingssleutelmodel voor het opslagaccount wordt gebruikt
titleSuffix: Azure Storage
description: Gebruik Azure portal, PowerShell of Azure CLI om te controleren hoe versleutelingssleutels worden beheerd voor het opslagaccount. Sleutels kunnen worden beheerd door Microsoft (de standaardinstelling) of door de klant. Door de klant beheerde sleutels moeten worden opgeslagen in Azure Key Vault.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/13/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 0df0ba4ce76d249bcb4738b41c94677e061f14ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409849"
---
# <a name="determine-which-azure-storage-encryption-key-model-is-in-use-for-the-storage-account"></a>Bepalen welk Azure Storage-versleutelingssleutelmodel wordt gebruikt voor het opslagaccount

Gegevens in uw opslagaccount worden automatisch versleuteld door Azure Storage. Azure Storage-versleuteling biedt twee opties voor het beheren van versleutelingssleutels op het niveau van het opslagaccount:

- **Door Microsoft beheerde sleutels.** Microsoft beheert standaard de sleutels die worden gebruikt om uw opslagaccount te versleutelen.
- **Door de klant beheerde sleutels.** U er optioneel voor kiezen om versleutelingssleutels voor uw opslagaccount te beheren. Door de klant beheerde sleutels moeten worden opgeslagen in Azure Key Vault.

Bovendien u een versleutelingssleutel op bieden op het niveau van een individuele aanvraag voor sommige Blob-opslagbewerkingen. Wanneer een versleutelingssleutel op de aanvraag is opgegeven, overschrijft die sleutel de versleutelingssleutel die actief is op het opslagaccount. Zie [Een door de klant verstrekte sleutel opgeven voor een aanvraag voor Blob-opslag](../blobs/storage-blob-customer-provided-key.md)voor meer informatie.

Zie [Azure Storage-versleuteling voor gegevens in rust](storage-service-encryption.md)voor meer informatie over versleutelingssleutels.

## <a name="check-the-encryption-key-model-for-the-storage-account"></a>Het coderingssleutelmodel voor het opslagaccount controleren

Als u wilt bepalen of een opslagaccount door Microsoft beheerde sleutels of door de klant beheerde sleutels voor versleuteling gebruikt, gebruikt u een van de volgende benaderingen.

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

Voer de volgende stappen uit om het versleutelingsmodel voor het opslagaccount te controleren met behulp van de Azure-portal:

1. Ga in Azure Portal naar het nieuwe opslagaccount.
1. Selecteer de **instelling Versleuteling** en noteer de instelling.

In de volgende afbeelding wordt een opslagaccount weergegeven dat is versleuteld met door Microsoft beheerde sleutels:

![Account versleuteld weergeven met door Microsoft beheerde sleutels](media/storage-encryption-key-model-get/microsoft-managed-encryption-key-setting-portal.png)

En de volgende afbeelding toont een opslagaccount dat is versleuteld met door de klant beheerde sleutels:

![Schermafbeelding van de instelling van versleutelingssleutels in azure-portal](media/storage-encryption-key-model-get/customer-managed-encryption-key-setting-portal.png)

# <a name="powershell"></a>[Powershell](#tab/powershell)

Als u het versleutelingsmodel voor het opslagaccount wilt controleren met PowerShell, belt u de opdracht [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) en controleert u vervolgens de eigenschap **KeySource** voor het account.

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account>
$account.Encryption.KeySource
```

Als de waarde van de `Microsoft.Storage`eigenschap **KeySource** is, wordt het account versleuteld met door Microsoft beheerde sleutels. Als de waarde van de `Microsoft.Keyvault`eigenschap **KeySource** is, wordt het account versleuteld met door de klant beheerde sleutels.

# <a name="azure-cli"></a>[Azure-CLI](#tab/cli)

Als u het versleutelingsmodel voor het opslagaccount wilt controleren met Azure CLI, roept u de opdracht [showshow van het AZ-opslagaccount](/cli/azure/storage/account#az-storage-account-show) aan en controleert u vervolgens de eigenschap **keySource** voor het account.

```azurecli-interactive
key_source=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query encryption.keySource \
    --output tsv)
```

Als de waarde van de `Microsoft.Storage`eigenschap **keySource** is, wordt het account versleuteld met door Microsoft beheerde sleutels. Als de waarde van de `Microsoft.Keyvault`eigenschap **keySource** is, wordt het account versleuteld met door de klant beheerde sleutels.

---

## <a name="next-steps"></a>Volgende stappen

- [Azure Storage-versleuteling voor gegevens in rust](storage-service-encryption.md)
- [Door de klant beheerde sleutels gebruiken met Azure Key Vault om Azure Storage-versleuteling te beheren](encryption-customer-managed-keys.md)