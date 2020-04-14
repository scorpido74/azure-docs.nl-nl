---
title: Azure Data Lake Storage Gen2 Python SDK voor bestanden & ACL's
description: Gebruik Python manage directories en file and directory access control lists (ACL) in opslagaccounts waar hiërarchische naamruimte (HNS) is ingeschakeld.
author: normesta
ms.service: storage
ms.date: 04/10/2020
ms.author: normesta
ms.topic: article
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: a79f3110206a01b9b974952f0ec0d299644be11f
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262346"
---
# <a name="use-python-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Python gebruiken om mappen, bestanden en ACL's te beheren in Azure Data Lake Storage Gen2

In dit artikel ziet u hoe u Python gebruiken om mappen, bestanden en machtigingen te maken en te beheren in opslagaccounts waarop hiërarchische naamruimte (HNS) is ingeschakeld. 

[Pakket (Python Package Index)](https://pypi.org/project/azure-storage-file-datalake/) | [Samples](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples) | [API referentie](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-storage-file-datalake/12.0.0/azure.storage.filedatalake.html) | [Gen1 naar Gen2 mapping](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md) | [Feedback geven](https://github.com/Azure/azure-sdk-for-python/issues)

## <a name="prerequisites"></a>Vereisten

> [!div class="checklist"]
> * Een Azure-abonnement. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).
> * Een opslagaccount met hiërarchische naamruimte (HNS) ingeschakeld. Volg [deze](data-lake-storage-quickstart-create-account.md) instructies om er een te maken.

## <a name="set-up-your-project"></a>Uw project instellen

