---
title: Snelstart over het beheren van Azure-bestandsshares met Azure CLI
description: In deze snelstart leert u hoe Azure CLI gebruikt om Azure Files te beheren.
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 10/26/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 95d7abca27ec9db46a72140bc8a61b2841c63fcb
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77598558"
---
# <a name="quickstart-create-and-manage-azure-file-shares-using-azure-cli"></a>Snelstart: Azure-bestandsshares maken en beheren met Azure CLI
In deze handleiding worden de basisbeginselen besproken van het werken met [Azure-bestandsshares](storage-files-introduction.md) met behulp van Azure CLI. Azure-bestandsshares zijn net als andere bestandsshares, maar worden in de cloud opgeslagen en ondersteund op het Azure-platform. Azure-bestandsshares ondersteunen het SMB-protocol volgens de industriestandaard en bieden de mogelijkheid bestanden te delen tussen meerdere machines, toepassingen en exemplaren. 

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om Azure CLI lokaal te installeren en te gebruiken, moet u voor de stappen in dit artikel Azure CLI-versie 2.0.4 of hoger uitvoeren. Voer **az --version** uit om uw Azure CLI-versie te vinden. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren](/cli/azure/install-azure-cli). 

Azure CLI-opdrachten retourneren standaard JavaScript Object Notation (JSON). JSON is de standaardmethode voor het verzenden en ontvangen van berichten van REST API's. Om te kunnen werken met JSON-antwoorden, wordt in enkele van de voorbeelden in dit artikel de parameter *query* gebruikt in de Azure CLI-opdrachten. Deze parameter gebruikt de [JMESPath-querytaal](http://jmespath.org/) voor het parseren van JSON. Raadpleeg de [zelfstudie over JMESPath](http://jmespath.org/tutorial.html) voor meer informatie over hoe u de resultaten van Azure CLI-opdrachten kunt gebruiken door de JMESPath-querytaal te volgen.

## <a name="create-a-resource-group"></a>Een resourcegroep maken
Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. Als u nog geen Azure-resourcegroep hebt, kunt u met de opdracht [az group create](/cli/azure/group) een groep maken. 

In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* op de locatie *West US 2 ge:*

```azurecli-interactive 
export resourceGroupName="myResourceGroup"
region="westus2"

az group create \
    --name $resourceGroupName \
    --location $region \
    --output none
```

## <a name="create-a-storage-account"></a>Een opslagaccount maken
Een opslagaccount is een gedeelde opslaggroep waarin u Azure-bestandsshares of andere opslagresources, zoals blobs of wachtrijen, kunt implementeren. Een opslagaccount kan een onbeperkt aantal bestandsshares bevatten. Een share kan een onbeperkt aantal bestanden opslaan, tot de capaciteitslimiet van het opslagaccount.

In het volgende voorbeeld wordt een opslagaccount gemaakt met de opdracht [AZ-opslagaccount.](/cli/azure/storage/account) Namen van opslagaccounts moeten uniek zijn. Gebruik `$RANDOM` om een nummer toe te voegen aan de naam en deze zo uniek te maken.

```azurecli-interactive 
export storageAccountName="mystorageacct$RANDOM"

az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --location $region \
    --kind StorageV2 \
    --sku Standard_LRS \
    --enable-large-file-share \
    --output none
```

> [!Note]  
> Aandelen groter dan 5 TiB (maximaal 100 TiB per aandeel) zijn alleen beschikbaar in lokaal redundante (LRS) en zone redundante (ZRS) opslagaccounts. Als u een georedundant (GRS) of geo-zone-redundante (GZRS)-opslagaccount wilt maken, verwijdert u de `--enable-large-file-share` parameter.

### <a name="get-the-storage-account-key"></a>Opslagaccountsleutel opvragen
Opslagaccountsleutels regelen de toegang tot resources in een opslagaccount. Wanneer u een opslagaccount maakt, worden de sleutels automatisch gemaakt. U kunt de opslagaccountsleutels voor uw opslagaccount opvragen met de opdracht [az storage account keys list](/cli/azure/storage/account/keys): 

```azurecli-interactive 
export storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')
```

## <a name="create-an-azure-file-share"></a>Een Azure-bestandsshare maken
U kunt nu uw eerste Azure-bestandsshare maken. U kunt bestandsshares maken met de opdracht [az storage share create](/cli/azure/storage/share). In dit voorbeeld wordt een Azure-bestandsshare gemaakt met de naam *myshare*: 

```azurecli-interactive
shareName="myshare"

az storage share create \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --name $shareName \
    --quota 1024 \
    --output none
```

De namen van shares mogen alleen kleine letters, cijfers en afbreekstreepjes bevatten, maar mogen niet met een afbreekstreepje beginnen. Zie [Aandelen, mappen, bestanden en metagegevens benoemen en verwijzen](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata)voor meer informatie over het benoemen van bestandsshares en bestanden.

