---
title: 'Quick Start: een BLOB maken met Azure CLI'
titleSuffix: Azure Storage
description: In deze Quick Start leert u hoe u de Azure CLI kunt gebruiken om een BLOB te Azure Storage uploaden, een BLOB te downloaden en de blobs in een container weer te geven.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.date: 04/23/2020
ms.author: tamram
ms.openlocfilehash: 237982c2135430891022ab7a823f8374844e8d90
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82117536"
---
# <a name="quickstart-create-download-and-list-blobs-with-azure-cli"></a>Quick Start: blobs maken, downloaden en vermelden met Azure CLI

Azure CLI is de nieuwe opdrachtregel van Azure voor het beheren van Azure-resources. U kunt deze gebruiken in uw browser met Azure Cloud Shell. U kunt deze ook installeren op Mac OS, Linux of Windows en uitvoeren vanaf de opdrachtregel. In deze quickstart leert u hoe u Azure CLI kunt gebruiken om gegevens naar en van Azure Blob-opslag te uploaden en te downloaden.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="install-the-azure-cli-locally"></a>De Azure CLI lokaal installeren

Als u ervoor kiest om de Azure CLI lokaal te installeren en te gebruiken, moet u voor deze Snelstartgids gebruikmaken van de Azure CLI-versie 2.0.46 of hoger. Voer `az --version` uit om uw versie te bepalen. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli).

Als u de Azure CLI lokaal uitvoert, moet u zich aanmelden en verifiëren. Deze stap is niet nodig als u Azure Cloud Shell gebruikt. Als u zich wilt aanmelden bij Azure CLI `az login` , voert u uit en verifieert u dit in het browser venster:

```azurecli
az login
```

Zie [Aanmelden met Azure cli](/cli/azure/authenticate-azure-cli)voor meer informatie over verificatie met Azure cli.

## <a name="authorize-access-to-blob-storage"></a>Toegang tot Blob Storage machtigen

U kunt de toegang tot de Blob-opslag toestaan via de Azure CLI met behulp van de Azure AD-referenties of door de toegangs sleutel voor het opslag account te gebruiken. Het gebruik van Azure AD-referenties wordt aanbevolen. In dit artikel wordt beschreven hoe u Blob Storage-bewerkingen kunt autoriseren met behulp van Azure AD.

Azure CLI-opdrachten voor gegevens bewerkingen op Blob Storage ondersteunen `--auth-mode` de para meter, waarmee u kunt opgeven hoe een bepaalde bewerking moet worden geautoriseerd. Stel de `--auth-mode` para meter `login` in op voor autoriseren met Azure AD-referenties. Zie [toegang verlenen aan BLOB-of wachtrij gegevens met Azure cli](../common/authorize-data-operations-cli.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)voor meer informatie.

Alleen Blob Storage-gegevens bewerkingen ondersteunen `--auth-mode` de para meter. Beheer bewerkingen, zoals het maken van een resource groep of een opslag account, gebruiken automatisch Azure AD-referenties voor autorisatie.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een Azure-resourcegroep met de opdracht [az group create](/cli/azure/group). Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd.

Vergeet niet om de waarden van de tijdelijke aanduidingen tussen punt haken te vervangen door uw eigen waarden:

```azurecli
az group create \
    --name <resource-group> \
    --location <location>
```

## <a name="create-a-storage-account"></a>Create a storage account

Maak een algemeen opslagaccount met de opdracht [az storage account create](/cli/azure/storage/account). Het algemeen opslagaccount kan voor alle vier de services worden gebruikt: blobs, bestanden, tabellen en wachtrijen.

Vergeet niet om de waarden van de tijdelijke aanduidingen tussen punt haken te vervangen door uw eigen waarden:

```azurecli
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_ZRS \
    --encryption-services blob
```

## <a name="create-a-container"></a>Een container maken

Blobs worden altijd naar een container geüpload. U kunt groepen blobs in containers organiseren die vergelijkbaar zijn met de manier waarop u uw bestanden op uw computer in mappen ordent.

Gebruik de opdracht [az storage container create](/cli/azure/storage/container) om een container te maken voor het opslaan van blobs. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen punt haken te vervangen door uw eigen waarden:

