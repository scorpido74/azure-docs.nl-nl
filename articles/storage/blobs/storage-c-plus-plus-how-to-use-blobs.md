---
title: Objectopslag (Blob) gebruiken vanuit C++ - Azure | Microsoft Documenten
description: Ongestructureerde gegevens opslaan in de cloud met Azure Blob-opslag (object).
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/21/2018
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: 0a9015e33f5456efeac7f7c887995ac4a69f0259
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75941813"
---
# <a name="how-to-use-blob-storage-from-c"></a>Blob-opslag gebruiken vanuit C++

In deze handleiding wordt uitgelegd hoe u veelvoorkomende scenario's uitvoeren met Azure Blob-opslag. In de voorbeelden wordt uitgelegd hoe u blobs uploadt, lijstt, downloadt en verwijdert. De voorbeelden zijn geschreven in C++ en maken gebruik van de [Azure Storage-clientbibliotheek voor C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md).   

Zie Inleiding tot Azure [Blob-opslag voor](storage-blobs-introduction.md)meer informatie over Blob-opslag.

> [!NOTE]
> Deze handleiding is gericht op de Azure-opslagclientbibliotheek voor C++ versie 1.0.0 en hoger. Microsoft raadt aan om de nieuwste versie van de opslagclientbibliotheek voor C++ te gebruiken, beschikbaar via [NuGet](https://www.nuget.org/packages/wastorage) of [GitHub.](https://github.com/Azure/azure-storage-cpp)

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>Een C++-toepassing maken
In deze handleiding gebruikt u opslagfuncties die kunnen worden uitgevoerd in een C++ toepassing.  

Hiervoor moet u de Azure-opslagclientbibliotheek voor C++ installeren en een Azure-opslagaccount in uw Azure-abonnement maken.   

Voor het installeren van de Azure-opslagclientbibliotheek voor C++ kunt u de volgende methoden gebruiken:

* **Linux:** Volg de instructies in de [Azure Storage Client Library voor C++ README: Aan de slag op linux-pagina.](https://github.com/Azure/azure-storage-cpp#getting-started-on-linux)
* **Windows:** Gebruik [vcpkg](https://github.com/microsoft/vcpkg) in Windows als afhankelijkheidsmanager. Volg de [snelle start](https://github.com/microsoft/vcpkg#quick-start) om vcpkg te initialiseren. Gebruik vervolgens de volgende opdracht om de bibliotheek te installeren:

```powershell
.\vcpkg.exe install azure-storage-cpp
```

In het [README-bestand](https://github.com/Azure/azure-storage-cpp#download--install) vindt u een handleiding voor het bouwen van de broncode en exporteren naar NuGet.

## <a name="configure-your-application-to-access-blob-storage"></a>Uw toepassing configureren om toegang te krijgen tot Blob-opslag
Voeg de volgende instructies toe aan de bovenkant van het C++-bestand waar u de Azure-opslag-API's wilt gebruiken om toegang te krijgen tot blobs:  

```cpp
#include <was/storage_account.h>
#include <was/blob.h>
#include <cpprest/filestream.h>  
#include <cpprest/containerstream.h> 
```

## <a name="setup-an-azure-storage-connection-string"></a>Een Azure-opslagverbindingstekenreeks instellen
Een Azure-opslagclient gebruikt een opslagverbindingstekenreeks om eindpunten en referenties voor toegang tot gegevensbeheerservices op te slaan. Wanneer u in een clienttoepassing wordt uitgevoerd, moet u de tekenreeks voor de opslagverbinding in de volgende indeling opgeven, met behulp van de naam van uw opslagaccount en de opslagtoegangssleutel voor het opslagaccount dat wordt vermeld in de [Azure Portal](https://portal.azure.com) voor de waarden *AccountName* en *AccountKey.* Zie [Over Azure Storage Accounts](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)voor informatie over opslagaccounts en toegangssleutels. In dit voorbeeld ziet u hoe u een statisch veld kunt declareren voor het opslaan van de verbindingstekenreeks:  

```cpp
// Define the connection-string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

Als u uw toepassing wilt testen in uw lokale Windows-computer, u de Microsoft [Azure-opslagemulator](../storage-use-emulator.md) gebruiken die is geïnstalleerd met de [Azure SDK.](https://azure.microsoft.com/downloads/) De opslagemulator is een hulpprogramma dat de blob-, wachtrij- en tabelservices simuleert die beschikbaar zijn in Azure op uw lokale ontwikkelingsmachine. In het volgende voorbeeld ziet u hoe u een statisch veld kunt declareren voor het opslaan van de verbindingstekenreeks naar uw lokale opslagemulator:

```cpp
// Define the connection-string with Azure Storage Emulator.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

Als u de Azure-opslagemulator wilt starten, selecteert u de knop **Start** of drukt u op de **Windows-toets.** Begin met het typen van **Azure Storage Emulator**en selecteer Microsoft Azure Storage **Emulator** in de lijst met toepassingen.  

In de volgende voorbeelden wordt ervan uitgegaan dat u een van deze twee methoden hebt gebruikt om de opslagverbindingstekenreeks op te halen.  

## <a name="retrieve-your-storage-account"></a>Uw opslagaccount ophalen
U de **klasse cloud_storage_account** gebruiken om uw opslagaccountgegevens weer te geven. Voor het ophalen van uw opslagaccountgegevens uit de opslagverbindingstekenreeks kunt u de methode **parse** gebruiken.  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

Ontvang vervolgens een verwijzing naar een **cloud_blob_client** klasse, omdat u hiermee objecten ophalen die containers en blobs vertegenwoordigen die zijn opgeslagen in blob-opslag. Met de volgende code wordt een **cloud_blob_client** object gemaakt met het object opslagaccount dat we hierboven hebben opgehaald:  

```cpp
// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();  
```

## <a name="how-to-create-a-container"></a>Hoe: Een container maken
[!INCLUDE [storage-container-naming-rules-include](../../../includes/storage-container-naming-rules-include.md)]

In dit voorbeeld ziet u hoe u een container kunt maken als deze nog niet bestaat:  

```cpp
try
{
    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the blob client.
    azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

    // Retrieve a reference to a container.
    azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

    // Create the container if it doesn't already exist.
    container.create_if_not_exists();
}
catch (const std::exception& e)
{
    std::wcout << U("Error: ") << e.what() << std::endl;
}  
```

Standaard is de nieuwe container privé en moet u uw opslagtoegangssleutel opgeven om blobs uit deze container te downloaden. Als u de bestanden (blobs) in de container voor iedereen beschikbaar wilt maken, u instellen dat de container openbaar is met behulp van de volgende code:  

```cpp
// Make the blob container publicly accessible.
azure::storage::blob_container_permissions permissions;
permissions.set_public_access(azure::storage::blob_container_public_access_type::blob);
container.upload_permissions(permissions);  
```

Iedereen op internet kan blobs in een openbare container zien, maar u ze alleen wijzigen of verwijderen als u de juiste toegangssleutel hebt.  

## <a name="how-to-upload-a-blob-into-a-container"></a>Hoe: Een blob uploaden naar een container
Azure Blob-opslag ondersteunt blokblobs en paginablobs. In de meeste gevallen is een blok-blob het aangewezen type om te gebruiken.  

Om een bestand naar een blok-blob te uploaden, haalt u een containerverwijzing op en gebruikt u deze om een blok-blobverwijzing op te halen. Zodra u een blob-verwijzing hebt, u elke stroom gegevens naar deze gegevens uploaden door de **upload_from_stream-methode** aan te roepen. Met deze bewerking wordt de blob gemaakt (als deze nog niet bestaat) of overschreven (als deze wel al bestaat). Het volgende voorbeeld laat zien hoe u een blob uploadt naar een container. Hierbij wordt ervan uitgegaan dat de container al is gemaakt.  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-1".
azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

// Create or overwrite the "my-blob-1" blob with contents from a local file.
concurrency::streams::istream input_stream = concurrency::streams::file_stream<uint8_t>::open_istream(U("DataFile.txt")).get();
blockBlob.upload_from_stream(input_stream);
input_stream.close().wait();

// Create or overwrite the "my-blob-2" and "my-blob-3" blobs with contents from text.
// Retrieve a reference to a blob named "my-blob-2".
azure::storage::cloud_block_blob blob2 = container.get_block_blob_reference(U("my-blob-2"));
blob2.upload_text(U("more text"));

// Retrieve a reference to a blob named "my-blob-3".
azure::storage::cloud_block_blob blob3 = container.get_block_blob_reference(U("my-directory/my-sub-directory/my-blob-3"));
blob3.upload_text(U("other text"));  
```

U ook de **upload_from_file** methode gebruiken om een bestand te uploaden naar een blokblob.

## <a name="how-to-list-the-blobs-in-a-container"></a>How to: De blobs in een container weergeven
Als u een lijst van de blobs in een container wilt weergeven, moet u eerst een containerverwijzing ophalen. U vervolgens de **list_blobs** methode van de container gebruiken om de blobs en/of mappen erin op te halen. Als u toegang wilt krijgen tot de uitgebreide set eigenschappen en methoden voor een **geretourneerde list_blob_item,** moet u de **methode list_blob_item.as_blob** aanroepen om een **cloud_blob** object te krijgen, of de **methode list_blob.as_directory** om een cloud_blob_directory object te krijgen. De volgende code laat zien hoe u de URI van elk item in de container **my-sample-container** ophalen en uitvoeren:

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Output URI of each item.
azure::storage::list_blob_item_iterator end_of_results;
for (auto it = container.list_blobs(); it != end_of_results; ++it)
{
    if (it->is_blob())
    {
        std::wcout << U("Blob: ") << it->as_blob().uri().primary_uri().to_string() << std::endl;
    }
    else
    {
        std::wcout << U("Directory: ") << it->as_directory().uri().primary_uri().to_string() << std::endl;
    }
}
```

Zie Azure Storage Resources [in C++ aanbieden](../storage-c-plus-plus-enumeration.md)voor meer informatie over aanbiedingsbewerkingen.

## <a name="how-to-download-blobs"></a>Hoe: Blobs downloaden
Als u blobs wilt downloaden, haalt u eerst een blobverwijzing op en roept u vervolgens de **download_to_stream-methode** aan. In het volgende voorbeeld wordt de **methode download_to_stream** gebruikt om de blob-inhoud over te zetten naar een streamobject dat u vervolgens aanhouden in een lokaal bestand.  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-1".
azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

// Save blob contents to a file.
concurrency::streams::container_buffer<std::vector<uint8_t>> buffer;
concurrency::streams::ostream output_stream(buffer);
blockBlob.download_to_stream(output_stream);

std::ofstream outfile("DownloadBlobFile.txt", std::ofstream::binary);
std::vector<unsigned char>& data = buffer.collection();

outfile.write((char *)&data[0], buffer.size());
outfile.close();  
```

U ook de **download_to_file** methode gebruiken om de inhoud van een blob naar een bestand te downloaden.
Daarnaast u ook de **download_text** methode gebruiken om de inhoud van een blob te downloaden als teksttekenreeks.  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-2".
azure::storage::cloud_block_blob text_blob = container.get_block_blob_reference(U("my-blob-2"));

// Download the contents of a blog as a text string.
utility::string_t text = text_blob.download_text();
```

## <a name="how-to-delete-blobs"></a>Hoe: Blobs verwijderen
Als u een blob wilt verwijderen, krijgt u eerst een blobverwijzing en roept u vervolgens de **delete_blob-methode** aan.  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-1".
azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

// Delete the blob.
blockBlob.delete_blob();
```

## <a name="next-steps"></a>Volgende stappen
Nu u de basisbeginselen van blob-opslag hebt geleerd, volgt u deze koppelingen voor meer informatie over Azure Storage.  

* [Queue Storage gebruiken met C++](../storage-c-plus-plus-how-to-use-queues.md)
* [Tabelopslag gebruiken vanuit C++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [Azure-opslagbronnen aanbieden in C++](../storage-c-plus-plus-enumeration.md)
* [Opslagclientbibliotheek voor C++ Naslaginformatie](https://azure.github.io/azure-storage-cpp)
* [Documentatie bij Azure Storage](https://azure.microsoft.com/documentation/services/storage/)
* [Gegevensoverdracht met het AzCopy-opdrachtregelprogramma](../storage-use-azcopy.md)

