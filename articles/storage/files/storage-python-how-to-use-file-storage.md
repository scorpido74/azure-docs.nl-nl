---
title: Ontwikkelen voor Azure Files met python | Microsoft Docs
description: Meer informatie over het ontwikkelen van python-toepassingen en-services die gebruikmaken van Azure Files voor het opslaan van bestands gegevens.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 12/14/2018
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-python
ms.openlocfilehash: 46512d61238c45936e7ebed4310993159cb43d34
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/06/2020
ms.locfileid: "87845439"
---
# <a name="develop-for-azure-files-with-python"></a>Ontwikkelen voor Azure Files met Python
[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-try-azure-tools-files](../../../includes/storage-try-azure-tools-files.md)]

In deze zelf studie worden de basis beginselen van het gebruik van python gebruikt voor het ontwikkelen van toepassingen of services die gebruikmaken van Azure Files om bestands gegevens op te slaan. In deze zelf studie maken we een eenvoudige console toepassing en laten we zien hoe u basis bewerkingen uitvoert met python en Azure Files:

* Azure-bestands shares maken
* Mappen maken
* Bestanden en mappen in een Azure-bestands share opsommen
* Een bestand uploaden, downloaden en verwijderen

> [!Note]  
> Omdat Azure Files mogelijk toegankelijk is via SMB, is het mogelijk om eenvoudige toepassingen te schrijven die toegang hebben tot de Azure-bestands share met behulp van de Standard python I/O-klassen en-functies. In dit artikel wordt beschreven hoe u toepassingen schrijft die gebruikmaken van de Azure Storage python SDK, die gebruikmaakt van de [Azure Files rest API](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api) om te praten met Azure files.

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Azure Storage SDK voor python downloaden en installeren

