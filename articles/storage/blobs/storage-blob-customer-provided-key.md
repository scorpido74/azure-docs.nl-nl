---
title: Een door de klant verstrekte sleutel opgeven voor een verzoek om Blob-opslag met .NET - Azure Storage
description: Meer informatie over het opgeven van een door de klant verstrekte sleutel op een verzoek naar Blob-opslag met .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/26/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: ea8254f108aed9d40e6970a27409035b1e10ab41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74806997"
---
# <a name="specify-a-customer-provided-key-on-a-request-to-blob-storage-with-net"></a>Een door de klant verstrekte sleutel opgeven op een verzoek om Blob-opslag met .NET

Clients die aanvragen indienen tegen Azure Blob-opslag hebben de mogelijkheid om een versleutelingssleutel op een afzonderlijk verzoek te verstrekken. Het opnemen van de versleutelingssleutel op de aanvraag biedt gedetailleerde controle over versleutelingsinstellingen voor Blob-opslagbewerkingen. Door de klant verstrekte sleutels (preview) kunnen worden opgeslagen in Azure Key Vault of in een andere sleutelwinkel.

In dit artikel ziet u hoe u een door de klant verstrekte sleutel opgeeft op een verzoek met .NET.

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

Zie de sectie getiteld **Authenticeren met de Azure Identity-bibliotheek in Azure-bestanden** voor meer informatie over het verifiëren met de Azure Identity-clientbibliotheek vanuit [Azure Storage.](../common/storage-auth-aad-msi.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json#authenticate-with-the-azure-identity-library)

## <a name="example-use-a-customer-provided-key-to-upload-a-blob"></a>Voorbeeld: gebruik een door de klant geleverde sleutel om een blob te uploaden

In het volgende voorbeeld wordt een door de klant geleverde sleutel gemaakt en wordt die sleutel gebruikt om een blob te uploaden. De code uploadt een blok en verbindt vervolgens de bloklijst om de blob naar Azure Storage te schrijven.

```csharp
async static Task UploadBlobWithClientKey(string accountName, string containerName,
    string blobName, Stream data, byte[] key)
{
    const string blobServiceEndpointSuffix = ".blob.core.windows.net";
    Uri accountUri = new Uri("https://" + accountName + blobServiceEndpointSuffix);

    // Specify the customer-provided key on the options for the client.
    BlobClientOptions options = new BlobClientOptions()
    {
        CustomerProvidedKey = new CustomerProvidedKey(key)
    };

    // Create a client object for the Blob service, including options.
    BlobServiceClient serviceClient = new BlobServiceClient(accountUri, 
        new DefaultAzureCredential(), options);

    // Create a client object for the container.
    // The container client retains the credential and client options.
    BlobContainerClient containerClient = serviceClient.GetBlobContainerClient(containerName);

    // Create a new block blob client object.
    // The blob client retains the credential and client options.
    BlobClient blobClient = containerClient.GetBlobClient(blobName);

    try
    {
        // Create the container if it does not exist.
        await containerClient.CreateIfNotExistsAsync();

        // Upload the data using the customer-provided key.
        await blobClient.UploadAsync(data);
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

## <a name="next-steps"></a>Volgende stappen

- [Azure Storage-versleuteling voor gegevens in rust](../common/storage-service-encryption.md)
- [Toegang tot blobs en wachtrijen autoriseren met Azure Active Directory en beheerde identiteiten voor Azure Resources](../common/storage-auth-aad-msi.md)
