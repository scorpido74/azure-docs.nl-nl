---
title: Een BLOB-container maken of verwijderen met .NET-Azure Storage
description: Meer informatie over het maken of verwijderen van een BLOB-container in uw Azure Storage-account met behulp van de .NET-client bibliotheek.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 70f905d15c69876ced56c25ec92f858bb15b7d36
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75372207"
---
# <a name="create-or-delete-a-container-in-azure-storage-with-net"></a>Een container maken of verwijderen in Azure Storage met .NET

Blobs in Azure Storage zijn ingedeeld in containers. Voordat u een BLOB kunt uploaden, moet u eerst een container maken. In dit artikel wordt beschreven hoe u containers maakt en verwijdert met de [Azure Storage-client bibliotheek voor .net](/dotnet/api/overview/azure/storage/client).

## <a name="name-a-container"></a>Een container een naam

Een container naam moet een geldige DNS-naam zijn, omdat deze deel uitmaakt van de unieke URI die wordt gebruikt om de container of de blobs te adresseren. Volg deze regels bij het benoemen van een container:

- Container namen kunnen tussen de 3 en 63 tekens lang zijn.
- Container namen moeten beginnen met een letter of cijfer en mogen alleen kleine letters, cijfers en het streepje (-) bevatten.
- Twee of meer opeenvolgende streepjes tekens zijn niet toegestaan in container namen.

De URI voor een container heeft de volgende indeling:

`https://myaccount.blob.core.windows.net/mycontainer`

## <a name="create-a-container"></a>Een container maken

Als u een container wilt maken, roept u een van de volgende methoden op:

- [Maken](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.create)
- [CreateAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createasync)
- [createIfNotExists](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexists)
- [CreateIfNotExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexistsasync)

De methoden **Create** en **CreateAsync** genereren een uitzonde ring als er al een container met dezelfde naam bestaat.

De **CreateIfNotExists** -en **CreateIfNotExistsAsync** -methoden retour neren een Booleaanse waarde die aangeeft of de container is gemaakt. Als er al een container met dezelfde naam bestaat, retour neren deze methoden **False** om aan te geven dat er geen nieuwe container is gemaakt.

Containers worden direct onder het opslag account gemaakt. Het is niet mogelijk om één container onder een andere te nesten.

In het volgende voor beeld wordt een container asynchroon gemaakt:

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

## <a name="create-the-root-container"></a>De basis container maken

Een basis container fungeert als een standaard container voor uw opslag account. Elk opslag account kan één basis container hebben, die $root moet worden genoemd *.* U moet de basis container expliciet maken of verwijderen.

U kunt verwijzen naar een blob die is opgeslagen in de hoofd container zonder de naam van de hoofd container op te nemen. Met de hoofd container kunt u verwijzen naar een BLOB op het hoogste niveau van de hiërarchie van het opslag account. U kunt bijvoorbeeld verwijzen naar een blob die zich in de hoofd container op de volgende manier bevindt:

`https://myaccount.blob.core.windows.net/default.html`

In het volgende voor beeld wordt de hoofd container synchroon gemaakt:

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

De methoden **Delete** en **DeleteAsync** genereren een uitzonde ring als de container niet bestaat.

De **DeleteIfExists** -en **DeleteIfExistsAsync** -methoden retour neren een Booleaanse waarde die aangeeft of de container is verwijderd. Als de opgegeven container niet bestaat, retour neren deze methoden **False** om aan te geven dat de container niet is verwijderd.

Nadat u een container hebt verwijderd, kunt u niet meer dan 30 seconden een container maken met dezelfde naam en mogelijk langer. Terwijl de container wordt verwijderd, mislukt een poging om een container met dezelfde naam te maken, met de HTTP-fout code 409 (conflict). Eventuele andere bewerkingen in de container of de blobs die deze bevat, mislukken met de HTTP-fout code 404 (niet gevonden) terwijl de container wordt verwijderd.

In het volgende voor beeld wordt de opgegeven container verwijderd en wordt de uitzonde ring verwerkt als de container niet bestaat:

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

In het volgende voor beeld ziet u hoe u alle containers verwijdert die beginnen met een opgegeven voor voegsel. In het voor beeld wordt de lease onderbroken als er een bestaande lease in de container is.

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

- [Container bewerking maken](/rest/api/storageservices/create-container)
- [Bewerking voor het verwijderen van een container](/rest/api/storageservices/delete-container)
