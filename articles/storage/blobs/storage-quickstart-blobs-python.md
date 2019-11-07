---
title: 'Snelstartgids: Azure Blob-opslag bibliotheek V12-python'
description: In deze Quick Start leert u hoe u de Azure Blob Storage-client bibliotheek versie 12 voor python kunt gebruiken om een container en een BLOB in Blob-opslag (object) te maken. Hierna leert u hoe u de blob naar uw lokale computer downloadt en hoe u alle blobs in een container kunt weergeven.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/05/2019
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: 0851ee9061fcb01a5e007f0cc4989d6a3febc665
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73605154"
---
# <a name="quickstart-azure-blob-storage-client-library-v12-for-python"></a>Snelstartgids: Azure Blob Storage-client bibliotheek V12 voor python

Ga aan de slag met de Azure Blob Storage-client bibliotheek V12 voor python. Azure Blob Storage is Microsoft's oplossing voor opslag van objecten in de cloud. Volg de stappen om het pakket te installeren en voorbeeld code voor basis taken uit te proberen. Blob Storage is geoptimaliseerd voor het opslaan van grote hoeveelheden ongestructureerde gegevens.

> [!NOTE]
> Om aan de slag te gaan met de vorige SDK-versie, raadpleegt u [Quick Start: client bibliotheek voor Azure Blob Storage voor python](storage-quickstart-blobs-python-legacy.md).

Gebruik de Azure Blob Storage-client bibliotheek voor het volgende:

* Een container maken
* Een BLOB uploaden naar Azure Storage
* Alle blobs in een container weer geven
* De BLOB downloaden naar uw lokale computer
* Een container verwijderen

