---
title: Ontwikkelen voor Azure-bestanden met C++ | Microsoft Documenten
description: Meer informatie over het ontwikkelen van C++ toepassingen en services die Azure Files gebruiken om bestandsgegevens op te slaan.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 09/19/2017
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 97af40bd1f57acb5b26d3b6216984dfb8e3a5181
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68699803"
---
# <a name="develop-for-azure-files-with-c"></a>Ontwikkelen voor Azure-bestanden met C++

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-try-azure-tools-files](../../../includes/storage-try-azure-tools-files.md)]

## <a name="about-this-tutorial"></a>Over deze zelfstudie

In deze zelfstudie leert u hoe u basisbewerkingen uitvoert op Azure-bestanden. Via voorbeelden die in C++ zijn geschreven, leert u hoe u shares en directories maken, bestanden uploaden, aanbieden en verwijderen. Als u nieuw bent in Azure Files, is het nuttig om de concepten in de volgende secties te doorlopen om de voorbeelden te begrijpen.

* Azure-bestandsshares maken en verwijderen
* Mappen maken en verwijderen
* Bestanden en mappen opsommen in een Azure-bestandsshare
* Een bestand uploaden, downloaden en verwijderen
* Het quotum (maximale grootte) instellen voor een Azure-bestandsshare
* Een Shared Access Signature (SAS-sleutel) maken voor een bestand dat gebruikmaakt van een gedeeld toegangsbeleid dat voor de share is gedefinieerd.

> [!Note]  
> Omdat Azure Files mogelijk via SMB worden geopend, is het mogelijk om eenvoudige toepassingen te schrijven die toegang hebben tot de Azure-bestandsshare met behulp van de standaard C++ I/O-klassen en -functies. In dit artikel wordt beschreven hoe u toepassingen schrijft die gebruikmaken van de Azure Storage C++ SDK, die de [API Voor bestandsrest](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api) gebruikt om met Azure-bestanden te praten.

## <a name="create-a-c-application"></a>Een C++-toepassing maken

Als u de voorbeelden wilt maken, moet u de Azure Storage Client Library 2.4.0 voor C++ installeren. U had ook een Azure-opslagaccount moeten maken.

Als u de Azure Storage Client 2.4.0 voor C++ wilt installeren, u een van de volgende methoden gebruiken:

