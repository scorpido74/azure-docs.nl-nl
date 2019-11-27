---
title: Python gebruiken voor bestanden & Acl's in Azure Data Lake Storage Gen2 (preview-versie)
description: Gebruik python Manage directory's en File and Directory Access Control Lists (ACL) in opslag accounts met een hiërarchische naam ruimte (HNS) ingeschakeld.
author: normesta
ms.service: storage
ms.date: 11/24/2019
ms.author: normesta
ms.topic: article
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: a65e8b2c5fbcdd4f6df0f8f39b24fa97b8deeca3
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2019
ms.locfileid: "74534433"
---
# <a name="use-python-for-files--acls-in-azure-data-lake-storage-gen2-preview"></a>Python gebruiken voor bestanden & Acl's in Azure Data Lake Storage Gen2 (preview-versie)

In dit artikel wordt beschreven hoe u python gebruikt om directory's, bestanden en machtigingen te maken en te beheren in opslag accounts met een hiërarchische naam ruimte (HNS) ingeschakeld. 

> [!IMPORTANT]
> De Azure Data Lake Storage-client bibliotheek voor python bevindt zich momenteel in de open bare preview.

[Pakket (python-pakket index) | -](https://pypi.org/project/azure-storage-file-datalake/) voor [beelden](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples) | [API-verwijzing](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-storage-file-datalake/12.0.0b5/index.html) | [gen1 naar Gen2-toewijzing](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md) | [feedback geven](https://github.com/Azure/azure-sdk-for-python/issues)

## <a name="prerequisites"></a>Vereisten

> [!div class="checklist"]
> * Een Azure-abonnement. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).
> * Een opslag account met een hiërarchische naam ruimte (HNS) ingeschakeld. Volg [deze](data-lake-storage-quickstart-create-account.md) instructies om er een te maken.

## <a name="set-up-your-project"></a>Uw project instellen

