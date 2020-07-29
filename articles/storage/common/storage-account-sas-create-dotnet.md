---
title: Een account-SAS maken met .NET
titleSuffix: Azure Storage
description: Meer informatie over het maken van een account voor Shared Access Signature (SAS) met behulp van de .NET-client bibliotheek.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/06/2019
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: common
ms.openlocfilehash: 75f4a3c835a843e824c2ac0b437843cd91824bfa
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85512712"
---
# <a name="create-an-account-sas-with-net"></a>Een account-SAS maken met .NET

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

In dit artikel wordt beschreven hoe u de sleutel van het opslag account gebruikt om een account-SAS te maken met de [Azure Storage-client bibliotheek voor .net](/dotnet/api/overview/azure/storage?view=azure-dotnet).

## <a name="create-an-account-sas"></a>Een account-SAS maken

Als u een account-SAS voor een container wilt maken, roept u de methode [Cloud Storage account. GetSharedAccessSignature](/dotnet/api/microsoft.azure.storage.cloudstorageaccount.getsharedaccesssignature) aan.

In het volgende code voorbeeld worden een account-SA'S gemaakt die geldig zijn voor de BLOB-en bestands Services, en worden de machtigingen lezen, schrijven en weer geven voor de client machtigingen voor toegang tot Api's op service niveau. De account-SAS beperkt het Protocol tot HTTPS, zodat de aanvraag moet worden gemaakt met HTTPS. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen de punthaken te vervangen door uw eigen waarden:

```csharp
static string GetAccountSASToken()
{
    // To create the account SAS, you need to use Shared Key credentials. Modify for your account.
    const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=<storage-account>;AccountKey=<account-key>";
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);

    // Create a new access policy for the account.
    SharedAccessAccountPolicy policy = new SharedAccessAccountPolicy()
        {
            Permissions = SharedAccessAccountPermissions.Read | SharedAccessAccountPermissions.Write | SharedAccessAccountPermissions.List,
            Services = SharedAccessAccountServices.Blob | SharedAccessAccountServices.File,
            ResourceTypes = SharedAccessAccountResourceTypes.Service,
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Protocols = SharedAccessProtocol.HttpsOnly
        };

    // Return the SAS token.
    return storageAccount.GetSharedAccessSignature(policy);
}
```

## <a name="use-an-account-sas-from-a-client"></a>Een account-SAS van een client gebruiken

Als u de account-SA'S wilt gebruiken om toegang te krijgen tot serviceniveau-Api's voor de Blob service, moet u een Blob service-client object maken met behulp van de SAS en het Blob Storage-eind punt voor uw opslag account. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen de punthaken te vervangen door uw eigen waarden:

```csharp
static void UseAccountSAS(string sasToken)
{
    // Create new storage credentials using the SAS token.
    StorageCredentials accountSAS = new StorageCredentials(sasToken);
    // Use these credentials and the account name to create a Blob service client.
    CloudStorageAccount accountWithSAS = new CloudStorageAccount(accountSAS, "<storage-account>", endpointSuffix: null, useHttps: true);
    CloudBlobClient blobClientWithSAS = accountWithSAS.CreateCloudBlobClient();

    // Now set the service properties for the Blob client created with the SAS.
    blobClientWithSAS.SetServiceProperties(new ServiceProperties()
    {
        HourMetrics = new MetricsProperties()
        {
            MetricsLevel = MetricsLevel.ServiceAndApi,
            RetentionDays = 7,
            Version = "1.0"
        },
        MinuteMetrics = new MetricsProperties()
        {
            MetricsLevel = MetricsLevel.ServiceAndApi,
            RetentionDays = 7,
            Version = "1.0"
        },
        Logging = new LoggingProperties()
        {
            LoggingOperations = LoggingOperations.All,
            RetentionDays = 14,
            Version = "1.0"
        }
    });

    // The permissions granted by the account SAS also permit you to retrieve service properties.
    ServiceProperties serviceProperties = blobClientWithSAS.GetServiceProperties();
    Console.WriteLine(serviceProperties.HourMetrics.MetricsLevel);
    Console.WriteLine(serviceProperties.HourMetrics.RetentionDays);
    Console.WriteLine(serviceProperties.HourMetrics.Version);
}
```

## <a name="next-steps"></a>Volgende stappen

- [Beperkte toegang verlenen tot Azure Storage-resources met behulp van Shared Access signatures (SAS)](storage-sas-overview.md)
- [Een account-SAS maken](/rest/api/storageservices/create-account-sas)