* **Linux:** Volg de instructies in de pagina [Azure Storage Client For C++ README.](https://github.com/Azure/azure-storage-cpp/blob/master/README.md)
* **Windows:** Klik in Visual Studio op **Extra &gt; NuGet Package Manager &gt; Package Manager Console**. Typ de volgende opdracht in de [NuGet Package Manager-console](https://docs.nuget.org/docs/start-here/using-the-package-manager-console) en druk op **ENTER**.
  

```powershell
Install-Package wastorage
```

## <a name="set-up-your-application-to-use-azure-files"></a>Uw toepassing instellen voor azure-bestanden

Voeg de volgende instructies toe aan de bovenkant van het C++ bronbestand waarin u Azure-bestanden wilt manipuleren:

```cpp
#include <was/storage_account.h>
#include <was/file.h>
```

## <a name="set-up-an-azure-storage-connection-string"></a>Een azure-opslagverbindingstekenreeks instellen

Als u Bestandsopslag wilt gebruiken, moet u verbinding maken met uw Azure-opslagaccount. De eerste stap zou zijn om een verbindingstekenreeks te configureren, die we gebruiken om verbinding te maken met uw opslagaccount. Laten we een statische variabele definiëren om dat te doen.

```cpp
// Define the connection-string with your values.
const utility::string_t
storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

## <a name="connecting-to-an-azure-storage-account"></a>Verbinding maken met een Azure-opslagaccount

U de **klasse cloud_storage_account** gebruiken om uw opslagaccountgegevens weer te geven. Voor het ophalen van uw opslagaccountgegevens uit de opslagverbindingstekenreeks kunt u de methode **parse** gebruiken.

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account =
  azure::storage::cloud_storage_account::parse(storage_connection_string);
```

## <a name="create-an-azure-file-share"></a>Een Azure-bestandsshare maken

Alle bestanden en mappen in een Azure-bestandsshare bevinden zich in een container die **delen**wordt genoemd. Uw opslagaccount kan zoveel aandelen hebben als uw accountcapaciteit toestaat. Als u toegang wilt krijgen tot een aandeel en de inhoud ervan, moet u een Azure Files-client gebruiken.

```cpp
// Create the Azure Files client.
azure::storage::cloud_file_client file_client =
  storage_account.create_cloud_file_client();
```

Met de Azure Files-client u vervolgens een verwijzing naar een aandeel verkrijgen.

```cpp
// Get a reference to the file share
azure::storage::cloud_file_share share =
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));
```

Als u de share wilt maken, gebruikt u de **create_if_not_exists** methode van het **object cloud_file_share.**

```cpp
if (share.create_if_not_exists()) {
    std::wcout << U("New share created") << std::endl;
}
```

Op dit punt, **aandeel** houdt een verwijzing naar een aandeel genaamd **my-sample-share**.

## <a name="delete-an-azure-file-share"></a>Een Azure-bestandsshare verwijderen

Het verwijderen van een aandeel gebeurt door de **delete_if_exists-methode** op een cloud_file_share object aan te roepen. Hier is voorbeeldcode die dat doet.

```cpp
// Get a reference to the share.
azure::storage::cloud_file_share share =
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));

// delete the share if exists
share.delete_share_if_exists();
```

## <a name="create-a-directory"></a>Een map maken

U opslag ordenen door bestanden in submappen te plaatsen in plaats van ze allemaal in de hoofdmap te hebben. Met Azure Files u zoveel mappen maken als uw account toestaat. De onderstaande code maakt een map met de naam **my-sample-directory** onder de hoofdmap, evenals een submap met de naam **my-sample-subdirectory**.

```cpp
// Retrieve a reference to a directory
azure::storage::cloud_file_directory directory = share.get_directory_reference(_XPLATSTR("my-sample-directory"));

// Return value is true if the share did not exist and was successfully created.
directory.create_if_not_exists();

// Create a subdirectory.
azure::storage::cloud_file_directory subdirectory =
  directory.get_subdirectory_reference(_XPLATSTR("my-sample-subdirectory"));
subdirectory.create_if_not_exists();
```

## <a name="delete-a-directory"></a>Een map verwijderen

Het verwijderen van een map is een eenvoudige taak, hoewel moet worden opgemerkt dat u een map die nog steeds bestanden of andere mappen bevat, niet verwijderen.

```cpp
// Get a reference to the share.
azure::storage::cloud_file_share share =
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));

// Get a reference to the directory.
azure::storage::cloud_file_directory directory =
  share.get_directory_reference(_XPLATSTR("my-sample-directory"));

// Get a reference to the subdirectory you want to delete.
azure::storage::cloud_file_directory sub_directory =
  directory.get_subdirectory_reference(_XPLATSTR("my-sample-subdirectory"));

// Delete the subdirectory and the sample directory.
sub_directory.delete_directory_if_exists();

directory.delete_directory_if_exists();
```

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Bestanden en mappen opsommen in een Azure-bestandsshare

Het verkrijgen van een lijst van bestanden en mappen binnen een aandeel is gemakkelijk te doen door **list_files_and_directories** te bellen op een **cloud_file_directory** referentie. Als u toegang wilt krijgen tot de uitgebreide set eigenschappen en methoden voor een **geretourneerde list_file_and_directory_item,** moet u de **methode list_file_and_directory_item.as_file** aanroepen om een **cloud_file** object te krijgen, of de **methode list_file_and_directory_item.as_directory** om een **cloud_file_directory** object te krijgen.

In de volgende code wordt uitgelegd hoe u de URI van elk item in de hoofdmap van het aandeel ophalen en uitvoeren.

```cpp
//Get a reference to the root directory for the share.
azure::storage::cloud_file_directory root_dir =
  share.get_root_directory_reference();

// Output URI of each item.
azure::storage::list_file_and_directory_result_iterator end_of_results;

for (auto it = directory.list_files_and_directories(); it != end_of_results; ++it)
{
    if(it->is_directory())
    {
        ucout << "Directory: " << it->as_directory().uri().primary_uri().to_string() << std::endl;
    }
    else if (it->is_file())
    {
        ucout << "File: " << it->as_file().uri().primary_uri().to_string() << std::endl;
    }
}
```

## <a name="upload-a-file"></a>Bestand uploaden

Een Azure-bestandsshare bevat op zijn minst een hoofdmap waar bestanden zich kunnen bevinden. In deze sectie leert u hoe u een bestand vanuit lokale opslag uploadt naar de hoofdmap van een share.

De eerste stap bij het uploaden van een bestand is het verkrijgen van een verwijzing naar de directory waar het zich zou moeten bevinden. U doet dit door de **get_root_directory_reference** methode van het shareobject aan te roepen.

```cpp
//Get a reference to the root directory for the share.
azure::storage::cloud_file_directory root_dir = share.get_root_directory_reference();
```

Nu u een verwijzing hebt naar de hoofdmap van het aandeel, u er een bestand op uploaden. In dit voorbeeld wordt geüpload vanuit een bestand, vanuit tekst en vanuit een stream.

```cpp
// Upload a file from a stream.
concurrency::streams::istream input_stream =
  concurrency::streams::file_stream<uint8_t>::open_istream(_XPLATSTR("DataFile.txt")).get();

azure::storage::cloud_file file1 =
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-1"));
file1.upload_from_stream(input_stream);

// Upload some files from text.
azure::storage::cloud_file file2 =
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-2"));
file2.upload_text(_XPLATSTR("more text"));

// Upload a file from a file.
azure::storage::cloud_file file4 =
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-3"));
file4.upload_from_file(_XPLATSTR("DataFile.txt"));
```

## <a name="download-a-file"></a>Bestand downloaden

Als u bestanden wilt downloaden, haalt u eerst een bestandsverwijzing op en roept u vervolgens de **methode download_to_stream** aan om de bestandsinhoud over te zetten naar een streamobject, dat u vervolgens aanhouden in een lokaal bestand. U ook de **download_to_file** methode gebruiken om de inhoud van een bestand naar een lokaal bestand te downloaden. U de **methode download_text** gebruiken om de inhoud van een bestand als teksttekenreeks te downloaden.

In het volgende voorbeeld worden de **download_to_stream-** en **download_text** methoden gebruikt om aan te tonen dat de bestanden worden gedownload, die in eerdere secties zijn gemaakt.

```cpp
// Download as text
azure::storage::cloud_file text_file =
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-2"));
utility::string_t text = text_file.download_text();
ucout << "File Text: " << text << std::endl;

// Download as a stream.
azure::storage::cloud_file stream_file =
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-3"));

concurrency::streams::container_buffer<std::vector<uint8_t>> buffer;
concurrency::streams::ostream output_stream(buffer);
stream_file.download_to_stream(output_stream);
std::ofstream outfile("DownloadFile.txt", std::ofstream::binary);
std::vector<unsigned char>& data = buffer.collection();
outfile.write((char *)&data[0], buffer.size());
outfile.close();
```

## <a name="delete-a-file"></a>Een bestand verwijderen

Een andere veelvoorkomende Azure-bewerking is het verwijderen van bestanden. Met de volgende code wordt een bestand met de naam my-sample-file-3 verwijderd dat is opgeslagen onder de hoofdmap.

```cpp
// Get a reference to the root directory for the share.
azure::storage::cloud_file_share share =
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));

azure::storage::cloud_file_directory root_dir =
  share.get_root_directory_reference();

azure::storage::cloud_file file =
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-3"));

file.delete_file_if_exists();
```

## <a name="set-the-quota-maximum-size-for-an-azure-file-share"></a>Het quotum (maximale grootte) instellen voor een Azure-bestandsshare

U het quotum (of de maximale grootte) voor een bestandsshare instellen in gigabytes. U kunt ook controleren hoeveel gegevens er momenteel zijn opgeslagen in de share.

Door het quotum voor een share in te stellen, kunt u de totale grootte instellen van de bestanden die zijn opgeslagen in de share. Als de totale grootte van de bestanden in de share het quotum voor de share overschrijdt, kunnen clients bestaande bestanden niet meer vergroten en geen nieuwe bestanden meer maken, tenzij deze bestanden leeg zijn.

In onderstaand voorbeeld ziet u hoe u het huidige gebruik voor een share controleert en een quotum voor de share instelt.

```cpp
// Parse the connection string for the storage account.
azure::storage::cloud_storage_account storage_account =
  azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the file client.
azure::storage::cloud_file_client file_client =
  storage_account.create_cloud_file_client();

// Get a reference to the share.
azure::storage::cloud_file_share share =
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));
if (share.exists())
{
    std::cout << "Current share usage: " << share.download_share_usage() << "/" << share.properties().quota();

    //This line sets the quota to 2560GB
    share.resize(2560);

    std::cout << "Quota increased: " << share.download_share_usage() << "/" << share.properties().quota();

}
```

## <a name="generate-a-shared-access-signature-for-a-file-or-file-share"></a>Een Shared Access Signature genereren voor een bestand of bestandsshare

U een share access signature (SAS) genereren voor een bestandsshare of voor een individueel bestand. U kunt op een bestandsshare ook een beleid voor gedeelde toegang maken om handtekeningen voor gedeelde toegang te beheren. Het maken van een beleid voor gedeelde toegang wordt aanbevolen omdat dit een manier biedt om de SAS in te trekken als deze verdacht is.

In het volgende voorbeeld wordt een beleid voor gedeelde toegang gemaakt op een share en wordt dat beleid vervolgens gebruikt om de beperkingen te verstrekken voor een SAS voor een bestand in de share.

```cpp
// Parse the connection string for the storage account.
azure::storage::cloud_storage_account storage_account =
  azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the file client and get a reference to the share
azure::storage::cloud_file_client file_client =
  storage_account.create_cloud_file_client();

azure::storage::cloud_file_share share =
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));

if (share.exists())
{
    // Create and assign a policy
    utility::string_t policy_name = _XPLATSTR("sampleSharePolicy");

    azure::storage::file_shared_access_policy sharedPolicy =
      azure::storage::file_shared_access_policy();

    //set permissions to expire in 90 minutes
    sharedPolicy.set_expiry(utility::datetime::utc_now() +
       utility::datetime::from_minutes(90));

    //give read and write permissions
    sharedPolicy.set_permissions(azure::storage::file_shared_access_policy::permissions::write | azure::storage::file_shared_access_policy::permissions::read);

    //set permissions for the share
    azure::storage::file_share_permissions permissions;

    //retrieve the current list of shared access policies
    azure::storage::shared_access_policies<azure::storage::file_shared_access_policy> policies;

    //add the new shared policy
    policies.insert(std::make_pair(policy_name, sharedPolicy));

    //save the updated policy list
    permissions.set_policies(policies);
    share.upload_permissions(permissions);

    //Retrieve the root directory and file references
    azure::storage::cloud_file_directory root_dir =
        share.get_root_directory_reference();
    azure::storage::cloud_file file =
      root_dir.get_file_reference(_XPLATSTR("my-sample-file-1"));

    // Generate a SAS for a file in the share
    //  and associate this access policy with it.
    utility::string_t sas_token = file.get_shared_access_signature(sharedPolicy);

    // Create a new CloudFile object from the SAS, and write some text to the file.
    azure::storage::cloud_file file_with_sas(azure::storage::storage_credentials(sas_token).transform_uri(file.uri().primary_uri()));
    utility::string_t text = _XPLATSTR("My sample content");
    file_with_sas.upload_text(text);

    //Download and print URL with SAS.
    utility::string_t downloaded_text = file_with_sas.download_text();
    ucout << downloaded_text << std::endl;
    ucout << azure::storage::storage_credentials(sas_token).transform_uri(file.uri().primary_uri()).to_string() << std::endl;

}
```

## <a name="next-steps"></a>Volgende stappen

Zie de volgende bronnen voor meer informatie over Azure Storage:

* [Opslagclientbibliotheek voor C++](https://github.com/Azure/azure-storage-cpp)
* [Voorbeelden van Azure Storage File Service in C++](https://github.com/Azure-Samples/storage-file-cpp-getting-started)
* [Azure Storage Explorer](https://go.microsoft.com/fwlink/?LinkID=822673&clcid=0x409)
* [Documentatie bij Azure Storage](https://azure.microsoft.com/documentation/services/storage/)