De [Azure Storage SDK voor python](https://github.com/azure/azure-storage-python) vereist python 2,7, 3,3, 3,4, 3,5 of 3,6.
 
## <a name="install-via-pypi"></a>Installeren via PyPi

Als u wilt installeren via de python-pakket index (PyPI), typt u:

```bash
pip install azure-storage-file
```

> [!NOTE]
> Als u een upgrade uitvoert van de Azure Storage SDK voor python versie 0,36 of eerder, verwijdert u de oudere SDK met `pip uninstall azure-storage` voordat u het meest recente pakket installeert.

Voor alternatieve installatie methoden gaat u naar de [Azure Storage SDK voor python op github](https://github.com/Azure/azure-storage-python/).

## <a name="view-the-sample-application"></a>De voorbeeld toepassing weer geven
Zie [Azure Storage: aan de slag met Azure files in python](https://github.com/Azure-Samples/storage-file-python-getting-started)om een voorbeeld toepassing weer te geven en uit te voeren die laat zien hoe u python gebruikt met Azure files. 

Als u de voorbeeld toepassing wilt uitvoeren, moet u ervoor zorgen dat u zowel de als-pakketten hebt geïnstalleerd `azure-storage-file` `azure-storage-common` .

## <a name="set-up-your-application-to-use-azure-files"></a>Uw toepassing instellen voor gebruik Azure Files
Voeg het volgende toe boven aan elk python-bron bestand waarin u programmatisch toegang wilt krijgen Azure Storage.

```python
from azure.storage.file import FileService
```

## <a name="set-up-a-connection-to-azure-files"></a>Een verbinding met Azure Files instellen 
`FileService`Met het object kunt u werken met shares, mappen en bestanden. Met de volgende code wordt een `FileService` object gemaakt met behulp van de naam van het opslag account en de account sleutel. Vervang `<myaccount>` en `<mykey>` door uw accountnaam en -sleutel.

```python
file_service = FileService(account_name='myaccount', account_key='mykey')
```

## <a name="create-an-azure-file-share"></a>Een Azure-bestandsshare maken
In het volgende code voorbeeld kunt u een- `FileService` object gebruiken om de share te maken als deze niet bestaat.

```python
file_service.create_share('myshare')
```

## <a name="create-a-directory"></a>Een map maken
U kunt opslag ook indelen door bestanden in submappen te plaatsen in plaats van deze allemaal in de hoofdmap. Met Azure Files kunt u zoveel mappen maken als uw account toestaat. Met de onderstaande code wordt een submap gemaakt met de naam **sampledir** onder de hoofdmap.

```python
file_service.create_directory('myshare', 'sampledir')
```

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Bestanden en mappen in een Azure-bestands share opsommen
Als u de bestanden en mappen in een share wilt weer geven, gebruikt u de methode ** \_ directory's \_ en \_ bestanden weer geven** . Deze methode retourneert een generator. Met de volgende code wordt de **naam** van elk bestand en elke map in een share naar de-console uitgevoerd.

```python
generator = file_service.list_directories_and_files('myshare')
for file_or_dir in generator:
    print(file_or_dir.name)
```

## <a name="upload-a-file"></a>Bestand uploaden 
Azure-bestands share bevat ten minste een hoofdmap waarin bestanden kunnen worden opgeslagen. In deze sectie leert u hoe u een bestand van de lokale opslag uploadt naar de hoofdmap van een share.

Gebruik de `create_file_from_path` methoden, of om een bestand te maken en gegevens te uploaden `create_file_from_stream` `create_file_from_bytes` `create_file_from_text` . Dit zijn methoden op hoog niveau die de benodigde Chunking uitvoeren wanneer de grootte van de gegevens groter is dan 64 MB.

`create_file_from_path`uploadt de inhoud van een bestand uit het opgegeven pad en `create_file_from_stream` uploadt de inhoud van een al geopend bestand/stream. `create_file_from_bytes`uploadt een byte matrix en `create_file_from_text` uploadt de opgegeven tekst waarde met behulp van de opgegeven code ring (wordt standaard ingesteld op UTF-8).

In het volgende voor beeld wordt de inhoud van het **sunset.png** -bestand geüpload naar het bestand **MyFile** .

```python
from azure.storage.file import ContentSettings
file_service.create_file_from_path(
    'myshare',
    None,  # We want to create this blob in the root directory, so we specify None for the directory_name
    'myfile',
    'sunset.png',
    content_settings=ContentSettings(content_type='image/png'))
```

## <a name="download-a-file"></a>Bestand downloaden
Als u gegevens uit een bestand wilt downloaden, gebruikt,, `get_file_to_path` `get_file_to_stream` `get_file_to_bytes` of `get_file_to_text` . Dit zijn methoden op hoog niveau die de benodigde Chunking uitvoeren wanneer de grootte van de gegevens groter is dan 64 MB.

In het volgende voor beeld ziet u hoe u `get_file_to_path` de inhoud van het bestand **MyFile** downloadt en opslaat in het **out-sunset.png** -bestand.

```python
file_service.get_file_to_path('myshare', None, 'myfile', 'out-sunset.png')
```

## <a name="delete-a-file"></a>Een bestand verwijderen
Als u een bestand wilt verwijderen, roept u vervolgens aan `delete_file` .

```python
file_service.delete_file('myshare', None, 'myfile')
```

## <a name="create-share-snapshot"></a>Momentopname van share maken
U kunt een punt in de tijd kopie van uw volledige bestands share maken.

```python
snapshot = file_service.snapshot_share(share_name)
snapshot_id = snapshot.snapshot
```

**Een moment opname van een share maken met meta gegevens**

```python
metadata = {"foo": "bar"}
snapshot = file_service.snapshot_share(share_name, metadata=metadata)
```

## <a name="list-shares-and-snapshots"></a>Shares en moment opnamen weer geven 
U kunt alle moment opnamen voor een bepaalde share weer geven.

```python
shares = list(file_service.list_shares(include_snapshots=True))
```

## <a name="browse-share-snapshot"></a>Bladeren in share-moment opname
U kunt door de inhoud van elke moment opname van een share bladeren om bestanden en mappen vanaf dat moment op te halen.

```python
directories_and_files = list(
    file_service.list_directories_and_files(share_name, snapshot=snapshot_id))
```

## <a name="get-file-from-share-snapshot"></a>Bestand van moment opname van share ophalen
U kunt een bestand downloaden van een share-moment opname voor uw herstel scenario.

```python
with open(FILE_PATH, 'wb') as stream:
    file = file_service.get_file_to_stream(
        share_name, directory_name, file_name, stream, snapshot=snapshot_id)
```

## <a name="delete-a-single-share-snapshot"></a>Een moment opname van één share verwijderen  
U kunt één moment opname van een share verwijderen.

```python
file_service.delete_share(share_name, snapshot=snapshot_id)
```

## <a name="delete-share-when-share-snapshots-exist"></a>Share verwijderen wanneer moment opnamen van shares bestaan
Een share die moment opnamen bevat, kan pas worden verwijderd als alle moment opnamen eerst worden verwijderd.

```python
file_service.delete_share(share_name, delete_snapshots=DeleteSnapshot.Include)
```

## <a name="next-steps"></a>Volgende stappen
Nu u hebt geleerd hoe u Azure Files kunt bewerken met python, volgt u deze koppelingen voor meer informatie.

* [Python Developer Center](https://azure.microsoft.com/develop/python/)
* [REST-API voor Azure Storage-services](https://msdn.microsoft.com/library/azure/dd179355)
* [Microsoft Azure Storage SDK voor python](https://github.com/Azure/azure-storage-python)
