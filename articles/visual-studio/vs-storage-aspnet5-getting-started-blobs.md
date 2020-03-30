---
title: Aan de slag met blob-opslag met Visual Studio (ASP.NET Core)
description: Azure Blob-opslag gebruiken in een Visual Studio ASP.NET Core-project nadat u een opslagaccount hebt gemaakt met visual studio-verbonden services
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72298837"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet-core"></a>Aan de slag met Azure Blob-opslag en Visual Studio connected services (ASP.NET Core)

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

In dit artikel wordt beschreven hoe u aan de slag met Azure Blob-opslag in Visual Studio nadat u een Azure-opslagaccount hebt gemaakt of verwezen in een ASP.NET Core-project met behulp van de functie Visual Studio **Connected Services.** De **bewerking Verbonden Services** installeert de juiste NuGet-pakketten om toegang te krijgen tot Azure-opslag in uw project en voegt de verbindingstekenreeks voor het opslagaccount toe aan uw projectconfiguratiebestanden. (Zie [Opslagdocumentatie](https://azure.microsoft.com/documentation/services/storage/) voor algemene informatie over Azure Storage.)

Azure Blob-opslag is een service voor het opslaan van grote hoeveelheden ongestructureerde gegevens die overal ter wereld toegankelijk zijn via HTTP of HTTPS. Een enkele blob kan elke grootte zijn. Blobs kunnen zaken zijn zoals afbeeldingen, audio- en videobestanden, ruwe gegevens en documentbestanden. In dit artikel wordt beschreven hoe u aan de slag met blobopslag nadat u een Azure-opslagaccount hebt gemaakt met behulp van de Visual Studio **Connected Services** in een ASP.NET Core-project.

Net zoals bestanden live in mappen, opslag blobs leven in containers. Nadat u een blob hebt gemaakt, maakt u een of meer containers in die blob. In een blob met de naam 'Plakboek' u bijvoorbeeld containers maken die 'afbeeldingen' worden genoemd om afbeeldingen op te slaan en een andere zogenaamde 'audio' om audiobestanden op te slaan. Nadat u de containers hebt gemaakt, u afzonderlijke bestanden naar deze bestanden uploaden. Zie [Snelstart: blobs uploaden, downloaden en aanbieden met .NET](../storage/blobs/storage-quickstart-blobs-dotnet.md) voor meer informatie over het programmatisch manipuleren van blobs.

Sommige Azure Storage API's zijn asynchroon en de code in dit artikel gaat ervan uit dat async-methoden worden gebruikt. Zie [Asynchrone programmering](https://docs.microsoft.com/dotnet/csharp/async) voor meer informatie.

## <a name="access-blob-containers-in-code"></a>Blob-containers in code openen

Als u blobs in ASP.NET Core-projecten programmatisch wilt openen, moet u de volgende code toevoegen als deze nog niet aanwezig is:

1. Voeg de `using` nodige instructies toe:

    ```cs
    using Microsoft.Extensions.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Extensions.Logging.LogLevel;
    ```

1. Download `CloudStorageAccount` een object dat uw opslagaccountgegevens vertegenwoordigt. Gebruik de volgende code om uw opslagverbindingstekenreeks en opslagaccountgegevens uit de Azure-serviceconfiguratie te halen:

    ```cs
     CloudStorageAccount storageAccount = new CloudStorageAccount(
        new Microsoft.WindowsAzure.Storage.Auth.StorageCredentials(
        "<storage-account-name>",
        "<access-key>"), true);
    ```

1. Gebruik `CloudBlobClient` een object `CloudBlobContainer` om een verwijzing naar een bestaande container in uw opslagaccount te krijgen:

    ```cs
    // Create a blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get a reference to a container named "mycontainer."
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    ```

## <a name="create-a-container-in-code"></a>Een container maken in code

U de `CloudBlobClient` container ook gebruiken om een `CreateIfNotExistsAsync`container in uw opslagaccount te maken door te bellen naar:

```cs
// Create a blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Get a reference to a container named "my-new-container."
CloudBlobContainer container = blobClient.GetContainerReference("my-new-container");

// If "mycontainer" doesn't exist, create it.
await container.CreateIfNotExistsAsync();
```

Als u de bestanden in de container voor iedereen beschikbaar wilt maken, stelt u de container in als openbaar:

```cs
await container.SetPermissionsAsync(new BlobContainerPermissions
{
    PublicAccess = BlobContainerPublicAccessType.Blob
});
```

## <a name="upload-a-blob-into-a-container"></a>Een blob uploaden naar een container

Als u een blobbestand wilt uploaden naar een container, krijgt u een containerverwijzing en gebruikt u deze om een blobverwijzing te krijgen. Upload vervolgens elke stroom van gegevens `UploadFromStreamAsync` naar die verwijzing door de methode aan te roepen. Met deze bewerking wordt de blob gemaakt als deze er nog niet is en wordt een bestaande blob overschrijft. 

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

Als u de blobs in een container wilt weergeven, krijgt u eerst een containerverwijzing en roept u de `ListBlobsSegmentedAsync` methode aan om de blobs en/of mappen erin op te halen. Als u toegang wilt krijgen tot de `IListBlobItem`uitgebreide set `CloudBlockBlob`eigenschappen `CloudPageBlob`en `CloudBlobDirectory` methoden voor een geretourneerde , castuert u deze naar een , of object. Als u het blobtype niet kent, gebruikt u een typecontrole om te bepalen naar welke u het type wilt casten.

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

Zie [Snelstart: blobs uploaden, downloaden en weergeven met .NET](../storage/blobs/storage-quickstart-blobs-dotnet.md#list-the-blobs-in-a-container) voor andere manieren om de inhoud van een blobcontainer weer te geven.

## <a name="download-a-blob"></a>Een blob downloaden

Als u een blob wilt downloaden, krijgt u `DownloadToStreamAsync` eerst een verwijzing naar de blob en roept u de methode aan. In het volgende `DownloadToStreamAsync` voorbeeld wordt de methode gebruikt om de blob-inhoud over te zetten naar een streamobject dat u vervolgens opslaan als een lokaal bestand.

```cs
// Get a reference to a blob named "photo1.jpg".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

// Save the blob contents to a file named "myfile".
using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
{
    await blockBlob.DownloadToStreamAsync(fileStream);
}
```

Zie [Snelstart: blobs uploaden, downloaden en aanbieden met .NET](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-blobs) voor andere manieren om blobs op te slaan als bestanden.

## <a name="delete-a-blob"></a>Een blob verwijderen

Als u een blob wilt verwijderen, krijgt u `DeleteAsync` eerst een verwijzing naar de blob en roept u de methode aan:

```cs
// Get a reference to a blob named "myblob.txt".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

// Delete the blob.
await blockBlob.DeleteAsync();
```

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]
