---
title: BLOB-containers weer geven met .NET-Azure Storage
description: Meer informatie over het weer geven van BLOB-containers in uw Azure Storage-account met behulp van de .NET-client bibliotheek.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 06454798deb4a5bc5064e28535a837f73c083e1c
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/29/2019
ms.locfileid: "71671302"
---
# <a name="list-blob-containers-with-net"></a>BLOB-containers weer geven met .NET

Wanneer u de containers in een Azure Storage-account in uw code opneemt, kunt u een aantal opties opgeven om te beheren hoe de resultaten worden geretourneerd door Azure Storage. In dit artikel wordt beschreven hoe u containers kunt weer geven met behulp [van de Azure Storage-client bibliotheek voor .net](/dotnet/api/overview/azure/storage/client).  

## <a name="understand-container-listing-options"></a>Informatie over container opties

Als u containers in uw opslag account wilt weer geven, roept u een van de volgende methoden aan:

- [ListContainersSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmented)
- [ListContainersSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmentedasync)

De Overloads voor deze methoden bieden extra opties voor het beheren van de manier waarop containers door de vermelding worden geretourneerd. Deze opties worden in de volgende secties beschreven.

### <a name="manage-how-many-results-are-returned"></a>Bepalen hoeveel resultaten er worden geretourneerd

Standaard retourneert een lijst bewerking Maxi maal 5000 resultaten per keer. Als u een kleinere set resultaten wilt retour neren, geeft u een andere waarde `maxresults` dan nul op voor de para meter bij het aanroepen van een van de **ListContainerSegmented** -methoden.

Als uw opslag account meer dan 5000 containers bevat of als u een waarde `maxresults` hebt opgegeven zodat de lijst bewerking een subset van containers in het opslag account retourneert, retourneert Azure Storage een *vervolg token* met de lijst met containers. Een vervolg token is een ondoorzichtige waarde die u kunt gebruiken om de volgende set resultaten op te halen uit Azure Storage.

Controleer in uw code de waarde van het vervolg token om te bepalen of deze null is. Wanneer het vervolg token null is, is de set met resultaten voltooid. Als het vervolg token niet null is, roept u **ListContainersSegmented** of **ListContainersSegmentedAsync** opnieuw aan, waarbij u in het vervolg token de volgende set resultaten ophaalt, totdat het vervolg token null is.

### <a name="filter-results-with-a-prefix"></a>Resultaten filteren met een voor voegsel

Als u de lijst met containers wilt filteren, geeft u een `prefix` teken reeks op voor de para meter. De voorvoegsel teken reeks kan een of meer tekens bevatten. Azure Storage retourneert vervolgens alleen de containers waarvan de namen met het voor voegsel beginnen.

### <a name="return-container-metadata"></a>Meta gegevens van container retour neren

Als u de meta gegevens van de container wilt retour neren met de resultaten, geeft u de **meta gegevens** waarde op voor de [ContainerListDetails](/dotnet/api/microsoft.azure.storage.blob.containerlistingdetails) -inventarisatie. Azure Storage bevat de meta gegevens van elke geretourneerde container, dus u hoeft niet ook een van de **FetchAttributes** -methoden aan te roepen om de meta gegevens van de container op te halen.

## <a name="example-list-containers"></a>Voorbeeld: Containers weergeven

In het volgende voor beeld worden de containers in een opslag account die beginnen met een opgegeven voor voegsel, asynchroon weer gegeven. In het voor beeld worden containers in stappen van vijf resultaten tegelijk weer gegeven en wordt het vervolg token gebruikt om het volgende segment met resultaten op te halen. Het voor beeld retourneert ook container-meta gegevens met de resultaten.

```csharp
private static async Task ListContainersWithPrefixAsync(CloudBlobClient blobClient, string prefix)
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

[Containers weer geven](/rest/api/storageservices/list-containers2)voor het inventariseren van[BLOB-resources](/rest/api/storageservices/enumerating-blob-resources) 

