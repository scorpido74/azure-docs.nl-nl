---
title: Snelstart - Een blob maken met Azure CLI
titleSuffix: Azure Storage
description: In deze snelstart leert u hoe u de Azure CLI-upload een blob naar Azure Storage gebruiken, een blob downloaden en de blobs in een container weergeven.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.date: 02/26/2020
ms.author: tamram
ms.openlocfilehash: 2e1b1ac2ea315759b18dc882b98837bca0a84d46
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80061447"
---
# <a name="quickstart-create-download-and-list-blobs-with-azure-cli"></a>Snelstart: blobs maken, downloaden en weergeven met Azure CLI

Azure CLI is de nieuwe opdrachtregel van Azure voor het beheren van Azure-resources. U kunt deze gebruiken in uw browser met Azure Cloud Shell. U kunt deze ook installeren op Mac OS, Linux of Windows en uitvoeren vanaf de opdrachtregel. In deze quickstart leert u hoe u Azure CLI kunt gebruiken om gegevens naar en van Azure Blob-opslag te uploaden en te downloaden.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="install-the-azure-cli-locally"></a>De Azure CLI lokaal installeren

Als u ervoor kiest de Azure CLI lokaal te installeren en te gebruiken, moet u met deze quickstart de Azure CLI-versie 2.0.46 of hoger uitvoeren. Voer `az --version` uit om uw versie te bepalen. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli).

Als u de Azure CLI lokaal uitvoert, moet u zich aanmelden en verifiëren. Deze stap is niet nodig als u Azure Cloud Shell gebruikt. Als u zich wilt `az login` aanmelden bij Azure CLI, voert u het uit en verifieert u dit in het browservenster:

```azurecli
az login
```

Zie [Aanmelden bij Azure CLI voor](/cli/azure/authenticate-azure-cli)meer informatie over verificatie met Azure CLI.

## <a name="authorize-access-to-blob-storage"></a>Toegang tot Blob-opslag autoriseren

U de toegang tot Blob-opslag vanuit azure CLI autoriseren met Azure AD-referenties of met behulp van de toegangssleutel voor het opslagaccount. Het gebruik van Azure AD-referenties wordt aanbevolen. In dit artikel ziet u hoe u Blob-opslagbewerkingen autoriseert met Azure AD.

Azure CLI-opdrachten voor gegevensbewerkingen `--auth-mode` tegen Blob-opslag ondersteunen de parameter, waarmee u opgeven hoe u een bepaalde bewerking autoriseren. Stel `--auth-mode` de `login` parameter in om te autoriseren met Azure AD-referenties. Zie [Toegang tot blob- of wachtrijgegevens met Azure CLI autoriseren voor](../common/authorize-data-operations-cli.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)meer informatie.

Alleen blob-opslaggegevensbewerkingen ondersteunen de `--auth-mode` parameter. Beheerbewerkingen, zoals het maken van een resourcegroep of opslagaccount, gebruiken automatisch Azure AD-referenties voor autorisatie.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een Azure-resourcegroep met de opdracht [az group create](/cli/azure/group). Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd.

Vergeet niet om plaatsaanduidingswaarden in hoekhaakjes te vervangen door uw eigen waarden:

```azurecli
az group create \
    --name <resource-group> \
    --location <location>
```

## <a name="create-a-storage-account"></a>Een opslagaccount maken

Maak een algemeen opslagaccount met de opdracht [az storage account create](/cli/azure/storage/account). Het algemeen opslagaccount kan voor alle vier de services worden gebruikt: blobs, bestanden, tabellen en wachtrijen.

Vergeet niet om plaatsaanduidingswaarden in hoekhaakjes te vervangen door uw eigen waarden:

```azurecli
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_ZRS \
    --encryption blob
```

## <a name="create-a-container"></a>Een container maken

Blobs worden altijd naar een container geüpload. U groepen blobs in containers ordenen die vergelijkbaar zijn met de manier waarop u uw bestanden op uw computer in mappen ordent.

