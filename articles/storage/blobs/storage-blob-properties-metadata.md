---
title: Eigenschappen en meta gegevens voor een BLOB beheren met .NET-Azure Storage
description: Meer informatie over het instellen en ophalen van systeem eigenschappen en het opslaan van aangepaste meta gegevens op blobs in uw Azure Storage-account met behulp van de .NET-client bibliotheek.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/09/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: 395210c582ba7f5e8170a96a46e2816336e33b2d
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/26/2020
ms.locfileid: "77624034"
---
# <a name="manage-blob-properties-and-metadata-with-net"></a>BLOB-eigenschappen en meta gegevens beheren met .NET

Naast de gegevens die ze bevatten, ondersteunen blobs systeem eigenschappen en meta gegevens die door de gebruiker zijn gedefinieerd. In dit artikel wordt beschreven hoe u systeem eigenschappen en door de gebruiker gedefinieerde meta gegevens beheert met de [Azure Storage-client bibliotheek voor .net](/dotnet/api/overview/azure/storage/client).

## <a name="about-properties-and-metadata"></a>Over eigenschappen en meta gegevens

- **Systeem eigenschappen**: systeem eigenschappen bestaan op elke Blob Storage-Resource. Sommige van deze kunnen worden gelezen of ingesteld, terwijl anderen alleen-lezen zijn. Onder de kaften sommige systeem eigenschappen overeenkomen met bepaalde standaard-HTTP-headers. De Azure Storage-client bibliotheek voor .NET houdt deze eigenschappen voor u bij.

- Door de **gebruiker gedefinieerde META**gegevens: door de gebruiker gedefinieerde meta gegevens bestaan uit een of meer naam/waarde-paren die u opgeeft voor een Blob Storage-Resource. U kunt meta gegevens gebruiken om aanvullende waarden met de resource op te slaan. Meta gegevens waarden zijn alleen bedoeld voor eigen doel einden en zijn niet van invloed op de werking van de resource.

Het ophalen van meta gegevens en eigenschaps waarden voor een Blob Storage-Resource is een proces dat uit twee stappen bestaat. Voordat u deze waarden kunt lezen, moet u deze expliciet ophalen door de `FetchAttributes`-of `FetchAttributesAsync`-methode aan te roepen. De uitzonde ring op deze regel is dat de methoden `Exists` en `ExistsAsync` de juiste `FetchAttributes`-methode aanroepen onder de voor vallen. Wanneer u een van deze methoden aanroept, hoeft u `FetchAttributes`ook niet aan te roepen.

> [!IMPORTANT]
> Als u merkt dat de eigenschaps-of meta gegevens waarden voor een opslag bron niet zijn ingevuld, controleert u of de code de `FetchAttributes`-of `FetchAttributesAsync`-methode aanroept.

## <a name="set-and-retrieve-properties"></a>Eigenschappen instellen en ophalen

In het volgende code voorbeeld worden de `ContentType`-en `ContentLanguage` systeem eigenschappen ingesteld op een blob.

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

Als u BLOB-eigenschappen wilt ophalen, roept u de `FetchAttributes`-of `FetchAttributesAsync`-methode op uw BLOB aan om de eigenschap `Properties` in te vullen. In het volgende code voorbeeld worden de systeem eigenschappen van een BLOB opgehaald en worden enkele waarden weer gegeven:

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

## <a name="set-and-retrieve-metadata"></a>Meta gegevens instellen en ophalen

U kunt meta gegevens opgeven als een of meer naam/waarde-paren voor een BLOB of container resource. Als u meta gegevens wilt instellen, voegt u naam/waarde-paren toe aan de verzameling `Metadata` op de resource. Roep vervolgens een van de volgende methoden aan om de waarden te schrijven:

- [SetMetadata](/dotnet/api/microsoft.azure.storage.blob.cloudblob.setmetadata)
- [SetMetadataAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.setmetadataasync)

Naam/waarde-paren van meta gegevens zijn geldige HTTP-headers en moeten voldoen aan alle beperkingen voor HTTP-headers. Namen van meta gegevens moeten geldige namen voor HTTP- C# headers en geldige id's zijn, mogen alleen ASCII-tekens bevatten en moeten worden behandeld als niet hoofdletter gevoelig. [Base64-Encode](https://docs.microsoft.com/dotnet/api/system.convert.tobase64string) of [URL-Codeer](https://docs.microsoft.com/dotnet/api/system.web.httputility.urlencode) meta data-waarden die niet-ASCII-tekens bevatten.

De naam van uw meta gegevens moet voldoen aan de naamgevings C# conventies voor id's. Namen van meta gegevens behouden de case die wordt gebruikt wanneer ze zijn gemaakt, maar zijn niet hoofdletter gevoelig bij het instellen of lezen. Als twee of meer meta gegevens headers met dezelfde naam worden verzonden voor een resource, retourneert Azure Blob Storage de HTTP-fout code 400 (ongeldige aanvraag).

In het volgende code voorbeeld worden meta gegevens ingesteld voor een blob. Er wordt één waarde ingesteld met behulp van de `Add` methode van de verzameling. De andere waarde wordt ingesteld met behulp van de syntaxis van de impliciete sleutel/waarde.

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

Als u meta gegevens wilt ophalen, roept u de `FetchAttributes`-of `FetchAttributesAsync`-methode aan op uw BLOB of container om de `Metadata` verzameling te vullen. Lees vervolgens de waarden, zoals wordt weer gegeven in het onderstaande voor beeld.

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

- [Bewerking BLOB-eigenschappen instellen](/rest/api/storageservices/set-blob-properties)
- [Bewerking BLOB-eigenschappen ophalen](/rest/api/storageservices/get-blob-properties)
- [Bewerking voor BLOB-meta gegevens instellen](/rest/api/storageservices/set-blob-metadata)
- [Bewerking BLOB-meta gegevens ophalen](/rest/api/storageservices/get-blob-metadata)
