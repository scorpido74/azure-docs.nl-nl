---
title: Een BLOB kopiëren met .NET-Azure Storage
description: Meer informatie over het kopiëren van een BLOB in uw Azure Storage-account met behulp van de .NET-client bibliotheek.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/20/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: 9ffa69980f020580376aea447f40ac615f26cf03
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/12/2020
ms.locfileid: "79135884"
---
# <a name="copy-a-blob-with-net"></a>Een BLOB kopiëren met .NET

In dit artikel wordt beschreven hoe u een BLOB kopieert met een Azure Storage-account. Ook wordt uitgelegd hoe u een asynchrone Kopieer bewerking afbreekt. De voorbeeld code maakt gebruik [van de Azure Storage-client bibliotheek voor .net](/dotnet/api/overview/azure/storage?view=azure-dotnet).

## <a name="about-copying-blobs"></a>Over het kopiëren van blobs

Wanneer u een BLOB in hetzelfde opslag account kopieert, is het een synchrone bewerking. Wanneer u over accounts kopieert, is dit een asynchrone bewerking. De [StartCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy?view=azure-dotnet) -en [StartCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync?view=azure-dotnet) -methoden retour neren een copy id-waarde die wordt gebruikt om de status te controleren of de Kopieer bewerking af te breken.

De bron-BLOB voor een Kopieer bewerking kan een blok-blob, een toevoeg-blob, een pagina-BLOB of een moment opname zijn. Als de doel-BLOB al bestaat, moet deze van hetzelfde type BLOB als de bron-BLOB zijn. Alle bestaande doel-blobs worden overschreven. 

De doel-Blob kan niet worden gewijzigd terwijl een Kopieer bewerking wordt uitgevoerd. Een bestemmings-Blob kan slechts één openstaande bewerking voor het kopiëren van BLOB hebben. Met andere woorden, een BLOB kan niet het doel zijn voor meerdere in behandeling zijnde Kopieer bewerkingen.

De gehele bron-BLOB of het volledige bestand wordt altijd gekopieerd. Het kopiëren van een bereik van bytes of een set blokken wordt niet ondersteund.

Wanneer een BLOB wordt gekopieerd, worden de systeem eigenschappen gekopieerd naar de doel-blob met dezelfde waarden.

Voor alle BLOB-typen kunt u de eigenschap [CopyState. status](/dotnet/api/microsoft.azure.storage.blob.copystate.status?view=azure-dotnet) op de doel-BLOB controleren om de status van de Kopieer bewerking op te halen. De uiteindelijke BLOB wordt doorgevoerd wanneer de Kopieer bewerking is voltooid.

Een Kopieer bewerking kan een van de volgende vormen hebben:

  - U kunt een bron-BLOB kopiëren naar een doel-blob met een andere naam. De doel-Blob kan een bestaande blob van hetzelfde BLOB-type zijn (blok keren, toevoegen of pagina), of kan een nieuwe BLOB zijn die wordt gemaakt door de Kopieer bewerking.
  - U kunt een bron-BLOB kopiëren naar een doel-blob met dezelfde naam, waardoor de doel-BLOB effectief wordt vervangen. Een dergelijke Kopieer bewerking verwijdert alle onverwerkte blokken en overschrijft de meta gegevens van de doel-blob.
  - U kunt een bron bestand in de Azure-bestands service kopiëren naar een bestemmings-blob. De doel-Blob kan een bestaande blok-BLOB zijn, of kan een nieuwe blok-BLOB zijn die wordt gemaakt door de Kopieer bewerking. Kopiëren van bestanden naar pagina-blobs of toevoeg-blobs wordt niet ondersteund.
  - U kunt een moment opname kopiëren via de bijbehorende basis-blob. Door een moment opname te promo veren tot de positie van de basis-blob, kunt u een eerdere versie van een BLOB herstellen.
  - U kunt een moment opname kopiëren naar een doel-blob met een andere naam. De resulterende doel-blob is een schrijf bare Blob en geen moment opname.

## <a name="copy-a-blob"></a>Een BLOB kopiëren

Als u een BLOB wilt kopiëren, roept u een van de volgende methoden aan:

 - [StartCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy?view=azure-dotnet)
 - [StartCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync?view=azure-dotnet)

In het volgende code voorbeeld wordt een verwijzing opgehaald naar een blob die eerder is gemaakt en wordt deze gekopieerd naar een nieuwe Blob in dezelfde container:

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

## <a name="abort-a-blob-copy-operation"></a>Een Kopieer bewerking voor een BLOB afbreken

Het afbreken van een Kopieer bewerking resulteert in een doel-blob met een lengte van nul voor blok-blobs, toevoeg-blobs en pagina-blobs. De meta gegevens voor de doel-BLOB hebben echter de nieuwe waarden gekopieerd van de bron-BLOB of worden expliciet ingesteld in de aanroep [StartCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy?view=azure-dotnet) of [StartCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync?view=azure-dotnet) . Als u wilt dat de oorspronkelijke meta gegevens vóór de kopie worden bewaard, maakt u een moment opname van de doel-BLOB voordat u `StartCopy` of `StartCopyAsync`aanroept.

Wanneer u een actieve bewerking voor het kopiëren van blobs afbreekt, wordt de [CopyState. status](/dotnet/api/microsoft.azure.storage.blob.copystate.status?view=azure-dotnet#Microsoft_Azure_Storage_Blob_CopyState_Status) van de doel-BLOB ingesteld op [CopyStatus. abort](/dotnet/api/microsoft.azure.storage.blob.copystatus?view=azure-dotnet).

De [AbortCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.abortcopy?view=azure-dotnet) -en [AbortCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.abortcopyasync?view=azure-dotnet) -methoden annuleren een actieve BLOB copy-bewerking en behouden een bestemmings-blob met een lengte van nul en volledige meta gegevens.

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

De volgende onderwerpen bevatten informatie over het kopiëren van blobs en het afbreken van doorlopende Kopieer bewerkingen met behulp van de Azure REST Api's.

- [BLOB kopiëren](/rest/api/storageservices/copy-blob)
- [Kopiëren van BLOB afbreken](/rest/api/storageservices/abort-copy-blob)