Gebruik de opdracht [az storage container create](/cli/azure/storage/container) om een container te maken voor het opslaan van blobs. Vergeet niet om plaatsaanduidingswaarden in hoekhaakjes te vervangen door uw eigen waarden:

```azurecli
az storage container create \
    --account-name <storage-account> \
    --name <container> \
    --auth-mode login
```

## <a name="upload-a-blob"></a>Een blob uploaden

Blob-opslag ondersteunt blok-blobs, toevoeg-blobs en pagina-blobs. De voorbeelden in deze quickstart laten zien hoe je met blokblobs werkt.

Maak eerst een bestand dat u wilt uploaden naar een blokblob. Als u Azure Cloud Shell gebruikt, gebruikt u de volgende opdracht om een bestand te maken:

```bash
vi helloworld
```

Wanneer het bestand wordt geopend, drukt u op **Invoegen**. Typ *Hallo wereld,* druk vervolgens op **Esc**. Typ vervolgens *:x*en druk op **Enter**.

In dit voorbeeld wordt met de opdracht [az storage blob upload](/cli/azure/storage/blob) een blob geüpload naar de container die u in de laatste stap hebt gemaakt. Het is niet nodig om een bestandspad op te geven omdat het bestand is gemaakt in de hoofdmap. Vergeet niet om plaatsaanduidingswaarden in hoekhaakjes te vervangen door uw eigen waarden:

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

Gebruik de opdracht [az storage blob list](/cli/azure/storage/blob) om de blobs in de container weer te geven. Vergeet niet om plaatsaanduidingswaarden in hoekhaakjes te vervangen door uw eigen waarden:

```azurecli
az storage blob list \
    --account-name <storage-account> \
    --container-name <container> \
    --output table \
    --auth-mode login
```

## <a name="download-a-blob"></a>Een blob downloaden

Gebruik de opdracht [az storage blob download](/cli/azure/storage/blob) om de blob te downloaden die u eerder hebt geüpload. Vergeet niet om plaatsaanduidingswaarden in hoekhaakjes te vervangen door uw eigen waarden:

```azurecli
az storage blob download \
    --account-name <storage-account> \
    --container-name <container> \
    --name helloworld \
    --file ~/destination/path/for/file \
    --auth-mode login
```

## <a name="data-transfer-with-azcopy"></a>Gegevensoverdracht met AzCopy

Het azcopy-hulpprogramma biedt krachtige, scriptbare gegevensoverdracht voor Azure Storage. U AzCopy gebruiken om gegevens over te zetten van en naar Blob-opslag en Azure-bestanden. Zie [Aan de slag met AzCopy](../common/storage-use-azcopy-v10.md)voor meer informatie over AzCopy v10, de nieuwste versie van AzCopy. Zie [Gegevens overbrengen met AzCopy- en Blob-opslag](../common/storage-use-azcopy-blobs.md)voor meer informatie over het gebruik van AzCopy v10 met Blob-opslag.

In het volgende voorbeeld wordt AzCopy gebruikt om een lokaal bestand naar een blob te uploaden. Vergeet niet om de voorbeeldwaarden te vervangen door uw eigen waarden:

```bash
azcopy login
azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u de resources die u hebt gemaakt als onderdeel van deze quickstart, inclusief het opslagaccount, wilt verwijderen, verwijdert u de brongroep met behulp van de opdracht Verwijderen van de [AZ-groep.](/cli/azure/group) Vergeet niet om plaatsaanduidingswaarden in hoekhaakjes te vervangen door uw eigen waarden:

```azurecli
az group delete \
    --name <resource-group> \
    --no-wait
```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u bestanden overbrengen tussen een lokaal bestandssysteem en een container in Azure Blob-opslag. Raadpleeg de zelfstudie voor het werken met Azure Blob Storage, voor meer informatie over het werken met blobs in Azure Storage.

> [!div class="nextstepaction"]
> [Procedure: Blob-opslagbewerkingen uitvoeren met Azure CLI](storage-how-to-use-blobs-cli.md)
