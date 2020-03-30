---
title: Blobs weergeven met .NET - Azure Storage
description: Meer informatie over het weergeven van blobs in een container in uw Azure Storage-account met behulp van de .NET-clientbibliotheek. In codevoorbeelden wordt uitgelegd hoe blobs in een platte vermelding worden weergegeven of hoe blobs hiërarchisch worden weergegeven, alsof ze zijn ingedeeld in mappen of mappen.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 02/25/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: eb62883859a3efeb1c05deb38d8a40fba76e9cdf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137917"
---
# <a name="list-blobs-with-net"></a>Blobs weergeven met .NET

Wanneer u blobs uit uw code opgeeft, u een aantal opties opgeven om te beheren hoe resultaten worden geretourneerd vanuit Azure Storage. U het aantal resultaten opgeven dat in elke reeks resultaten moet worden retournerd en vervolgens de volgende sets ophalen. U een voorvoegsel opgeven om blobs terug te geven waarvan de namen beginnen met dat teken of tekenreeks. En u blobs in een platte lijststructuur of hiërarchisch weergeven. Een hiërarchische vermelding retourneert blobs alsof ze zijn ingedeeld in mappen. 

In dit artikel ziet u hoe u blobs weergeven met behulp van de [Azure Storage-clientbibliotheek voor .NET](/dotnet/api/overview/azure/storage?view=azure-dotnet).  

## <a name="understand-blob-listing-options"></a>Opties voor blob-aanbiedingen begrijpen

Als u de blobs in een opslagaccount wilt weergeven, roept u een van de volgende methoden aan:

- [CloudBlobClient.ListBlobs](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobs)
- [CloudBlobClient.ListBlobsGesegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobssegmented)
- [CloudBlobClient.ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobssegmentedasync)

Als u de blobs in een container wilt weergeven, roept u een van de volgende methoden aan:

- [CloudBlobContainer.ListBlobs](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobs)
- [CloudBlobContainer.ListBlobsGesegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmented)
- [CloudBlobContainer.ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmentedasync)

De overbelasting voor deze methoden biedt extra opties voor het beheren van hoe blobs worden geretourneerd door de aanbiedingsbewerking. Deze opties worden beschreven in de volgende secties.

### <a name="manage-how-many-results-are-returned"></a>Beheren hoeveel resultaten worden geretourneerd

Standaard levert een aanbiedingsbewerking maximaal 5000 resultaten tegelijk op. Als u een kleinere set resultaten wilt retourneren, geeft u een niet-nulwaarde voor de `maxresults` parameter op wanneer u een van de methoden **ListBlobs** aanroept.

Als een lijstbewerking meer dan 5000 blobs retourneert `maxresults` of als u een waarde hebt opgegeven voor een zodanige waarde dat de lijstbewerking een subset van containers in het opslagaccount retourneert, retourneert Azure Storage een *vervolgtoken* met de lijst met blobs. Een vervolgtoken is een ondoorzichtige waarde die u gebruiken om de volgende set resultaten uit Azure Storage op te halen.

Controleer in uw code de waarde van het vervolgtoken om te bepalen of het null is. Wanneer het vervolgtoken null is, is de set resultaten voltooid. Als het vervolgtoken niet null is, roept u opnieuw de bewerking van de lijst aan en geeft u het vervolgtoken door om de volgende set resultaten op te halen, totdat het vervolgtoken null is.

### <a name="filter-results-with-a-prefix"></a>Resultaten filteren met een voorvoegsel

Als u de lijst met containers `prefix` wilt filteren, geeft u een tekenreeks op voor de parameter. De tekenreeks voor het voorvoegsel kan een of meer tekens bevatten. Azure Storage retourneert vervolgens alleen de blobs waarvan de namen beginnen met dat voorvoegsel.

### <a name="return-metadata"></a>Metagegevens retourneren

Als u blobmetagegevens wilt retourneren met de resultaten, geeft u de **waarde met aforatie met contactpersonen** op voor de opsomming [bloblistingdetails.](/dotnet/api/microsoft.azure.storage.blob.bloblistingdetails) Azure Storage bevat metagegevens waarbij elke blob wordt geretourneerd, zodat u in deze context geen van de methoden **FetchAttributes** hoeft aan te roepen om de blob-metagegevens op te halen.

### <a name="flat-listing-versus-hierarchical-listing"></a>Platte aanbieding versus hiërarchische aanbieding

Blobs in Azure Storage zijn georganiseerd in een vlak paradigma, in plaats van een hiërarchisch paradigma (zoals een klassiek bestandssysteem). U echter blobs ordenen in *virtuele mappen* om een mapstructuur na te bootsen. Een virtuele map maakt deel uit van de naam van de blob en wordt aangegeven door het tekensteken van de scheidingsteken.

Als u blobs wilt ordenen in virtuele mappen, gebruikt u een scheidingstekenteken in de naam van de blob. Het standaardscheidingstekenteken is een voorwaartse slash (/), maar u elk teken opgeven als de scheidingsteken.

Als u uw blobs een naam geeft met een scheidingsteken, u ervoor kiezen blobs hiërarchisch te vermelden. Voor een hiërarchische lijstbewerking retourneert Azure Storage alle virtuele mappen en blobs onder het bovenliggende object. U de bewerking van de vermelding recursief aanroepen om de hiërarchie te doorlopen, vergelijkbaar met hoe u een klassiek bestandssysteem programmatisch zou doorkruisen.