```azurecli
az storage container create \
    --account-name <storage-account> \
    --name <container> \
    --auth-mode login
```

## <a name="upload-a-blob"></a>Een blob uploaden

Blob-opslag ondersteunt blok-blobs, toevoeg-blobs en pagina-blobs. In de voor beelden in deze Snelstartgids ziet u hoe u kunt werken met blok-blobs.

Maak eerst een bestand dat u wilt uploaden naar een blok-blob. Als u Azure Cloud Shell gebruikt, gebruikt u de volgende opdracht om een bestand te maken:

```bash
vi helloworld
```

Wanneer het bestand wordt geopend, drukt u op **Insert (invoegen**). Typ *Hallo wereld*en druk vervolgens op **ESC**. Typ vervolgens *: x*en druk vervolgens op **Enter**.

In dit voorbeeld wordt met de opdracht [az storage blob upload](/cli/azure/storage/blob) een blob geüpload naar de container die u in de laatste stap hebt gemaakt. Het is niet nodig om een bestandspad op te geven sinds het bestand is gemaakt in de hoofdmap. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen punt haken te vervangen door uw eigen waarden:

```azurecli
az storage blob upload \
    --account-name <storage-account> \
    --container-name <container> \
    --name helloworld \
    --file helloworld \
    --auth-mode login
```

Met deze bewerking wordt de blob gemaakt als deze nog niet bestaat, of overschreven als dat wel het geval is. Upload zoveel bestanden als u nodig hebt, voordat u doorgaat.

Als u meerdere bestanden tegelijk wilt uploaden, kunt u de opdracht [az storage blob upload-batch](/cli/azure/storage/blob) gebruiken.

## <a name="list-the-blobs-in-a-container"></a>De blobs in een container in een lijst weergeven

Gebruik de opdracht [az storage blob list](/cli/azure/storage/blob) om de blobs in de container weer te geven. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen punt haken te vervangen door uw eigen waarden:

```azurecli
az storage blob list \
    --account-name <storage-account> \
    --container-name <container> \
    --output table \
    --auth-mode login
```

## <a name="download-a-blob"></a>Een blob downloaden

Gebruik de opdracht [az storage blob download](/cli/azure/storage/blob) om de blob te downloaden die u eerder hebt geüpload. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen punt haken te vervangen door uw eigen waarden:

```azurecli
az storage blob download \
    --account-name <storage-account> \
    --container-name <container> \
    --name helloworld \
    --file ~/destination/path/for/file \
    --auth-mode login
```

## <a name="data-transfer-with-azcopy"></a>Gegevensoverdracht met AzCopy

Het opdracht regel hulpprogramma AzCopy biedt hoogwaardige, script bare gegevens overdracht voor Azure Storage. U kunt AzCopy gebruiken om gegevens over te dragen van en naar Blob Storage en Azure Files. Zie [aan de slag met AzCopy](../common/storage-use-azcopy-v10.md)voor meer informatie over AzCopy V10 toevoegen, de nieuwste versie van AzCopy. Zie [gegevens overdragen met AzCopy en Blob Storage](../common/storage-use-azcopy-blobs.md)voor meer informatie over het gebruik van AzCopy V10 toevoegen met Blob Storage.

In het volgende voor beeld wordt AzCopy gebruikt om een lokaal bestand te uploaden naar een blob. Vergeet niet om de voorbeeld waarden te vervangen door uw eigen waarden:

```bash
azcopy login
azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u de resources wilt verwijderen die u hebt gemaakt als onderdeel van deze Snelstartgids, met inbegrip van het opslag account, verwijdert u de resource groep met de opdracht [AZ Group delete](/cli/azure/group) . Vergeet niet om de waarden van de tijdelijke aanduidingen tussen punt haken te vervangen door uw eigen waarden:

```azurecli
az group delete \
    --name <resource-group> \
    --no-wait
```

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u bestanden overbrengt tussen een lokaal bestands systeem en een container in Azure Blob Storage. Raadpleeg de zelfstudie voor het werken met Azure Blob Storage, voor meer informatie over het werken met blobs in Azure Storage.

> [!div class="nextstepaction"]
> [Procedure: Blob-opslagbewerkingen uitvoeren met Azure CLI](storage-how-to-use-blobs-cli.md)
