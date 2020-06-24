---
title: .NET gebruiken om een gebruikers delegering SA'S te maken voor een container of BLOB
titleSuffix: Azure Storage
description: Meer informatie over het maken van een gebruiker met Azure Active Directory referenties met behulp van de .NET-client bibliotheek voor Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/18/2019
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: blobs
ms.openlocfilehash: d5e8f5e3939ade4e5344ef586a62b6a19082e34e
ms.sourcegitcommit: ad66392df535c370ba22d36a71e1bbc8b0eedbe3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/16/2020
ms.locfileid: "84809045"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-net"></a>Een SAS voor gebruikers overdracht maken voor een container of BLOB met .NET

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

In dit artikel wordt beschreven hoe u Azure Active Directory (Azure AD)-referenties gebruikt om een gebruikers delegering SA'S te maken voor een container of BLOB met de Azure Storage-client bibliotheek voor .NET versie 12.

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="assign-rbac-roles-for-access-to-data"></a>RBAC-rollen toewijzen voor toegang tot gegevens

Wanneer een Azure AD-beveiligings-principal probeert toegang te krijgen tot BLOB-gegevens, moet die beveiligingsprincipal machtigingen hebben voor de resource. Of de beveiligingsprincipal een beheerde identiteit in azure of een Azure AD-gebruikers account voor het uitvoeren van code in de ontwikkel omgeving is, moet aan de beveiligingsprincipal een RBAC-rol worden toegewezen die toegang verleent tot BLOB-gegevens in Azure Storage. Voor informatie over het toewijzen van machtigingen via RBAC, zie de sectie **RBAC-rollen toewijzen voor toegangs rechten** in [toegang verlenen tot Azure-blobs en-wacht rijen met behulp van Azure Active Directory](../common/storage-auth-aad.md#assign-rbac-roles-for-access-rights).

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

Voor meer informatie over het verifiëren van de identiteit van de Azure Identity client-bibliotheek van Azure Storage, zie de sectie **verificatie met de Azure Identity-bibliotheek** in [toegang verlenen tot blobs en wacht rijen met Azure Active Directory en beheerde identiteiten voor Azure-resources](../common/storage-auth-aad-msi.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json#authenticate-with-the-azure-identity-library).

## <a name="add-using-directives"></a>Using-instructies toevoegen

Voeg de volgende `using` instructies toe aan uw code voor het gebruik van de identiteits-en Azure Storage-client bibliotheken van Azure.

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Azure;
using Azure.Identity;
using Azure.Storage.Sas;
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
```

## <a name="get-an-authenticated-token-credential"></a>Een geverifieerde token referentie ophalen

Maak een instantie van de [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) -klasse om een token referentie op te halen die door uw code kan worden gebruikt om aanvragen voor Azure Storage te autoriseren.

Het volgende code fragment laat zien hoe de referenties van de geverifieerde token worden opgehaald en gebruikt om een serviceclient voor Blob Storage te maken:

```csharp
// Construct the blob endpoint from the account name.
string blobEndpoint = string.Format("https://{0}.blob.core.windows.net", accountName);

// Create a new Blob service client with Azure AD credentials.
BlobServiceClient blobClient = new BlobServiceClient(new Uri(blobEndpoint),
                                                     new DefaultAzureCredential());
```

## <a name="get-the-user-delegation-key"></a>De sleutel voor gebruikers overdracht ophalen

Elke SAS is ondertekend met een sleutel. Als u een gebruikers delegering SAS wilt maken, moet u eerst een sleutel voor gebruikers overdracht aanvragen, die vervolgens wordt gebruikt om de SAS te ondertekenen. De sleutel voor gebruikers overdracht is vergelijkbaar met de account sleutel die wordt gebruikt om een service-SAS of een account-SAS te ondertekenen, behalve dat deze afhankelijk is van uw Azure AD-referenties. Wanneer een client een sleutel voor gebruikers overdracht aanvraagt met een OAuth 2,0-token, retourneert Azure Storage de gebruikers delegatie sleutel namens de gebruiker.

Zodra u de sleutel voor gebruikers overdracht hebt, kunt u deze sleutel gebruiken om een wille keurig aantal hand tekeningen voor gedeelde toegang voor gebruikers overdracht te maken, gedurende de levens duur van de sleutel. De sleutel voor gebruikers delegering is onafhankelijk van het OAuth 2,0-token dat is gebruikt om het te verkrijgen, zodat het token niet hoeft te worden vernieuwd, zolang de sleutel nog geldig is. U kunt opgeven dat de sleutel geldig is gedurende een periode van Maxi maal zeven dagen.

Gebruik een van de volgende methoden om de sleutel voor gebruikers overdracht aan te vragen:

- [GetUserDelegationKey](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkey)
- [GetUserDelegationKeyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkeyasync)

Met het volgende code fragment wordt de sleutel voor gebruikers overdracht opgehaald en worden de bijbehorende eigenschappen wegge schreven:

```csharp
// Get a user delegation key for the Blob service that's valid for seven days.
// You can use the key to generate any number of shared access signatures over the lifetime of the key.
UserDelegationKey key = await blobClient.GetUserDelegationKeyAsync(DateTimeOffset.UtcNow,
                                                                   DateTimeOffset.UtcNow.AddDays(7));

// Read the key's properties.
Console.WriteLine("User delegation key properties:");
Console.WriteLine("Key signed start: {0}", key.SignedStartsOn);
Console.WriteLine("Key signed expiry: {0}", key.SignedExpiresOn);
Console.WriteLine("Key signed object ID: {0}", key.SignedObjectId);
Console.WriteLine("Key signed tenant ID: {0}", key.SignedTenantId);
Console.WriteLine("Key signed service: {0}", key.SignedService);
Console.WriteLine("Key signed version: {0}", key.SignedVersion);
```

## <a name="create-the-sas-token"></a>Het SAS-token maken

In het volgende code fragment wordt een nieuwe [BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) gemaakt en worden de para meters voor de gebruikers delegering sa's opgegeven. Het fragment roept vervolgens de [ToSasQueryParameters](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters) aan om de SAS-token teken reeks op te halen. Ten slotte bouwt de code de volledige URI, met inbegrip van het bron adres en het SAS-token.

```csharp
// Create a SAS token that's valid for one hour.
BlobSasBuilder sasBuilder = new BlobSasBuilder()
{
    BlobContainerName = containerName,
    BlobName = blobName,
    Resource = "b",
    StartsOn = DateTimeOffset.UtcNow,
    ExpiresOn = DateTimeOffset.UtcNow.AddHours(1)
};

// Specify read permissions for the SAS.
sasBuilder.SetPermissions(BlobSasPermissions.Read);

// Use the key to get the SAS token.
string sasToken = sasBuilder.ToSasQueryParameters(key, accountName).ToString();

// Construct the full URI, including the SAS token.
UriBuilder fullUri = new UriBuilder()
{
    Scheme = "https",
    Host = string.Format("{0}.blob.core.windows.net", accountName),
    Path = string.Format("{0}/{1}", containerName, blobName),
    Query = sasToken
};
```

## <a name="example-get-a-user-delegation-sas"></a>Voor beeld: een SAS voor gebruikers overdracht ophalen

De volgende voorbeeld methode toont de volledige code voor het verifiëren van de beveiligingsprincipal en het maken van de SA'S voor gebruikers overdracht:

```csharp
async static Task<Uri> GetUserDelegationSasBlob(string accountName, string containerName, string blobName)
{
    // Construct the blob endpoint from the account name.
    string blobEndpoint = string.Format("https://{0}.blob.core.windows.net", accountName);

    // Create a new Blob service client with Azure AD credentials.  
    BlobServiceClient blobClient = new BlobServiceClient(new Uri(blobEndpoint), 
                                                            new DefaultAzureCredential());

    // Get a user delegation key for the Blob service that's valid for seven days.
    // You can use the key to generate any number of shared access signatures over the lifetime of the key.
    UserDelegationKey key = await blobClient.GetUserDelegationKeyAsync(DateTimeOffset.UtcNow, 
                                                                        DateTimeOffset.UtcNow.AddDays(7));

    // Read the key's properties.
    Console.WriteLine("User delegation key properties:");
    Console.WriteLine("Key signed start: {0}", key.SignedStartsOn);
    Console.WriteLine("Key signed expiry: {0}", key.SignedExpiresOn);
    Console.WriteLine("Key signed object ID: {0}", key.SignedObjectId);
    Console.WriteLine("Key signed tenant ID: {0}", key.SignedTenantId);
    Console.WriteLine("Key signed service: {0}", key.SignedService);
    Console.WriteLine("Key signed version: {0}", key.SignedVersion);
    Console.WriteLine();

    // Create a SAS token that's valid for one hour.
    BlobSasBuilder sasBuilder = new BlobSasBuilder()
    {
        BlobContainerName = containerName,
        BlobName = blobName,
        Resource = "b",
        StartsOn = DateTimeOffset.UtcNow,
        ExpiresOn = DateTimeOffset.UtcNow.AddHours(1)
    };

    // Specify read permissions for the SAS.
    sasBuilder.SetPermissions(BlobSasPermissions.Read);

    // Use the key to get the SAS token.
    string sasToken = sasBuilder.ToSasQueryParameters(key, accountName).ToString();

    // Construct the full URI, including the SAS token.
    UriBuilder fullUri = new UriBuilder()
    {
        Scheme = "https",
        Host = string.Format("{0}.blob.core.windows.net", accountName),
        Path = string.Format("{0}/{1}", containerName, blobName),
        Query = sasToken
    };

    Console.WriteLine("User delegation SAS URI: {0}", fullUri);
    Console.WriteLine();
    return fullUri.Uri;
}
```

## <a name="example-read-a-blob-with-a-user-delegation-sas"></a>Voor beeld: een blob met een gebruikers delegering SAS lezen

In het volgende voor beeld worden de SA'S voor gebruikers overdracht getest die in het vorige voor beeld zijn gemaakt vanuit een gesimuleerde client toepassing. Als de SAS geldig is, kan de client toepassing de inhoud van de BLOB lezen. Als de SAS ongeldig is, bijvoorbeeld als deze is verlopen, retourneert Azure Storage fout code 403 (verboden).

```csharp
private static async Task ReadBlobWithSasAsync(Uri sasUri)
{
    // Try performing blob operations using the SAS provided.

    // Create a blob client object for blob operations.
    BlobClient blobClient = new BlobClient(sasUri, null);

    // Download and read the contents of the blob.
    try
    {
        // Download blob contents to a stream and read the stream.
        BlobDownloadInfo blobDownloadInfo = await blobClient.DownloadAsync();
        using (StreamReader reader = new StreamReader(blobDownloadInfo.Content, true))
        {
            string line;
            while ((line = reader.ReadLine()) != null)
            {
                Console.WriteLine(line);
            }
        }

        Console.WriteLine();
        Console.WriteLine("Read operation succeeded for SAS {0}", sasUri);
        Console.WriteLine();
    }
    catch (RequestFailedException e)
    {
        // Check for a 403 (Forbidden) error. If the SAS is invalid,
        // Azure Storage returns this error.
        if (e.Status == 403)
        {
            Console.WriteLine("Read operation failed for SAS {0}", sasUri);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }
        else
        {
            Console.WriteLine(e.Message);
            Console.ReadLine();
            throw;
        }
    }
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>Zie ook

- [Beperkte toegang verlenen tot Azure Storage-resources met behulp van Shared Access signatures (SAS)](../common/storage-sas-overview.md)
- [Sleutel bewerking voor gebruikers overdracht ophalen](/rest/api/storageservices/get-user-delegation-key)
- [Een SAS (REST API) voor gebruikers overdracht maken](/rest/api/storageservices/create-user-delegation-sas)
