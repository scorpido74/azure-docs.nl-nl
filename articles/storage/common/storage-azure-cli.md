---
title: De Azure CLI gebruiken met Azure Storage | Microsoft Docs
description: Informatie over het gebruik van de Azure-opdracht regel interface (Azure CLI) met Azure Storage om opslag accounts te maken en te beheren en te werken met Azure-blobs en-bestanden.
services: storage
author: tamram
ms.service: storage
ms.devlang: azurecli
ms.topic: article
ms.date: 06/02/2017
ms.author: tamram
ms.reviewer: seguler
ms.subservice: common
ms.openlocfilehash: 46ae70bf4f1c2fe0276a3327ff37650dd57341d0
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70259390"
---
# <a name="using-the-azure-cli-with-azure-storage"></a>De Azure CLI gebruiken met Azure Storage

De open-source, platformoverschrijdende Azure CLI biedt een reeks opdrachten voor het werken met het Azure-platform. Het biedt veel van de functionaliteit die wordt aangetroffen in de [Azure Portal](https://portal.azure.com), waaronder Rich Data Access.

In deze hand leiding wordt uitgelegd hoe u de [Azure cli](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2) gebruikt voor het uitvoeren van verschillende taken die werken met resources in uw Azure Storage-account. We raden u aan de meest recente versie van de CLI te downloaden en installeren of te upgraden voordat u deze hand leiding gebruikt.

In de voor beelden in de hand leiding wordt uitgegaan van het gebruik van de bash-shell op Ubuntu, maar andere platforms moeten op dezelfde manier worden uitgevoerd. 

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

[!INCLUDE [storage-cli-versions](../../../includes/storage-cli-versions.md)]

## <a name="prerequisites"></a>Vereisten
In deze hand leiding wordt ervan uitgegaan dat u bekend bent met de basis concepten van Azure Storage. Er wordt ook van uitgegaan dat u voldoet aan de vereisten voor het maken van het account die hieronder zijn opgegeven voor Azure en de Storage-service.

### <a name="accounts"></a>Accounts
* **Azure-account**: Als u nog geen Azure-abonnement hebt, [maakt u een gratis Azure-account](https://azure.microsoft.com/free/).
* **Opslagaccount**: Zie [een opslag account maken](storage-quickstart-create-account.md) in [over Azure-opslag accounts](storage-create-storage-account.md).

### <a name="install-the-azure-cli"></a>Azure-CLI installeren

Down load en installeer de Azure CLI door de instructies te volgen die worden beschreven in [de Azure cli installeren](/cli/azure/install-az-cli2).

> [!TIP]
> Als u problemen ondervindt met de installatie, raadpleegt u de sectie [problemen met de installatie](/cli/azure/install-az-cli2) van het artikel en de hand leiding voor het [oplossen van problemen](https://github.com/Azure/azure-cli/blob/master/doc/install_troubleshooting.md) met github.
>

## <a name="working-with-the-cli"></a>Werken met de CLI

Nadat u de CLI hebt geïnstalleerd, kunt u de `az` opdracht in de opdracht regel interface (bash, Terminal, opdracht prompt) gebruiken om toegang te krijgen tot de Azure cli-opdrachten. Typ de `az` opdracht voor een volledige lijst met basis opdrachten (de volgende voorbeeld uitvoer is afgekapt):

```
     /\
    /  \    _____   _ _ __ ___
   / /\ \  |_  / | | | \'__/ _ \
  / ____ \  / /| |_| | | |  __/
 /_/    \_\/___|\__,_|_|  \___|


Welcome to the cool new Azure CLI!

Here are the base commands:

    account          : Manage subscriptions.
    acr              : Manage Azure container registries.
    acs              : Manage Azure Container Services.
    ad               : Synchronize on-premises directories and manage Azure Active Directory
                       resources.
    ...
```

Voer in de opdracht regel interface de opdracht `az storage --help` uit om de subgroepen van de `storage` opdracht weer te geven. De beschrijvingen van de subgroepen bieden een overzicht van de functionaliteit die de Azure CLI biedt voor het werken met uw opslag resources.

```
Group
    az storage: Durable, highly available, and massively scalable cloud storage.

Subgroups:
    account  : Manage storage accounts.
    blob     : Object storage for unstructured data.
    container: Manage blob storage containers.
    cors     : Manage Storage service Cross-Origin Resource Sharing (CORS).
    directory: Manage file storage directories.
    entity   : Manage table storage entities.
    file     : File shares that use the standard SMB 3.0 protocol.
    logging  : Manage Storage service logging information.
    message  : Manage queue storage messages.
    metrics  : Manage Storage service metrics.
    queue    : Use queues to effectively scale applications according to traffic.
    share    : Manage file shares.
    table    : NoSQL key-value storage using semi-structured datasets.
```

## <a name="connect-the-cli-to-your-azure-subscription"></a>De CLI koppelen aan uw Azure-abonnement

Als u wilt werken met de resources in uw Azure-abonnement, moet u zich eerst aanmelden bij uw `az login`Azure-account met. Er zijn verschillende manieren om u aan te melden:

* **Interactieve aanmelding**:`az login`
* **Meld u aan met de gebruikers naam en het wacht woord**:`az login -u johndoe@contoso.com -p VerySecret`
  * Dit werkt niet met micro soft-accounts of-accounts die gebruikmaken van multi-factor Authentication.
* **Meld u aan met een Service-Principal**:`az login --service-principal -u http://azure-cli-2016-08-05-14-31-15 -p VerySecret --tenant contoso.onmicrosoft.com`

## <a name="azure-cli-sample-script"></a>Azure CLI-voorbeeld script

Vervolgens werken we met een klein shell-script dat enkele eenvoudige Azure CLI-opdrachten uitgeeft om te communiceren met Azure Storage-resources. Het script maakt eerst een nieuwe container in uw opslagaccount en uploadt vervolgens een bestaand bestand (als blob) naar die container. Vervolgens worden alle blobs in de container weer gegeven en wordt het bestand gedownload naar een bestemming op uw lokale computer die u opgeeft.

```bash
#!/bin/bash
# A simple Azure Storage example script

export AZURE_STORAGE_ACCOUNT=<storage_account_name>
export AZURE_STORAGE_KEY=<storage_account_key>

export container_name=<container_name>
export blob_name=<blob_name>
export file_to_upload=<file_to_upload>
export destination_file=<destination_file>

echo "Creating the container..."
az storage container create --name $container_name

echo "Uploading the file..."
az storage blob upload --container-name $container_name --file $file_to_upload --name $blob_name

echo "Listing the blobs..."
az storage blob list --container-name $container_name --output table

echo "Downloading the file..."
az storage blob download --container-name $container_name --name $blob_name --file $destination_file --output table

echo "Done"
```

**Het script configureren en uitvoeren**

1. Open uw favoriete tekst editor en kopieer en plak het voor gaande script in de editor.

2. Werk vervolgens de variabelen van het script bij om uw configuratie-instellingen weer te geven. Vervang de volgende waarden zoals opgegeven:

   * storage_account_name de naam van uw opslag account. **\<\>**
   * storage_account_key de primaire of secundaire toegangs sleutel voor uw opslag account. **\<\>**
   * container_name een naam van de nieuwe container die moet worden gemaakt, zoals ' Azure-cli-sample-container '. **\<\>**
   * blob_name een naam voor de doel-Blob in de container. **\<\>**
   * file_to_upload het pad naar het kleine bestand op uw lokale computer, zoals "~/images/HelloWorld.png". **\<\>**
   * destination_file het pad van het doel bestand, zoals "~/downloadedImage.png". **\<\>**

3. Nadat u de benodigde variabelen hebt bijgewerkt, slaat u het script op en sluit u de editor af. Bij de volgende stappen wordt ervan uitgegaan dat u de naam van het script **my_storage_sample. sh**.

4. Markeer het script als uitvoerbaar als dat nodig is:`chmod +x my_storage_sample.sh`

5. Voer het script uit. Bijvoorbeeld in bash:`./my_storage_sample.sh`

De uitvoer ziet er ongeveer als volgt uit en de **\<destination_file\>** die u in het script hebt opgegeven, moet op de lokale computer worden weer gegeven.

```
Creating the container...
{
  "created": true
}
Uploading the file...
Percent complete: %100.0
Listing the blobs...
Name       Blob Type      Length  Content Type              Last Modified
---------  -----------  --------  ------------------------  -------------------------
README.md  BlockBlob        6700  application/octet-stream  2017-05-12T20:54:59+00:00
Downloading the file...
Name
---------
README.md
Done
```

> [!TIP]
> De voor gaande uitvoer is in **tabel** indeling. U kunt opgeven welke uitvoer indeling moet worden gebruikt door het `--output` argument in uw cli-opdrachten op te geven of het `az configure`globaal in te stellen met.
>

## <a name="manage-storage-accounts"></a>Opslagaccounts beheren

### <a name="create-a-new-storage-account"></a>Een nieuw opslagaccount maken
U hebt een opslagaccount nodig om Azure Storage te gebruiken. U kunt een nieuw Azure Storage-account maken nadat u uw computer hebt geconfigureerd om verbinding te maken met uw abonnement.

```azurecli
az storage account create \
    --location <location> \
    --name <account_name> \
    --resource-group <resource_group> \
    --sku <account_sku>
```

* `--location`[Vereist]: Locatie. Bijvoorbeeld ' vs-West '.
* `--name`[Vereist]: De naam van het opslagaccount. De naam moet 3 tot 24 tekens lang zijn en mag alleen kleine letters alfanumerieke tekens bevatten.
* `--resource-group`[Vereist]: De naam van de resource groep.
* `--sku`[Vereist]: De SKU van het opslag account. Toegestane waarden:
  * `Premium_LRS`
  * `Standard_GRS`
  * `Standard_LRS`
  * `Standard_RAGRS`
  * `Standard_ZRS`
  * `Standard_GZRS`evaluatie
  * `Standard_RAGZRS`evaluatie

### <a name="set-default-azure-storage-account-environment-variables"></a>Standaard omgevings variabelen van Azure Storage-account instellen

U kunt meerdere opslag accounts in uw Azure-abonnement hebben. Als u een van de waarden voor alle volgende opslag opdrachten wilt gebruiken, kunt u deze omgevings variabelen instellen:

Geef eerst uw opslagaccountsleutels weer met behulp van de opdracht [az storage account keys list](/cli/azure/storage/account/keys):

```azurecli-interactive
az storage account keys list \
    --account-name <account_name> \
    --resource-group <resource_group> \
    --output table
```

Nu u de sleutel hebt, kunt u deze definiëren en de account naam opgeven als omgevings variabelen:

```azurecli
export AZURE_STORAGE_ACCOUNT=<account_name>
export AZURE_STORAGE_KEY=<key>
```

Een andere manier om een standaard-opslag account in te stellen, is met behulp van een connection string. Haal eerst de Connection String op met de `show-connection-string` opdracht:

```azurecli
az storage account show-connection-string \
    --name <account_name> \
    --resource-group <resource_group>
```

Kopieer vervolgens de uitvoer Connection String en stel de `AZURE_STORAGE_CONNECTION_STRING` omgevings variabele in (mogelijk moet u de Connection String tussen aanhalings tekens plaatsen):

```azurecli
export AZURE_STORAGE_CONNECTION_STRING="<connection_string>"
```

> [!NOTE]
> Voor alle voor beelden in de volgende secties van dit artikel wordt ervan uitgegaan `AZURE_STORAGE_ACCOUNT` dat `AZURE_STORAGE_KEY` u de omgevings variabelen en hebt ingesteld.

## <a name="create-and-manage-blobs"></a>Blobs maken en beheren
Azure Blob-opslag is een service voor het opslaan van grote hoeveel heden ongestructureerde gegevens, zoals tekst of binaire gegevens, die overal ter wereld toegankelijk zijn via HTTP of HTTPS. In deze sectie wordt ervan uitgegaan dat u al bekend bent met Azure Blob Storage-concepten. Zie voor meer informatie aan de [slag met Azure Blob Storage met behulp](../blobs/storage-dotnet-how-to-use-blobs.md) van de concepten van .net en [BLOB-service](/rest/api/storageservices/blob-service-concepts).

### <a name="create-a-container"></a>Een container maken
Elke Blob in azure Storage moet zich in een container bevinden. U kunt een container maken met behulp `az storage container create` van de opdracht:

```azurecli
az storage container create --name <container_name>
```

U kunt een van de drie Lees toegang voor een nieuwe container instellen door het optionele `--public-access` argument op te geven:

* `off`(standaard instelling): Container gegevens zijn privé voor de account eigenaar.
* `blob`: Open bare Lees toegang voor blobs.
* `container`: Open bare Lees-en lijst toegang tot de volledige container.

Zie [Anonieme leestoegang tot containers en blobs beheren](../blobs/storage-manage-access-to-resources.md) voor meer informatie.

### <a name="upload-a-blob-to-a-container"></a>Een blob uploaden naar een container
Azure Blob-opslag ondersteunt blok-, toevoeg-en pagina-blobs. Upload blobs naar een container met behulp `blob upload` van de opdracht:

```azurecli
az storage blob upload \
    --file <local_file_path> \
    --container-name <container_name> \
    --name <blob_name>
```

Als u rechtstreeks wilt uploaden naar een map in de container in uw opslag account, vervangt `--name <blob_name>` `--name <folder/blob_name>`u door.

 De `blob upload` opdracht uploadt standaard *. VHD-bestanden naar pagina-blobs of blok-blobs. Als u een ander type wilt opgeven wanneer u een BLOB uploadt, `--type` kunt u het argument-- `append`toegestane `block`waarden gebruiken `page`, en.

 Zie voor meer informatie over de verschillende typen blobs ' [blok-blobs, toevoeg-blobs en pagina-blobs](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs)'.


### <a name="download-a-blob-from-a-container"></a>Een blob downloaden uit een container
In dit voor beeld ziet u hoe u een BLOB kunt downloaden uit een container:

```azurecli
az storage blob download \
    --container-name mycontainer \
    --name myblob.png \
    --file ~/mydownloadedblob.png
```

### <a name="list-the-blobs-in-a-container"></a>De blobs in een container in een lijst weergeven

Geef de blobs in een container weer met de opdracht [AZ Storage BLOB List](/cli/azure/storage/blob) .

```azurecli
az storage blob list \
    --container-name mycontainer \
    --output table
```

### <a name="copy-blobs"></a>Blobs kopiëren
U kunt blobs binnen of tussen opslagaccounts en regio's asynchroon kopiëren.

In het volgende voorbeeld ziet u hoe u blobs van één opslagaccount naar een ander kopieert. Eerst wordt er een container in het bronopslagaccount gemaakt en openbare leestoegang opgegeven voor de blobs. Daarna wordt er een bestand naar de container geüpload en wordt de blob vanuit die container naar een container in het doelopslagaccount gekopieerd.

```azurecli
# Create container in source account
az storage container create \
    --account-name sourceaccountname \
    --account-key sourceaccountkey \
    --name sourcecontainer \
    --public-access blob

# Upload blob to container in source account
az storage blob upload \
    --account-name sourceaccountname \
    --account-key sourceaccountkey \
    --container-name sourcecontainer \
    --file ~/Pictures/sourcefile.png \
    --name sourcefile.png

# Copy blob from source account to destination account (destcontainer must exist)
az storage blob copy start \
    --account-name destaccountname \
    --account-key destaccountkey \
    --destination-blob destfile.png \
    --destination-container destcontainer \
    --source-uri https://sourceaccountname.blob.core.windows.net/sourcecontainer/sourcefile.png
```

In het bovenstaande voor beeld moet de doel container al aanwezig zijn in het doel opslag account om de Kopieer bewerking te kunnen volt ooien. Bovendien moet de bron-blob die in het argument `--source-uri` is opgegeven, een SAS-token (Shared Access Signature) bevatten of openbaar toegankelijk zijn, zoals in dit voorbeeld.

### <a name="delete-a-blob"></a>Een blob verwijderen
Als u een BLOB wilt verwijderen, `blob delete` gebruikt u de opdracht:

```azurecli
az storage blob delete --container-name <container_name> --name <blob_name>
```

### <a name="set-the-content-type"></a>Type bestandsinhoud instellen

Het type inhoud, ook wel het MIME-type genoemd, geeft de indeling aan van de gegevens in de blob. Browsers en andere software maken gebruik van het type inhoud om te bepalen hoe de gegevens verwerkt moeten worden. Het inhouds type voor PNG-afbeeldingen is `image/png`bijvoorbeeld. Als u het inhouds type wilt instellen, `blob update` gebruikt u de opdracht:

```azurecli
az storage blob update
    --container-name <container_name> 
    --name <blob_name>
    --content-type <content_type>
```

## <a name="create-and-manage-file-shares"></a>Bestands shares maken en beheren
Azure Files biedt gedeelde opslag voor toepassingen die gebruikmaken van het SMB-protocol (Server Message Block). Microsoft Azure virtuele machines en Cloud Services, en on-premises toepassingen, kunnen bestands gegevens delen via gekoppelde shares. U kunt bestands shares en bestands gegevens beheren via de Azure CLI. Zie [Inleiding tot Azure files](../files/storage-files-introduction.md)voor meer informatie over Azure files.

### <a name="create-a-file-share"></a>Een bestandsshare maken
Een Azure-bestands share is een SMB-bestands share in Azure. Alle mappen en bestanden moeten worden gemaakt in een bestands share. Een account kan een onbeperkt aantal shares bevatten en een share kan een onbeperkt aantal bestanden opslaan, tot aan de capaciteits limieten van het opslag account. In het volgende voor beeld wordt een bestands share met de naam **MyShare**gemaakt.

```azurecli
az storage share create --name myshare
```

### <a name="create-a-directory"></a>Een map maken
Een Directory biedt een hiërarchische structuur in een Azure-bestands share. In het volgende voor beeld wordt een map gemaakt met de naam **myDir** in de bestands share.

```azurecli
az storage directory create --name myDir --share-name myshare
```

Een mappad kan meerdere niveaus bevatten, bijvoorbeeld **dir1/dir2**. U moet er echter voor zorgen dat alle bovenliggende directory's bestaan voordat u een submap maakt. Voor het pad **dir1/dir2**moet u eerst Directory **dir1**maken en vervolgens Directory- **dir2**maken.

### <a name="upload-a-local-file-to-a-share"></a>Een lokaal bestand uploaden naar een share
In het volgende voor beeld wordt een bestand geüpload van **~/temp/samplefile.txt** naar de hoofdmap van de **MyShare** -bestands share. Met `--source` het argument geeft u het bestaande lokale bestand op dat moet worden geüpload.

```azurecli
az storage file upload --share-name myshare --source ~/temp/samplefile.txt
```

Net als bij het maken van mappen kunt u een mappad binnen de share opgeven om het bestand te uploaden naar een bestaande map in de share:

```azurecli
az storage file upload --share-name myshare/myDir --source ~/temp/samplefile.txt
```

Een bestand in de share kan Maxi maal 1 TB groot zijn.

### <a name="list-the-files-in-a-share"></a>De bestanden in een share weer geven
U kunt bestanden en mappen in een share weer geven met behulp van de `az storage file list` opdracht:

```azurecli
# List the files in the root of a share
az storage file list --share-name myshare --output table

# List the files in a directory within a share
az storage file list --share-name myshare/myDir --output table

# List the files in a path within a share
az storage file list --share-name myshare --path myDir/mySubDir/MySubDir2 --output table
```

### <a name="copy-files"></a>Bestanden kopiëren      
U kunt een bestand kopiëren naar een ander bestand, een bestand naar een BLOB of een BLOB naar een bestand. Als u bijvoorbeeld een bestand wilt kopiëren naar een map in een andere share:        
        
```azurecli
az storage file copy start \
--source-share share1 --source-path dir1/file.txt \
--destination-share share2 --destination-path dir2/file.txt     
```

## <a name="create-share-snapshot"></a>Moment opname van de share maken
U kunt een moment opname van een share maken `az storage share snapshot` met behulp van de opdracht:

```cli
az storage share snapshot -n <share name>
```

Voorbeeld uitvoer
```json
{
  "metadata": {},
  "name": "<share name>",
  "properties": {
    "etag": "\"0x8D50B7F9A8D7F30\"",
    "lastModified": "2017-10-04T23:28:22+00:00",
    "quota": null
  },
  "snapshot": "2017-10-04T23:28:35.0000000Z"
}
```

### <a name="list-share-snapshots"></a>Momentopnamen van shares opvragen

U kunt moment opnamen van shares van een bepaalde share weer geven met`az storage share list --include-snapshots`

```cli
az storage share list --include-snapshots
```

**Voorbeeld uitvoer**
```json
[
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": "2017-10-04T19:44:13.0000000Z"
  },
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": "2017-10-04T19:45:18.0000000Z"
  },
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": null
  }
]
```

### <a name="browse-share-snapshots"></a>Bladeren door momentopnamen van shares
U kunt ook naar een bepaalde moment opname van `az storage file list`een share bladeren om de inhoud ervan te bekijken. Een moet de share naam `--share-name <snare name>` en de tijds tempel opgeven`--snapshot '2017-10-04T19:45:18.0000000Z'`

```azurecli-interactive
az storage file list --share-name sharesnapshotdefs --snapshot '2017-10-04T19:45:18.0000000Z' -otable
```

**Voorbeeld uitvoer**
```
Name            Content Length    Type    Last Modified
--------------  ----------------  ------  ---------------
HelloWorldDir/                    dir
IMG_0966.JPG    533568            file
IMG_1105.JPG    717711            file
IMG_1341.JPG    608459            file
IMG_1405.JPG    652156            file
IMG_1611.JPG    442671            file
IMG_1634.JPG    1495999           file
IMG_1635.JPG    974058            file

```
### <a name="restore-from-share-snapshots"></a>Herstellen vanaf moment opnamen van shares

U kunt een bestand herstellen door een bestand te kopiëren of te downloaden van een moment `az storage file download` opname van een share met behulp van de opdracht

```azurecli-interactive
az storage file download --path IMG_0966.JPG --share-name sharesnapshotdefs --snapshot '2017-10-04T19:45:18.0000000Z'
```
**Voorbeeld uitvoer**
```
{
  "content": null,
  "metadata": {},
  "name": "IMG_0966.JPG",
  "properties": {
    "contentLength": 533568,
    "contentRange": "bytes 0-533567/533568",
    "contentSettings": {
      "cacheControl": null,
      "contentDisposition": null,
      "contentEncoding": null,
      "contentLanguage": null,
      "contentType": "application/octet-stream"
    },
    "copy": {
      "completionTime": null,
      "id": null,
      "progress": null,
      "source": null,
      "status": null,
      "statusDescription": null
    },
    "etag": "\"0x8D50B5F49F7ACDF\"",
    "lastModified": "2017-10-04T19:37:03+00:00",
    "serverEncrypted": true
  }
}
```
## <a name="delete-share-snapshot"></a>Moment opname van share verwijderen
U kunt een moment opname van een share verwijderen `az storage share delete` met behulp `--snapshot` van de opdracht door een para meter met de time stamp van de moment opname van share

```cli
az storage share delete -n <share name> --snapshot '2017-10-04T23:28:35.0000000Z' 
```

Voorbeeld uitvoer
```json
{
  "deleted": true
}
```

## <a name="next-steps"></a>Volgende stappen
Hier volgen enkele aanvullende bronnen voor meer informatie over het werken met de Azure CLI. 

* [Aan de slag met Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)
* [Naslag informatie over Azure CLI-opdrachten](/cli/azure)
* [Azure CLI op GitHub](https://github.com/Azure/azure-cli)