[API-referentie documentatie](/python/api/azure-storage-blob) | [bibliotheek bron code](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob) | [pakket (Python-pakket index)](https://pypi.org/project/azure-storage-blob/) | voor [beelden](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob/samples)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/)
* Azure Storage-account: [een opslag account maken](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* [Python](https://www.python.org/downloads/) voor uw besturings systeem-2,7, 3,5 of hoger

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
1. De structuur voor het programma maken, inclusief zeer eenvoudige uitzonde ringen verwerken

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

### <a name="copy-your-credentials-from-the-azure-portal"></a>Kopieer uw referenties van de Azure Portal

Wanneer de voorbeeld toepassing een aanvraag indient om Azure Storage, moet deze worden geautoriseerd. Als u een aanvraag wilt autoriseren, voegt u de referenties van uw opslag account toe aan de toepassing als een connection string. U kunt de referenties van het opslagaccount weergeven door de volgende stappen te volgen:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).
2. Zoek uw opslagaccount.
3. In de sectie **Instellingen** van het overzicht met opslagaccounts selecteert u **Toegangssleutels**. Hier worden de toegangssleutels van uw account weergegeven, evenals de volledige verbindingsreeks voor elke sleutel.
4. Zoek de waarde van de **Verbindingsreeks** onder **key1** en selecteer de knop **Kopiëren** om de verbindingsreeks te kopiëren. U gaat in de volgende stap de waarde voor de verbinding toevoegen aan een omgevingsvariabele.

    ![Schermopname waarin een verbindingsreeks vanuit de Azure-portal wordt gekopieerd](../../../includes/media/storage-copy-connection-string-portal/portal-connection-string.png)

### <a name="configure-your-storage-connection-string"></a>De opslagverbindingsreeks configureren

Nadat u de verbindingsreeks hebt gekopieerd, schrijft u deze naar een nieuwe omgevingsvariabele op de lokale computer waarop de toepassing wordt uitgevoerd. Als u de omgevingsvariabele wilt instellen, opent u een consolevenster en volgt u de aanwijzingen voor uw besturingssysteem. Vervang `<yourconnectionstring>` door de daad werkelijke connection string.

#### <a name="windows"></a>Windows

```cmd
setx CONNECT_STR "<yourconnectionstring>"
```

Nadat u de omgevings variabele in Windows hebt toegevoegd, moet u een nieuw exemplaar van het opdracht venster starten.

#### <a name="linux"></a>Linux

```bash
export CONNECT_STR="<yourconnectionstring>"
```

#### <a name="macos"></a>macOS

```bash
export CONNECT_STR="<yourconnectionstring>"
```

#### <a name="restart-programs"></a>Program ma's opnieuw starten

Nadat u de omgevings variabele hebt toegevoegd, start u alle actieve Program ma's die moeten worden gelezen van de omgevings variabele opnieuw. Start uw ontwikkel omgeving of editor bijvoorbeeld opnieuw op voordat u doorgaat.

## <a name="object-model"></a>Object model

Azure Blob-opslag is geoptimaliseerd voor het opslaan van enorme hoeveel heden ongestructureerde gegevens. Ongestructureerde gegevens zijn gegevens die niet voldoen aan een bepaald gegevensmodel of bepaalde definitie, zoals tekst of binaire gegevens. Er zijn drie typen resources voor blobopslag:

* Het opslag account
* Een container in het opslag account
* Een BLOB in de container

Het volgende diagram geeft de relatie tussen deze resources weer.

![Diagram van de blobopslagarchitectuur](./media/storage-blob-introduction/blob1.png)

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
# running the application called CONNECT_STR. If the environment variable is
# created after the application is launched in a console or with Visual Studio,
# the shell or application needs to be closed and reloaded to take the
# environment variable into account.
connect_str = os.getenv('CONNECT_STR')
```

### <a name="create-a-container"></a>Een container maken

Kies een naam voor de nieuwe container. De onderstaande code voegt een UUID-waarde toe aan de container naam om ervoor te zorgen dat deze uniek is.

> [!IMPORTANT]
> Containernamen moeten uit kleine letters bestaan. Zie [Containers, blobs en metagegevens een naam geven en hiernaar verwijderen](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) voor meer informatie over de naamgeving van containers en blobs.

Maak een instantie van de klasse [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) door de methode [from_connection_string](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#from-connection-string-conn-str--credential-none----kwargs-) aan te roepen. Vervolgens roept u de [create_container](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#create-container-name--metadata-none--public-access-none----kwargs-) -methode aan om de container in uw opslag account te maken.

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
1. Hiermee wordt een verwijzing naar een [BlobClient](/python/api/azure-storage-blob/azure.storage.blob.blobclient) -object opgehaald door de methode [Get_blob_client](/python/api/azure-storage-blob/azure.storage.blob.containerclient#get-blob-client-blob--snapshot-none-) op de [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) aan te roepen vanuit de sectie [een container maken](#create-a-container) .
1. Hiermee wordt het lokale tekst bestand geüpload naar de BLOB door de methode [upload_blob](/python/api/azure-storage-blob/azure.storage.blob.blobclient#upload-blob-data--blob-type--blobtype-blockblob---blockblob----length-none--metadata-none----kwargs-) aan te roepen.

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

Vermeld de blobs in de container door de methode [list_blobs](/python/api/azure-storage-blob/azure.storage.blob.containerclient#list-blobs-name-starts-with-none--include-none----kwargs-) aan te roepen. In dit geval is er slechts één BLOB aan de container toegevoegd, zodat de vermelding in de lijst alleen die ene BLOB retourneert.

Voeg deze code toe aan het einde van het `try` blok:

```python
print("\nListing blobs...")

# List the blobs in the container
blob_list = container_client.list_blobs()
for blob in blob_list:
    print("\t" + blob.name)
```

### <a name="download-blobs"></a>Blobs downloaden

Down load de eerder gemaakte BLOB door de methode [download_blob](/python/api/azure-storage-blob/azure.storage.blob.blobclient#download-blob-offset-none--length-none----kwargs-) aan te roepen. De voorbeeld code voegt het achtervoegsel ' downloaden ' toe aan de naam van het bestand, zodat u beide bestanden in het lokale bestands systeem kunt zien.

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

Met de volgende code wordt de resources opgeschoond die de app heeft gemaakt door de volledige container te verwijderen met de methode [delete_container](/python/api/azure-storage-blob/azure.storage.blob.containerclient#delete-container---kwargs-) . U kunt ook de lokale bestanden verwijderen, indien gewenst.

De app wordt gepauzeerd voor gebruikers invoer door `input()` aan te roepen voordat de blob, container en lokale bestanden worden verwijderd. Dit is een goede kans om te controleren of de resources daad werkelijk zijn gemaakt, voordat ze worden verwijderd.

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
* Voor zelf studies, voor beelden, snel starten en andere documentatie gaat u naar [Azure voor python-ontwikkel aars](/azure/python/).