Installeer de Azure Data Lake Storage-client bibliotheek voor python met behulp van [PIP](https://pypi.org/project/pip/).

```
pip install azure-storage-file-datalake --pre
```

Voeg deze import instructies toe aan de bovenkant van het code bestand.

```python
import os, uuid, sys
from azure.storage.filedatalake import DataLakeServiceClient
```

## <a name="connect-to-the-account"></a>Verbinding maken met het account

Als u de fragmenten in dit artikel wilt gebruiken, moet u een **DataLakeServiceClient** -exemplaar maken dat het opslag account vertegenwoordigt. De eenvoudigste manier om een account sleutel te gebruiken is. 

In dit voor beeld wordt een account sleutel gebruikt voor het maken van een **DataLakeServiceClient** -exemplaar dat het opslag account vertegenwoordigt. 

```python
try:  
    global service_client
        
    service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
        "https", storage_account_name), credential=storage_account_key)
    
except Exception as e:
    print(e)
```
 
- Vervang de waarde van de tijdelijke plaatsaanduiding `storage_account_name` door de naam van uw opslagaccount.

- Vervang de waarde van de tijdelijke `storage-account-key` door de toegangs sleutel voor uw opslag account.

## <a name="create-a-file-system"></a>Een bestandssysteem maken

Een bestands systeem fungeert als een container voor uw bestanden. U kunt er een maken door de methode **FileSystemDataLakeServiceClient. create_file_system** aan te roepen.

In dit voor beeld wordt een bestands systeem gemaakt met de naam `my-file-system`.

```python
def create_file_system():
    try:
        global file_system_client

        file_system_client = service_client.create_file_system(file_system="my-file-system")
    
    except Exception as e:
        print(e) 
```


## <a name="create-a-directory"></a>Een map maken

Maak een verwijzing naar een directory door de methode **FileSystemClient. create_directory** aan te roepen.

In dit voor beeld wordt een map met de naam `my-directory` toegevoegd aan een bestands systeem. 

```python
def create_directory():
    try:
        file_system_client.create_directory("my-directory")
    
    except Exception as e:
     print(e) 
```

## <a name="rename-or-move-a-directory"></a>Een map een andere naam geven of verplaatsen

Wijzig de naam of verplaats een map door de methode **DataLakeDirectoryClient. rename_directory** aan te roepen. Geef een para meter door aan het pad van de gewenste map. 

In dit voor beeld wordt de naam van een submap gewijzigd in `my-subdirectory-renamed`.

```python
def rename_directory():
    try:
       
       file_system_client = service_client.get_file_system_client(file_system="my-file-system")
       directory_client = file_system_client.get_directory_client("my-directory")
       
       new_dir_name = "my-directory-renamed"
       directory_client.rename_directory(rename_destination=directory_client.file_system_name + '/' + new_dir_name)

    except Exception as e:
     print(e) 
```

## <a name="delete-a-directory"></a>Een map verwijderen

Verwijder een directory door de methode **DataLakeDirectoryClient. delete_directory** aan te roepen.

In dit voor beeld wordt een map met de naam `my-directory`verwijderd.  

```python
def delete_directory():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")
        directory_client = file_system_client.get_directory_client("my-directory")

        directory_client.delete_directory()
    except Exception as e:
     print(e) 
```

## <a name="manage-directory-permissions"></a>Mapmachtigingen beheren

Haal de toegangs beheer lijst (ACL) van een directory op door de methode **DataLakeDirectoryClient. get_access_control** aan te roepen en de ACL in te stellen door de methode **DataLakeDirectoryClient. set_access_control** aan te roepen.

In dit voor beeld wordt de toegangs beheer lijst van een map met de naam `my-directory`opgehaald en ingesteld. De teken reeks `rwxr-xrw-` de machtigingen lezen, schrijven en uitvoeren van de gebruiker die eigenaar is, geeft de groep die eigenaar is alleen lezen-en uitvoer machtigingen en geeft alle andere Lees-en schrijf rechten.

```python
def manage_directory_permissions():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        acl_props = directory_client.get_access_control()
        
        print(acl_props['permissions'])
        
        new_dir_permissions = "rwxr-xrw-"
        
        directory_client.set_access_control(permissions=new_dir_permissions)
        
        acl_props = directory_client.get_access_control()
        
        print(acl_props['permissions'])
    
    except Exception as e:
     print(e) 
```

## <a name="upload-a-file-to-a-directory"></a>Een bestand uploaden naar een map 

Maak eerst een bestands verwijzing in de doel directory door een instantie van de klasse **DataLakeFileClient** te maken. Upload een bestand door de methode **DataLakeFileClient. append_data** aan te roepen. Zorg ervoor dat u de upload voltooit door de methode **DataLakeFileClient. flush_data** aan te roepen.

In dit voor beeld wordt een tekst bestand geüpload naar een map met de naam `my-directory`.   

```python
def upload_file_to_directory():
    try:

        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        file_client = directory_client.create_file("uploaded-file.txt")
        local_file = open("C:\\file-to-upload.txt",'r')

        file_contents = local_file.read()

        file_client.append_data(data=file_contents, offset=0, length=len(file_contents))

        file_client.flush_data(len(file_contents))

    except Exception as e:
      print(e) 
```

## <a name="manage-file-permissions"></a>Bestands machtigingen beheren

Haal de toegangs beheer lijst (ACL) van een bestand op door de methode **DataLakeFileClient. get_access_control** aan te roepen en de ACL in te stellen door de methode **DataLakeFileClient. set_access_control** aan te roepen.

In dit voor beeld wordt de ACL van een bestand met de naam `my-file.txt`opgehaald en ingesteld. De teken reeks `rwxr-xrw-` de machtigingen lezen, schrijven en uitvoeren van de gebruiker die eigenaar is, geeft de groep die eigenaar is alleen lezen-en uitvoer machtigingen en geeft alle andere Lees-en schrijf rechten.

```python
def manage_file_permissions():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        file_client = directory_client.get_file_client("uploaded-file.txt")

        acl_props = file_client.get_access_control()
        
        print(acl_props['permissions'])
        
        new_file_permissions = "rwxr-xrw-"
        
        file_client.set_access_control(permissions=new_file_permissions)
        
        acl_props = file_client.get_access_control()
        
        print(acl_props['permissions'])

    except Exception as e:
     print(e) 
```

## <a name="download-from-a-directory"></a>Downloaden uit een directory 

Open een lokaal bestand om te schrijven. Maak vervolgens een **DataLakeFileClient** -exemplaar dat het bestand vertegenwoordigt dat u wilt downloaden. Roep de **DataLakeFileClient. read_file** aan om bytes uit het bestand te lezen en schrijf vervolgens die bytes naar het lokale bestand. 

```python
def download_file_from_directory():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        local_file = open("C:\\file-to-download.txt",'wb')

        file_client = directory_client.get_file_client("uploaded-file.txt")

        downloaded_bytes = file_client.read_file()

        local_file.write(downloaded_bytes)

        local_file.close()

    except Exception as e:
     print(e)
```
## <a name="list-directory-contents"></a>Mapinhoud weergeven

Mapinhoud weer geven door de methode **FileSystemClient. get_paths** aan te roepen en vervolgens de resultaten te inventariseren.

In dit voor beeld wordt het pad afgedrukt van elke submap en elk bestand dat zich bevindt in een map met de naam `my-directory`.

```python
def list_directory_contents():
    try:
        
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        paths = file_system_client.get_paths(path="my-directory")

        for path in paths:
            print(path.name + '\n')

    except Exception as e:
     print(e) 
```

## <a name="see-also"></a>Zie ook

* [API-referentie documentatie](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-storage-file-datalake/12.0.0b5/index.html)
* [Pakket (python-pakket index)](https://pypi.org/project/azure-storage-file-datalake/)
* [Voorbeelden](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)
* [Toewijzing van gen1 naar Gen2](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
* [Bekende problemen](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Feedback geven](https://github.com/Azure/azure-sdk-for-python/issues)
