---
title: Het type opslag account en de SKU-naam ophalen met .NET
titleSuffix: Azure Storage
description: Meer informatie over het verkrijgen van Azure Storage account type en SKU-naam met behulp van de .NET-client bibliotheek.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/06/2019
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: 8fa1e258b07ab98040cbbc5217be789e0bb1b783
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/27/2020
ms.locfileid: "89020130"
---
# <a name="get-storage-account-type-and-sku-name-with-net"></a>Het type opslag account en de SKU-naam ophalen met .NET

In dit artikel wordt beschreven hoe u het Azure Storage account type en de SKU-naam voor een BLOB kunt ophalen met behulp van de [Azure Storage-client bibliotheek voor .net](/dotnet/api/overview/azure/storage?view=azure-dotnet).

Account gegevens zijn beschikbaar in service versies vanaf versie 2018-03-28.

## <a name="about-account-type-and-sku-name"></a>Over het account type en de SKU-naam

**Account type**: geldige account typen zijn `BlobStorage` , `BlockBlobStorage` ,, en `FileStorage` `Storage` `StorageV2` . [Overzicht van Azure Storage-account](storage-account-overview.md) bevat meer informatie, inclusief beschrijvingen van de verschillende opslag accounts.

**SKU-naam**: geldige SKU-namen zijn onder andere `Premium_LRS` , `Premium_ZRS` , `Standard_GRS` , `Standard_GZRS` , `Standard_LRS` , `Standard_RAGRS` , en `Standard_RAGZRS` `Standard_ZRS` . SKU-namen zijn hoofdletter gevoelig en zijn teken reeks velden in de [SkuName-klasse](/dotnet/api/microsoft.azure.management.storage.models.skuname?view=azure-dotnet).

## <a name="retrieve-account-information"></a>Account gegevens ophalen

Roep de methode [GetAccountProperties](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getaccountproperties?view=azure-dotnet) of [GetAccountPropertiesAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getaccountpropertiesasync?view=azure-dotnet) aan om het type opslag account en de SKU-naam op te halen die aan een BLOB zijn gekoppeld.

In het volgende code voorbeeld worden de alleen-lezen-account eigenschappen opgehaald en weer gegeven.

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

Meer informatie over andere bewerkingen die u kunt uitvoeren op een opslag account via de [Azure Portal](https://portal.azure.com) en de Azure rest API.

- [Bewerking account gegevens ophalen (REST)](/rest/api/storageservices/get-account-information)