## <a name="use-a-flat-listing"></a>Een platte aanbieding gebruiken

Standaard retourneert een aanbiedingsbewerking blobs in een platte aanbieding. In een platte lijst worden blobs niet geordend op virtuele map.

In het volgende voorbeeld worden de blobs in de opgegeven container weergegeven met een platte vermelding, waarbij een optionele segmentgrootte is opgegeven en wordt de blobnaam naar een consolevenster geschreven.

```csharp
private static async Task ListBlobsFlatListingAsync(CloudBlobContainer container, int? segmentSize)
{
    BlobContinuationToken continuationToken = null;
    CloudBlob blob;

    try
    {
        // Call the listing operation and enumerate the result segment.
        // When the continuation token is null, the last segment has been returned
        // and execution can exit the loop.
        do
        {
            BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(string.Empty,
                true, BlobListingDetails.Metadata, segmentSize, continuationToken, null, null);

            foreach (var blobItem in resultSegment.Results)
            {
                // A flat listing operation returns only blobs, not virtual directories.
                blob = (CloudBlob)blobItem;

                // Write out some blob properties.
                Console.WriteLine("Blob name: {0}", blob.Name);
            }

            Console.WriteLine();

           // Get the continuation token and loop until it is null.
           continuationToken = resultSegment.ContinuationToken;

        } while (continuationToken != null);
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

De monsteruitvoer is vergelijkbaar met:

```
Blob name: FolderA/blob1.txt
Blob name: FolderA/blob2.txt
Blob name: FolderA/blob3.txt
Blob name: FolderA/FolderB/blob1.txt
Blob name: FolderA/FolderB/blob2.txt
Blob name: FolderA/FolderB/blob3.txt
Blob name: FolderA/FolderB/FolderC/blob1.txt
Blob name: FolderA/FolderB/FolderC/blob2.txt
Blob name: FolderA/FolderB/FolderC/blob3.txt
```

## <a name="use-a-hierarchical-listing"></a>Een hiërarchische vermelding gebruiken

Wanneer u een lijstbewerking hiërarchisch aanroept, retourneert Azure Storage de virtuele mappen en blobs op het eerste niveau van de hiërarchie. De eigenschap [Prefix](/dotnet/api/microsoft.azure.storage.blob.cloudblobdirectory.prefix) van elke virtuele map is zo ingesteld dat u het voorvoegsel doorgeven in een recursieve aanroep om de volgende map op te halen.

Als u blobs hiërarchisch `useFlatBlobListing` wilt weergeven, stelt u de parameter van de aanbiedingsmethode in op **false**.

In het volgende voorbeeld worden de blobs in de opgegeven container weergegeven met een platte vermelding, waarbij een optionele segmentgrootte is opgegeven en wordt de blobnaam naar het consolevenster geschreven.

```csharp
private static async Task ListBlobsHierarchicalListingAsync(CloudBlobContainer container, string prefix)
{
    CloudBlobDirectory dir;
    CloudBlob blob;
    BlobContinuationToken continuationToken = null;

    try
    {
        // Call the listing operation and enumerate the result segment.
        // When the continuation token is null, the last segment has been returned and
        // execution can exit the loop.
        do
        {
            BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(prefix,
                false, BlobListingDetails.Metadata, null, continuationToken, null, null);
            foreach (var blobItem in resultSegment.Results)
            {
                // A hierarchical listing may return both virtual directories and blobs.
                if (blobItem is CloudBlobDirectory)
                {
                    dir = (CloudBlobDirectory)blobItem;

                    // Write out the prefix of the virtual directory.
                    Console.WriteLine("Virtual directory prefix: {0}", dir.Prefix);

                    // Call recursively with the prefix to traverse the virtual directory.
                    await ListBlobsHierarchicalListingAsync(container, dir.Prefix);
                }
                else
                {
                    // Write out the name of the blob.
                    blob = (CloudBlob)blobItem;

                    Console.WriteLine("Blob name: {0}", blob.Name);
                }
                Console.WriteLine();
            }

            // Get the continuation token and loop until it is null.
            continuationToken = resultSegment.ContinuationToken;

        } while (continuationToken != null);
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

De monsteruitvoer is vergelijkbaar met:

```
Virtual directory prefix: FolderA/
Blob name: FolderA/blob1.txt
Blob name: FolderA/blob2.txt
Blob name: FolderA/blob3.txt

Virtual directory prefix: FolderA/FolderB/
Blob name: FolderA/FolderB/blob1.txt
Blob name: FolderA/FolderB/blob2.txt
Blob name: FolderA/FolderB/blob3.txt

Virtual directory prefix: FolderA/FolderB/FolderC/
Blob name: FolderA/FolderB/FolderC/blob1.txt
Blob name: FolderA/FolderB/FolderC/blob2.txt
Blob name: FolderA/FolderB/FolderC/blob3.txt
```

> [!NOTE]
> Blob-momentopnamen kunnen niet worden weergegeven in een hiërarchische lijstbewerking.

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>Volgende stappen

- [Blobs weergeven](/rest/api/storageservices/list-blobs)
- [Blobbronnen opsommen](/rest/api/storageservices/enumerating-blob-resources)
