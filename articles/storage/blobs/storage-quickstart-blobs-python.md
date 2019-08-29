---
title: 'Azure-snelstart: Een blob maken in objectopslag met Python | Microsoft Docs'
description: In deze snelstart maakt u een opslagaccount en een container in object(blob)-opslag. Vervolgens gebruikt u de opslagclientbibliotheek voor Python om een blob in Azure Storage te uploaden, een blob te downloaden en de blobs in een container te vermelden.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/29/2019
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: 059e41b744ad92cdec9057e4a17f470b5b769c62
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142971"
---
# <a name="quickstart-upload-download-and-list-blobs-with-python"></a>Quickstart: Blobs downloaden, uploaden en vermelden met behulp van Python

In deze Snelstartgids ziet u hoe u python kunt gebruiken om blok-blobs te uploaden, te downloaden en weer te geven in een container in Azure Blob Storage. Blobs zijn simpelweg objecten die een wille keurige hoeveelheid tekst of binaire gegevens kunnen bevatten (zoals afbeeldingen, documenten, streaming media, Archief gegevens enzovoort) en die verschillen in Azure Storage van bestands shares, schema's zonder tabellen en berichten wachtrijen. (Zie [Inleiding tot Azure Storage](/azure/storage/common/storage-introduction)voor meer informatie.)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Zorg dat u de volgende aanvullende vereisten hebt geïnstalleerd:

