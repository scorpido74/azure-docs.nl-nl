---
title: .NET gebruiken om eigenschappen en metagegevens voor een blobcontainer te beheren
titleSuffix: Azure Storage
description: Meer informatie over het instellen en ophalen van systeemeigenschappen en het opslaan van aangepaste metagegevens op blobcontainers in uw Azure Storage-account met behulp van de .NET-clientbibliotheek.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.openlocfilehash: c66b521b5cd75825fcafe07b24d5d527c45f5153
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79135918"
---
# <a name="manage-container-properties-and-metadata-with-net"></a>Containereigenschappen en metagegevens beheren met .NET

Blob-containers ondersteunen systeemeigenschappen en door de gebruiker gedefinieerde metagegevens, naast de gegevens die ze bevatten. In dit artikel ziet u hoe u systeemeigenschappen en door de gebruiker gedefinieerde metagegevens beheert met de [Azure Storage-clientbibliotheek voor .NET](/dotnet/api/overview/azure/storage?view=azure-dotnet).

## <a name="about-properties-and-metadata"></a>Informatie over eigenschappen en metagegevens

- **Systeemeigenschappen:** er bestaan systeemeigenschappen op elke Blob-opslagbron. Sommige kunnen worden gelezen of ingesteld, terwijl anderen alleen-lezen zijn. Onder de hoezen komen sommige systeemeigenschappen overeen met bepaalde standaard HTTP-headers. De Azure Storage-clientbibliotheek voor .NET behoudt deze eigenschappen voor u.

- **Door de gebruiker gedefinieerde metagegevens:** door de gebruiker gedefinieerde metagegevens bestaan uit een of meer naamwaardeparen die u opgeeft voor een Blob-opslagbron. U metagegevens gebruiken om extra waarden op te slaan met de bron. Metagegevenswaarden zijn alleen voor uw eigen doeleinden en hebben geen invloed op de manier waarop de resource zich gedraagt.

Het ophalen van eigenschap- en metagegevenswaarden voor een Blob-opslagbron is een proces in twee stappen. Voordat u deze waarden lezen, moet u ze expliciet ophalen door de methode **FetchAttributes** of **FetchAttributesAsync aan te** roepen. De uitzondering op deze regel is dat de methoden **Bestaan** en **BestaanAsync** de juiste methode **FetchAttributes** onder de covers aanroepen. Wanneer u een van deze methoden aanroept, hoeft u **fetchattributes**niet ook aan te roepen.

> [!IMPORTANT]
> Als u merkt dat eigenschap- of metagegevenswaarden voor een opslagbron niet zijn ingevuld, controleert u of uw code de methode **FetchAttributes** of **FetchAttributesAsync** aanroept.

Metagegevensnaam/waardeparen zijn geldige HTTP-headers en moeten zich dus houden aan alle beperkingen voor HTTP-headers. Metagegevensnamen moeten geldige HTTP-kopnamen en geldige C#-id's zijn, mogen alleen ASCII-tekens bevatten en moeten worden behandeld als hoofdletter-ongevoelig. Metagegevenswaarden die niet-ASCII-tekens bevatten, moeten Base64-gecodeerd of DOOR URL-gecodeerd zijn.

## <a name="retrieve-container-properties"></a>Containereigenschappen ophalen

Als u containereigenschappen wilt ophalen, roept u een van de volgende methoden aan:

- [Kenmerken ophalen](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.fetchattributes)
- [FetchAttributesAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.fetchattributesasync)

In het volgende codevoorbeeld worden de systeemeigenschappen van een container opgehaald en worden bepaalde eigenschapswaarden naar een consolevenster geschreven:

```csharp
private static async Task ReadContainerPropertiesAsync(CloudBlobContainer container)
{
    try
    {
        // Fetch some container properties and write out their values.
        await container.FetchAttributesAsync();
        Console.WriteLine("Properties for container {0}", container.StorageUri.PrimaryUri);
        Console.WriteLine("Public access level: {0}", container.Properties.PublicAccess);
        Console.WriteLine("Last modified time in UTC: {0}", container.Properties.LastModified);
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

## <a name="set-and-retrieve-metadata"></a>Metagegevens instellen en ophalen

U metagegevens opgeven als een of meer naamwaardeparen op een blob- of containerbron. Als u metagegevens wilt instellen, voegt u naamwaardeparen toe aan de **verzameling metagegevens** op de bron en roept u een van de volgende methoden aan om de waarden te schrijven:

- [SetMetadata](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setmetadata)
- [SetMetadataAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setmetadataasync)

De naam van uw metagegevens moet voldoen aan de naamgevingsconventies voor C#-id's. Metagegevensnamen behouden de case waarmee ze zijn gemaakt, maar zijn case-ongevoelig wanneer ze worden ingesteld of gelezen. Als twee of meer metagegevenskoppen met dezelfde naam worden ingediend voor een resource, retourneert Blob-opslag HTTP-foutcode 400 (Bad Request).

In het volgende codevoorbeeld worden metagegevens ingesteld op een container. Eén waarde wordt ingesteld met de methode **Toevoegen van** de verzameling. De andere waarde wordt ingesteld met behulp van de syntaxis van impliciete sleutel/waarde. Beide zijn geldig.

```csharp
public static async Task AddContainerMetadataAsync(CloudBlobContainer container)
{
    try
    {
        // Add some metadata to the container.
        container.Metadata.Add("docType", "textDocuments");
        container.Metadata["category"] = "guidance";

        // Set the container's metadata.
        await container.SetMetadataAsync();
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

Als u metagegevens wilt ophalen, roept u de methode **FetchAttributes** **async** op uw blob of container aan om de **verzameling metagegevens** in te vullen en vervolgens de waarden te lezen, zoals in het onderstaande voorbeeld wordt weergegeven.

```csharp
public static async Task ReadContainerMetadataAsync(CloudBlobContainer container)
{
    try
    {
        // Fetch container attributes in order to populate the container's properties and metadata.
        await container.FetchAttributesAsync();

        // Enumerate the container's metadata.
        Console.WriteLine("Container metadata:");
        foreach (var metadataItem in container.Metadata)
        {
            Console.WriteLine("\tKey: {0}", metadataItem.Key);
            Console.WriteLine("\tValue: {0}", metadataItem.Value);
        }
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

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>Zie ook

- [Containereigenschappen uitvoeren](/rest/api/storageservices/get-container-properties)
- [Containermetagegevens instellen](/rest/api/storageservices/set-container-metadata)
- [Containermetagegevens uitvoeren](/rest/api/storageservices/set-container-metadata)
