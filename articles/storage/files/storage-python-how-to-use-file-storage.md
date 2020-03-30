---
title: Ontwikkelen voor Azure-bestanden met Python | Microsoft Documenten
description: Meer informatie over het ontwikkelen van Python-toepassingen en -services die Azure Files gebruiken om bestandsgegevens op te slaan.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 139e3009722761172b7bbd57805a7f5b07e55fc0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68699382"
---
# <a name="develop-for-azure-files-with-python"></a>Ontwikkelen voor Azure Files met Python
[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-try-azure-tools-files](../../../includes/storage-try-azure-tools-files.md)]

In deze zelfstudie worden de basisprincipes van het gebruik van Python getoond om toepassingen of services te ontwikkelen die Azure-bestanden gebruiken om bestandsgegevens op te slaan. In deze zelfstudie maken we een eenvoudige consoletoepassing en laten we zien hoe u basisacties uitvoeren met Python- en Azure-bestanden:

* Azure-bestandsshares maken
* Mappen maken
* Bestanden en mappen opsommen in een Azure-bestandsshare
* Een bestand uploaden, downloaden en verwijderen

> [!Note]  
> Omdat Azure Files mogelijk via SMB worden geopend, is het mogelijk om eenvoudige toepassingen te schrijven die toegang hebben tot de Azure-bestandsshare met behulp van de standaard Python I/O-klassen en -functies. In dit artikel wordt beschreven hoe u toepassingen schrijft die gebruikmaken van de Azure Storage Python SDK, die de [AZURE Files REST API](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api) gebruikt om met Azure Files te praten.

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Azure Storage SDK voor Python downloaden en installeren