* [Python](https://www.python.org/downloads/)
* [Azure Storage SDK voor Python](https://github.com/Azure/azure-sdk-for-python)

## <a name="download-the-sample-application"></a>De voorbeeldtoepassing downloaden
De [voorbeeldtoepassing](https://github.com/Azure-Samples/storage-blobs-python-quickstart.git) in deze snelstart is een Python-basistoepassing.  

Gebruik [git](https://git-scm.com/) om een kopie van de toepassing naar uw ontwikkelomgeving te downloaden. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-python-quickstart.git 
```

Met deze opdracht wordt de opslagplaats *Azure-Samples/storage-blobs-python-quickstart* gekloond naar uw lokale git-map. Als u het Python-programma wilt uitvoeren, opent u het bestand *example.py* in de hoofdmap van de opslagplaats.  

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>De opslagverbindingsreeks configureren
Geef in de toepassing de naam en accountsleutel van uw opslagaccount op om een `BlockBlobService`-object te maken. Open het bestand *example.py* vanuit de Solution Explorer in uw IDE. Vervang de waarden van `accountname` en `accountkey` door uw accountnaam en -sleutel. 

```python
block_blob_service = BlockBlobService(
    account_name='accountname', account_key='accountkey')
```

## <a name="run-the-sample"></a>De voorbeeldtoepassing uitvoeren
In dit voorbeeld wordt een testbestand gemaakt in de map *Documenten*. Door het voorbeeldprogramma uit te voeren, wordt een testbestand geüpload naar de Blob-opslag, worden de blobs in the container vermeld en wordt het bestand met een nieuwe naam gedownload. 

Installeer eerst de afhankelijkheden door `pip install` uit te voeren:

```python
    pip install azure-storage-blob
```

Voer vervolgens het voorbeeld uit. U ziet berichten die vergelijkbaar zijn met de volgende uitvoer:
  
```output
Temp file = C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

Uploading to Blob storage as blobQuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

List blobs in the container
         Blob name: QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

Downloading blob to C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078_DOWNLOADED.txt
```
Kijk voordat u verder gaat of de map *Documenten* de twee bestanden bevat. Als u ze opent, ziet u dat ze hetzelfde zijn.

U kunt ook een hulpprogramma zoals [Azure Storage Explorer](https://storageexplorer.com) gebruiken om de bestanden in de Blob-opslag te bekijken. Azure Storage Explorer is een gratis hulpprogramma voor meerdere platforms waarmee u toegang hebt tot de gegevens van uw opslagaccount. 

Nadat u de bestanden hebt gecontroleerd, drukt u op een willekeurige toets om de demo te voltooien en de testbestanden te verwijderen. Nu u weet wat het voorbeeldprogramma doet, opent u het bestand *example.py* om de code te bekijken. 

## <a name="understand-the-sample-code"></a>De voorbeeldcode begrijpen

Nu doorlopen we de voorbeeldcode, zodat u begrijpt hoe deze werkt.

### <a name="get-references-to-the-storage-objects"></a>Verwijzingen naar de opslagobjecten ophalen
Eerst maakt u verwijzingen naar objecten die worden gebruikt om toegang te krijgen tot de Blob-opslag en deze te beheren. Deze objecten worden boven op elkaar gebouwd en elk ervan wordt door de volgende in de lijst gebruikt.

* Maak een exemplaar van het object **BlockBlobService** dat naar de Blob-service in uw opslagaccount verwijst. 

* Maak een exemplaar van het **CloudBlobContainer**-object dat de container vertegenwoordigt die u wilt openen. Containers worden gebruikt om uw blobs te organiseren op een wijze die lijkt op het gebruik van mappen op uw computer om uw bestanden te organiseren.

Wanneer u de Cloud Blob-container hebt, instantieert u het **CloudBlockBlob**-object dat wijst naar de specifieke blob waarin u bent geïnteresseerd. Daarna kunt u de blob uploaden, downloaden en kopiëren wanneer dat nodig is.

> [!IMPORTANT]
> Containernamen moeten uit kleine letters bestaan. Zie [Naming and referencing Containers, Blobs, and Metadata](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Containers, blobs en metagegevens een naam geven en ernaar verwijzen) voor meer informatie over namen van containers en blobs.

In deze sectie gaat u exemplaren maken van de objecten, een nieuwe container maken en vervolgens machtigingen instellen voor de container, zodat de blobs openbaar zijn. De container heeft de naam **quickstartblobs**. 

```python
# Create the BlockBlockService that is used to call the Blob service for the storage account.
block_blob_service = BlockBlobService(
    account_name='accountname', account_key='accountkey')

# Create a container called 'quickstartblobs'.
container_name = 'quickstartblobs'
block_blob_service.create_container(container_name)

# Set the permission so the blobs are public.
block_blob_service.set_container_acl(
    container_name, public_access=PublicAccess.Container)
```
### <a name="upload-blobs-to-the-container"></a>Blobs uploaden naar de container

Blob-opslag ondersteunt blok-blobs, toevoeg-blobs en pagina-blobs. Blok-blobs worden het meest gebruikt, en dat is wat er wordt gebruikt in deze quickstart.  

Als u een bestand naar een blob wilt uploaden, hebt u het volledige bestandspad nodig. U verkrijgt dit door de naam van de map en de bestandsnaam op uw lokale schijf samen te voegen. Daarna kunt u het bestand met de methode `create_blob_from_path` uploaden naar het opgegeven pad. 

Met de voorbeeld code wordt een lokaal bestand gemaakt dat moet worden gebruikt voor het uploaden en downloaden. Sla het bestand op dat moet worden geüpload als *full_path_to_file* en de naam van de BLOB als *local_file_name*. In het volgende voorbeeld wordt het bestand geüpload naar de container met de naam **quickstartblobs**.

```python
# Create a file in Documents to test the upload and download.
local_path = os.path.expanduser("~\Documents")
local_file_name = "QuickStart_" + str(uuid.uuid4()) + ".txt"
full_path_to_file = os.path.join(local_path, local_file_name)

# Write text to the file.
file = open(full_path_to_file, 'w')
file.write("Hello, World!")
file.close()

print("Temp file = " + full_path_to_file)
print("\nUploading to Blob storage as blob" + local_file_name)

# Upload the created file, use local_file_name for the blob name.
block_blob_service.create_blob_from_path(
    container_name, local_file_name, full_path_to_file)
```

Er zijn meerdere uploadmethoden die u kunt gebruiken met Blob-opslag. Als u bijvoorbeeld een geheugenstroom hebt, kunt u de methode `create_blob_from_stream` gebruiken in plaats van `create_blob_from_path`. 

Blok-blobs kunnen tot 4,7 TB groot zijn en kunnen van alles zijn: van Excel-spreadsheets tot grote videobestanden. Pagina-blobs worden hoofdzakelijk gebruikt voor de VHD-bestanden die worden gebruikt als back-up voor IaaS-VM's. Toevoeg-blobs worden gebruikt voor logboekregistratie, bijvoorbeeld wanneer u gegevens wilt wegschrijven naar een bestand en vervolgens gegevens wilt blijven toevoegen. De meeste objecten die worden opgeslagen in Blob-opslag, zijn blok-blobs.

### <a name="list-the-blobs-in-a-container"></a>De blobs in een container in een lijst weergeven

Haal een lijst met bestanden op in de container met behulp van de methode `list_blobs`. Deze methode retourneert een generator. Met de volgende code wordt de lijst met blobs opgehaald&mdash;en doorlopen&mdash;waarbij de namen van de blobs worden weergegeven die in een container zijn aangetroffen.  

```python
# List the blobs in the container.
print("\nList blobs in the container")
generator = block_blob_service.list_blobs(container_name)
for blob in generator:
    print("\t Blob name: " + blob.name)
```

### <a name="download-the-blobs"></a>De blobs downloaden

Down load blobs naar uw lokale schijf met `get_blob_to_path` behulp van de methode. Met de volgende code wordt een blob gedownload die in een vorige sectie is geüpload. *_DOWNLOADED* wordt toegevoegd als achtervoegsel aan de naam van de blob, zodat u beide bestanden op de lokale schijf kunt zien. 

```python
# Download the blob(s).
# Add '_DOWNLOADED' as prefix to '.txt' so you can see both files in Documents.
full_path_to_file2 = os.path.join(local_path, local_file_name.replace(
   '.txt', '_DOWNLOADED.txt'))
print("\nDownloading blob to " + full_path_to_file2)
block_blob_service.get_blob_to_path(
    container_name, local_file_name, full_path_to_file2)
```

### <a name="clean-up-resources"></a>Resources opschonen
Als u de blobs die in deze snelstart zijn geüpload niet langer nodig hebt, kunt u de volledige container verwijderen met de methode `delete_container`. Als u in plaats daarvan afzonderlijke bestanden wilt verwijderen, gebruikt u de methode `delete_blob`.

```python
# Clean up resources. This includes the container and the temp files.
block_blob_service.delete_container(container_name)
os.remove(full_path_to_file)
os.remove(full_path_to_file2)
```
## <a name="resources-for-developing-python-applications-with-blobs"></a>Resources voor het ontwikkelen van Python-toepassingen met blobs

Raadpleeg de volgende aanvullende resources voor meer informatie over Python-ontwikkeling met Blob-opslag:

### <a name="binaries-and-source-code"></a>Binaire bestanden en broncode

- Bekijk, download en installeer de [broncode voor de Python-clientbibliotheek](https://github.com/Azure/azure-storage-python) voor Azure Storage op GitHub.

### <a name="client-library-reference-and-samples"></a>Naslaginformatie en voorbeelden voor de .NET-clientbibliotheek

- Zie het [Python API-referentiemateriaal](https://docs.microsoft.com/python/api/overview/azure/storage) voor meer informatie over de Python-clientbibliotheek.
- Verken met behulp van de Python-clientbibliotheek geschreven [Blob-opslagvoorbeelden](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=python&term=blob).

## <a name="next-steps"></a>Volgende stappen
 
In deze snelstart hebt u geleerd hoe u bestanden overbrengt tussen een lokale schijf en Azure Blob-opslag met behulp van Python. Voor meer informatie over het werken met Blob-opslag, gaat u naar de instructies voor Blob-opslag.

> [!div class="nextstepaction"]
> [Instructies voor bewerkingen in Blob-opslag](./storage-python-how-to-use-blob-storage.md)
 
Zie [Azure Blob-opslagresources beheren met Storage Explorer](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) voor meer informatie over Storage Explorer en blobs.
