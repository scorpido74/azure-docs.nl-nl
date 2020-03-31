---
title: 'Snelstart: Azure Blob-opslagbibliotheek v12 - .NET'
description: In deze snelstart leert u hoe u de Azure Blob-opslagclientbibliotheekversie 12 voor .NET gebruiken om een container en een blob in Blob-opslag (object) te maken. Hierna leert u hoe u de blob naar uw lokale computer downloadt en hoe u alle blobs in een container kunt weergeven.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/05/2019
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: 5cfb0430bc94d347afd75bc01170a71a7ad53565
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240504"
---
# <a name="quickstart-azure-blob-storage-client-library-v12-for-net"></a>Snelstart: Azure Blob-opslagclientbibliotheek v12 voor .NET

Aan de slag met de Azure Blob-opslagclientbibliotheek v12 voor .NET. Azure Blob Storage is Microsoft's oplossing voor opslag van objecten in de cloud. Volg stappen om het pakket te installeren en probeer voorbeeldcode uit voor basistaken. Blob Storage is geoptimaliseerd voor het opslaan van grote hoeveelheden ongestructureerde gegevens.

> [!NOTE]
> Zie [Quickstart: Azure Blob-opslagclientbibliotheek voor .NET om](storage-quickstart-blobs-dotnet-legacy.md)aan de slag te gaan met de vorige SDK-versie.

Gebruik de Azure Blob-opslagclientbibliotheek v12 voor .NET om:

* Een container maken
* Een blob uploaden naar Azure Storage
* Alle blobs in een container weergeven
* De blob downloaden naar uw lokale computer
* Een container verwijderen

