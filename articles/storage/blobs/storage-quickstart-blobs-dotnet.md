---
title: 'Snelstartgids: Azure Blob-opslag bibliotheek V12-.NET'
description: In deze Quick Start leert u hoe u de Azure Blob Storage-client bibliotheek versie 12 voor .NET kunt gebruiken om een container en een BLOB in Blob-opslag (object) te maken. Hierna leert u hoe u de blob naar uw lokale computer downloadt en hoe u alle blobs in een container kunt weergeven.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/05/2019
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: 2a1a9b1973ded5db7182fb1898fc7222904c39c3
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/10/2020
ms.locfileid: "75863958"
---
# <a name="quickstart-azure-blob-storage-client-library-v12-for-net"></a>Snelstartgids: Azure Blob Storage-client bibliotheek V12 voor .NET

Ga aan de slag met de Azure Blob Storage-client bibliotheek V12 voor .NET. Azure Blob Storage is Microsoft's oplossing voor opslag van objecten in de cloud. Volg de stappen om het pakket te installeren en voorbeeld code voor basis taken uit te proberen. Blob Storage is geoptimaliseerd voor het opslaan van grote hoeveelheden ongestructureerde gegevens.

> [!NOTE]
> Zie [Snelstartgids: Azure Blob Storage-client bibliotheek voor .net](storage-quickstart-blobs-dotnet-legacy.md)om aan de slag te gaan met de vorige SDK-versie.

Gebruik de Azure Blob Storage-client bibliotheek V12 voor .NET voor het volgende:

* Een container maken
* Een BLOB uploaden naar Azure Storage
* Alle blobs in een container weer geven
* De BLOB downloaden naar uw lokale computer
* Een container verwijderen

