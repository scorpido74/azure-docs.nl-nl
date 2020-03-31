---
title: Een blobcontainer maken of verwijderen met .NET - Azure Storage
description: Meer informatie over het maken of verwijderen van een blobcontainer in uw Azure Storage-account met behulp van de .NET-clientbibliotheek.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: c95ed6dde3c00c0688ccfd58565fd112427c8899
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79135935"
---
# <a name="create-or-delete-a-container-in-azure-storage-with-net"></a>Een container maken of verwijderen in Azure Storage met .NET

Blobs in Azure Storage zijn ingedeeld in containers. Voordat u een blob uploaden, moet u eerst een container maken. In dit artikel ziet u hoe u containers maakt en verwijdert met de [Azure Storage-clientbibliotheek voor .NET](/dotnet/api/overview/azure/storage?view=azure-dotnet).

## <a name="name-a-container"></a>Een container een naam geven

Een containernaam moet een geldige DNS-naam zijn, omdat deze deel uitmaakt van de unieke URI die wordt gebruikt om de container of de blobs aan te pakken. Volg deze regels bij het benoemen van een container:

- Containernamen kunnen tussen de 3 en 63 tekens lang zijn.
- Containernamen moeten beginnen met een letter of getal en mogen alleen kleine letters, cijfers en het streepje (-) teken bevatten.
- Twee of meer opeenvolgende streepjestekens zijn niet toegestaan in containernamen.

De URI voor een container is in deze indeling:

`https://myaccount.blob.core.windows.net/mycontainer`

## <a name="create-a-container"></a>Een container maken

Als u een container wilt maken, roept u een van de volgende methoden aan:

- [Maken](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.create)
- [CreateAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createasync)
- [Makenals bestaat niet](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexists)
- [CreateIfNotExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexistsasync)

Met de methoden **Maken** en **Maken Async** wordt een uitzondering gemaakt als er al een container met dezelfde naam bestaat.

De methoden **CreateIfNotExists** en **CreateIfNotExistsAsync** geven een Booleaanse waarde als aanduiding of de container is gemaakt. Als er al een container met dezelfde naam bestaat, worden deze methoden **False** geretourneerd om aan te geven dat er geen nieuwe container is gemaakt.

Containers worden direct onder het opslagaccount gemaakt. Het is niet mogelijk om de ene container onder de andere te nestelen.

In het volgende voorbeeld wordt een container asynchroon gezoend gezoend:

```csharp
private static async Task<CloudBlobContainer> CreateSampleContainerAsync(CloudBlobClient blobClient)
{
    // Name the sample container based on new GUID, to ensure uniqueness.
    // The container name must be lowercase.
    string containerName = "container-" + Guid.NewGuid();

    // Get a reference to a sample container.
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);

    try
    {
        // Create the container if it does not already exist.
        bool result = await container.CreateIfNotExistsAsync();
        if (result == true)
        {
            Console.WriteLine("Created container {0}", container.Name);
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
    }

    return container;
}
```

## <a name="create-the-root-container"></a>De hoofdcontainer maken

Een rootcontainer dient als standaardcontainer voor uw opslagaccount. Elke opslagrekening kan één wortelcontainer hebben, die $root moet worden *genoemd.*. U moet de hoofdcontainer expliciet maken of verwijderen.

U verwijzen naar een blob die is opgeslagen in de hoofdcontainer zonder de naam van de hoofdcontainer op te nemen. Met de hoofdcontainer u verwijzen naar een blob op het hoogste niveau van de hiërarchie van opslagaccount. U bijvoorbeeld op de volgende manier verwijzen naar een blob die zich in de hoofdcontainer bevindt:

`https://myaccount.blob.core.windows.net/default.html`

In het volgende voorbeeld wordt de hoofdcontainer synchroon gezoend:

```csharp
private static void CreateRootContainer(CloudBlobClient blobClient)
{
    try
    {
        // Create the root container if it does not already exist.
        CloudBlobContainer container = blobClient.GetContainerReference("$root");
        if (container.CreateIfNotExists())
        {
            Console.WriteLine("Created root container.");
        }
        else
        {
            Console.WriteLine("Root container already exists.");
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
    }
}
```

## <a name="delete-a-container"></a>Een container verwijderen

Als u een container in .NET wilt verwijderen, gebruikt u een van de volgende methoden:

- [Verwijderen](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.delete)
- [DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteasync)
- [DeleteIfExists](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteifexists)
- [DeleteIfExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteifexistsasync)

Met de methoden **Delete en** **DeleteAsync** wordt een uitzondering gemaakt als de container niet bestaat.

De methoden **DeleteIfExists** en **DeleteIfExistsAsync** geven een Booleaanse waarde als u aangeeft of de container is verwijderd. Als de opgegeven container niet bestaat, worden deze methoden **false** geretourneerd om aan te geven dat de container niet is verwijderd.

Nadat u een container hebt verwijderd, u niet ten minste 30 seconden en mogelijk langer een container met dezelfde naam maken. Terwijl de container wordt verwijderd, mislukt een poging om een container met dezelfde naam te maken met HTTP-foutcode 409 (Conflict). Andere bewerkingen op de container of de blobs die deze bevat, mislukken met HTTP-foutcode 404 (Niet gevonden) terwijl de container wordt verwijderd.

In het volgende voorbeeld wordt de opgegeven container verwijderd en wordt de uitzondering verwerkt als de container niet bestaat:

```csharp
private static async Task DeleteSampleContainerAsync(CloudBlobClient blobClient, string containerName)
{
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);

    try
    {
        // Delete the specified container and handle the exception.
        await container.DeleteAsync();
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

In het volgende voorbeeld ziet u hoe u alle containers verwijdert die beginnen met een opgegeven voorvoegsel. Het voorbeeld breekt de lease als er een bestaande lease op de container.

```csharp
private static async Task DeleteContainersWithPrefixAsync(CloudBlobClient blobClient, string prefix)
{
    Console.WriteLine("Delete all containers beginning with the specified prefix");
    try
    {
        foreach (var container in blobClient.ListContainers(prefix))
        {
            Console.WriteLine("\tContainer:" + container.Name);
            if (container.Properties.LeaseState == LeaseState.Leased)
            {
                await container.BreakLeaseAsync(null);
            }

            await container.DeleteAsync();
        }

        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>Zie ook

- [Containerbewerking maken](/rest/api/storageservices/create-container)
- [Bewerking voor het verwijderen van een container](/rest/api/storageservices/delete-container)
