---
title: 'Azure-snelstart: een blob maken in objectopslag met Azure CLI | Microsoft Docs'
description: In deze Quick Start leert u hoe u de Azure CLI kunt gebruiken om een BLOB te Azure Storage uploaden, een BLOB te downloaden en de blobs in een container weer te geven.
services: storage
author: tamram
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 11/06/2019
ms.author: tamram
ms.openlocfilehash: 1f3143eced90f97c090c0005375ef50fe48c5f5f
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747913"
---
# <a name="quickstart-upload-download-and-list-blobs-using-the-azure-cli"></a>Quickstart: blobs uploaden, downloaden en vermelden met behulp van Azure CLI

Azure CLI is de nieuwe opdrachtregel van Azure voor het beheren van Azure-resources. U kunt deze gebruiken in uw browser met Azure Cloud Shell. U kunt deze ook installeren op Mac OS, Linux of Windows en uitvoeren vanaf de opdrachtregel. In deze quickstart leert u hoe u Azure CLI kunt gebruiken om gegevens naar en van Azure Blob-opslag te uploaden en te downloaden.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor deze Quickstart gebruikmaken van Azure CLI versie 2.0.4 of hoger. Voer `az --version` uit om uw versie te bepalen. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli).

[!INCLUDE [storage-quickstart-tutorial-intro-include-cli](../../../includes/storage-quickstart-tutorial-intro-include-cli.md)]

## <a name="create-a-container"></a>Een container maken

Blobs worden altijd naar een container geüpload. U kunt groepen blobs ordenen net zoals u bestanden op uw computer in mappen ordent.

Gebruik de opdracht [az storage container create](/cli/azure/storage/container) om een container te maken voor het opslaan van blobs.

```azurecli-interactive
az storage container create --name sample-container
```

## <a name="upload-a-blob"></a>Een blob uploaden

Blob-opslag ondersteunt blok-blobs, toevoeg-blobs en pagina-blobs. In de voor beelden in deze Snelstartgids ziet u hoe u kunt werken met blok-blobs.

Maak eerst een bestand dat u wilt uploaden naar een blok-blob. Als u Azure Cloud Shell gebruikt, gebruikt u de volgende opdracht om een bestand te maken:

```bash
vi helloworld
```

Wanneer het bestand wordt geopend, drukt u op **Insert (invoegen**). Typ *Hallo wereld*en druk vervolgens op **ESC**. Typ vervolgens *: x*en druk vervolgens op **Enter**.

In dit voorbeeld wordt met de opdracht [az storage blob upload](/cli/azure/storage/blob) een blob geüpload naar de container die u in de laatste stap hebt gemaakt. Het is niet nodig om een bestandspad op te geven sinds het bestand is gemaakt in de hoofdmap:

```azurecli-interactive
az storage blob upload \
    --container-name sample-container \
    --name helloworld \
    --file helloworld
```

Met deze bewerking wordt de blob gemaakt als deze nog niet bestaat, of overschreven als dat wel het geval is. Upload zoveel bestanden als u nodig hebt, voordat u doorgaat.

Als u meerdere bestanden tegelijk wilt uploaden, kunt u de opdracht [az storage blob upload-batch](/cli/azure/storage/blob) gebruiken.

## <a name="list-the-blobs-in-a-container"></a>De blobs in een container in een lijst weergeven

Gebruik de opdracht [az storage blob list](/cli/azure/storage/blob) om de blobs in de container weer te geven.

```azurecli-interactive
az storage blob list \
    --container-name sample-container \
    --output table
```

## <a name="download-a-blob"></a>Een blob downloaden

Gebruik de opdracht [az storage blob download](/cli/azure/storage/blob) om de blob te downloaden die u eerder hebt geüpload.

```azurecli-interactive
az storage blob download \
    --container-name sample-container \
    --name helloworld \
    --file ~/destination/path/for/file
```

## <a name="data-transfer-with-azcopy"></a>Gegevensoverdracht met AzCopy

Het hulpprogramma [AzCopy](../common/storage-use-azcopy-linux.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) is een andere optie voor krachtige, scriptbare gegevensoverdracht voor Azure Storage. U kunt AzCopy gebruiken om gegevens over te brengen naar en van blob-, bestands- en tabelopslag.

In het volgende voor beeld wordt AzCopy gebruikt om een bestand met de naam *Mijnbestand. txt* te uploaden naar de container van de voor *beeld-container* . Vergeet niet om de waarden van de tijdelijke aanduidingen tussen punt haken te vervangen door uw eigen waarden:

```bash
azcopy \
    --source /mnt/myfiles \
    --destination https://<account-name>.blob.core.windows.net/sample-container \
    --dest-key <account-key> \
    --include "myfile.txt"
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u een van de resources in de resource groep niet meer nodig hebt, met inbegrip van het opslag account dat u in deze Quick Start hebt gemaakt, verwijdert u de resource groep met de opdracht [AZ Group delete](/cli/azure/group) . Vergeet niet om de waarden van de tijdelijke aanduidingen tussen punt haken te vervangen door uw eigen waarden:

```azurecli-interactive
az group delete --name <resource-group-name>
```

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u bestanden overbrengt tussen een lokaal bestands systeem en een container in Azure Blob Storage. Raadpleeg de zelfstudie voor het werken met Azure Blob Storage, voor meer informatie over het werken met blobs in Azure Storage.

> [!div class="nextstepaction"]
> [Procedure: Blob-opslagbewerkingen uitvoeren met Azure CLI](storage-how-to-use-blobs-cli.md)
