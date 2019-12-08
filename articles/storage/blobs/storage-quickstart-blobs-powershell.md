---
title: 'Quick Start: een BLOB maken met Power shell'
titleSuffix: Azure Storage
description: In deze snelstart gebruikt u Azure PowerShell in object(blob)-opslag. Vervolgens gebruikt u PowerShell om een blob in Azure Storage te uploaden, een blob te downloaden en de blobs in een container te vermelden.
services: storage
author: tamram
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 12/04/2019
ms.author: tamram
ms.openlocfilehash: c5d32b91043d310e51143357ad51631463f7e991
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/06/2019
ms.locfileid: "74892495"
---
# <a name="quickstart-upload-download-and-list-blobs-with-powershell"></a>Quick Start: blobs uploaden, downloaden en vermelden met Power shell

Gebruik de Azure PowerShell-module om Azure-resources te maken en beheren. Azure-resources kunnen vanaf de PowerShell-opdrachtregel of met scripts worden gemaakt of beheerd. In deze handleiding wordt beschreven hoe PowerShell kan worden gebruikt om bestanden over te dragen tussen de lokale schijf en Azure Blob Storage.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Vereisten

U hebt een Azure-abonnement nodig voor toegang tot Azure Storage. Als u nog geen abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Voor deze snelstart is Az-versie 0.7 of hoger van de Azure PowerShell-module vereist. Voer `Get-InstalledModule -Name Az -AllVersions | select Name,Version` uit om de versie te bekijken. Als u PowerShell wilt installeren of upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-Az-ps).

[!INCLUDE [storage-quickstart-tutorial-intro-include-powershell](../../../includes/storage-quickstart-tutorial-intro-include-powershell.md)]

## <a name="create-a-container"></a>Een container maken

Blobs worden altijd naar een container geüpload. U kunt groepen blobs ordenen net zoals u bestanden op uw computer in mappen ordent.

Stel de containernaam in en maak vervolgens de container met behulp van [New-AzStorageContainer](/powershell/module/az.storage/new-AzStoragecontainer). Stel de machtigingen in op `blob` om openbare toegang tot de bestanden te verlenen. De containernaam in dit voorbeeld is *quickstartblobs*.

```powershell
$containerName = "quickstartblobs"
New-AzStorageContainer -Name $containerName -Context $ctx -Permission blob
```

## <a name="upload-blobs-to-the-container"></a>Blobs uploaden naar de container

Blob-opslag ondersteunt blok-blobs, toevoeg-blobs en pagina-blobs. VHD-bestanden die worden gebruikt voor IaaS-VM's zijn pagina-blobs. Gebruik toevoeg-blobs voor logboekregistratie, bijvoorbeeld wanneer u gegevens wilt wegschrijven naar een bestand en vervolgens gegevens wilt blijven toevoegen. De meeste bestanden die zijn opgeslagen in Blob-opslag, zijn blok-blobs. 

Als u een bestand wilt uploaden naar een blok-blob, haalt u een containerverwijzing op en haalt u vervolgens een verwijzing op naar de blok-blob in deze container. Zodra u de blobverwijzing hebt, kunt u er gegevens naar uploaden met behulp van [Set-AzStorageBlobContent](/powershell/module/az.storage/set-AzStorageblobcontent). Met deze bewerking wordt de blob gemaakt als deze nog niet bestaat, of overschreven als dat wel het geval is.

In de volgende voorbeelden worden *Image001.jpg* en *Image002.png* uit de map *D:\\_TestImages* op de lokale schijf geüpload naar de container die u hebt gemaakt.

```powershell
# upload a file
Set-AzStorageBlobContent -File "D:\_TestImages\Image001.jpg" `
  -Container $containerName `
  -Blob "Image001.jpg" `
  -Context $ctx 

# upload another file
Set-AzStorageBlobContent -File "D:\_TestImages\Image002.png" `
  -Container $containerName `
  -Blob "Image002.png" `
  -Context $ctx
```

Upload zoveel bestanden als u nodig hebt, voordat u doorgaat.

## <a name="list-the-blobs-in-a-container"></a>De blobs in een container in een lijst weergeven

Haal een lijst met blobs in de container op met behulp van [Get-AzStorageBlob](/powershell/module/az.storage/get-AzStorageblob). In dit voorbeeld worden alleen de namen van de geüploade blobs weergegeven.

```powershell
Get-AzStorageBlob -Container $ContainerName -Context $ctx | select Name
```

## <a name="download-blobs"></a>Blobs downloaden

Download de blobs naar de lokale vaste schijf. Stel voor elke blob die u wilt downloaden de naam in en roep [Get-AzStorageBlobContent](/powershell/module/az.storage/get-AzStorageblobcontent) aan om de blob te downloaden.

In dit voorbeeld worden de blobs gedownload naar *D:\\_TestImages\Downloads* op de lokale schijf. 

```powershell
# download first blob
Get-AzStorageBlobContent -Blob "Image001.jpg" `
  -Container $containerName `
  -Destination "D:\_TestImages\Downloads\" `
  -Context $ctx 

# download another blob
Get-AzStorageBlobContent -Blob "Image002.png" `
  -Container $containerName `
  -Destination "D:\_TestImages\Downloads\" `
  -Context $ctx
```

## <a name="data-transfer-with-azcopy"></a>Gegevensoverdracht met AzCopy

Het hulpprogramma [AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) is een andere optie voor krachtige, scriptbare gegevensoverdracht voor Azure Storage. Gebruik AzCopy om gegevens over te brengen naar en van blob-, bestands- en tabelopslag.

Als een kort voorbeeld volgt hier de AzCopy-opdracht voor het uploaden van een bestand met de naam *myfile.txt* naar de container *mystoragecontainer* vanuit een PowerShell-venster.

```powershell
./AzCopy `
    /Source:C:\myfolder `
    /Dest:https://mystorageaccount.blob.core.windows.net/mystoragecontainer `
    /DestKey:<storage-account-access-key> `
    /Pattern:"myfile.txt"
```

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder alle activa die u hebt gemaakt. De eenvoudigste manier om assets te verwijderen is door de resourcegroep te verwijderen. Als u de resourcegroep verwijdert, worden alle resources verwijderd die deze groep bevat. In het volgende voorbeeld worden samen met de resourcegroep het opslagaccount en de resourcegroep zelf verwijderd.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u bestanden overgebracht tussen een lokale schijf en Azure Blob-Storage. Als u meer wilt weten over het werken met Blob-opslag met behulp van PowerShell, kunt u instructies voor Azure PowerShell met Azure Storage blijven gebruiken.

> [!div class="nextstepaction"]
> [Azure PowerShell gebruiken met Azure Storage](../common/storage-powershell-guide-full.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

### <a name="microsoft-azure-powershell-storage-cmdlets-reference"></a>Naslaginformatie over Microsoft Azure PowerShell Storage-cmdlets

* [PowerShell Storage-cmdlets](/powershell/module/az.storage)

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure Storage Explorer

* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) is een gratis, zelfstandige app van Microsoft waarmee u visueel met Azure Storage-gegevens kunt werken in Windows, macOS en Linux.