De [Azure Storage SDK voor Python](https://github.com/azure/azure-storage-python) vereist Python 2.7, 3.3, 3.4, 3.5 of 3.6.
 
## <a name="install-via-pypi"></a>Installeren via PyPi

Als u wilt installeren via de Python Package Index (PyPI), typt u:

```bash
pip install azure-storage-file
```

> [!NOTE]
> Als u een upgrade uitvoert van de Azure Storage SDK voor Python-versie `pip uninstall azure-storage` 0.36 of eerder, verwijdert u de oudere SDK voordat u het nieuwste pakket installeert.

Ga voor alternatieve installatiemethoden naar de [Azure Storage SDK voor Python op GitHub.](https://github.com/Azure/azure-storage-python/)

## <a name="view-the-sample-application"></a>De voorbeeldtoepassing weergeven
f Zie [Azure Storage: Aan de slag met Azure Files in Python](https://github.com/Azure-Samples/storage-file-python-getting-started)om een voorbeeldtoepassing weer te geven en uit te voeren die laat zien hoe u Python met Azure-bestanden gebruiken. 

Als u de voorbeeldtoepassing wilt uitvoeren, `azure-storage-file` `azure-storage-common` controleert u of u zowel de pakketten als de pakketten hebt geïnstalleerd.

## <a name="set-up-your-application-to-use-azure-files"></a>Uw toepassing instellen voor azure-bestanden
Voeg het volgende toe boven aan een Python-bronbestand waarin u programmatisch toegang wilt krijgen tot Azure Storage.

```python
from azure.storage.file import FileService
```

## <a name="set-up-a-connection-to-azure-files"></a>Een verbinding met Azure Files instellen 
Met `FileService` het object u werken met aandelen, mappen en bestanden. Met de volgende `FileService` code wordt een object gemaakt met de naam van het opslagaccount en de accountsleutel. Vervang `<myaccount>` en `<mykey>` door uw accountnaam en -sleutel.

```python
file_service = FileService(account_name='myaccount', account_key='mykey')
```

## <a name="create-an-azure-file-share"></a>Een Azure-bestandsshare maken
In het volgende codevoorbeeld kunt `FileService` u een object gebruiken om de share te maken als deze niet bestaat.

```python
file_service.create_share('myshare')
```

## <a name="create-a-directory"></a>Een map maken
U ook opslag ordenen door bestanden in submappen te plaatsen in plaats van ze allemaal in de hoofdmap te hebben. Met Azure Files u zoveel mappen maken als uw account toestaat. De onderstaande code maakt een submap met de naam **sampledir** onder de hoofdmap.

```python
file_service.create_directory('myshare', 'sampledir')
```

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Bestanden en mappen opsommen in een Azure-bestandsshare
Als u de bestanden en mappen in een share wilt vermelden, gebruikt u de **methode lijstmappen\_\_\_en bestanden.** Deze methode retourneert een generator. Met de volgende code wordt de **naam** van elk bestand en elke map in een share naar de console uitgevoerd.

```python
generator = file_service.list_directories_and_files('myshare')
for file_or_dir in generator:
    print(file_or_dir.name)
```

## <a name="upload-a-file"></a>Bestand uploaden 
Azure-bestandsshare bevat op zijn minst een hoofdmap waar bestanden zich kunnen bevinden. In deze sectie leert u hoe u een bestand vanuit lokale opslag uploadt naar de hoofdmap van een share.

Als u een bestand wilt `create_file_from_path`maken `create_file_from_stream` `create_file_from_bytes` en `create_file_from_text` gegevens wilt uploaden, gebruikt u de methoden of methoden. Het zijn methoden op hoog niveau die de nodige chunking uitvoeren wanneer de grootte van de gegevens meer dan 64 MB bedraagt.

`create_file_from_path`uploadt de inhoud van een bestand `create_file_from_stream` van het opgegeven pad en uploadt de inhoud van een al geopend bestand/stream. `create_file_from_bytes`uploadt een array met `create_file_from_text` bytes en uploadt de opgegeven tekstwaarde met de opgegeven codering (standaard naar UTF-8).

In het volgende voorbeeld wordt de inhoud van het **bestand sunset.png** in het **bestand myfile geüpload.**

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
Als u gegevens uit `get_file_to_path`een `get_file_to_stream` `get_file_to_bytes`bestand `get_file_to_text`wilt downloaden, gebruikt u , of . Het zijn methoden op hoog niveau die de nodige chunking uitvoeren wanneer de grootte van de gegevens meer dan 64 MB bedraagt.

In het volgende `get_file_to_path` voorbeeld wordt aangetoond dat u de inhoud van het **myfile-bestand kunt** downloaden en opslaan in het **out-sunset.png-bestand.**

```python
file_service.get_file_to_path('myshare', None, 'myfile', 'out-sunset.png')
```

## <a name="delete-a-file"></a>Een bestand verwijderen
Tot slot, om een `delete_file`bestand te verwijderen, aanroepen .

```python
file_service.delete_file('myshare', None, 'myfile')
```

## <a name="create-share-snapshot"></a>Momentopname delen maken
U een tijdskopie van uw volledige bestandsshare maken.

```python
snapshot = file_service.snapshot_share(share_name)
snapshot_id = snapshot.snapshot
```

**Momentopname delen maken met metagegevens**

```python
metadata = {"foo": "bar"}
snapshot = file_service.snapshot_share(share_name, metadata=metadata)
```

## <a name="list-shares-and-snapshots"></a>Aandelen en momentopnamen aanbieden 
U alle momentopnamen voor een bepaald aandeel weergeven.

```python
shares = list(file_service.list_shares(include_snapshots=True))
```

## <a name="browse-share-snapshot"></a>Bladeren door momentopname delen
U door inhoud van elke momentopname van elk aandeel bladeren om bestanden en mappen vanaf dat moment op te halen.

```python
directories_and_files = list(
    file_service.list_directories_and_files(share_name, snapshot=snapshot_id))
```

## <a name="get-file-from-share-snapshot"></a>Bestand ophalen van momentopname delen
U een bestand downloaden van een momentopname voor delen voor uw herstelscenario.

```python
with open(FILE_PATH, 'wb') as stream:
    file = file_service.get_file_to_stream(
        share_name, directory_name, file_name, stream, snapshot=snapshot_id)
```

## <a name="delete-a-single-share-snapshot"></a>Een momentopname voor één aandeel verwijderen  
U één momentopname van één aandeel verwijderen.

```python
file_service.delete_share(share_name, snapshot=snapshot_id)
```

## <a name="delete-share-when-share-snapshots-exist"></a>Delen verwijderen wanneer er momentopnamen voor delen bestaan
Een aandeel met momentopnamen kan niet worden verwijderd, tenzij alle momentopnamen eerst worden verwijderd.

```python
file_service.delete_share(share_name, delete_snapshots=DeleteSnapshot.Include)
```

## <a name="next-steps"></a>Volgende stappen
Nu u hebt geleerd hoe u Azure-bestanden met Python manipuleren, volgt u deze koppelingen voor meer informatie.

* [Python Developer Center](https://azure.microsoft.com/develop/python/)
* [REST-API voor Azure Storage-services](https://msdn.microsoft.com/library/azure/dd179355)
* [Microsoft Azure Storage SDK voor Python](https://github.com/Azure/azure-storage-python)
