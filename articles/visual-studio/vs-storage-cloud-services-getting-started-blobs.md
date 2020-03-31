---
title: Aan de slag met blob-opslag met Visual Studio (cloudservices)
description: Aan de slag met Azure Blob-opslag in een cloudserviceproject in Visual Studio nadat u verbinding hebt gemaakt met een opslagaccount met visual studio-verbonden services
services: storage
author: ghogen
manager: jillfra
ms.assetid: 1144a958-f75a-4466-bb21-320b7ae8f304
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: d151e55f627166d8ad7d8affa53740e86cd1e501
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72298805"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Aan de slag met aan Azure Blob Storage en Visual Studio verbonden services (cloudserviceprojecten)
[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Overzicht
In dit artikel wordt beschreven hoe u aan de slag met Azure Blob Storage nadat u een Azure Storage-account hebt gemaakt of verwezen met het dialoogvenster Visual Studio **Add Connected Services** in een Visual Studio-cloudservicesproject. We laten je zien hoe je blobcontainers openen en maken en hoe je veelvoorkomende taken uitvoeren, zoals het uploaden, aanbieden en downloaden van blobs. De voorbeelden zijn\# geschreven in C en gebruiken de [Microsoft Azure Storage Client Library voor .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

Azure Blob Storage is een service voor het opslaan van grote hoeveelheden ongestructureerde gegevens die overal ter wereld toegankelijk zijn via HTTP of HTTPS. Een enkele blob kan elke grootte zijn. Blobs kunnen zaken zijn zoals afbeeldingen, audio- en videobestanden, ruwe gegevens en documentbestanden.

Net zoals bestanden live in mappen, opslag blobs leven in containers. Nadat u een opslag hebt gemaakt, maakt u een of meer containers in de opslag. In een opslag met de naam 'Plakboek' u bijvoorbeeld containers maken in de opslag met de naam 'afbeeldingen' om afbeeldingen op te slaan en een andere zogenaamde 'audio' om audiobestanden op te slaan. Nadat u de containers hebt gemaakt, u afzonderlijke blobbestanden naar deze bestanden uploaden.

* Zie [Aan de slag met Azure Blob-opslag met .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md)voor meer informatie over het programmatisch manipuleren van blobs.
* Zie [Opslagdocumentatie](https://azure.microsoft.com/documentation/services/storage/)voor algemene informatie over Azure Storage.
* Zie [Cloud Services-documentatie](https://azure.microsoft.com/documentation/services/cloud-services/)voor algemene informatie over Azure Cloud Services.
* Zie [ASP.NET](https://www.asp.net)voor meer informatie over het programmeren ASP.NET toepassingen.

## <a name="access-blob-containers-in-code"></a>Blob-containers in code openen
Als u blobs in cloudserviceprojecten programmatisch wilt openen, moet u de volgende items toevoegen als ze nog niet aanwezig zijn.

1. Voeg de volgende codenaamruimtedeclaratie toe aan de bovenkant van een C#-bestand waarin u programmatisch toegang wilt krijgen tot Azure Storage.
   
        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;
        using System.Threading.Tasks;
        using LogLevel = Microsoft.Framework.Logging.LogLevel;
2. Download een **CloudStorageAccount-object** dat uw opslagaccountgegevens vertegenwoordigt. Gebruik de volgende code om de tekenreeks van uw opslagverbinding en opslagaccountgegevens uit de Azure-serviceconfiguratie te halen.
   
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("<storage account name>_AzureStorageConnectionString"));
3. Download een **CloudBlobClient-object** dat verwijst naar een bestaande container in uw opslagaccount.
   
        // Create a blob client.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
4. Zorg ervoor dat een **CloudBlobContainer-object** verwijst naar een specifieke blobcontainer.
   
        // Get a reference to a container named "mycontainer."
        CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

> [!NOTE]
> Gebruik alle code die in de vorige procedure wordt weergegeven voor de code die in de volgende secties wordt weergegeven.
> 
> 

## <a name="create-a-container-in-code"></a>Een container maken in code
> [!NOTE]
> Sommige API's die oproepen uitvoeren naar Azure Storage in ASP.NET zijn asynchroon. Zie [Asynchrone programmering met Async en wacht op](https://msdn.microsoft.com/library/hh191443.aspx) meer informatie. De code in het volgende voorbeeld gaat ervan uit dat u async-programmeermethoden gebruikt.
> 
> 

Als u een container in uw opslagaccount wilt maken, hoeft u alleen maar een oproep toe te voegen aan **CreateIfNotExistsAsync** zoals in de volgende code:

    // If "mycontainer" doesn't exist, create it.
    await container.CreateIfNotExistsAsync();


Als u de bestanden in de container voor iedereen beschikbaar wilt maken, u instellen dat de container openbaar is met behulp van de volgende code.

    await container.SetPermissionsAsync(new BlobContainerPermissions
    {
        PublicAccess = BlobContainerPublicAccessType.Blob
    });


Iedereen op internet kan blobs in een openbare container zien, maar u ze alleen wijzigen of verwijderen als u de juiste toegangssleutel hebt.

## <a name="upload-a-blob-into-a-container"></a>Een blob uploaden naar een container
Azure Storage ondersteunt blokblobs en paginablobs. In de meeste gevallen is een blok-blob het aangewezen type om te gebruiken.

Om een bestand naar een blok-blob te uploaden, haalt u een containerverwijzing op en gebruikt u deze om een blok-blobverwijzing op te halen. Zodra u een blobverwijzing hebt, kunt u er elke gewenste gegevensstroom naar uploaden door de methode **UploadFromStream** aan te roepen. Met deze bewerking wordt de blob gemaakt als deze nog niet bestaat, of overschreven als deze wel al bestaat. Het volgende voorbeeld laat zien hoe u een blob uploadt naar een container. Hierbij wordt ervan uitgegaan dat de container al is gemaakt.

    // Retrieve a reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

    // Create or overwrite the "myblob" blob with contents from a local file.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blockBlob.UploadFromStream(fileStream);
    }

## <a name="list-the-blobs-in-a-container"></a>De blobs in een container in een lijst weergeven
Als u een lijst van de blobs in een container wilt weergeven, moet u eerst een containerverwijzing ophalen. Vervolgens kunt u de methode **ListBlobs** van de container gebruiken voor het ophalen van de blobs en/of de mappen hierin. Als u toegang wilt krijgen tot de uitgebreide set eigenschappen en methoden voor een geretourneerd **IListBlobItem,** moet u deze casten naar een **cloudblockblob-,** **CloudPageBlob-** of **CloudBlobDirectory-object.** Als het type onbekend is, kunt u typecontrole gebruiken om te bepalen waarnaar het moet worden gecast. De volgende code toont hoe de URI van elk item in de **photos**-container wordt opgehaald en uitgevoerd:

    // Loop over items within the container and output the length and URI.
    foreach (IListBlobItem item in container.ListBlobs(null, false))
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

Zoals in het vorige codevoorbeeld wordt weergegeven, heeft de blobservice ook het concept van mappen in containers. Dit is zodat u uw blobs organiseren in een meer map-achtige structuur. Bekijk bijvoorbeeld de volgende set blok-blobs in een container met de naam **photos**:

    photo1.jpg
    2010/architecture/description.txt
    2010/architecture/photo3.jpg
    2010/architecture/photo4.jpg
    2011/architecture/photo5.jpg
    2011/architecture/photo6.jpg
    2011/architecture/description.txt
    2011/photo7.jpg

Wanneer u **ListBlobs** aanroept op de container (zoals in het vorige voorbeeld), bevat de geretourneerde verzameling **CloudBlobDirectory-** en **CloudBlockBlob-objecten** die de mappen en blobs op het hoogste niveau vertegenwoordigen. Hier is de resulterende output:

    Directory: https://<accountname>.blob.core.windows.net/photos/2010/
    Directory: https://<accountname>.blob.core.windows.net/photos/2011/
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


U kunt desgewenst de parameter **UseFlatBlobListing** van de methode **ListBlobs** instellen op **true**. Dit resulteert in elke blob wordt geretourneerd als een **CloudBlockBlob,** ongeacht directory. Hier is de oproep aan **ListBlobs:**

    // Loop over items within the container and output the length and URI.
    foreach (IListBlobItem item in container.ListBlobs(null, true))
    {
       ...
    }

en hier zijn de resultaten:

    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
    Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
    Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
    Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
    Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
    Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg

Zie [CloudBlobContainer.ListBlobs](https://msdn.microsoft.com/library/azure/dd135734.aspx)voor meer informatie.

## <a name="download-blobs"></a>Blobs downloaden
Om blobs te downloaden, moet u eerst een blobverwijzing ophalen en vervolgens de methode **DownloadToStream** aanroepen. In het volgende voorbeeld wordt de methode **DownloadToStream** gebruikt om de blobinhoud over te dragen naar een stroomobject, dat u vervolgens persistent kunt maken in een lokaal bestand.

    // Get a reference to a blob named "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // Save blob contents to a file.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        blockBlob.DownloadToStream(fileStream);
    }

U kunt ook de methode **DownloadToStream** gebruiken om de inhoud van een blob te downloaden als een tekenreeks.

    // Get a reference to a blob named "myblob.txt"
    CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");

    string text;
    using (var memoryStream = new MemoryStream())
    {
        blockBlob2.DownloadToStream(memoryStream);
        text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
    }

## <a name="delete-blobs"></a>Blobs verwijderen
Als u een blob wilt verwijderen, krijgt u eerst een blobverwijzing en roept u vervolgens de methode **Verwijderen** aan.

    // Get a reference to a blob named "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // Delete the blob.
    blockBlob.Delete();


## <a name="list-blobs-in-pages-asynchronously"></a>Blobs asynchroon op pagina's weergeven
Als u een groot aantal blobs in een lijst weergeeft of als u het aantal resultaten dat per lijst wordt geretourneerd, wilt bepalen, kunt u blobs weergeven op pagina's met resultaten. Dit voorbeeld laat zien hoe resultaten op pagina's asynchroon worden geretourneerd, zodat de uitvoering niet wordt geblokkeerd tijdens het wachten op het retourneren van een groot aantal resultaten.

In dit voorbeeld gebruiken we een platte bloblijst, maar u kunt ook een hiërarchische lijst uitvoeren door de parameter **useFlatBlobListing** van de methode **ListBlobsSegmentedAsync** in te stellen op **false**.

Omdat de voorbeeldmethode een asynchrone bewerking aanroept, moet deze worden voorafgegaan door het sleutelwoord **async** en een **Task**-object retourneren. Het sleutelwoord 'await' dat is opgegeven voor de methode **ListBlobsSegmentedAsync**, onderbreekt de uitvoering van de voorbeeldmethode totdat de taak in de lijst is voltooid.

    async public static Task ListBlobsSegmentedInFlatListing(CloudBlobContainer container)
    {
        // List blobs to the console window, with paging.
        Console.WriteLine("List blobs in pages:");

        int i = 0;
        BlobContinuationToken continuationToken = null;
        BlobResultSegment resultSegment = null;

        // Call ListBlobsSegmentedAsync and enumerate the result segment returned, while the continuation token is non-null.
        // When the continuation token is null, the last page has been returned and execution can exit the loop.
        do
        {
            // This overload allows control of the page size. You can return all remaining results by passing null for the maxResults parameter,
            // or by calling a different overload.
            resultSegment = await container.ListBlobsSegmentedAsync("", true, BlobListingDetails.All, 10, continuationToken, null, null);
            if (resultSegment.Results.Count<IListBlobItem>() > 0) { Console.WriteLine("Page {0}:", ++i); }
            foreach (var blobItem in resultSegment.Results)
            {
                Console.WriteLine("\t{0}", blobItem.StorageUri.PrimaryUri);
            }
            Console.WriteLine();

            //Get the continuation token.
            continuationToken = resultSegment.ContinuationToken;
        }
        while (continuationToken != null);
    }

## <a name="next-steps"></a>Volgende stappen
[!INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]

