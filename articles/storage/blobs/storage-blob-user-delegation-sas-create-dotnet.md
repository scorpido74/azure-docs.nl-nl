---
title: Een gebruikers delegering SA'S maken voor een container of BLOB met .NET (preview)-Azure Storage
description: Meer informatie over het maken van een SA'S voor het delegeren van gebruikers met Azure Active Directory referenties in Azure Storage met behulp van de .NET-client bibliotheek.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/17/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: c75a13a20c1dbb222db69145e24838deb111fb66
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/18/2019
ms.locfileid: "72595215"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-net-preview"></a>Een gebruikers delegering SA'S maken voor een container of BLOB met .NET (preview)

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

In dit artikel wordt beschreven hoe u Azure Active Directory (Azure AD)-referenties gebruikt om een gebruikers delegering SA'S te maken voor een container of BLOB met de Azure Storage-client bibliotheek voor .NET.

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="authenticate-with-the-azure-identity-library-preview"></a>Verifiëren met de Azure-identiteits bibliotheek (preview-versie)

Met de Azure Identity client-bibliotheek voor .NET (preview) wordt een beveiligingsprincipal geverifieerd. Wanneer uw code wordt uitgevoerd in azure, is de beveiligingsprincipal een beheerde identiteit voor Azure-resources.

Wanneer uw code wordt uitgevoerd in de ontwikkel omgeving, wordt de verificatie mogelijk automatisch verwerkt of is er mogelijk een browser aanmelding vereist, afhankelijk van de hulpprogram ma's die u gebruikt. Micro soft Visual Studio ondersteunt eenmalige aanmelding (SSO), zodat het actieve Azure AD-gebruikers account automatisch wordt gebruikt voor verificatie. Zie [eenmalige aanmelding bij toepassingen](../../active-directory/manage-apps/what-is-single-sign-on.md)voor meer informatie over SSO.

Andere ontwikkel hulpprogramma's vragen u mogelijk om u aan te melden via een webbrowser. U kunt ook een Service-Principal gebruiken om te verifiëren vanuit de ontwikkel omgeving. Zie [identiteit voor Azure-app maken in de portal](../../active-directory/develop/howto-create-service-principal-portal.md)voor meer informatie.

Na de verificatie krijgt de Azure Identity client-bibliotheek een token referentie. Deze token referentie wordt vervolgens ingekapseld in het service-client object dat u maakt om bewerkingen uit te voeren op basis van Azure Storage. De bibliotheek verwerkt dit voor uw probleemloos door de juiste token referentie op te halen.

Zie de [Azure Identity client-bibliotheek voor .net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity)voor meer informatie over de Azure Identity client-bibliotheek.

## <a name="assign-rbac-roles-for-access-to-data"></a>RBAC-rollen toewijzen voor toegang tot gegevens

Wanneer een Azure AD-beveiligings-principal probeert toegang te krijgen tot BLOB-gegevens, moet die beveiligingsprincipal machtigingen hebben voor de resource. Of de beveiligingsprincipal een beheerde identiteit in azure of een Azure AD-gebruikers account voor het uitvoeren van code in de ontwikkel omgeving is, moet aan de beveiligingsprincipal een RBAC-rol worden toegewezen die toegang verleent tot BLOB-gegevens in Azure Storage. Voor informatie over het toewijzen van machtigingen via RBAC, zie de sectie **RBAC-rollen toewijzen voor toegangs rechten** in [toegang verlenen tot Azure-blobs en-wacht rijen met behulp van Azure Active Directory](../common/storage-auth-aad.md#assign-rbac-roles-for-access-rights).

## <a name="install-the-preview-packages"></a>De preview-pakketten installeren

In de voor beelden in dit artikel wordt gebruikgemaakt van de nieuwste preview-versie van de [Azure Storage-client bibliotheek voor Blob Storage](https://www.nuget.org/packages/Azure.Storage.Blobs). Als u het preview-pakket wilt installeren, voert u de volgende opdracht uit vanuit de NuGet Package Manager-console:

```powershell
Install-Package Azure.Storage.Blobs -IncludePrerelease
```

In de voor beelden in dit artikel wordt ook gebruikgemaakt van de nieuwste preview-versie van de [Azure Identity client-bibliotheek voor .net](https://www.nuget.org/packages/Azure.Identity/) om te verifiëren met Azure AD-referenties. Als u het preview-pakket wilt installeren, voert u de volgende opdracht uit vanuit de NuGet Package Manager-console:

```powershell
Install-Package Azure.Identity -IncludePrerelease
```

## <a name="add-using-directives"></a>Using-instructies toevoegen

Voeg de volgende `using`-instructies aan uw code toe om de Preview-versies van de identiteits-en Azure Storage-client bibliotheken van Azure te gebruiken.

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

## <a name="get-an-authenticated-token-credential"></a>Een geverifieerde token referentie ophalen

Maak een instantie van de [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) -klasse om een token referentie op te halen die door uw code kan worden gebruikt om aanvragen voor Azure Storage te autoriseren.

Het volgende code fragment laat zien hoe de referenties van de geverifieerde token worden opgehaald en gebruikt om een serviceclient voor Blob Storage te maken:

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
