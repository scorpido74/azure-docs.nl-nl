---
title: Een account SAS maken met .NET
titleSuffix: Azure Storage
description: Meer informatie over het maken van een account shared access signature (SAS) met behulp van de .NET-clientbibliotheek.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 9da27cef7bafa94715a42db86fc5a5675a049eb1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137866"
---
# <a name="create-an-account-sas-with-net"></a>Een account SAS maken met .NET

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

In dit artikel ziet u hoe u de opslagaccountsleutel gebruiken om een accountSAS te maken met de [Azure Storage-clientbibliotheek voor .NET](/dotnet/api/overview/azure/storage?view=azure-dotnet).

## <a name="create-an-account-sas"></a>Een account-SAS maken

Als u een account SAS voor een container wilt maken, belt u de methode [CloudStorageAccount.GetSharedAccessSignature.](/dotnet/api/microsoft.azure.storage.cloudstorageaccount.getsharedaccesssignature)

In het volgende codevoorbeeld wordt een account SAS gemaakt dat geldig is voor de Blob- en Bestandsservices en geeft de clientmachtigingen machtigingen voor het lezen, schrijven en weergeven van machtigingen voor toegang tot API's op serviceniveau. Het account SAS beperkt het protocol tot HTTPS, dus het verzoek moet worden gedaan met HTTPS. Vergeet niet om plaatsaanduidingswaarden in hoekhaakjes te vervangen door uw eigen waarden:

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

## <a name="use-an-account-sas-from-a-client"></a>Een account SAS van een client gebruiken

Als u de sas van het account wilt gebruiken om toegang te krijgen tot API's op serviceniveau voor de Blob-service, maakt u een Blob-serviceobject met behulp van het SAS- en Blob-opslageindpunt voor uw opslagaccount. Vergeet niet om plaatsaanduidingswaarden in hoekhaakjes te vervangen door uw eigen waarden:

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

- [Beperkte toegang verlenen tot Azure Storage-bronnen met behulp van gedeelde toegangshandtekeningen (SAS)](storage-sas-overview.md)
- [Een account-SAS maken](/rest/api/storageservices/create-account-sas)