[API-referentie documentatie](/dotnet/api/azure.storage.blobs) | - [bibliotheek bron code](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs) | [pakket (NuGet)](https://www.nuget.org/packages/Azure.Storage.Blobs) | -voor [beelden](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/)
* Azure Storage-account: [een opslag account maken](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* Huidige [.net core SDK](https://dotnet.microsoft.com/download/dotnet-core) voor uw besturings systeem. Zorg ervoor dat u de SDK en niet de runtime ophaalt.

## <a name="setting-up"></a>Instellen

In deze sectie wordt uitgelegd hoe u een project voorbereidt dat werkt met de Azure Blob Storage-client bibliotheek V12 voor .NET.

### <a name="create-the-project"></a>Het project maken

Maak een .NET core-toepassing met de naam *BlobQuickstartV12*.

1. Gebruik in een console venster (zoals cmd, Power shell of bash) de opdracht `dotnet new` om een nieuwe console-app te maken met de naam *BlobQuickstartV12*. Met deze opdracht maakt u een eenvoudig ' C# Hallo wereld '-project met één bron bestand: *Program.cs*.

   ```console
   dotnet new console -n BlobQuickstartV12
   ```

1. Schakel over naar de zojuist gemaakte *BlobQuickstartV12* -map.

   ```console
   cd BlobQuickstartV12
   ```

1. Maak in de Directory *BlobQuickstartV12* een andere map met de naam *Data*. Hier worden de BLOB-gegevens bestanden gemaakt en opgeslagen.

    ```console
    mkdir data
    ```

### <a name="install-the-package"></a>Het pakket installeren

Terwijl u nog steeds in de toepassingsmap, installeert u de Azure Blob Storage-client bibliotheek voor .NET-pakket met behulp van de `dotnet add package` opdracht.

```console
dotnet add package Azure.Storage.Blobs
```

### <a name="set-up-the-app-framework"></a>Het app-Framework instellen

Vanuit de projectmap:

1. Open het *Program.cs* -bestand in de editor
1. De `Console.WriteLine("Hello World!");`-instructie verwijderen
1. `using`-instructies toevoegen
1. De declaratie van de `Main` methode bijwerken ter ondersteuning van async-code

Hier volgt de code:

```csharp
using Azure.Storage;
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

## <a name="object-model"></a>Object model

Azure Blob-opslag is geoptimaliseerd voor het opslaan van enorme hoeveel heden ongestructureerde gegevens. Ongestructureerde gegevens zijn gegevens die niet voldoen aan een bepaald gegevensmodel of bepaalde definitie, zoals tekst of binaire gegevens. Er zijn drie typen resources voor blobopslag:

* Het opslag account
* Een container in het opslag account
* Een BLOB in de container

Het volgende diagram geeft de relatie tussen deze resources weer.

![Diagram van de blobopslagarchitectuur](./media/storage-blob-introduction/blob1.png)

Gebruik de volgende .NET-klassen om te communiceren met deze resources:

* [BlobServiceClient](/dotnet/api/azure.storage.blobs.blobserviceclient): met de klasse `BlobServiceClient` kunt u Azure storage resources en BLOB-containers bewerken.
* [BlobContainerClient](/dotnet/api/azure.storage.blobs.blobcontainerclient): met de klasse `BlobContainerClient` kunt u Azure Storage containers en de bijbehorende blobs bewerken.
* [BlobClient](/dotnet/api/azure.storage.blobs.blobclient): met de klasse `BlobClient` kunt u Azure Storage blobs bewerken.
* [BlobDownloadInfo](/dotnet/api/azure.storage.blobs.models.blobdownloadinfo): de klasse `BlobDownloadInfo` vertegenwoordigt de eigenschappen en inhoud die zijn geretourneerd door het downloaden van een blob.

## <a name="code-examples"></a>Codevoorbeelden

In deze voorbeeld code fragmenten ziet u hoe u het volgende kunt uitvoeren met de Azure Blob Storage-client bibliotheek voor .NET:

* [De connection string ophalen](#get-the-connection-string)
* [Een container maken](#create-a-container)
* [Blobs uploaden naar een container](#upload-blobs-to-a-container)
* [De blobs in een container weer geven](#list-the-blobs-in-a-container)
* [Blobs downloaden](#download-blobs)
* [Container verwijderen](#delete-a-container)

### <a name="get-the-connection-string"></a>De verbindingsreeks ophalen

De onderstaande code haalt de connection string voor het opslag account op uit de omgevings variabele die in de sectie [uw opslag Connection String configureren](#configure-your-storage-connection-string) is gemaakt.

Voeg deze code toe in de `Main` methode:

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

Kies een naam voor de nieuwe container. De onderstaande code voegt een GUID-waarde toe aan de container naam om ervoor te zorgen dat deze uniek is.

> [!IMPORTANT]
> Containernamen moeten uit kleine letters bestaan. Zie [Containers, blobs en metagegevens een naam geven en hiernaar verwijderen](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) voor meer informatie over de naamgeving van containers en blobs.

Maak een instantie van de klasse [BlobServiceClient](/dotnet/api/azure.storage.blobs.blobserviceclient) . Vervolgens roept u de [CreateBlobContainerAsync](/dotnet/api/azure.storage.blobs.blobserviceclient.createblobcontainerasync) -methode aan om de container in uw opslag account te maken.

Voeg deze code toe aan het einde van de `Main` methode:

```csharp
// Create a BlobServiceClient object which will be used to create a container client
BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

//Create a unique name for the container
string containerName = "quickstartblobs" + Guid.NewGuid().ToString();

// Create the container and return a container client object
BlobContainerClient containerClient = await blobServiceClient.CreateBlobContainerAsync(containerName);
```

### <a name="upload-blobs-to-a-container"></a>Blobs uploaden naar een container

Het volgende code fragment:

1. Hiermee maakt u een tekst bestand in de lokale *gegevens* Directory.
1. Hiermee wordt een verwijzing naar een [BlobClient](/dotnet/api/azure.storage.blobs.blobclient) -object opgehaald door de methode [GetBlobClient](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobclient) in de container aan te roepen vanuit de sectie [een container maken](#create-a-container) .
1. Hiermee wordt het lokale tekst bestand geüpload naar de BLOB door de methode [UploadAsync](/dotnet/api/azure.storage.blobs.blobclient.uploadasync) aan te roepen. Met deze methode wordt de blob gemaakt als deze nog niet bestaat, of overschreven als dat wel het geval is.

Voeg deze code toe aan het einde van de `Main` methode:

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
await blobClient.UploadAsync(uploadFileStream);
uploadFileStream.Close();
```

### <a name="list-the-blobs-in-a-container"></a>De blobs in een container in een lijst weergeven

Vermeld de blobs in de container door de methode [GetBlobsAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsasync) aan te roepen. In dit geval is er slechts één BLOB aan de container toegevoegd, zodat de vermelding in de lijst alleen die ene BLOB retourneert.

Voeg deze code toe aan het einde van de `Main` methode:

```csharp
Console.WriteLine("Listing blobs...");

// List all blobs in the container
await foreach (BlobItem blobItem in containerClient.GetBlobsAsync())
{
    Console.WriteLine("\t" + blobItem.Name);
}
```

### <a name="download-blobs"></a>Blobs downloaden

Down load de eerder gemaakte BLOB door de methode [DownloadAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.downloadasync) aan te roepen. De voorbeeld code voegt het achtervoegsel ' gedownload ' toe aan de naam van het bestand, zodat u beide bestanden in het lokale bestands systeem kunt zien.

Voeg deze code toe aan het einde van de `Main` methode:

```csharp
// Download the blob to a local file
// Append the string "DOWNLOAD" before the .txt extension 
// so you can compare the files in the data directory
string downloadFilePath = localFilePath.Replace(".txt", "DOWNLOAD.txt");

Console.WriteLine("\nDownloading blob to\n\t{0}\n", downloadFilePath);

// Download the blob's contents and save it to a file
BlobDownloadInfo download = await blobClient.DownloadAsync();

using FileStream downloadFileStream = File.OpenWrite(downloadFilePath);
await download.Content.CopyToAsync(downloadFileStream);
downloadFileStream.Close();
```

### <a name="delete-a-container"></a>Een container verwijderen

Met de volgende code wordt de resources opgeschoond die de app heeft gemaakt door de volledige container te verwijderen met behulp van [DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteasync). Ook worden de lokale bestanden verwijderd die zijn gemaakt door de app.

De app wordt gepauzeerd voor gebruikers invoer door `Console.ReadLine` aan te roepen voordat de blob, container en lokale bestanden worden verwijderd. Dit is een goede kans om te controleren of de resources daad werkelijk zijn gemaakt, voordat ze worden verwijderd.

Voeg deze code toe aan het einde van de `Main` methode:

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

Met deze app wordt een test bestand gemaakt in de lokale *gegevensmap* en geüpload naar Blob Storage. In het voor beeld worden de blobs in de container weer gegeven en wordt het bestand met een nieuwe naam gedownload, zodat u de oude en nieuwe bestanden kunt vergelijken.

Ga naar de toepassingsmap en bouw en voer de toepassing uit.

```console
dotnet build
```

```console
dotnet run
```

De uitvoer van de app is vergelijkbaar met het volgende voor beeld:

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

Controleer voordat u begint met het opschonen de map *Data* voor de twee bestanden. Als u ze opent, ziet u dat ze identiek zijn.

Nadat u de bestanden hebt gecontroleerd, drukt u op **Enter** om de test bestanden te verwijderen en de demo te volt ooien.

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u geleerd hoe u blobs kunt uploaden, downloaden en er een lijst van maken met behulp van .NET.

Als u voor beeld-apps voor Blob-opslag wilt zien, gaat u door naar:

> [!div class="nextstepaction"]
> [Azure Blob Storage SDK V12 .NET-voor beelden](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples)

* Voor zelf studies, voor beelden, snel starten en andere documentatie gaat u naar [Azure voor .net-en .net core-ontwikkel aars](/dotnet/azure/).
* Zie voor meer informatie over .NET Core [Aan de slag met .NET in 10 minuten](https://www.microsoft.com/net/learn/get-started/).
