---
title: Een gebruikers delegering SA'S maken voor een container of BLOB met .NET (preview)-Azure Storage
description: Meer informatie over het maken van een SA'S voor het delegeren van gebruikers met Azure Active Directory referenties in Azure Storage met behulp van de .NET-client bibliotheek.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: 59de768e75a88d7cfa5b68fa306d0e83f1aa0ba3
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/29/2019
ms.locfileid: "71671331"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-net-preview"></a>Een gebruikers delegering SA'S maken voor een container of BLOB met .NET (preview)

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

In dit artikel wordt beschreven hoe u Azure Active Directory (Azure AD)-referenties gebruikt om een gebruikers delegering SA'S te maken voor een container of BLOB met de [Azure Storage-client bibliotheek voor .net](https://www.nuget.org/packages/Azure.Storage.Blobs).

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="install-the-preview-packages"></a>De preview-pakketten installeren

In de voor beelden in dit artikel wordt gebruikgemaakt van de nieuwste preview-versie van de Azure Storage-client bibliotheek voor Blob Storage. Als u het preview-pakket wilt installeren, voert u de volgende opdracht uit vanuit de NuGet Package Manager-console:

```
Install-Package Azure.Storage.Blobs -IncludePrerelease
```

In de voor beelden in dit artikel wordt ook gebruikgemaakt van de nieuwste preview-versie van de [Azure Identity client-bibliotheek voor .net](https://www.nuget.org/packages/Azure.Identity/) om te verifiëren met Azure AD-referenties. De Azure Identity client-bibliotheek verifieert een beveiligingsprincipal. De geverifieerde beveiligingsprincipal kan vervolgens de SA'S van de gebruikers delegering maken. Zie de [Azure Identity client-bibliotheek voor .net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity)voor meer informatie over de Azure Identity client-bibliotheek.

```
Install-Package Azure.Identity -IncludePrerelease
```

## <a name="create-a-service-principal"></a>Een service-principal maken

Als u wilt verifiëren met Azure AD-referenties via de client bibliotheek van Azure Identity, gebruikt u een service-principal of een beheerde identiteit als de beveiligingsprincipal, afhankelijk van waar de code wordt uitgevoerd. Als uw code wordt uitgevoerd in een ontwikkel omgeving, gebruikt u een service-principal voor test doeleinden. Als uw code in azure wordt uitgevoerd, gebruikt u een beheerde identiteit. In dit artikel wordt ervan uitgegaan dat u code uitvoert vanuit de ontwikkel omgeving en laat zien hoe u een Service-Principal kunt gebruiken om de gebruikers delegering SA'S te maken.

Roep de opdracht [AZ AD SP create-for-RBAC](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) om een service-principal te maken met Azure CLI en een RBAC-rol toe te wijzen. Geef een Azure Storage rol voor gegevens toegang op om toe te wijzen aan de nieuwe service-principal. De rol moet de actie **micro soft. Storage/Storage accounts/blobServices/generateUserDelegationKey** bevatten. Zie [ingebouwde rollen voor Azure-resources](../../role-based-access-control/built-in-roles.md)voor meer informatie over de ingebouwde rollen voor Azure Storage.

Daarnaast kunt u het bereik voor de roltoewijzing opgeven. De Service-Principal maakt de gebruikers delegerings sleutel. Dit is een bewerking die wordt uitgevoerd op het niveau van het opslag account, zodat de roltoewijzing moet worden ingedeeld op het niveau van het opslag account, de resource groep of het abonnement. Voor meer informatie over RBAC-machtigingen voor het maken van een SA'S voor het delegeren van gebruikers, zie de sectie **machtigingen toewijzen met RBAC** in [een gebruikers delegatie maken (rest API)](/rest/api/storageservices/create-user-delegation-sas).

Als u onvoldoende machtigingen hebt om een rol toe te wijzen aan de Service-Principal, moet u mogelijk de eigenaar van het account of de beheerder vragen de roltoewijzing uit te voeren.

In het volgende voor beeld wordt de Azure CLI gebruikt om een nieuwe service-principal te maken en de rol van **BLOB-gegevens lezer voor opslag** toe te wijzen aan het account bereik

```azurecli-interactive
az ad sp create-for-rbac \
    --name <service-principal> \
    --role "Storage Blob Data Reader" \
    --scopes /subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

De `az ad sp create-for-rbac` opdracht retourneert een lijst met Service-Principal-eigenschappen in JSON-indeling. Kopieer deze waarden zodat u ze kunt gebruiken om de vereiste omgevings variabelen in de volgende stap te maken.

```json
{
    "appId": "generated-app-ID",
    "displayName": "service-principal-name",
    "name": "http://service-principal-uri",
    "password": "generated-password",
    "tenant": "tenant-ID"
}
```

> [!IMPORTANT]
> RBAC-roltoewijzingen kunnen enkele minuten duren voordat deze wordt door gegeven.

## <a name="set-environment-variables"></a>Omgevingsvariabelen instellen

De Azure Identity client-bibliotheek leest waarden uit drie omgevings variabelen tijdens runtime om de service-principal te verifiëren. De volgende tabel beschrijft de waarde die moet worden ingesteld voor elke omgevings variabele.

|Omgevingsvariabele|Value
|-|-
|`AZURE_CLIENT_ID`|De App-ID voor de Service-Principal
|`AZURE_TENANT_ID`|De Azure AD-Tenant-ID van de Service-Principal
|`AZURE_CLIENT_SECRET`|Het wacht woord dat voor de Service-Principal is gegenereerd

> [!IMPORTANT]
> Nadat u de omgevings variabelen hebt ingesteld, sluit u het console venster en opent u het opnieuw. Als u Visual Studio of een andere ontwikkel omgeving gebruikt, moet u de ontwikkel omgeving mogelijk opnieuw opstarten om de nieuwe omgevings variabelen te registreren.

## <a name="add-using-directives"></a>Using-instructies toevoegen

Voeg de volgende `using`-instructies aan uw code toe om de Preview-versies van de Azure-identiteit en Azure Storage-client bibliotheken te gebruiken.

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Azure.Identity;
using Azure.Storage;
using Azure.Storage.Sas;
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
```

## <a name="authenticate-the-service-principal"></a>De Service-Principal verifiëren

Als u de Service-Principal wilt verifiëren, maakt u een instantie van de klasse [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) . De constructor `DefaultAzureCredential` leest de omgevings variabelen die u eerder hebt gemaakt.

Het volgende code fragment laat zien hoe u de geverifieerde referentie kunt ophalen en gebruiken om een serviceclient voor Blob Storage te maken

```csharp
string blobEndpoint = string.Format("https://{0}.blob.core.windows.net", accountName);

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
UserDelegationKey key = await blobClient.GetUserDelegationKeyAsync(DateTimeOffset.UtcNow,
                                                                   DateTimeOffset.UtcNow.AddDays(7));

Console.WriteLine("User delegation key properties:");
Console.WriteLine("Key signed start: {0}", key.SignedStart);
Console.WriteLine("Key signed expiry: {0}", key.SignedExpiry);
Console.WriteLine("Key signed object ID: {0}", key.SignedOid);
Console.WriteLine("Key signed tenant ID: {0}", key.SignedTid);
Console.WriteLine("Key signed service: {0}", key.SignedService);
Console.WriteLine("Key signed version: {0}", key.SignedVersion);
```

## <a name="create-the-sas-token"></a>Het SAS-token maken

In het volgende code fragment wordt een nieuwe [BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) gemaakt en worden de para meters voor de gebruikers delegering sa's opgegeven. Het fragment roept vervolgens de [ToSasQueryParameters](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters) aan om de SAS-token teken reeks op te halen. Ten slotte bouwt de code de volledige URI, met inbegrip van het bron adres en het SAS-token.

```csharp
BlobSasBuilder builder = new BlobSasBuilder()
{
    ContainerName = containerName,
    BlobName = blobName,
    Permissions = "r",
    Resource = "b",
    StartTime = DateTimeOffset.UtcNow,
    ExpiryTime = DateTimeOffset.UtcNow.AddMinutes(5)
};

string sasToken = sasBuilder.ToSasQueryParameters(key, accountName).ToString();

UriBuilder fullUri = new UriBuilder()
{
    Scheme = "https",
    Host = string.Format("{0}.blob.core.windows.net", accountName),
    Path = string.Format("{0}/{1}", containerName, blobName),
    Query = sasToken
};
```

## <a name="example-get-a-user-delegation-sas"></a>Voorbeeld: Een SAS voor gebruikers overdracht ophalen

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
    // Use the key to generate any number of shared access signatures over the lifetime of the key.
    UserDelegationKey key = await blobClient.GetUserDelegationKeyAsync(DateTimeOffset.UtcNow,
                                                                       DateTimeOffset.UtcNow.AddDays(7));

    // Read the key's properties.
    Console.WriteLine("User delegation key properties:");
    Console.WriteLine("Key signed start: {0}", key.SignedStart);
    Console.WriteLine("Key signed expiry: {0}", key.SignedExpiry);
    Console.WriteLine("Key signed object ID: {0}", key.SignedOid);
    Console.WriteLine("Key signed tenant ID: {0}", key.SignedTid);
    Console.WriteLine("Key signed service: {0}", key.SignedService);
    Console.WriteLine("Key signed version: {0}", key.SignedVersion);

    // Create a SAS token that's valid a short interval.
    BlobSasBuilder sasBuilder = new BlobSasBuilder()
    {
        ContainerName = containerName,
        BlobName = blobName,
        Permissions = "r",
        Resource = "b",
        StartTime = DateTimeOffset.UtcNow,
        ExpiryTime = DateTimeOffset.UtcNow.AddMinutes(5)
    };

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
    return fullUri.Uri;
}
```

## <a name="example-read-a-blob-with-a-user-delegation-sas"></a>Voorbeeld: Een BLOB lezen met een SAS voor gebruikers overdracht

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
    catch (StorageRequestFailedException e)
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

- [Sleutel bewerking voor gebruikers overdracht ophalen](/rest/api/storageservices/get-user-delegation-key)
- [Een SAS (REST API) voor gebruikers overdracht maken](/rest/api/storageservices/create-user-delegation-sas)
