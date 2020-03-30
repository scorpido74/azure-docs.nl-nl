---
title: Blob-containers weergeven met .NET - Azure Storage
description: Meer informatie over het weergeven van blobcontainers in uw Azure Storage-account met behulp van de .NET-clientbibliotheek.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 155b8f5d50c7b106daff8dab4df17200b844c988
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79135901"
---
# <a name="list-blob-containers-with-net"></a>Blobcontainers weergeven met .NET

Wanneer u de containers in een Azure Storage-account opmaakt vanuit uw code, u een aantal opties opgeven om te beheren hoe resultaten worden geretourneerd vanuit Azure Storage. In dit artikel ziet u hoe u containers weergeven met behulp van de [Azure Storage-clientbibliotheek voor .NET](/dotnet/api/overview/azure/storage?view=azure-dotnet).  

## <a name="understand-container-listing-options"></a>Opties voor containervermeldingen begrijpen

Als u containers in uw opslagaccount wilt vermelden, roept u een van de volgende methoden aan:

- [LijstcontainersGesegmenteerd](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmented)
- [LijstContainersSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmentedasync)

De overbelasting voor deze methoden biedt extra opties voor het beheren van de manier waarop containers worden geretourneerd door de lijstbewerking. Deze opties worden beschreven in de volgende secties.

### <a name="manage-how-many-results-are-returned"></a>Beheren hoeveel resultaten worden geretourneerd

Standaard levert een aanbiedingsbewerking maximaal 5000 resultaten tegelijk op. Als u een kleinere set resultaten wilt retourneren, geeft u een niet-nulwaarde voor de `maxresults` parameter op wanneer u een van de methoden **ListContainerSegmented aanroept.**

Als uw opslagaccount meer dan 5000 containers bevat of `maxresults` als u een waarde hebt opgegeven voor een waarde die de lijstbewerking een subset van containers in het opslagaccount retourneert, retourneert Azure Storage een *vervolgtoken* met de lijst met containers. Een vervolgtoken is een ondoorzichtige waarde die u gebruiken om de volgende set resultaten uit Azure Storage op te halen.

Controleer in uw code de waarde van het vervolgtoken om te bepalen of het null is. Wanneer het vervolgtoken null is, is de set resultaten voltooid. Als het vervolgtoken niet null is, belt u **ListContainersSegmented** of **ListContainersSegmentedAsync** opnieuw aan en geeft het vervolgtoken door om de volgende set resultaten op te halen, totdat het vervolgtoken null is.

### <a name="filter-results-with-a-prefix"></a>Resultaten filteren met een voorvoegsel

Als u de lijst met containers `prefix` wilt filteren, geeft u een tekenreeks op voor de parameter. De tekenreeks voor het voorvoegsel kan een of meer tekens bevatten. Azure Storage retourneert vervolgens alleen de containers waarvan de namen met dat voorvoegsel beginnen.

### <a name="return-metadata"></a>Metagegevens retourneren

Als u containermetagegevens wilt retourneren met de resultaten, geeft u de **waarde metagegevens** op voor de opsomming [ContainerListingDetails.](/dotnet/api/microsoft.azure.storage.blob.containerlistingdetails) Azure Storage bevat metagegevens bij elke geretourneerde container, zodat u niet ook een van de methoden **FetchAttributes** hoeft aan te roepen om de containermetagegevens op te halen.

## <a name="example-list-containers"></a>Voorbeeld: Containers aanbieden

In het volgende voorbeeld worden de containers in een opslagaccount asynchroon weergegeven die beginnen met een opgegeven voorvoegsel. In het voorbeeld worden containers in stappen van 5 resultaten tegelijk weergegeven en wordt het vervolgtoken gebruikt om het volgende segment van de resultaten te krijgen. In het voorbeeld worden ook containermetagegevens geretourneerd met de resultaten.

```csharp
private static async Task ListContainersWithPrefixAsync(CloudBlobClient blobClient,
                                                        string prefix)
{
    Console.WriteLine("List all containers beginning with prefix {0}, plus container metadata:", prefix);

    try
    {
        ContainerResultSegment resultSegment = null;
        BlobContinuationToken continuationToken = null;

        do
        {
            // List containers beginning with the specified prefix, returning segments of 5 results each.
            // Passing null for the maxResults parameter returns the max number of results (up to 5000).
            // Requesting the container's metadata with the listing operation populates the metadata,
            // so it's not necessary to also call FetchAttributes() to read the metadata.
            resultSegment = await blobClient.ListContainersSegmentedAsync(
                prefix, ContainerListingDetails.Metadata, 5, continuationToken, null, null);

            // Enumerate the containers returned.
            foreach (var container in resultSegment.Results)
            {
                Console.WriteLine("\tContainer:" + container.Name);

                // Write the container's metadata keys and values.
                foreach (var metadataItem in container.Metadata)
                {
                    Console.WriteLine("\t\tMetadata key: " + metadataItem.Key);
                    Console.WriteLine("\t\tMetadata value: " + metadataItem.Value);
                }
            }

            // Get the continuation token. If not null, get the next segment.
            continuationToken = resultSegment.ContinuationToken;

        } while (continuationToken != null);
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0} : {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
    }
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>Zie ook

[Blobbronnen](/rest/api/storageservices/list-containers2)
[aanbieden om blobbronnen op te sommen](/rest/api/storageservices/enumerating-blob-resources)
