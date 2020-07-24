---
title: BLOB-versie beheer inschakelen en beheren (preview)
titleSuffix: Azure Storage
description: Meer informatie over het inschakelen van BLOB-versie beheer in de Azure Portal of met behulp van een Azure Resource Manager sjabloon.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/15/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 7ff0bb6d593a199bd249441cf484d1f0580774ac
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87086044"
---
# <a name="enable-and-manage-blob-versioning"></a>BLOB-versie beheer inschakelen en beheren

U kunt BLOB-versie beheer (preview) voor het opslag account op elk gewenst moment in-of uitschakelen met behulp van de Azure Portal of een Azure Resource Manager sjabloon.

## <a name="enable-blob-versioning"></a>Blobversiebeheer inschakelen

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

BLOB-versie beheer inschakelen in de Azure Portal:

1. Navigeer naar uw opslag account in de portal.
1. Kies onder **BLOB service**de optie **gegevens beveiliging**.
1. Selecteer **ingeschakeld**in de sectie **versie beheer** .

:::image type="content" source="media/versioning-enable/portal-enable-versioning.png" alt-text="Scherm afbeelding die laat zien hoe u BLOB-versie beheer inschakelt in Azure Portal":::

# <a name="template"></a>[Sjabloon](#tab/template)

Als u BLOB-versie beheer met een sjabloon wilt inschakelen, maakt u een sjabloon met de eigenschap **IsVersioningEnabled** in **waar**. In de volgende stappen wordt beschreven hoe u een sjabloon maakt in de Azure Portal.

1. Kies in het Azure Portal **een resource maken**.
1. Typ in **de Marketplace zoeken de** **sjabloon implementatie**en druk vervolgens op **Enter**.
1. Kies **Sjabloonimlementatie**, kies **maken**en kies vervolgens **uw eigen sjabloon bouwen in de editor**.
1. Plak in de sjabloon editor de volgende JSON. Vervang de tijdelijke plaatsaanduiding `<accountName>` door de naam van uw opslagaccount.
1. Sla de sjabloon op.
1. Geef de resource groep van het account op en kies vervolgens de knop **aanschaffen** om de sjabloon te implementeren en BLOB-versie beheer in te scha kelen.

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Storage/storageAccounts/blobServices",
                "apiVersion": "2019-06-01",
                "name": "<accountName>/default",
                "properties": {
                    "IsVersioningEnabled": true
                }
            }
        ]
    }
    ```

Zie [resources implementeren met Azure Portal](../../azure-resource-manager/templates/deploy-portal.md)voor meer informatie over het implementeren van resources met sjablonen in de Azure Portal.

---

## <a name="modify-a-blob-to-trigger-a-new-version"></a>Een BLOB wijzigen om een nieuwe versie te activeren

In het volgende code voorbeeld ziet u hoe u het maken van een nieuwe versie kunt activeren met de Azure Storage-client bibliotheek voor .NET versie [12.5.0-Preview. 5](https://www.nuget.org/packages/Azure.Storage.Blobs/12.5.0-preview.5) of hoger. Voordat u dit voor beeld uitvoert, moet u ervoor zorgen dat versie beheer voor uw opslag account is ingeschakeld.

In het voor beeld wordt een blok-BLOB gemaakt en vervolgens worden de meta gegevens van de BLOB bijgewerkt. Bij het bijwerken van de meta gegevens van de BLOB wordt het maken van een nieuwe versie geactiveerd. In het voor beeld worden de initiële versie en de huidige versie opgehaald en wordt aangegeven dat alleen de huidige versie de meta gegevens bevat.

```csharp
public static async Task UpdateVersionedBlobMetadata(string containerName, string blobName)
{
    // Create a new service client from the connection string.
    BlobServiceClient blobServiceClient = new BlobServiceClient(ConnectionString);

    // Create a new container client.
    BlobContainerClient containerClient = blobServiceClient.GetBlobContainerClient(containerName);

    try
    {
        // Create the container.
        await containerClient.CreateIfNotExistsAsync();

        // Upload a block blob.
        BlockBlobClient blockBlobClient = containerClient.GetBlockBlobClient(blobName);

        string blobContents = string.Format("Block blob created at {0}.", DateTime.Now);
        byte[] byteArray = Encoding.ASCII.GetBytes(blobContents);

        string initalVersionId;
        using (MemoryStream stream = new MemoryStream(byteArray))
        {
            Response<BlobContentInfo> uploadResponse = await blockBlobClient.UploadAsync(stream, null, default);

            // Get the version ID for the current version.
            initalVersionId = uploadResponse.Value.VersionId;
        }

        // Update the blob's metadata to trigger the creation of a new version.
        Dictionary<string, string> metadata = new Dictionary<string, string>
        {
            { "key", "value" },
            { "key1", "value1" }
        };

        Response<BlobInfo> metadataResponse = await blockBlobClient.SetMetadataAsync(metadata);

        // Get the version ID for the new current version.
        string newVersionId = metadataResponse.Value.VersionId;

        // Request metadata on the previous version.
        BlockBlobClient initalVersionBlob = blockBlobClient.WithVersion(initalVersionId);
        Response<BlobProperties> propertiesResponse = await initalVersionBlob.GetPropertiesAsync();
        PrintMetadata(propertiesResponse);

        // Request metadata on the current version.
        BlockBlobClient newVersionBlob = blockBlobClient.WithVersion(newVersionId);
        Response<BlobProperties> newPropertiesResponse = await newVersionBlob.GetPropertiesAsync();
        PrintMetadata(newPropertiesResponse);
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
    finally
    {
        await containerClient.DeleteAsync();
    }
}

static void PrintMetadata(Response<BlobProperties> propertiesResponse)
{
    if (propertiesResponse.Value.Metadata.Count > 0)
    {
        Console.WriteLine("Metadata values for version {0}:", propertiesResponse.Value.VersionId);
        foreach (var item in propertiesResponse.Value.Metadata)
        {
            Console.WriteLine("Key:{0}  Value:{1}", item.Key, item.Value);
        }
    }
    else
    {
        Console.WriteLine("Version {0} has no metadata.", propertiesResponse.Value.VersionId);
    }
}
```

## <a name="next-steps"></a>Volgende stappen

- [Versie beheer van BLOB (preview)](versioning-overview.md)
- [Voorlopig verwijderen voor Azure Storage-blobs](soft-delete-overview.md)
