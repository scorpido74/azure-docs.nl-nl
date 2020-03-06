---
title: 'Snelstartgids: Azure Blob-opslag bibliotheek V12-python'
description: In deze Quick Start leert u hoe u de Azure Blob Storage-client bibliotheek versie 12 voor python kunt gebruiken om een container en een BLOB in Blob-opslag (object) te maken. Hierna leert u hoe u de blob naar uw lokale computer downloadt en hoe u alle blobs in een container kunt weergeven.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/24/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: 19a35a6586b04818c09190899714345d21e0088d
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78330750"
---
# <a name="quickstart-manage-blobs-with-python-v12-sdk"></a>Quick Start: blobs beheren met python V12 SDK

In deze Quick Start leert u hoe u blobs beheert met behulp van python. Blobs zijn objecten die grote hoeveel heden tekst of binaire gegevens kunnen bevatten, zoals afbeeldingen, documenten, streaming media en gegevens archivering. U kunt blobs uploaden, downloaden en vermelden en u kunt containers maken en verwijderen.

[API-referentie documentatie](/python/api/azure-storage-blob) | [bibliotheek bron code](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob) | [pakket (Python-pakket index)](https://pypi.org/project/azure-storage-blob/) | voor [beelden](https://docs.microsoft.com/azure/storage/common/storage-samples-python?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak gratis een account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Een Azure Storage-account. [Een opslagaccount maken](../common/storage-account-create.md).
- [Python](https://www.python.org/downloads/) 2,7, 3,5 of hoger.

> [!NOTE]
> Zie [Quick Start: blobs beheren met python v 2.1 SDK](storage-quickstart-blobs-python-legacy.md)om aan de slag te gaan met de vorige SDK-versie.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="setting-up"></a>Instellen

In deze sectie wordt uitgelegd hoe u een project voorbereidt dat werkt met de Azure Blob Storage-client bibliotheek V12 voor python.

### <a name="create-the-project"></a>Het project maken

Maak een python-toepassing *met de naam BLOB-Quick Start-V12*.

1. Maak in een console venster (zoals cmd, Power shell of bash) een nieuwe map voor het project.

    ```console
    mkdir blob-quickstart-v12
    ```

1. Schakel over naar de zojuist gemaakte map *BLOB-Quick Start-V12* .

    ```console
    cd blob-quickstart-v12
    ```

1. Maak in de Directory *BLOB-Quick Start-V12* een andere map met de naam *Data*. Hier worden de BLOB-gegevens bestanden gemaakt en opgeslagen.

    ```console
    mkdir data
    ```

### <a name="install-the-package"></a>Het pakket installeren

Terwijl u nog steeds in de toepassingsmap, installeert u de Azure Blob Storage-client bibliotheek voor python-pakket met behulp van de `pip install` opdracht.

```console
pip install azure-storage-blob
```

Met deze opdracht wordt de Azure Blob Storage-client bibliotheek voor python-pakket en alle bibliotheken waarvan deze afhankelijk is, geïnstalleerd. In dit geval is dat alleen de Azure core-bibliotheek voor python.

### <a name="set-up-the-app-framework"></a>Het app-Framework instellen

Vanuit de projectmap:

1. Een nieuw tekst bestand openen in de code-editor
1. `import`-instructies toevoegen
1. De structuur voor het programma maken, met inbegrip van basis afhandeling van uitzonde ringen

    Hier volgt de code:

    ```python
    import os, uuid
    from azure.storage.blob import BlobServiceClient, BlobClient, ContainerClient

    try:
        print("Azure Blob storage v12 - Python quickstart sample")
        # Quick start code goes here
    except Exception as ex:
        print('Exception:')
        print(ex)
    ```

1. Sla het nieuwe bestand op als *BLOB-QuickStart-V12.py* in de map *BLOB-Quick Start-V12* .

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Object model

Azure Blob-opslag is geoptimaliseerd voor het opslaan van enorme hoeveel heden ongestructureerde gegevens. Ongestructureerde gegevens zijn gegevens die niet voldoen aan een bepaald gegevensmodel of bepaalde definitie, zoals tekst of binaire gegevens. Er zijn drie typen resources voor blobopslag:

* Het opslag account
* Een container in het opslag account
* Een BLOB in de container

Het volgende diagram geeft de relatie tussen deze resources weer.

![Diagram van de blobopslagarchitectuur](./media/storage-blobs-introduction/blob1.png)

Gebruik de volgende python-klassen om te communiceren met deze resources:

* [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient): met de klasse `BlobServiceClient` kunt u Azure storage resources en BLOB-containers bewerken.
* [ContainerClient](/python/api/azure-storage-blob/azure.storage.blob.containerclient): met de klasse `ContainerClient` kunt u Azure Storage containers en de bijbehorende blobs bewerken.
* [BlobClient](/python/api/azure-storage-blob/azure.storage.blob.blobclient): met de klasse `BlobClient` kunt u Azure Storage blobs bewerken.

## <a name="code-examples"></a>Code voorbeelden

In deze voorbeeld code fragmenten ziet u hoe u het volgende kunt uitvoeren met de Azure Blob Storage-client bibliotheek voor python:

* [De connection string ophalen](#get-the-connection-string)
* [Een container maken](#create-a-container)
* [Blobs uploaden naar een container](#upload-blobs-to-a-container)
* [De blobs in een container weer geven](#list-the-blobs-in-a-container)
* [Blobs downloaden](#download-blobs)
* [Container verwijderen](#delete-a-container)

### <a name="get-the-connection-string"></a>De verbindingsreeks ophalen

De onderstaande code haalt de connection string voor het opslag account op uit de omgevings variabele die in de sectie [uw opslag Connection String configureren](#configure-your-storage-connection-string) is gemaakt.

Voeg deze code toe binnen het `try` blok:

```python
# Retrieve the connection string for use with the application. The storage
# connection string is stored in an environment variable on the machine
# running the application called AZURE_STORAGE_CONNECTION_STRING. If the environment variable is
# created after the application is launched in a console or with Visual Studio,
# the shell or application needs to be closed and reloaded to take the
# environment variable into account.
connect_str = os.getenv('AZURE_STORAGE_CONNECTION_STRING')
```

### <a name="create-a-container"></a>Een container maken

Kies een naam voor de nieuwe container. De onderstaande code voegt een UUID-waarde toe aan de container naam om ervoor te zorgen dat deze uniek is.

> [!IMPORTANT]
> Containernamen moeten uit kleine letters bestaan. Zie [Containers, blobs en metagegevens een naam geven en hiernaar verwijderen](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) voor meer informatie over de naamgeving van containers en blobs.

Maak een instantie van de klasse [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) door de [from_connection_string](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#from-connection-string-conn-str--credential-none----kwargs-) -methode aan te roepen. Vervolgens roept u de methode [create_container](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#create-container-name--metadata-none--public-access-none----kwargs-) aan om de container in uw opslag account te maken.

Voeg deze code toe aan het einde van het `try` blok:

```python
# Create the BlobServiceClient object which will be used to create a container client
blob_service_client = BlobServiceClient.from_connection_string(connect_str)

# Create a unique name for the container
container_name = "quickstart" + str(uuid.uuid4())

# Create the container
container_client = blob_service_client.create_container(container_name)
```

### <a name="upload-blobs-to-a-container"></a>Blobs uploaden naar een container

Het volgende code fragment:

1. Hiermee maakt u een tekst bestand in de lokale map.
1. Hiermee wordt een verwijzing naar een [BlobClient](/python/api/azure-storage-blob/azure.storage.blob.blobclient) -object opgehaald door de methode [get_blob_client](/python/api/azure-storage-blob/azure.storage.blob.containerclient#get-blob-client-blob--snapshot-none-) aan te roepen in de [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) van de sectie [Create a container](#create-a-container) .
1. Hiermee wordt het lokale tekst bestand geüpload naar de BLOB door de [upload_blob](/python/api/azure-storage-blob/azure.storage.blob.blobclient#upload-blob-data--blob-type--blobtype-blockblob---blockblob----length-none--metadata-none----kwargs-) methode aan te roepen.

Voeg deze code toe aan het einde van het `try` blok:

```python
# Create a file in local Documents directory to upload and download
local_path = "./data"
local_file_name = "quickstart" + str(uuid.uuid4()) + ".txt"
upload_file_path = os.path.join(local_path, local_file_name)

# Write text to the file
file = open(upload_file_path, 'w')
file.write("Hello, World!")
file.close()

# Create a blob client using the local file name as the name for the blob
blob_client = blob_service_client.get_blob_client(container=container_name, blob=local_file_name)

print("\nUploading to Azure Storage as blob:\n\t" + local_file_name)

# Upload the created file
with open(upload_file_path, "rb") as data:
    blob_client.upload_blob(data)
```

### <a name="list-the-blobs-in-a-container"></a>De blobs in een container in een lijst weergeven

Vermeld de blobs in de container door de [list_blobs](/python/api/azure-storage-blob/azure.storage.blob.containerclient#list-blobs-name-starts-with-none--include-none----kwargs-) methode aan te roepen. In dit geval is er slechts één BLOB aan de container toegevoegd, zodat de vermelding in de lijst alleen die ene BLOB retourneert.

Voeg deze code toe aan het einde van het `try` blok:

```python
print("\nListing blobs...")

# List the blobs in the container
blob_list = container_client.list_blobs()
for blob in blob_list:
    print("\t" + blob.name)
```

### <a name="download-blobs"></a>Blobs downloaden

Down load de eerder gemaakte BLOB door de [download_blob](/python/api/azure-storage-blob/azure.storage.blob.blobclient#download-blob-offset-none--length-none----kwargs-) methode aan te roepen. De voorbeeld code voegt het achtervoegsel ' downloaden ' toe aan de naam van het bestand, zodat u beide bestanden in het lokale bestands systeem kunt zien.

Voeg deze code toe aan het einde van het `try` blok:

```python
# Download the blob to a local file
# Add 'DOWNLOAD' before the .txt extension so you can see both files in Documents
download_file_path = os.path.join(local_path, str.replace(local_file_name ,'.txt', 'DOWNLOAD.txt'))
print("\nDownloading blob to \n\t" + download_file_path)

with open(download_file_path, "wb") as download_file:
    download_file.write(blob_client.download_blob().readall())
```

### <a name="delete-a-container"></a>Een container verwijderen

Met de volgende code wordt de resources opgeschoond die de app heeft gemaakt door de volledige container te verwijderen met behulp van de [delete_container](/python/api/azure-storage-blob/azure.storage.blob.containerclient#delete-container---kwargs-) methode. U kunt ook de lokale bestanden verwijderen, indien gewenst.

De app wordt gepauzeerd voor gebruikers invoer door `input()` aan te roepen voordat de blob, container en lokale bestanden worden verwijderd. Dit is een goede kans om te controleren of de resources correct zijn gemaakt voordat ze worden verwijderd.

Voeg deze code toe aan het einde van het `try` blok:

```python
# Clean up
print("\nPress the Enter key to begin clean up")
input()

print("Deleting blob container...")
container_client.delete_container()

print("Deleting the local source and downloaded files...")
os.remove(upload_file_path)
os.remove(download_file_path)

print("Done")
```

## <a name="run-the-code"></a>De code uitvoeren

Met deze app wordt een test bestand gemaakt in uw lokale map en geüpload naar Blob Storage. In het voor beeld worden de blobs in de container weer gegeven en wordt het bestand met een nieuwe naam gedownload, zodat u de oude en nieuwe bestanden kunt vergelijken.

Ga naar de map met het *BLOB-QuickStart-V12.py* -bestand en voer de volgende `python` opdracht uit om de app uit te voeren.

```console
python blob-quickstart-v12.py
```

De uitvoer van de app is vergelijkbaar met het volgende voor beeld:

```output
Azure Blob storage v12 - Python quickstart sample

Uploading to Azure Storage as blob:
        quickstartcf275796-2188-4057-b6fb-038352e35038.txt

Listing blobs...
        quickstartcf275796-2188-4057-b6fb-038352e35038.txt

Downloading blob to
        ./data/quickstartcf275796-2188-4057-b6fb-038352e35038DOWNLOAD.txt

Press the Enter key to begin clean up

Deleting blob container...
Deleting the local source and downloaded files...
Done
```

Controleer voordat u het opschoon proces start de map *documenten* voor de twee bestanden. Als u ze opent, ziet u dat ze identiek zijn.

Nadat u de bestanden hebt gecontroleerd, drukt u op **Enter** om de test bestanden te verwijderen en de demo te volt ooien.

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u blobs kunt uploaden, downloaden en vermelden met behulp van python.

Als u voor beeld-apps voor Blob-opslag wilt zien, gaat u door naar:

> [!div class="nextstepaction"]
> [Azure Blob Storage SDK V12 python-voor beelden](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob/samples)

* Zie de [Azure SDK voor python voor](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/README.md)meer informatie.
* Voor zelf studies, voor beelden, Quick starts en andere documentatie gaat u naar [Azure voor python-ontwikkel aars](/azure/python/).
