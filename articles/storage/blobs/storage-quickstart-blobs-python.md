---
title: 'Snelstart: Azure Blob-opslagbibliotheek v12 - Python'
description: In deze snelstart leert u hoe u de Azure Blob-opslagclientbibliotheekversie 12 voor Python gebruikt om een container en een blob in Blob-opslag (object) te maken. Hierna leert u hoe u de blob naar uw lokale computer downloadt en hoe u alle blobs in een container kunt weergeven.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/24/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: 8daf7380e859cd2f9b5890c716f7b7d95e6c3fe4
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80061339"
---
# <a name="quickstart-manage-blobs-with-python-v12-sdk"></a>Snelstart: blobs beheren met Python v12 SDK

In deze quickstart leer je blobs te beheren met Python. Blobs zijn objecten die grote hoeveelheden tekst of binaire gegevens kunnen bevatten, waaronder afbeeldingen, documenten, streamingmedia en archiefgegevens. Je uploadt, downloadt en lijstblobs en je maakt en verwijdert containers.

[API-naslagdocumentatie](/python/api/azure-storage-blob) | [Bibliotheekbroncodepakket](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob) | [(Python Package Index)](https://pypi.org/project/azure-storage-blob/) | [Voorbeelden](https://docs.microsoft.com/azure/storage/common/storage-samples-python?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak gratis een account aan.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- Een Azure Storage-account. [Een opslagaccount maken](../common/storage-account-create.md).
- [Python](https://www.python.org/downloads/) 2.7, 3.5 of hoger.

> [!NOTE]
> Zie [Snelstart: Blobs beheren met Python v2.1 SDK](storage-quickstart-blobs-python-legacy.md)om aan de slag te gaan met de vorige SDK-versie.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="setting-up"></a>Instellen

In deze sectie u een project voorbereiden om te werken met de Azure Blob-opslagclientbibliotheek v12 voor Python.

### <a name="create-the-project"></a>Het project maken

Maak een Python-toepassing met de naam *blob-quickstart-v12*.

1. Maak in een consolevenster (zoals cmd, PowerShell of Bash) een nieuwe map voor het project.

    ```console
    mkdir blob-quickstart-v12
    ```

1. Schakel over naar de nieuw gemaakte *blob-quickstart-v12-map.*

    ```console
    cd blob-quickstart-v12
    ```

1. Maak aan de zijkant van de *blob-quickstart-v12-map* een andere map met de naam *gegevens*. Dit is waar de blob-gegevensbestanden worden gemaakt en opgeslagen.

    ```console
    mkdir data
    ```

### <a name="install-the-package"></a>Het pakket installeren

Installeer de Azure Blob-opslagclientbibliotheek voor Python-pakket met `pip install` behulp van de opdracht terwijl u zich nog in de toepassingsmap bevindt.

```console
pip install azure-storage-blob
```

Met deze opdracht wordt de Azure Blob-opslagclientbibliotheek voor het Python-pakket en alle bibliotheken waarvan deze afhankelijk is geïnstalleerd. In dit geval is dat alleen de Azure-kernbibliotheek voor Python.

### <a name="set-up-the-app-framework"></a>Het app-framework instellen

In de projectmap:

1. Een nieuw tekstbestand openen in uw codeeditor
1. Instructies `import` toevoegen
1. De structuur voor het programma maken, inclusief basisafhandeling voor uitzonderingen

    Hier is de code:

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

1. Sla het nieuwe bestand op als *blob-quickstart-v12.py* in de *blob-quickstart-v12-map.*

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Objectmodel

Azure Blob-opslag is geoptimaliseerd voor het opslaan van enorme hoeveelheden ongestructureerde gegevens. Ongestructureerde gegevens zijn gegevens die niet voldoen aan een bepaald gegevensmodel of bepaalde definitie, zoals tekst of binaire gegevens. Er zijn drie typen resources voor blobopslag:

* Het opslagaccount
* Een container in het opslagaccount
* Een blob in de container

Het volgende diagram geeft de relatie tussen deze resources weer.

![Diagram van de blobopslagarchitectuur](./media/storage-blobs-introduction/blob1.png)

Gebruik de volgende Python-klassen om met deze bronnen te communiceren:

* [BlobServiceClient:](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) `BlobServiceClient` met de klasse u Azure Storage-bronnen en blob-containers manipuleren.
* [ContainerClient:](/python/api/azure-storage-blob/azure.storage.blob.containerclient) `ContainerClient` met de klasse u Azure Storage-containers en hun blobs manipuleren.
* [BlobClient:](/python/api/azure-storage-blob/azure.storage.blob.blobclient) `BlobClient` met de klasse u Azure Storage-blobs manipuleren.

## <a name="code-examples"></a>Codevoorbeelden

In deze voorbeeldcodefragmenten ziet u hoe u het volgende uitvoeren met de Azure Blob-opslagclientbibliotheek voor Python:

* [De verbindingsreeks ophalen](#get-the-connection-string)
* [Een container maken](#create-a-container)
* [Blobs uploaden naar een container](#upload-blobs-to-a-container)
* [De blobs in een container in een lijst weergeven](#list-the-blobs-in-a-container)
* [Blobs downloaden](#download-blobs)
* [Een container verwijderen](#delete-a-container)

### <a name="get-the-connection-string"></a>De verbindingsreeks ophalen

De onderstaande code haalt de verbindingstekenreeks voor het opslagaccount op uit de omgevingsvariabele die is gemaakt in de sectie [Uw opslagverbindingstekenreeks configureren.](#configure-your-storage-connection-string)

Voeg deze code `try` toe in het blok:

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

Bepaal een naam voor de nieuwe container. De onderstaande code voegt een UUID-waarde toe aan de containernaam om er zeker van te zijn dat deze uniek is.

> [!IMPORTANT]
> Containernamen moeten uit kleine letters bestaan. Zie [Containers, blobs en metagegevens een naam geven en hiernaar verwijderen](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) voor meer informatie over de naamgeving van containers en blobs.

Maak een instantie van de klasse [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) door de [from_connection_string-methode](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#from-connection-string-conn-str--credential-none----kwargs-) aan te roepen. Roep vervolgens de [create_container](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#create-container-name--metadata-none--public-access-none----kwargs-) methode aan om de container daadwerkelijk in uw opslagaccount te maken.

Voeg deze code toe `try` aan het einde van het blok:

```python
# Create the BlobServiceClient object which will be used to create a container client
blob_service_client = BlobServiceClient.from_connection_string(connect_str)

# Create a unique name for the container
container_name = "quickstart" + str(uuid.uuid4())

# Create the container
container_client = blob_service_client.create_container(container_name)
```

### <a name="upload-blobs-to-a-container"></a>Blobs uploaden naar een container

Het volgende codefragment:

1. Hiermee maakt u een tekstbestand in de lokale map.
1. Hier wordt een verwijzing naar een [BlobClient-object](/python/api/azure-storage-blob/azure.storage.blob.blobclient) weergegeven door de [get_blob_client-methode](/python/api/azure-storage-blob/azure.storage.blob.containerclient#get-blob-client-blob--snapshot-none-) aan te roepen op de [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) vanuit de sectie [Een container](#create-a-container) maken.
1. Uploadt het lokale tekstbestand naar de blob door de [upload_blob](/python/api/azure-storage-blob/azure.storage.blob.blobclient#upload-blob-data--blob-type--blobtype-blockblob---blockblob----length-none--metadata-none----kwargs-) methode aan te roepen.

Voeg deze code toe `try` aan het einde van het blok:

```python
# Create a file in local data directory to upload and download
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

Lijst van de blobs in de container door de [list_blobs](/python/api/azure-storage-blob/azure.storage.blob.containerclient#list-blobs-name-starts-with-none--include-none----kwargs-) methode aan te roepen. In dit geval is er slechts één blob aan de container toegevoegd, zodat de lijstbewerking alleen die ene blob retourneert.

Voeg deze code toe `try` aan het einde van het blok:

```python
print("\nListing blobs...")

# List the blobs in the container
blob_list = container_client.list_blobs()
for blob in blob_list:
    print("\t" + blob.name)
```

### <a name="download-blobs"></a>Blobs downloaden

Download de eerder gemaakte blob door de [download_blob](/python/api/azure-storage-blob/azure.storage.blob.blobclient#download-blob-offset-none--length-none----kwargs-) methode aan te roepen. De voorbeeldcode voegt een achtervoegsel van DOWNLOADEN toe aan de bestandsnaam, zodat u beide bestanden in het lokale bestandssysteem zien.

Voeg deze code toe `try` aan het einde van het blok:

```python
# Download the blob to a local file
# Add 'DOWNLOAD' before the .txt extension so you can see both files in the data directory
download_file_path = os.path.join(local_path, str.replace(local_file_name ,'.txt', 'DOWNLOAD.txt'))
print("\nDownloading blob to \n\t" + download_file_path)

with open(download_file_path, "wb") as download_file:
    download_file.write(blob_client.download_blob().readall())
```

### <a name="delete-a-container"></a>Een container verwijderen

Met de volgende code worden de resources die de app heeft gemaakt, gesruimd door de hele container te verwijderen met behulp van de [delete_container](/python/api/azure-storage-blob/azure.storage.blob.containerclient#delete-container---kwargs-) methode. U ook de lokale bestanden verwijderen, als u dat wilt.

De app pauzeert voor `input()` gebruikersinvoer door aan te roepen voordat de blob-, container- en lokale bestanden worden verwijderd. Dit is een goede kans om te controleren of de resources correct zijn gemaakt, voordat ze worden verwijderd.

Voeg deze code toe `try` aan het einde van het blok:

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

Deze app maakt een testbestand in uw lokale map en uploadt het naar Blob-opslag. In het voorbeeld worden vervolgens de blobs in de container weergegeven en wordt het bestand gedownload met een nieuwe naam, zodat u de oude en nieuwe bestanden vergelijken.

Navigeer naar de map met het *blob-quickstart-v12.py-bestand* en voer de volgende `python` opdracht uit om de app uit te voeren.

```console
python blob-quickstart-v12.py
```

De uitvoer van de app is vergelijkbaar met het volgende voorbeeld:

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

Controleer voordat u met het opschoneproces begint, de *gegevensmap* voor de twee bestanden. Als u ze opent, ziet u dat ze identiek zijn.

Nadat u de bestanden hebt geverifieerd, drukt u op **enter** om de testbestanden te verwijderen en de demo te voltooien.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart heb je geleerd hoe je blobs uploadt, downloadt en weermaakt met Python.

Ga verder met:

> [!div class="nextstepaction"]
> [Sdk v12 Python-voorbeelden voor Azure Blob-opslag](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob/samples)

* Zie de Azure [SDK voor Python voor](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/README.md)meer informatie.
* Ga naar [Azure for Python Developers voor](/azure/python/)zelfstudies, voorbeelden, quickstarts en andere documentatie.
