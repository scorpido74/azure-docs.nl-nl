---
title: Een blob kopiëren met .NET - Azure Storage
description: Meer informatie over het kopiëren van een blob in uw Azure Storage-account met behulp van de .NET-clientbibliotheek.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/20/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: 9ffa69980f020580376aea447f40ac615f26cf03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79135884"
---
# <a name="copy-a-blob-with-net"></a>Een blob kopiëren met .NET

In dit artikel wordt uitgelegd hoe u een blob kopiëren met een Azure Storage-account. Het toont ook hoe u een asynchrone kopieerbewerking afbreken. De voorbeeldcode maakt gebruik van de [Azure Storage-clientbibliotheek voor .NET](/dotnet/api/overview/azure/storage?view=azure-dotnet).

## <a name="about-copying-blobs"></a>Over het kopiëren van blobs

Wanneer u een blob kopieert binnen hetzelfde opslagaccount, is dit een synchrone bewerking. Wanneer u over accounts kopieert, is dit een asynchrone bewerking. Met de methoden [StartCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy?view=azure-dotnet) en [StartCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync?view=azure-dotnet) wordt een kopie-id-waarde retourneren die wordt gebruikt om de status te controleren of de kopieerbewerking af te breken.

De bronblob voor een kopieerbewerking kan een blokblob, een toevoegende blob, een paginablob of een momentopname zijn. Als de doelblob al bestaat, moet deze van hetzelfde blobtype zijn als de bronblob. Elke bestaande doelblob wordt overschreven. 

De doelblob kan niet worden gewijzigd terwijl een kopieerbewerking wordt uitgevoerd. Een doelblob kan slechts één uitstekende copy blob-bewerking hebben. Met andere woorden, een blob kan niet de bestemming zijn voor meerdere in behandeling zijnde kopieerbewerkingen.

De volledige bronblob of -bestand wordt altijd gekopieerd. Het kopiëren van een reeks bytes of blokken wordt niet ondersteund.

Wanneer een blob wordt gekopieerd, worden de systeemeigenschappen gekopieerd naar de doelblob met dezelfde waarden.

Voor alle blobtypen u de eigenschap [CopyState.Status](/dotnet/api/microsoft.azure.storage.blob.copystate.status?view=azure-dotnet) op de doelblob controleren om de status van de kopieerbewerking te krijgen. De laatste blob wordt vastgelegd wanneer de kopie is voltooid.

Een kopieerbewerking kan een van de volgende formulieren aannemen:

  - U een bronblob kopiëren naar een doelblob met een andere naam. De doelblob kan een bestaande blob van hetzelfde blobtype zijn (blokkeren, toevoegen of pagina) of kan een nieuwe blob zijn die is gemaakt door de kopieerbewerking.
  - U een bronblob kopiëren naar een doelblob met dezelfde naam, waardoor de doelblob effectief wordt vervangen. Een dergelijke kopieerbewerking verwijdert alle niet-vastgelegde blokken en overschrijft de metagegevens van de doelblob.
  - U een bronbestand in de Azure File-service kopiëren naar een doelblob. De doelblob kan een bestaande blokblob zijn of een nieuwe blokblob zijn die is gemaakt door de kopieerbewerking. Kopiëren van bestanden naar paginablobs of het toevoegen van blobs wordt niet ondersteund.
  - U een momentopname kopiëren over de basisblob. Door een momentopname te promoten in de positie van de basisblob, u een eerdere versie van een blob herstellen.
  - U een momentopname kopiëren naar een doelblob met een andere naam. De resulterende doelblob is een schrijfbare blob en geen momentopname.

## <a name="copy-a-blob"></a>Een blob kopiëren

Als u een blob wilt kopiëren, roept u een van de volgende methoden aan:

 - [StartCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy?view=azure-dotnet)
 - [StartCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync?view=azure-dotnet)

In het volgende codevoorbeeld wordt een verwijzing naar een blob die eerder is gemaakt, als een verwijzing naar een nieuwe blob in dezelfde container:

```csharp
private static async Task CopyBlockBlobAsync(CloudBlobContainer container)
{
    CloudBlockBlob sourceBlob = null;
    CloudBlockBlob destBlob = null;
    string leaseId = null;

    try
    {
        // Get a block blob from the container to use as the source.
        sourceBlob = container.ListBlobs().OfType<CloudBlockBlob>().FirstOrDefault();

        // Lease the source blob for the copy operation to prevent another client from modifying it.
        // Specifying null for the lease interval creates an infinite lease.
        leaseId = await sourceBlob.AcquireLeaseAsync(null);

        // Get a reference to a destination blob (in this case, a new blob).
        destBlob = container.GetBlockBlobReference("copy of " + sourceBlob.Name);

        // Ensure that the source blob exists.
        if (await sourceBlob.ExistsAsync())
        {
            // Get the ID of the copy operation.
            string copyId = await destBlob.StartCopyAsync(sourceBlob);

            // Fetch the destination blob's properties before checking the copy state.
            await destBlob.FetchAttributesAsync();

            Console.WriteLine("Status of copy operation: {0}", destBlob.CopyState.Status);
            Console.WriteLine("Completion time: {0}", destBlob.CopyState.CompletionTime);
            Console.WriteLine("Bytes copied: {0}", destBlob.CopyState.BytesCopied.ToString());
            Console.WriteLine("Total bytes: {0}", destBlob.CopyState.TotalBytes.ToString());
            Console.WriteLine();
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
    finally
    {
        // Break the lease on the source blob.
        if (sourceBlob != null)
        {
            await sourceBlob.FetchAttributesAsync();

            if (sourceBlob.Properties.LeaseState != LeaseState.Available)
            {
                await sourceBlob.BreakLeaseAsync(new TimeSpan(0));
            }
        }
    }
}
```

## <a name="abort-a-blob-copy-operation"></a>Een blobkopiebewerking afbreken

Het afbreken van een kopieerbewerking resulteert in een doelblob met nullengte voor blokblobs, toevoegenblobs en paginablobs. De metagegevens voor de doelblob hebben echter de nieuwe waarden gekopieerd van de bronblob of expliciet ingesteld in de [aanroep StartCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy?view=azure-dotnet) of [StartCopyAsync.](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync?view=azure-dotnet) Als u de oorspronkelijke metagegevens van vóór `StartCopy` de `StartCopyAsync`kopie wilt bewaren, maakt u een momentopname van de doelblob voordat u belt of .

Wanneer u een lopende blobkopiebewerking afbreekt, wordt de [CopyState.Status van](/dotnet/api/microsoft.azure.storage.blob.copystate.status?view=azure-dotnet#Microsoft_Azure_Storage_Blob_CopyState_Status) de doelblob ingesteld op [CopyStatus.Aborted](/dotnet/api/microsoft.azure.storage.blob.copystatus?view=azure-dotnet).

De methoden [AbortCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.abortcopy?view=azure-dotnet) en [AbortCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.abortcopyasync?view=azure-dotnet) annuleren een doorlopende blob-kopieerbewerking en laten een doelblob achter met nullengte en volledige metagegevens.

```csharp
// Fetch the destination blob's properties before checking the copy state.
await destBlob.FetchAttributesAsync();

// Check the copy status. If it is still pending, abort the copy operation.
if (destBlob.CopyState.Status == CopyStatus.Pending)
{
    await destBlob.AbortCopyAsync(copyId);
    Console.WriteLine("Copy operation {0} has been aborted.", copyId);
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>Volgende stappen

De volgende onderwerpen bevatten informatie over het kopiëren van blobs en het afbreken van lopende kopieerbewerkingen met behulp van de Azure REST API's.

- [Blob kopiëren](/rest/api/storageservices/copy-blob)
- [Blob kopiëren afbreken](/rest/api/storageservices/abort-copy-blob)
