---
title: Geef een door de klant opgegeven sleutel op een aanvraag naar Blob-opslag met .NET-Azure Storage
description: Meer informatie over hoe u een door de klant opgegeven sleutel kunt opgeven voor een aanvraag voor Blob-opslag met behulp van .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/20/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 001b1e0b9c738e263e8425f43076ef6e451a297f
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/27/2020
ms.locfileid: "89018821"
---
# <a name="specify-a-customer-provided-key-on-a-request-to-blob-storage-with-net"></a>Geef een door de klant opgegeven sleutel op een aanvraag naar Blob-opslag met .NET

Clients die aanvragen indienen voor Azure Blob-opslag hebben de optie om een versleutelings sleutel op te geven voor een afzonderlijke aanvraag. Met inbegrip van de versleutelings sleutel op de aanvraag biedt gedetailleerde controle over de versleutelings instellingen voor Blob Storage-bewerkingen. Door de klant verschafte sleutels kunnen worden opgeslagen in Azure Key Vault of in een andere sleutel opslag.

In dit artikel wordt uitgelegd hoe u een door de klant opgegeven sleutel kunt opgeven voor een aanvraag met .NET.

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

Voor meer informatie over het verifiëren van de identiteit van de Azure Identity client-bibliotheek van Azure Storage, zie de sectie **verificatie met de Azure Identity-bibliotheek** in [toegang verlenen tot blobs en wacht rijen met Azure Active Directory en beheerde identiteiten voor Azure-resources](../common/storage-auth-aad-msi.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json#authenticate-with-the-azure-identity-library).

## <a name="example-use-a-customer-provided-key-to-upload-a-blob"></a>Voor beeld: een door de klant verschafte sleutel gebruiken om een BLOB te uploaden

In het volgende voor beeld wordt een door de klant verschafte sleutel gemaakt en wordt deze sleutel gebruikt voor het uploaden van een blob. De code uploadt een blok en voert vervolgens de blokkerings lijst door om de BLOB te schrijven naar Azure Storage.

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

- [Azure Storage-versleuteling voor inactieve gegevens](../common/storage-service-encryption.md)
- [Toegang verlenen tot blobs en wacht rijen met Azure Active Directory en beheerde identiteiten voor Azure-resources](../common/storage-auth-aad-msi.md)
