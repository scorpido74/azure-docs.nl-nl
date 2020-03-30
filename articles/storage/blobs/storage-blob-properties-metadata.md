---
title: Eigenschappen en metagegevens voor een blob beheren met .NET - Azure Storage
description: Meer informatie over het instellen en ophalen van systeemeigenschappen en het opslaan van aangepaste metagegevens op blobs in uw Azure Storage-account met behulp van de .NET-clientbibliotheek.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/09/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: b4abd7e29dec67ddc1be50a2a6703da2a25551d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137659"
---
# <a name="manage-blob-properties-and-metadata-with-net"></a>Blob-eigenschappen en metagegevens beheren met .NET

Naast de gegevens die ze bevatten, ondersteunen blobs systeemeigenschappen en door de gebruiker gedefinieerde metagegevens. In dit artikel ziet u hoe u systeemeigenschappen en door de gebruiker gedefinieerde metagegevens beheert met de [Azure Storage-clientbibliotheek voor .NET](/dotnet/api/overview/azure/storage?view=azure-dotnet).

## <a name="about-properties-and-metadata"></a>Informatie over eigenschappen en metagegevens

- **Systeemeigenschappen:** er bestaan systeemeigenschappen op elke Blob-opslagbron. Sommige kunnen worden gelezen of ingesteld, terwijl anderen alleen-lezen zijn. Onder de hoezen komen sommige systeemeigenschappen overeen met bepaalde standaard HTTP-headers. De Azure Storage-clientbibliotheek voor .NET behoudt deze eigenschappen voor u.

- **Door de gebruiker gedefinieerde metagegevens:** door de gebruiker gedefinieerde metagegevens bestaan uit een of meer naamwaardeparen die u opgeeft voor een Blob-opslagbron. U metagegevens gebruiken om extra waarden op te slaan met de bron. Metagegevenswaarden zijn alleen voor uw eigen doeleinden en hebben geen invloed op hoe de bron zich gedraagt.

Het ophalen van metagegevens en eigenschapswaarden voor een Blob-opslagbron is een proces in twee stappen. Voordat u deze waarden lezen, moet u `FetchAttributes` `FetchAttributesAsync` ze expliciet ophalen door de or-methode aan te roepen. De uitzondering op deze `Exists` regel `ExistsAsync` is dat `FetchAttributes` de en methoden de juiste methode onder de covers aanroepen. Wanneer u een van deze methoden aanroept, hoeft `FetchAttributes`u niet ook te bellen.

> [!IMPORTANT]
> Als u merkt dat eigenschap- of metagegevenswaarden voor een opslagbron `FetchAttributes` `FetchAttributesAsync` niet zijn ingevuld, controleert u of uw code de of-methode aanroept.

## <a name="set-and-retrieve-properties"></a>Eigenschappen instellen en ophalen

In het volgende `ContentType` codevoorbeeld worden de en `ContentLanguage` de systeemeigenschappen van een blob ingesteld.

```csharp
public static async Task SetBlobPropertiesAsync(CloudBlob blob)
{
    try
    {
        Console.WriteLine("Setting blob properties.");

        // You must explicitly set the MIME ContentType every time
        // the properties are updated or the field will be cleared.
        blob.Properties.ContentType = "text/plain";
        blob.Properties.ContentLanguage = "en-us";

        // Set the blob's properties.
        await blob.SetPropertiesAsync();
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

Als u blob-eigenschappen `FetchAttributes` `FetchAttributesAsync` wilt ophalen, roept `Properties` u de of methode op uw blob aan om de eigenschap te vullen. In het volgende codevoorbeeld worden de systeemeigenschappen van een blob weergegeven en worden enkele waarden weergegeven:

```csharp
private static async Task GetBlobPropertiesAsync(CloudBlob blob)
{
    try
    {
        // Fetch the blob properties.
        await blob.FetchAttributesAsync();

        // Display some of the blob's property values.
        Console.WriteLine(" ContentLanguage: {0}", blob.Properties.ContentLanguage);
        Console.WriteLine(" ContentType: {0}", blob.Properties.ContentType);
        Console.WriteLine(" Created: {0}", blob.Properties.Created);
        Console.WriteLine(" LastModified: {0}", blob.Properties.LastModified);
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

U metagegevens opgeven als een of meer naamwaardeparen op een blob- of containerbron. Als u metagegevens wilt instellen, voegt u naamwaardeparen toe aan de `Metadata` verzameling op de resource. Roep vervolgens een van de volgende methoden aan om de waarden te schrijven:

- [SetMetadata](/dotnet/api/microsoft.azure.storage.blob.cloudblob.setmetadata)
- [SetMetadataAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.setmetadataasync)

Metagegevensnaam/waardeparen zijn geldige HTTP-headers en moeten voldoen aan alle beperkingen voor HTTP-headers. Metagegevensnamen moeten geldige HTTP-kopnamen en geldige C#-id's zijn, mogen alleen ASCII-tekens bevatten en moeten worden behandeld als hoofdletter-ongevoelig. [Base64-encode](https://docs.microsoft.com/dotnet/api/system.convert.tobase64string) of [URL-encode](https://docs.microsoft.com/dotnet/api/system.web.httputility.urlencode) metadata waarden die niet-ASCII tekens bevatten.

De naam van uw metagegevens moet voldoen aan de naamgevingsconventies voor C#-id's. Metagegevensnamen behouden de aanvraag die wordt gebruikt bij het maken, maar zijn hoofdletters ongevoelig wanneer ze zijn ingesteld of gelezen. Als twee of meer metagegevenskoppen met dezelfde naam worden ingediend voor een bron, retourneert Azure Blob-opslag HTTP-foutcode 400 (Bad Request).

In het volgende codevoorbeeld worden metagegevens ingesteld op een blob. Eén waarde wordt ingesteld met `Add` behulp van de methode van de verzameling. De andere waarde wordt ingesteld met behulp van de syntaxis van impliciete sleutel/waarde.

```csharp
public static async Task AddBlobMetadataAsync(CloudBlob blob)
{
    try
    {
        // Add metadata to the blob by calling the Add method.
        blob.Metadata.Add("docType", "textDocuments");

        // Add metadata to the blob by using key/value syntax.
        blob.Metadata["category"] = "guidance";

        // Set the blob's metadata.
        await blob.SetMetadataAsync();
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

Als u metagegevens `FetchAttributesAsync` wilt ophalen, roept u `Metadata` de `FetchAttributes` methode of methode op uw blob of container aan om de verzameling te vullen en leest u de waarden, zoals in het onderstaande voorbeeld wordt weergegeven.

```csharp
public static async Task ReadBlobMetadataAsync(CloudBlob blob)
{
    try
    {
        // Fetch blob attributes in order to populate 
        // the blob's properties and metadata.
        await blob.FetchAttributesAsync();

        Console.WriteLine("Blob metadata:");

        // Enumerate the blob's metadata.
        foreach (var metadataItem in blob.Metadata)
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

- [Blob-eigenschappen instellen](/rest/api/storageservices/set-blob-properties)
- [Blob-eigenschappen uitvoeren](/rest/api/storageservices/get-blob-properties)
- [Blob-metagegevens instellen](/rest/api/storageservices/set-blob-metadata)
- [Blob-metagegevens uitvoeren](/rest/api/storageservices/get-blob-metadata)
