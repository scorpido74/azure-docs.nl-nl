---
title: Voor beelden Azure Storage met .NET | Microsoft Docs
description: Voorbeeld code en toepassingen voor Azure Storage weer geven, downloaden en uitvoeren. Ontdek aan de slag met voor beelden voor blobs, wacht rijen, tabellen en bestanden, met behulp van de .NET Storage-client bibliotheken.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/06/2019
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.openlocfilehash: 50c5067c3db2f07da225b72d9ba0a8f0bdc44368
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2020
ms.locfileid: "75748140"
---
# <a name="azure-storage-samples-using-net"></a>Voor beelden Azure Storage met .NET

De volgende tabel bevat een overzicht van onze voor beelden van opslag plaatsen en de scenario's die in elk voor beeld worden behandeld. Klik op de koppelingen om de bijbehorende voorbeeld code in GitHub weer te geven.

> [!NOTE]
> In deze voor beelden wordt de Azure Storage .NET V11-bibliotheek gebruikt. Zie voor [beelden](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples) in de GitHub-opslag plaats voor V12-code.

## <a name="blob-samples-v11"></a>Voor beelden van blobs (V11)

| **Scenario** | **Voorbeeldcode** |
|--------------|-----------------|
| BLOB toevoegen | [Aan de slag met blobs](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs#L1144) |
| Blok-blob | [Webtoepassing voor Azure Blob Storage Photo Gallery](https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs) |
| Clientversleuteling | [Voor beelden van BLOB-versleuteling](https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/EncryptionSamples/BlobGettingStarted/Program.cs) |
| Blob kopiëren | [Aan de slag met blobs](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| Container maken | [Webtoepassing voor Azure Blob Storage Photo Gallery](https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs) |
| Blob verwijderen | [Webtoepassing voor Azure Blob Storage Photo Gallery](https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs) |
| Container verwijderen | [Aan de slag met blobs](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| Meta gegevens/eigenschappen/statistieken voor blobs | [Aan de slag met blobs](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| Container-ACL/meta gegevens/eigenschappen | [Webtoepassing voor Azure Blob Storage Photo Gallery](https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs) |
| Paginabereiken ophalen | [Aan de slag met blobs](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| BLOB/container lease | [Aan de slag met blobs](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| BLOB/container weer geven | [Aan de slag met blobs](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/GettingStarted.cs) |
| Pagina-BLOB | [Aan de slag met blobs](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/GettingStarted.cs) |
| SAS | [Aan de slag met blobs](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| Service-eigenschappen | [Aan de slag met blobs](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| Moment opname-BLOB | [Back-ups maken van Azure virtual machine-schijven met incrementele moment opnamen](https://github.com/Azure-Samples/storage-blob-dotnet-back-up-with-incremental-snapshots/blob/master/Program.cs) |

## <a name="file-samples-v11"></a>Bestands voorbeelden (V11)

| **Scenario** | **Voorbeeldcode** |
|--------------|-----------------|
| Shares/Directory's/bestanden maken | [Azure Storage .NET File Storage-voor beeld](https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs) |
| Shares/mappen/bestanden verwijderen | [Aan de slag met Azure File Service in .NET](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/master/FileStorage/GettingStarted.cs) |
| Mapeigenschappen/meta gegevens | [Azure Storage .NET File Storage-voor beeld](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs) |
| Bestanden downloaden | [Azure Storage .NET File Storage-voor beeld](https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs) |
| Bestands eigenschappen/meta gegevens/statistieken | [Azure Storage .NET File Storage-voor beeld](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs) |
| Eigenschappen van bestands service | [Azure Storage .NET File Storage-voor beeld](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs) |
| Mappen en bestanden weer geven | [Azure Storage .NET File Storage-voor beeld](https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs) |
| Shares weer geven | [Azure Storage .NET File Storage-voor beeld](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs) |
| Eigenschappen/meta gegevens/statistieken delen | [Azure Storage .NET File Storage-voor beeld](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs) |

## <a name="queue-samples-v11"></a>Voor beelden van wachtrij (V11)

| **Scenario** | **Voorbeeldcode** |
|--------------|-----------------|
| Bericht toevoegen | [Aan de slag met Azure Queue service in .NET](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs) |
| Clientversleuteling | [Versleuteling van de .NET-wachtrij aan client zijde Azure Storage](https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/EncryptionSamples/QueueGettingStarted/Program.cs) |
| Wacht rijen maken | [Aan de slag met Azure Queue service in .NET](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs) |
| Bericht/wachtrij verwijderen | [Aan de slag met Azure Queue service in .NET](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs) |
| Bericht bekijken | [Aan de slag met Azure Queue service in .NET](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs) |
| Wachtrij-ACL/meta gegevens/statistieken | [Aan de slag met Azure Queue service in .NET](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/Advanced.cs) |
| Eigenschappen van Queue-service | [Aan de slag met Azure Queue service in .NET](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/Advanced.cs) |
| Bericht bijwerken | [Aan de slag met Azure Queue service in .NET](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs) |

## <a name="table-samples-v11"></a>Tabel voorbeelden (V11)

| **Scenario** | **Voorbeeldcode** |
|--------------|-----------------|
| Tabel maken | [Gelijktijdigheid beheren met Azure Storage-voorbeeld toepassing](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262) |
| Entiteit/tabel verwijderen | [Aan de slag met Azure Table Storage in .NET](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs) |
| Entiteit invoegen/samen voegen/vervangen | [Gelijktijdigheid beheren met Azure Storage-voorbeeld toepassing](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262) |
| Entiteiten opvragen | [Aan de slag met Azure Table Storage in .NET](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs) |
| Query tabellen | [Aan de slag met Azure Table Storage in .NET](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs) |
| Tabel-ACL/eigenschappen | [Aan de slag met Azure Table Storage in .NET](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/AdvancedSamples.cs) |
| Entiteit bijwerken | [Gelijktijdigheid beheren met Azure Storage-voorbeeld toepassing](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262) |

## <a name="azure-code-samples-library"></a>Bibliotheek voor Azure-code voorbeelden

Als u de volledige voorbeeld bibliotheek wilt weer geven, gaat u naar de [Azure code samples](https://azure.microsoft.com/resources/samples/?service=storage) -bibliotheek, die voor beelden bevat voor Azure Storage die u lokaal kunt downloaden en uitvoeren. De voorbeeld bibliotheek code bevat voorbeeld code in. zip-indeling. U kunt ook bladeren en de GitHub-opslag plaats voor elk voor beeld klonen.

[!INCLUDE [storage-dotnet-samples-include](../../../includes/storage-dotnet-samples-include.md)]

## <a name="getting-started-guides"></a>Aan de slag-hand leidingen

Bekijk de volgende hand leidingen als u op zoek bent naar instructies voor het installeren en aan de slag met de Azure Storage-client bibliotheken.

* [Aan de slag met Azure Blob service in .NET](../blobs/storage-dotnet-how-to-use-blobs.md)
* [Aan de slag met Azure Queue service in .NET](../storage-dotnet-how-to-use-queues.md)
* [Aan de slag met Azure Table service in .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Aan de slag met Azure File Service in .NET](../storage-dotnet-how-to-use-files.md)

## <a name="next-steps"></a>Volgende stappen

Voor informatie over voor beelden voor andere talen:

* Java: [Azure Storage voor beelden met behulp van Java](storage-samples-java.md)
* Java script/node. js: [Azure Storage voor beelden met behulp van Java script](storage-samples-javascript.md)
* Python: [Azure Storage voor beelden met behulp van python](storage-samples-python.md)
* Alle andere talen: [Azure Storage](../storage-samples.md) -voor beelden
