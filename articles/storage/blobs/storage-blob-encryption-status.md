---
title: De versleutelingsstatus van een blob controleren - Azure Storage
description: Meer informatie over het gebruik van Azure portal, PowerShell of Azure CLI om te controleren of een bepaalde blob is versleuteld. Als een blob niet is versleuteld, leest u hoe u AzCopy gebruiken om versleuteling te forceren door de blob te downloaden en opnieuw te uploaden.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/26/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 5cef0e94a43b3ef16d45f7f43658f962e07b5345
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74707595"
---
# <a name="check-the-encryption-status-of-a-blob"></a>De versleutelingsstatus van een blob controleren

Elke blokblob, toevoegenblob of paginablob die na 20 oktober 2017 naar Azure Storage is geschreven, wordt versleuteld met Azure Storage-versleuteling. Blobs die vóór deze datum zijn gemaakt, worden nog steeds versleuteld door een achtergrondproces.

In dit artikel ziet u hoe u bepalen of een bepaalde blob is versleuteld.

## <a name="check-a-blobs-encryption-status"></a>De versleutelingsstatus van een blob controleren

Gebruik de Azure-portal, PowerShell of Azure CLI om te bepalen of een blob is versleuteld zonder code.

### <a name="azure-portal"></a>[Azure-portal](#tab/portal)

Voer de volgende stappen uit om de Azure-portal te gebruiken om te controleren of een blob is versleuteld:

1. Ga in Azure Portal naar het nieuwe opslagaccount.
1. Selecteer **Containers** om naar een lijst met containers in het account te navigeren.
1. Zoek de blob en geef het tabblad **Overzicht** weer.
1. Bekijk de eigenschap **Server Encrypted.** Als **True ,** zoals weergegeven in de volgende afbeelding, wordt de blob versleuteld. De eigenschappen van de blob bevatten ook de datum en tijd waarop de blob is gemaakt.

    ![Schermafbeelding van het controleren van de eigenschap Server encrypted in Azure-portal](media/storage-blob-encryption-status/blob-encryption-property-portal.png)

### <a name="powershell"></a>[Powershell](#tab/powershell)

Als u PowerShell wilt gebruiken om te controleren of een blob is versleuteld, controleert u de eigenschap **IsServerEncrypted van** de blob. Vergeet niet om plaatsaanduidingswaarden in hoekhaakjes te vervangen door uw eigen waarden:

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account>
$blob = Get-AzStorageBlob -Context $account.Context `
    -Container <container> `
    -Blob <blob>
$blob.ICloudBlob.Properties.IsServerEncrypted
```

Als u wilt bepalen wanneer de blob is gemaakt, controleert u de waarde van de eigenschap **Gemaakt:**

```powershell
$blob.ICloudBlob.Properties.IsServerEncrypted
```

### <a name="azure-cli"></a>[Azure-CLI](#tab/cli)

Als u Azure CLI wilt gebruiken om te controleren of een blob is versleuteld, controleert u de **isServerEncrypted-eigenschap van** de blob. Vergeet niet om plaatsaanduidingswaarden in hoekhaakjes te vervangen door uw eigen waarden:

```azurecli-interactive
az storage blob show \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --query "properties.serverEncrypted"
```

Als u wilt bepalen wanneer de blob is gemaakt, controleert u de waarde van de **gemaakte** eigenschap.

---

## <a name="force-encryption-of-a-blob"></a>Versleuteling van een blob forceren

Als een blob die vóór 20 oktober 2017 is gemaakt, nog niet is versleuteld door het achtergrondproces, u versleuteling onmiddellijk forceren door de blob te downloaden en opnieuw te uploaden. Een eenvoudige manier om dit te doen is met AzCopy.

Als u een blob wilt downloaden naar uw lokale bestandssysteem met AzCopy, gebruikt u de volgende syntaxis:

```
azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-file-path>'

Example:
azcopy copy 'https://storagesamples.blob.core.windows.net/sample-container/blob1.txt' 'C:\temp\blob1.txt'
```

Als u de blob opnieuw wilt uploaden naar Azure Storage met AzCopy, gebruikt u de volgende syntaxis:

```
azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'

Example:
azcopy copy 'C:\temp\blob1.txt' 'https://storagesamples.blob.core.windows.net/sample-container/blob1.txt'
```

Zie [Gegevens overbrengen met AzCopy en Blob-opslag](../common/storage-use-azcopy-blobs.md)voor meer informatie over het gebruik van AzCopy om blobgegevens te kopiëren.

## <a name="next-steps"></a>Volgende stappen

[Azure Storage-versleuteling voor gegevens in rust](../common/storage-service-encryption.md)
