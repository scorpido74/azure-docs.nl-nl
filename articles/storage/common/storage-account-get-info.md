---
title: Opslagaccounttype en SKU-naam met .NET
titleSuffix: Azure Storage
description: Meer informatie over het wijzigen van het type Azure Storage-account type en De naam van sKU's met behulp van de .NET-clientbibliotheek.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/06/2019
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.openlocfilehash: 1495ed55c24a0f94b911d60d1db0f32940ea134a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137055"
---
# <a name="get-storage-account-type-and-sku-name-with-net"></a>Opslagaccounttype en SKU-naam met .NET

In dit artikel ziet u hoe u het azure storage-accounttype en de SKU-naam voor een blob krijgen met behulp van de [Azure Storage-clientbibliotheek voor .NET](/dotnet/api/overview/azure/storage?view=azure-dotnet).

Accountgegevens zijn beschikbaar op serviceversies vanaf versie 2018-03-28.

## <a name="about-account-type-and-sku-name"></a>Informatie over accounttype en SKU-naam

**Accounttype**: Geldige `BlobStorage`accounttypen `FileStorage` `Storage`omvatten `StorageV2`, `BlockBlobStorage`, , en . [Het overzicht van azure-opslagaccounts](storage-account-overview.md) bevat meer informatie, waaronder beschrijvingen van de verschillende opslagaccounts.

**SKU-naam**: Geldige `Premium_LRS`SKU-namen `Standard_RAGRS`omvatten `Standard_RAGZRS`, `Standard_ZRS` `Premium_ZRS`, `Standard_GRS`, `Standard_GZRS` `Standard_LRS`, , en . SKU-namen zijn hoofdlettergevoelig en zijn tekenreeksvelden in de [klasse SkuName](/dotnet/api/microsoft.azure.management.storage.models.skuname?view=azure-dotnet).

## <a name="retrieve-account-information"></a>Accountgegevens ophalen

Als u het type opslagaccount en de SKU-naam wilt krijgen die aan een blob zijn gekoppeld, belt u de methode [GetAccountProperties](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getaccountproperties?view=azure-dotnet) of [GetAccountPropertiesAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getaccountpropertiesasync?view=azure-dotnet) aan.

In het volgende codevoorbeeld worden de alleen-lezen accounteigenschappen opgehaald en weergegeven.

```csharp
private static async Task GetAccountInfoAsync(CloudBlob blob)
{
    try
    {
        // Get the blob's storage account properties.
        AccountProperties acctProps = await blob.GetAccountPropertiesAsync();

        // Display the properties.
        Console.WriteLine("Account properties");
        Console.WriteLine("  AccountKind: {0}", acctProps.AccountKind);
        Console.WriteLine("      SkuName: {0}", acctProps.SkuName);
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

## <a name="next-steps"></a>Volgende stappen

Meer informatie over andere bewerkingen die u uitvoeren op een opslagaccount via de [Azure-portal](https://portal.azure.com) en de Azure REST API.

- [Accountgegevensuitvoeren (REST)](/rest/api/storageservices/get-account-information)
