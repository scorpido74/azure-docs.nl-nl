---
title: Aan de slag met Blob Storage met behulp van Visual Studio (ASP.NET Core)
description: Aan de slag met Azure Blob Storage in een Visual Studio ASP.NET Core-project nadat u een opslag account hebt gemaakt met Visual Studio Connected Services
services: storage
author: ghogen
manager: jillfra
ms.assetid: 094b596a-c92c-40c4-a0f5-86407ae79672
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/14/2017
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 81df41470c893f569fd17345e8bdf4b29641ec64
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/13/2019
ms.locfileid: "72298837"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet-core"></a>Aan de slag met Azure Blob Storage en Visual Studio Connected Services (ASP.NET Core)

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

In dit artikel wordt beschreven hoe u aan de slag gaat met Azure Blob Storage in Visual Studio nadat u een Azure-opslag account in een ASP.NET Core project hebt gemaakt of hiernaar wordt verwezen met behulp van de functie voor **verbonden services** van Visual Studio. **Met de verbinding Connected Services** worden de juiste NuGet-pakketten geïnstalleerd voor toegang tot Azure Storage in uw project en wordt de Connection String voor het opslag account toegevoegd aan uw project configuratie bestanden. (Zie [opslag documentatie](https://azure.microsoft.com/documentation/services/storage/) voor algemene informatie over Azure Storage.)

Azure Blob-opslag is een service voor het opslaan van grote hoeveel heden ongestructureerde gegevens die overal ter wereld toegankelijk zijn via HTTP of HTTPS. Eén Blob kan elke grootte hebben. Blobs kunnen dingen zijn zoals afbeeldingen, audio-en video bestanden, onbewerkte gegevens en document bestanden. In dit artikel wordt beschreven hoe u aan de slag gaat met Blob Storage nadat u een Azure-opslag account hebt gemaakt met behulp van de met Visual Studio **verbonden services** in een ASP.net core-project.

Net zoals bestanden Live in mappen, opslag-blobs in containers. Nadat u een BLOB hebt gemaakt, maakt u een of meer containers in die blob. In een blob met de naam ' Plakboek ' kunt u bijvoorbeeld containers maken met de naam ' installatie kopieën ' om afbeeldingen op te slaan en een andere met de naam ' Audio ' om audio bestanden op te slaan. Nadat u de containers hebt gemaakt, kunt u er afzonderlijke bestanden naar uploaden. Zie [Quick Start: blobs uploaden, downloaden en vermelden met behulp van .net](../storage/blobs/storage-quickstart-blobs-dotnet.md) voor meer informatie over het programmatisch manipuleren van blobs.

Sommige van de Azure Storage Api's zijn asynchroon en de code in dit artikel gaat ervan uit dat er asynchrone methoden worden gebruikt. Zie [asynchrone programmering](https://docs.microsoft.com/dotnet/csharp/async) voor meer informatie.

## <a name="access-blob-containers-in-code"></a>Toegang krijgen tot BLOB-containers in code

Als u via een programma toegang wilt krijgen tot blobs in ASP.NET Core projecten, moet u de volgende code toevoegen als deze nog niet aanwezig is:

1. Voeg de vereiste `using`-instructies toe:

    ```cs
    using Microsoft.Extensions.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Extensions.Logging.LogLevel;
    ```

1. Een `CloudStorageAccount`-object ophalen dat de gegevens van uw opslag account vertegenwoordigt. Gebruik de volgende code om uw opslag connection string en informatie over het opslag account op te halen uit de configuratie van de Azure-service:

    ```cs
     CloudStorageAccount storageAccount = new CloudStorageAccount(
        new Microsoft.WindowsAzure.Storage.Auth.StorageCredentials(
        "<storage-account-name>",
        "<access-key>"), true);
    ```

1. Gebruik een `CloudBlobClient`-object om een `CloudBlobContainer`-verwijzing naar een bestaande container in uw opslag account op te halen:

    ```cs
    // Create a blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get a reference to a container named "mycontainer."
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    ```

## <a name="create-a-container-in-code"></a>Een container maken in code

U kunt ook de `CloudBlobClient` gebruiken om een container in uw opslag account te maken door `CreateIfNotExistsAsync` aan te roepen:

```cs
// Create a blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Get a reference to a container named "my-new-container."
CloudBlobContainer container = blobClient.GetContainerReference("my-new-container");

// If "mycontainer" doesn't exist, create it.
await container.CreateIfNotExistsAsync();
```

Als u de bestanden in de container voor iedereen beschikbaar wilt maken, stelt u de container in op openbaar:

```cs
await container.SetPermissionsAsync(new BlobContainerPermissions
{
    PublicAccess = BlobContainerPublicAccessType.Blob
});
```

## <a name="upload-a-blob-into-a-container"></a>Een blob uploaden naar een container

Als u een blob-bestand naar een container wilt uploaden, haalt u een container verwijzing op en gebruikt u deze om een BLOB-verwijzing op te halen. Upload vervolgens een gegevens stroom naar die verwijzing door de `UploadFromStreamAsync`-methode aan te roepen. Met deze bewerking wordt de BLOB gemaakt als deze nog niet bestaat en overschrijft een bestaande blob. 

```cs
// Get a reference to a blob named "myblob".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

// Create or overwrite the "myblob" blob with the contents of a local file
// named "myfile".
using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
{
    await blockBlob.UploadFromStreamAsync(fileStream);
}
```

## <a name="list-the-blobs-in-a-container"></a>De blobs in een container in een lijst weergeven

Als u de blobs in een container wilt weer geven, moet u eerst een container verwijzing ophalen en vervolgens de `ListBlobsSegmentedAsync`-methode aanroepen om de blobs en/of de mappen erin op te halen. Om toegang te krijgen tot de uitgebreide set eigenschappen en methoden voor een geretourneerde `IListBlobItem`, moet u deze converteren naar een `CloudBlockBlob`-, `CloudPageBlob`-of `CloudBlobDirectory`-object. Als u het type BLOB niet weet, gebruikt u een type controle om te bepalen waarnaar het moet worden geconverteerd.

```cs
BlobContinuationToken token = null;
do
{
    BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(token);
    token = resultSegment.ContinuationToken;

    foreach (IListBlobItem item in resultSegment.Results)
    {
        if (item.GetType() == typeof(CloudBlockBlob))
        {
            CloudBlockBlob blob = (CloudBlockBlob)item;
            Console.WriteLine("Block blob of length {0}: {1}", blob.Properties.Length, blob.Uri);
        }

        else if (item.GetType() == typeof(CloudPageBlob))
        {
            CloudPageBlob pageBlob = (CloudPageBlob)item;

            Console.WriteLine("Page blob of length {0}: {1}", pageBlob.Properties.Length, pageBlob.Uri);
        }

        else if (item.GetType() == typeof(CloudBlobDirectory))
        {
            CloudBlobDirectory directory = (CloudBlobDirectory)item;

            Console.WriteLine("Directory: {0}", directory.Uri);
        }
    }
} while (token != null);
```

Zie [Quick Start: blobs uploaden, downloaden en vermelden met behulp van .net](../storage/blobs/storage-quickstart-blobs-dotnet.md#list-the-blobs-in-a-container) voor andere manieren om de inhoud van een BLOB-container weer te geven.

## <a name="download-a-blob"></a>Een blob downloaden

Als u een BLOB wilt downloaden, moet u eerst een verwijzing naar de BLOB ophalen en vervolgens de `DownloadToStreamAsync`-methode aanroepen. In het volgende voor beeld wordt de methode `DownloadToStreamAsync` gebruikt om de blob-inhoud over te dragen naar een Stream-object dat u vervolgens als lokaal bestand kunt opslaan.

```cs
// Get a reference to a blob named "photo1.jpg".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

// Save the blob contents to a file named "myfile".
using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
{
    await blockBlob.DownloadToStreamAsync(fileStream);
}
```

Zie [Quick Start: blobs uploaden, downloaden en vermelden met behulp van .net](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-blobs) voor andere manieren om blobs op te slaan als bestanden.

## <a name="delete-a-blob"></a>Een blob verwijderen

Als u een BLOB wilt verwijderen, moet u eerst een verwijzing naar de BLOB ophalen en vervolgens de methode `DeleteAsync` aanroepen:

```cs
// Get a reference to a blob named "myblob.txt".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

// Delete the blob.
await blockBlob.DeleteAsync();
```

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]