Installeer de Azure Data Lake Storage-clientbibliotheek voor Python met [pip](https://pypi.org/project/pip/).

```
pip install azure-storage-file-datalake
```

Voeg deze importinstructies toe aan de bovenkant van uw codebestand.

```python
import os, uuid, sys
from azure.storage.filedatalake import DataLakeServiceClient
from azure.core._match_conditions import MatchConditions
from azure.storage.filedatalake._models import ContentSettings
```

## <a name="connect-to-the-account"></a>Verbinding maken met het account

Als u de fragmenten in dit artikel wilt gebruiken, moet u een **Instantie DataLakeServiceClient** maken die het opslagaccount vertegenwoordigt. 

### <a name="connect-by-using-an-account-key"></a>Verbinding maken met een accountsleutel

Dit is de eenvoudigste manier om verbinding te maken met een account. 

In dit voorbeeld wordt een **Instantie DataLakeServiceClient** gemaakt met behulp van een accountsleutel.

```python
try:  
    global service_client
        
    service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
        "https", storage_account_name), credential=storage_account_key)
    
except Exception as e:
    print(e)
```
 
- Vervang de waarde van de tijdelijke plaatsaanduiding `storage_account_name` door de naam van uw opslagaccount.

- Vervang `storage_account_key` de tijdelijke aanduidingswaarde door uw toegangssleutel voor het opslagaccount.

### <a name="connect-by-using-azure-active-directory-ad"></a>Verbinding maken met Azure Active Directory (AD)

U de [Azure-identiteitsclientbibliotheek voor Python](https://pypi.org/project/azure-identity/) gebruiken om uw toepassing te verifiëren met Azure AD.

In dit voorbeeld wordt een **GegevensLakeServiceClient-exemplaar** gemaakt met behulp van een client-id, een clientgeheim en een tenant-id.  Zie Een token [van Azure AD ophalen van Azure AD voor het toestaan van aanvragen van een clienttoepassing](../common/storage-auth-aad-app.md)om deze waarden te verkrijgen.

```python
def initialize_storage_account_ad(storage_account_name, client_id, client_secret, tenant_id):
    
    try:  
        global service_client

        credential = ClientSecretCredential(tenant_id, client_id, client_secret)

        service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
            "https", storage_account_name), credential=credential)
    
    except Exception as e:
        print(e)
```

> [!NOTE]
> Zie voor meer voorbeelden de [Azure-identiteitsclientbibliotheek voor Python-documentatie.](https://pypi.org/project/azure-identity/)

## <a name="create-a-file-system"></a>Een bestandssysteem maken

Een bestandssysteem fungeert als een container voor uw bestanden. U er een maken door de methode **FileSystemDataLakeServiceClient.create_file_system** aan te roepen.

In dit voorbeeld wordt `my-file-system`een bestandssysteem met de naam .

```python
def create_file_system():
    try:
        global file_system_client

        file_system_client = service_client.create_file_system(file_system="my-file-system")
    
    except Exception as e:
        print(e) 
```


## <a name="create-a-directory"></a>Een map maken

Maak een adreslijstverwijzing door de methode **FileSystemClient.create_directory** aan te roepen.

In dit voorbeeld `my-directory` wordt een map toegevoegd met de naam aan een bestandssysteem. 

```python
def create_directory():
    try:
        file_system_client.create_directory("my-directory")
    
    except Exception as e:
     print(e) 
```

## <a name="rename-or-move-a-directory"></a>De naam van een map wijzigen of verplaatsen

Wijzig de naam of verplaats een map door de **methode DataLakeDirectoryClient.rename_directory** aan te roepen. Geef het pad van de gewenste map een parameter. 

In dit voorbeeld wordt de naam `my-subdirectory-renamed`van een submap gewijzigd in de naam .

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

Een map verwijderen door de methode **DataLakeDirectoryClient.delete_directory** aan te roepen.

In dit voorbeeld wordt `my-directory`een map met de naam .  

```python
def delete_directory():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")
        directory_client = file_system_client.get_directory_client("my-directory")

        directory_client.delete_directory()
    except Exception as e:
     print(e) 
```

## <a name="manage-directory-permissions"></a>Adreslijstmachtigingen beheren

Download de lijst met toegangsbeheer (ACL) van een map door de methode **DataLakeDirectoryClient.get_access_control** aan te roepen en de ACL in te stellen door de methode **DataLakeDirectoryClient.set_access_control** aan te roepen.

> [!NOTE]
> Als uw toepassing de toegang autoriseert met Azure Active Directory (Azure AD), controleert u of de beveiligingsprincipal die uw toepassing gebruikt om toegang te autoriseren, is toegewezen aan de [rol Opslagblob-gegevenseigenaar](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Zie [Toegangsbeheer in Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)voor meer informatie over hoe ACL-machtigingen worden toegepast en de effecten van het wijzigen ervan.

In dit voorbeeld wordt de ACL `my-directory`van een map met de naam . De `rwxr-xrw-` tekenreeks geeft de eigenaar van de gebruiker machtigingen lezen, schrijven en uitvoeren, geeft de eigenaargroep alleen lees- en uitvoermachtigingen en geeft alle anderen lees- en schrijftoestemming.

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

Maak eerst een bestandsverwijzing in de doelmap door een instantie van de klasse **DataLakeFileClient te** maken. Upload een bestand door de methode **DataLakeFileClient.append_data** aan te roepen. Zorg ervoor dat u de upload voltooit door de methode **DataLakeFileClient.flush_data** aan te roepen.

In dit voorbeeld wordt een tekstbestand geüpload naar een map met de naam `my-directory`.   

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

> [!TIP]
> Als uw bestandsgrootte groot is, moet uw code meerdere oproepen doen naar de methode **DataLakeFileClient.append_data.** Overweeg in plaats daarvan de methode **DataLakeFileClient.upload_data** te gebruiken. Op die manier u het hele bestand in één gesprek uploaden. 

## <a name="upload-a-large-file-to-a-directory"></a>Een groot bestand uploaden naar een map

Gebruik de **methode DataLakeFileClient.upload_data** om grote bestanden te uploaden zonder meerdere gesprekken te hoeven voeren naar de **methode DataLakeFileClient.append_data.**

```python
def upload_file_to_directory_bulk():
    try:

        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        file_client = directory_client.get_file_client("uploaded-file.txt")

        local_file = open("C:\\file-to-upload.txt",'r')

        file_contents = local_file.read()

        file_client.upload_data(file_contents, overwrite=True)

    except Exception as e:
      print(e) 
```

## <a name="manage-file-permissions"></a>Bestandsmachtigingen beheren

Download de lijst met toegangsbeheer (ACL) van een bestand door de methode **DataLakeFileClient.get_access_control** aan te roepen en stel de ACL in door de methode **DataLakeFileClient.set_access_control** aan te roepen.

> [!NOTE]
> Als uw toepassing de toegang autoriseert met Azure Active Directory (Azure AD), controleert u of de beveiligingsprincipal die uw toepassing gebruikt om toegang te autoriseren, is toegewezen aan de [rol Opslagblob-gegevenseigenaar](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Zie [Toegangsbeheer in Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)voor meer informatie over hoe ACL-machtigingen worden toegepast en de effecten van het wijzigen ervan.

In dit voorbeeld wordt de ACL `my-file.txt`van een bestand met de naam . De `rwxr-xrw-` tekenreeks geeft de eigenaar van de gebruiker machtigingen lezen, schrijven en uitvoeren, geeft de eigenaargroep alleen lees- en uitvoermachtigingen en geeft alle anderen lees- en schrijftoestemming.

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

## <a name="download-from-a-directory"></a>Downloaden uit een map 

Open een lokaal bestand om te schrijven. Maak vervolgens een **Instantie DataLakeFileClient** die het bestand vertegenwoordigt dat u wilt downloaden. Bel de **DataLakeFileClient.read_file** om bytes uit het bestand te lezen en vervolgens deze bytes naar het lokale bestand te schrijven. 

```python
def download_file_from_directory():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        local_file = open("C:\\file-to-download.txt",'wb')

        file_client = directory_client.get_file_client("uploaded-file.txt")

        download = file_client.download_file()

        downloaded_bytes = download.readall()

        local_file.write(downloaded_bytes)

        local_file.close()

    except Exception as e:
     print(e)
```
## <a name="list-directory-contents"></a>Mapinhoud weergeven

Lijst inhoud van de map door de **methode FileSystemClient.get_paths** aan te roepen en vervolgens de resultaten op te sommen.

In dit voorbeeld wordt het pad van elke submap `my-directory`en bestand afgedrukt dat zich in een map met de naam bevindt.

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

* [API-referentiedocumentatie](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-storage-file-datalake/12.0.0b5/index.html)
* [Pakket (Python-pakketindex)](https://pypi.org/project/azure-storage-file-datalake/)
* [Voorbeelden](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)
* [Gen1 naar Gen2 mapping](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
* [Bekende problemen](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Feedback geven](https://github.com/Azure/azure-sdk-for-python/issues)
