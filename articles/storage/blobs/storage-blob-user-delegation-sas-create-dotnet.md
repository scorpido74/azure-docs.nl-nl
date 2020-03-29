---
title: .NET gebruiken om een SAS voor gebruikersdelegatie te maken voor een container of blob
titleSuffix: Azure Storage
description: Meer informatie over het maken van een SAS voor gebruikersoverdrachten met Azure Active Directory-referenties met behulp van de .NET-clientbibliotheek voor Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/18/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: 385d2c3b88bc2e4d653dae2dc9670cb9e9388faf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75371833"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-net"></a>Een gebruikersdelegatie SAS maken voor een container of blob met .NET

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

In dit artikel ziet u hoe u Azure Active Directory-referenties (Azure AD) gebruikt om een SAS voor gebruikersdelegatie sas te maken voor een container of blob met de Azure Storage-clientbibliotheek voor .NET.

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="assign-rbac-roles-for-access-to-data"></a>RBAC-rollen toewijzen voor toegang tot gegevens

Wanneer een Azure AD-beveiligingsprincipal probeert toegang te krijgen tot blobgegevens, moet die beveiligingsprincipal machtigingen voor de bron hebben. Of de beveiligingsprincipal nu een beheerde identiteit is in Azure of een Azure AD-gebruikersaccount met code in de ontwikkelomgeving, de beveiligingsprincipal moet een RBAC-rol krijgen toegewezen die toegang geeft tot blobgegevens in Azure Storage. Zie de sectie getiteld **RBAC-rollen toewijzen voor toegangsrechten** in [Toegang tot Azure-blobs en wachtrijen met Azure Active Directory](../common/storage-auth-aad.md#assign-rbac-roles-for-access-rights)voor informatie over het toewijzen van machtigingen via RBAC.

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

Zie de sectie getiteld **Authenticeren met de Azure Identity-bibliotheek in Azure-bestanden** voor meer informatie over het verifiëren met de Azure Identity-clientbibliotheek vanuit [Azure Storage.](../common/storage-auth-aad-msi.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json#authenticate-with-the-azure-identity-library)

## <a name="add-using-directives"></a>Using-instructies toevoegen

Voeg de `using` volgende richtlijnen toe aan uw code om de azure identity- en Azure Storage-clientbibliotheken te gebruiken.

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

## <a name="get-an-authenticated-token-credential"></a>Een geverifieerde tokenreferentie ophalen

Als u een tokenreferentie wilt ontvangen die uw code kan gebruiken om aanvragen voor Azure Storage te autoriseren, maakt u een instantie van de klasse [DefaultAzureCredential.](/dotnet/api/azure.identity.defaultazurecredential)

In het volgende codefragment ziet u hoe u de geverifieerde tokenreferenties krijgen en deze gebruiken om een serviceclient voor Blob-opslag te maken:

```csharp
// Construct the blob endpoint from the account name.
string blobEndpoint = string.Format("https://{0}.blob.core.windows.net", accountName);

// Create a new Blob service client with Azure AD credentials.
BlobServiceClient blobClient = new BlobServiceClient(new Uri(blobEndpoint),
                                                     new DefaultAzureCredential());
```

## <a name="get-the-user-delegation-key"></a>De sleutel voor gebruikersdelegatie ophalen

Elke SAS is ondertekend met een sleutel. Als u een SAS voor gebruikersdelegatie wilt maken, moet u eerst een gebruikersdelegatiesleutel aanvragen, die vervolgens wordt gebruikt om de SAS te ondertekenen. De gebruikersdelegatiesleutel is analoog aan de accountsleutel die wordt gebruikt om een service SAS of een account SAS te ondertekenen, behalve dat deze is gebaseerd op uw Azure AD-referenties. Wanneer een client een gebruikersdelegatiesleutel aanvraagt met een OAuth 2.0-token, retourneert Azure Storage de gebruikersdelegatiesleutel namens de gebruiker.

Zodra u de sleutel voor gebruikersoverdracht hebt, u die sleutel gebruiken om een willekeurig aantal gedeelde toegangshandtekeningen voor gebruikersdelegeren te maken gedurende de levensduur van de sleutel. De gebruikersdelegatiesleutel is onafhankelijk van het OAuth 2.0-token dat wordt gebruikt om het te verkrijgen, zodat het token niet hoeft te worden vernieuwd zolang de sleutel nog geldig is. U opgeven dat de sleutel maximaal 7 dagen geldig is.

Gebruik een van de volgende methoden om de toets voor gebruikersoverdracht op te vragen:

- [GetUserDelegationKey](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkey)
- [GetUserDelegationKeyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkeyasync)

In het volgende codefragment wordt de toets voor de delegatie van de gebruiker opdeed en worden de eigenschappen ervan uitgeschreven:

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

In het volgende codefragment wordt een nieuwe [BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) gemaakt en worden de parameters voor de sas voor gebruikersdelegatie weergegeven. Het fragment roept vervolgens de [ToSasQueryParameters](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters) aan om de SAS-tekenreeks te krijgen. Ten slotte bouwt de code de volledige URI, inclusief het resourceadres en het SAS-token.

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

## <a name="example-get-a-user-delegation-sas"></a>Voorbeeld: Een gebruikersdelegatie SAS

In de volgende voorbeeldmethode wordt de volledige code weergegeven voor het verifiëren van de beveiligingsprincipal en het maken van de SAS voor gebruikersdelegatie:

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

## <a name="example-read-a-blob-with-a-user-delegation-sas"></a>Voorbeeld: Een blob lezen met een SAS-delegatie van een gebruikersdelegatie

In het volgende voorbeeld wordt de sas voor gebruikersdelegatie sas getest die in het vorige voorbeeld is gemaakt vanuit een gesimuleerde clienttoepassing. Als de SAS geldig is, kan de clienttoepassing de inhoud van de blob lezen. Als de SAS ongeldig is, bijvoorbeeld als deze is verlopen, retourneert Azure Storage foutcode 403 (Verboden).

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

- [Beperkte toegang verlenen tot Azure Storage-bronnen met behulp van gedeelde toegangshandtekeningen (SAS)](../common/storage-sas-overview.md)
- [De sleutelbewerking voor gebruikersdelegatie uitvoeren](/rest/api/storageservices/get-user-delegation-key)
- [Een SAS voor gebruikersdelegatie (REST API) maken](/rest/api/storageservices/create-user-delegation-sas)