## <a name="use-your-azure-file-share"></a>Uw Azure-bestandsshare gebruiken
Azure Files biedt twee methoden voor het werken met bestanden en mappen in uw Azure-bestandsshare: het [SMB-protocol (Server Message Block)](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) volgens de industriestandaard en het [File REST-protocol](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api). 

Zie het volgende document op basis van uw besturingssysteem om een bestandsshare met SMB te koppelen:
- [Linux](storage-how-to-use-files-linux.md)
- [Macos](storage-how-to-use-files-mac.md)
- [Windows](storage-how-to-use-files-windows.md)

### <a name="using-an-azure-file-share-with-the-file-rest-protocol"></a>Een Azure-bestandsshare gebruiken met het File REST-protocol 
Het is mogelijk om direct met het File REST-protocol te werken (het bewerken van REST HTTP roept uzelf), maar de meest voorkomende manier om het File REST-protocol te gebruiken, is door de Azure CLI, de [Azure PowerShell-module](storage-how-to-use-files-powershell.md)of een Azure Storage SDK te gebruiken, die allemaal een mooie wrapper rond het File REST-protocol bieden in de scripting/programmeertaal van uw keuze.  

In de meeste gevallen gebruikt u Azure Files en de Azure-bestandsshare via het SMB-protocol, omdat dit u de mogelijkheid biedt om de bestaande toepassingen en hulpprogramma's te gebruiken die u verwacht te kunnen gebruiken, maar er zijn diverse redenen waarom het gebruik van de File REST-API in plaats van SMB voordelen oplevert, zoals:

- U bladert door uw bestandsshare via de Azure Bach Cloud Shell (die geen ondersteuning biedt voor het koppelen van bestandsshares via SMB).
- U profiteert van serverloze resources, zoals [Azure Functions](../../azure-functions/functions-overview.md). 
- U maakt een service voor toegevoegde waarde die met veel Azure-bestandsshares wordt gebruikt, zoals het uitvoeren van back-up- of antivirusscans.

In de volgende voorbeelden ziet u hoe u Azure CLI gebruikt voor het bewerken van een Azure-bestandsshare met het File REST-protocol. 

### <a name="create-a-directory"></a>Een map maken
Als u in de hoofdmap van uw Azure-bestandsshare een nieuwe map wilt maken met de naam *myDirectory*, gebruikt u de opdracht [`az storage directory create`](/cli/azure/storage/directory):

```azurecli-interactive
az storage directory create \
   --account-name $storageAccountName \
   --account-key $storageAccountKey \
   --share-name $shareName \
   --name "myDirectory" \
   --output none
```

### <a name="upload-a-file"></a>Bestand uploaden
Als u wilt laten zien [`az storage file upload`](/cli/azure/storage/file) hoe u een bestand uploadt met de opdracht, maakt u eerst een bestand dat u wilt uploaden op het krasstation van Cloud Shell. In het volgende voorbeeld maakt u het bestand en uploadt u het bestand:

```azurecli-interactive
cd ~/clouddrive/
date > SampleUpload.txt

az storage file upload \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --source "SampleUpload.txt" \
    --path "myDirectory/SampleUpload.txt"
```

Als u Azure CLI lokaal uitvoert, vervangt u `~/clouddrive` door een bestaand pad op uw computer.

Nadat u het bestand hebt [`az storage file list`](/cli/azure/storage/file) geüpload, u de opdracht gebruiken om ervoor te zorgen dat het bestand is geüpload naar uw Azure-bestandsshare:

```azurecli-interactive
az storage file list \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --path "myDirectory" \
    --output table
```

### <a name="download-a-file"></a>Bestand downloaden
U [`az storage file download`](/cli/azure/storage/file) de opdracht gebruiken om een kopie te downloaden van het bestand dat u hebt geüpload naar het krasstation van Cloud Shell:

```azurecli-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists, because you've run this example before
rm -f SampleDownload.txt

az storage file download \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --path "myDirectory/SampleUpload.txt" \
    --dest "SampleDownload.txt" \
    --output none
```

### <a name="copy-files"></a>Bestanden kopiëren
Een veelvoorkomende taak is het kopiëren van bestanden van het ene bestand delen naar een ander bestand delen. Maak een nieuwe share, zodat u deze functionaliteit ziet. Kopieer het bestand dat u hebt geüpload naar deze nieuwe share met behulp van de opdracht [az storage file copy](/cli/azure/storage/file/copy): 

```azurecli-interactive
otherShareName="myshare2"

az storage share create \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --name $otherShareName \
    --quota 1024 \
    --output none

az storage directory create \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $otherShareName \
    --name "myDirectory2" \
    --output none

az storage file copy start \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --source-share $shareName \
    --source-path "myDirectory/SampleUpload.txt" \
    --destination-share $otherShareName \
    --destination-path "myDirectory2/SampleCopy.txt"
```

Als u nu de bestanden in de nieuwe share opvraagt, ziet u het gekopieerde bestand:

```azurecli-interactive
az storage file list \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $otherShareName \
    --path "myDirectory2" \
    --output table
```

Hoewel `az storage file copy start` de opdracht handig is voor bestandsverplaatsingen tussen Azure-bestandsshares, `rsync` voor migraties en `robocopy` grotere gegevensbewegingen, raden we aan op macOS en Linux en op Windows. `rsync`en `robocopy` gebruik SMB om de gegevensbewegingen uit te voeren in plaats van de FileREST API.

## <a name="create-and-manage-share-snapshots"></a>Momentopnamen van shares maken en beheren
Een andere handige taak die u kunt doen met een Azure-bestandsshare is het maken van een momentopname van de share. Een momentopname bevat voor een specifiek moment de actuele inhoud van een Azure-bestandsshare. Momentopnamen van een share zijn vergelijkbaar met bepaalde technologieën van besturingssystemen die u mogelijk al kent, zoals:

- [Logische Volume Manager (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) snapshots voor Linux-systemen.
- [APFS-snapshots (Apple File System)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) voor macOS.
- [(Volume Shadow Copy Service) (VSS)](https://docs.microsoft.com/windows/desktop/VSS/volume-shadow-copy-service-portal) voor Windows-bestandssystemen, zoals NTFS en ReFS.
 
U een momentopname voor [`az storage share snapshot`](/cli/azure/storage/share) delen maken met de opdracht:

```azurecli-interactive
snapshot=$(az storage share snapshot \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --name $shareName \
    --query "snapshot" | tr -d '"')
```

### <a name="browse-share-snapshot-contents"></a>Bladeren door inhoud van share-momentopnamen
U kunt door de inhoud van een momentopname van een share bladeren door het tijdstempel van de momentopname dat u hebt vastgelegd in de variabele `$snapshot`, door te geven aan de opdracht `az storage file list`:

```azurecli-interactive
az storage file list \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --snapshot $snapshot \
    --output table
```

### <a name="list-share-snapshots"></a>Momentopnamen van shares opvragen
Gebruik de volgende opdracht om een lijst weer te geven met de momentopnamen die u hebt gemaakt voor de share:

```azurecli-interactive
az storage share list \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --include-snapshot \
    --query "[? name== '$shareName' && snapshot!=null].snapshot" \
    --output tsv
```

### <a name="restore-from-a-share-snapshot"></a>Bestand herstellen vanuit een share-momentopname
U kunt een bestand herstellen met behulp van de opdracht `az storage file copy start` die u eerder hebt gebruikt. Verwijder eerst het SampleUpload.txt-bestand dat u hebt geüpload, zodat u dit kunt herstellen vanuit de momentopname:

```azurecli-interactive
# Delete SampleUpload.txt
az storage file delete \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --path "myDirectory/SampleUpload.txt" \
    --output none

# Build the source URI for a snapshot restore
URI=$(az storage account show \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --query "primaryEndpoints.file" | tr -d '"')

URI=$URI$shareName"/myDirectory/SampleUpload.txt?sharesnapshot="$snapshot

# Restore SampleUpload.txt from the share snapshot
az storage file copy start \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --source-uri $URI \
    --destination-share $shareName \
    --destination-path "myDirectory/SampleUpload.txt"
```

### <a name="delete-a-share-snapshot"></a>Een share-momentopname verwijderen
U een momentopname voor [`az storage share delete`](/cli/azure/storage/share) delen verwijderen met behulp van de opdracht. Gebruik de variabele die de verwijzing `$SNAPSHOT` naar de `--snapshot`-parameter bevat:

```azurecli-interactive
az storage share delete \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --name $shareName \
    --snapshot $snapshot \
    --output none
```

## <a name="clean-up-resources"></a>Resources opschonen
Wanneer u klaar bent, [`az group delete`](/cli/azure/group) kunt u de opdracht gebruiken om de brongroep en alle gerelateerde bronnen te verwijderen: 

```azurecli-interactive 
az group delete --name $resourceGroupName
```

U kunt resources ook afzonderlijk verwijderen.
- De Azure-bestandsshares verwijderen die u voor dit artikel hebt gemaakt:

    ```azurecli-interactive
    az storage share list \
            --account-name $storageAccountName \
            --account-key $storageAccountKey \
            --query "[].name" \
            --output tsv | \
        xargs -L1 bash -ec '\
            az storage share delete \
                --account-name "$storageAccountName" \
                --account-key "$storageAccountKey" \
                --name $0 \
                --delete-snapshots include \
                --output none'
    ```

- Het opslagaccount zelf verwijderen. (Dit verwijdert impliciet de Azure-bestandsshares en alle andere opslagresources, zoals een Azure Blob-opslagcontainer, die u hebt gemaakt.)

    ```azurecli-interactive
    az storage account delete \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --yes
    ```

## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [Wat is Azure Files?](storage-files-introduction.md)