[API-naslagdocumentatie](/dotnet/api/azure.storage.blobs) | [Bibliotheekbroncodepakket](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs) | [(NuGet)](https://www.nuget.org/packages/Azure.Storage.Blobs) | [Voorbeelden](https://docs.microsoft.com/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement - [maak er gratis een](https://azure.microsoft.com/free/)
* Azure-opslagaccount - [een opslagaccount maken](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* Huidige [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) voor uw besturingssysteem. Zorg ervoor dat u de SDK en niet de runtime.

## <a name="setting-up"></a>Instellen

In deze sectie u een project voorbereiden om te werken met de Azure Blob-opslagclientbibliotheek v12 voor .NET.

### <a name="create-the-project"></a>Het project maken

Maak een .NET Core-toepassing met de naam *BlobQuickstartV12*.

1. Gebruik de `dotnet new` opdracht om in een consolevenster (zoals cmd, PowerShell of Bash) een nieuwe console-app te maken met de naam *BlobQuickstartV12.* Met deze opdracht wordt een eenvoudig "Hello World" C#-project gemaakt met één bronbestand: *Program.cs*.

   ```console
   dotnet new console -n BlobQuickstartV12
   ```

1. Overschakelen naar de nieuw gemaakte *BlobQuickstartV12-map.*

   ```console
   cd BlobQuickstartV12
   ```

1. Maak aan de zijkant van de *blobQuickstartV12-map* een andere map met de naam *gegevens*. Dit is waar de blob-gegevensbestanden worden gemaakt en opgeslagen.

    ```console
    mkdir data
    ```

### <a name="install-the-package"></a>Het pakket installeren

Installeer de Azure Blob-opslagclientbibliotheek voor .NET-pakket met `dotnet add package` behulp van de opdracht terwijl u zich nog in de toepassingsmap bevindt.

```console
dotnet add package Azure.Storage.Blobs
```

### <a name="set-up-the-app-framework"></a>Het app-framework instellen

In de projectmap:

1. Het *Program.cs-bestand* openen in uw editor
1. De `Console.WriteLine("Hello World!");` instructie verwijderen
1. Richtlijnen `using` toevoegen
1. De `Main` methodedeclaratie bijwerken om async-code te ondersteunen

Hier is de code:

```csharp
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using System;
using System.IO;
using System.Threading.Tasks;

namespace BlobQuickstartV12
{
    class Program
    {
        static async Task Main()
        {
        }
    }
}
```

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Objectmodel

Azure Blob-opslag is geoptimaliseerd voor het opslaan van enorme hoeveelheden ongestructureerde gegevens. Ongestructureerde gegevens zijn gegevens die niet voldoen aan een bepaald gegevensmodel of bepaalde definitie, zoals tekst of binaire gegevens. Er zijn drie typen resources voor blobopslag:

* Het opslagaccount
* Een container in het opslagaccount
* Een blob in de container

Het volgende diagram geeft de relatie tussen deze resources weer.

![Diagram van de blobopslagarchitectuur](./media/storage-blobs-introduction/blob1.png)

Gebruik de volgende .NET-klassen om met deze bronnen te communiceren:

* [BlobServiceClient:](/dotnet/api/azure.storage.blobs.blobserviceclient) `BlobServiceClient` met de klasse u Azure Storage-bronnen en blob-containers manipuleren.
* [BlobContainerClient:](/dotnet/api/azure.storage.blobs.blobcontainerclient) `BlobContainerClient` met de klasse u Azure Storage-containers en hun blobs manipuleren.
* [BlobClient:](/dotnet/api/azure.storage.blobs.blobclient) `BlobClient` met de klasse u Azure Storage-blobs manipuleren.
* [BlobDownloadInfo:](/dotnet/api/azure.storage.blobs.models.blobdownloadinfo) `BlobDownloadInfo` De klasse vertegenwoordigt de eigenschappen en inhoud die zijn geretourneerd van het downloaden van een blob.

## <a name="code-examples"></a>Codevoorbeelden

In deze voorbeeldcodefragmenten ziet u hoe u het volgende uitvoeren met de Azure Blob-opslagclientbibliotheek voor .NET:

* [De verbindingsreeks ophalen](#get-the-connection-string)
* [Een container maken](#create-a-container)
* [Blobs uploaden naar een container](#upload-blobs-to-a-container)
* [De blobs in een container in een lijst weergeven](#list-the-blobs-in-a-container)
* [Blobs downloaden](#download-blobs)
* [Een container verwijderen](#delete-a-container)

### <a name="get-the-connection-string"></a>De verbindingsreeks ophalen

De onderstaande code haalt de verbindingstekenreeks voor het opslagaccount op uit de omgevingsvariabele die is gemaakt in de sectie [Uw opslagverbindingstekenreeks configureren.](#configure-your-storage-connection-string)

Voeg deze code `Main` toe in de methode:

```csharp
Console.WriteLine("Azure Blob storage v12 - .NET quickstart sample\n");

// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the
// environment variable is created after the application is launched in a
// console or with Visual Studio, the shell or application needs to be closed
// and reloaded to take the environment variable into account.
string connectionString = Environment.GetEnvironmentVariable("AZURE_STORAGE_CONNECTION_STRING");
```

### <a name="create-a-container"></a>Een container maken

Bepaal een naam voor de nieuwe container. De onderstaande code voegt een GUID-waarde toe aan de containernaam om ervoor te zorgen dat deze uniek is.

> [!IMPORTANT]
> Containernamen moeten uit kleine letters bestaan. Zie [Containers, blobs en metagegevens een naam geven en hiernaar verwijderen](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) voor meer informatie over de naamgeving van containers en blobs.

Maak een instantie van de klasse [BlobServiceClient.](/dotnet/api/azure.storage.blobs.blobserviceclient) Roep vervolgens de methode [CreateBlobContainerAsync](/dotnet/api/azure.storage.blobs.blobserviceclient.createblobcontainerasync) aan om de container in uw opslagaccount te maken.

Voeg deze code toe `Main` aan het einde van de methode:

```csharp
// Create a BlobServiceClient object which will be used to create a container client
BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

//Create a unique name for the container
string containerName = "quickstartblobs" + Guid.NewGuid().ToString();

// Create the container and return a container client object
BlobContainerClient containerClient = await blobServiceClient.CreateBlobContainerAsync(containerName);
```

### <a name="upload-blobs-to-a-container"></a>Blobs uploaden naar een container

Het volgende codefragment:

1. Hiermee maakt u een tekstbestand in de lokale *gegevensmap.*
1. Hier wordt een verwijzing naar een [BlobClient-object](/dotnet/api/azure.storage.blobs.blobclient) weergegeven door de [methode GetBlobClient](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobclient) op de container aan te roepen vanuit de sectie [Een container](#create-a-container) maken.
1. Uploadt het lokale tekstbestand naar de blob door de [methode UploadAsync](/dotnet/api/azure.storage.blobs.blobclient.uploadasync#Azure_Storage_Blobs_BlobClient_UploadAsync_System_IO_Stream_System_Boolean_System_Threading_CancellationToken_) aan te roepen. Met deze methode wordt de blob gemaakt als deze nog niet bestaat, of overschreven als dat wel het geval is.

Voeg deze code toe `Main` aan het einde van de methode:

```csharp
// Create a local file in the ./data/ directory for uploading and downloading
string localPath = "./data/";
string fileName = "quickstart" + Guid.NewGuid().ToString() + ".txt";
string localFilePath = Path.Combine(localPath, fileName);

// Write text to the file
await File.WriteAllTextAsync(localFilePath, "Hello, World!");

// Get a reference to a blob
BlobClient blobClient = containerClient.GetBlobClient(fileName);

Console.WriteLine("Uploading to Blob storage as blob:\n\t {0}\n", blobClient.Uri);

// Open the file and upload its data
using FileStream uploadFileStream = File.OpenRead(localFilePath);
await blobClient.UploadAsync(uploadFileStream, true);
uploadFileStream.Close();
```

### <a name="list-the-blobs-in-a-container"></a>De blobs in een container in een lijst weergeven

Maak een lijst van de blobs in de container door de [methode GetBlobsAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsasync) aan te roepen. In dit geval is er slechts één blob aan de container toegevoegd, zodat de lijstbewerking alleen die ene blob retourneert.

Voeg deze code toe `Main` aan het einde van de methode:

```csharp
Console.WriteLine("Listing blobs...");

// List all blobs in the container
await foreach (BlobItem blobItem in containerClient.GetBlobsAsync())
{
    Console.WriteLine("\t" + blobItem.Name);
}
```

### <a name="download-blobs"></a>Blobs downloaden

Download de eerder gemaakte blob door de [methode DownloadAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.downloadasync) aan te roepen. De voorbeeldcode voegt een achtervoegsel van 'GEDOWNLOAD' toe aan de bestandsnaam, zodat u beide bestanden in het lokale bestandssysteem zien.

Voeg deze code toe `Main` aan het einde van de methode:

```csharp
// Download the blob to a local file
// Append the string "DOWNLOAD" before the .txt extension 
// so you can compare the files in the data directory
string downloadFilePath = localFilePath.Replace(".txt", "DOWNLOAD.txt");

Console.WriteLine("\nDownloading blob to\n\t{0}\n", downloadFilePath);

// Download the blob's contents and save it to a file
BlobDownloadInfo download = await blobClient.DownloadAsync();

using (FileStream downloadFileStream = File.OpenWrite(downloadFilePath))
{
    await download.Content.CopyToAsync(downloadFileStream);
    downloadFileStream.Close();
}
```

### <a name="delete-a-container"></a>Een container verwijderen

Met de volgende code worden de resources die de app heeft gemaakt door de hele container te verwijderen met [DeleteAsync, opschonen.](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteasync) Het verwijdert ook de lokale bestanden die door de app zijn gemaakt.

De app pauzeert voor `Console.ReadLine` gebruikersinvoer door aan te roepen voordat de blob-, container- en lokale bestanden worden verwijderd. Dit is een goede kans om te controleren of de resources daadwerkelijk correct zijn gemaakt, voordat ze worden verwijderd.

Voeg deze code toe `Main` aan het einde van de methode:

```csharp
// Clean up
Console.Write("Press any key to begin clean up");
Console.ReadLine();

Console.WriteLine("Deleting blob container...");
await containerClient.DeleteAsync();

Console.WriteLine("Deleting the local source and downloaded files...");
File.Delete(localFilePath);
File.Delete(downloadFilePath);

Console.WriteLine("Done");
```

## <a name="run-the-code"></a>De code uitvoeren

Deze app maakt een testbestand in uw lokale *gegevensmap* en uploadt het naar Blob-opslag. In het voorbeeld worden vervolgens de blobs in de container weergegeven en wordt het bestand gedownload met een nieuwe naam, zodat u de oude en nieuwe bestanden vergelijken.

Navigeer naar uw toepassingsmap en bouw en voer de toepassing uit.

```console
dotnet build
```

```console
dotnet run
```

De uitvoer van de app is vergelijkbaar met het volgende voorbeeld:

```output
Azure Blob storage v12 - .NET quickstart sample

Uploading to Blob storage as blob:
         https://mystorageacct.blob.core.windows.net/quickstartblobs60c70d78-8d93-43ae-954d-8322058cfd64/quickstart2fe6c5b4-7918-46cb-96f4-8c4c5cb2fd31.txt

Listing blobs...
        quickstart2fe6c5b4-7918-46cb-96f4-8c4c5cb2fd31.txt

Downloading blob to
        ./data/quickstart2fe6c5b4-7918-46cb-96f4-8c4c5cb2fd31DOWNLOADED.txt

Press any key to begin clean up
Deleting blob container...
Deleting the local source and downloaded files...
Done
```

Controleer voordat u met het opschoneproces begint, de *gegevensmap* voor de twee bestanden. Als u ze opent, ziet u dat ze identiek zijn.

Nadat u de bestanden hebt geverifieerd, drukt u op **enter** om de testbestanden te verwijderen en de demo te voltooien.

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u geleerd hoe u blobs kunt uploaden, downloaden en er een lijst van maken met behulp van .NET.

Ga verder met:

> [!div class="nextstepaction"]
> [Azure Blob-opslag SDK v12 .NET-voorbeelden](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples)

* Ga voor zelfstudies, voorbeelden, snelle starts en andere documentatie naar [Azure voor .NET- en .NET Core-ontwikkelaars](/dotnet/azure/).
* Zie voor meer informatie over .NET Core [Aan de slag met .NET in 10 minuten](https://www.microsoft.com/net/learn/get-started/).
