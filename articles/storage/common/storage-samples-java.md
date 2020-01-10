---
title: Voor beelden Azure Storage met Java | Microsoft Docs
description: Voorbeeld code en toepassingen voor Azure Storage weer geven, downloaden en uitvoeren. Ontdek aan de slag met voor beelden voor blobs, wacht rijen, tabellen en bestanden, met behulp van de client bibliotheken voor Java-opslag.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/06/2019
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.openlocfilehash: dfdde1ae981dcd2d539dec3667e44e90cef4d1c8
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2020
ms.locfileid: "75748046"
---
# <a name="azure-storage-samples-using-java"></a>Azure Storage voor beelden met behulp van Java

De volgende tabel bevat een overzicht van onze voor beelden van opslag plaatsen en de scenario's die in elk voor beeld worden behandeld. Klik op de koppelingen om de bijbehorende voorbeeld code in GitHub weer te geven.

> [!NOTE]
> In deze voor beelden wordt gebruikgemaakt van de Azure Storage Java V11-bibliotheek. Zie voor [beelden](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob) in de GitHub-opslag plaats voor V12-code.

## <a name="blob-samples-v11"></a>Voor beelden van blobs (V11)

| **Scenario** | **Voorbeeldcode** |
|--------------|-----------------|
| BLOB toevoegen | [Aan de slag met Azure Blob service in Java](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| Blok-blob | [Aan de slag met Azure Blob service in Java](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| Clientversleuteling | [Aan de slag met de Azure-versleuteling aan de client zijde in Java](https://github.com/Azure-Samples/storage-java-client-side-encryption) |
| Blob kopiëren | [Aan de slag met Azure Blob service in Java](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| Container maken | [Aan de slag met Azure Blob service in Java](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| Blob verwijderen | [Aan de slag met Azure Blob service in Java](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| Container verwijderen | [Aan de slag met Azure Blob service in Java](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| Meta gegevens/eigenschappen/statistieken voor blobs | [Aan de slag met Azure Blob service in Java](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobAdvanced.java) |
| Container-ACL/meta gegevens/eigenschappen | [Aan de slag met Azure Blob service in Java](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobAdvanced.java) |
| Paginabereiken ophalen | [Aan de slag met Azure Blob service in Java](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java#L399) |
| BLOB/container lease | [Aan de slag met Azure Blob service in Java](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| BLOB/container weer geven | [Aan de slag met Azure Blob service in Java](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| Pagina-BLOB | [Aan de slag met Azure Blob service in Java](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| SAS | [Voor beeld van SAS-tests](https://github.com/Azure/azure-storage-java/blob/89540f018f1160ce55619c6fe7b5f5ff57d0ce10/src/test/java/com/microsoft/azure/storage/Samples.java#L513) |
| Service-eigenschappen | [Aan de slag met Azure Blob service in Java](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobAdvanced.java) |
| Moment opname-BLOB | [Aan de slag met Azure Blob service in Java](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |

## <a name="file-samples-v11"></a>Bestands voorbeelden (V11)

| **Scenario** | **Voorbeeldcode** |
|--------------|-----------------|
| Shares/Directory's/bestanden maken | [Aan de slag met Azure File Service in Java](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java) |
| Shares/mappen/bestanden verwijderen | [Aan de slag met Azure File Service in Java](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java) |
| Mapeigenschappen/meta gegevens | [Aan de slag met Azure File Service in Java](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java) |
| Bestanden downloaden | [Aan de slag met Azure File Service in Java](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java) |
| Bestands eigenschappen/meta gegevens/statistieken | [Aan de slag met Azure File Service in Java](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java) |
| Eigenschappen van bestands service | [Aan de slag met Azure File Service in Java](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java) |
| Mappen en bestanden weer geven | [Aan de slag met Azure File Service in Java](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java) |
| Shares weer geven | [Aan de slag met Azure File Service in Java](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java) |
| Eigenschappen/meta gegevens/statistieken delen | [Aan de slag met Azure File Service in Java](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java) |

## <a name="queue-samples-v11"></a>Voor beelden van wachtrij (V11)

| **Scenario** | **Voorbeeldcode** |
|--------------|-----------------|
| Bericht toevoegen | [Aan de slag met Azure Queue service in Java](https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java#L63) |
| Clientversleuteling | [Aan de slag met de Azure-versleuteling aan de client zijde in Java](https://github.com/Azure-Samples/storage-java-client-side-encryption/blob/master/src/gettingstarted/KeyVaultGettingStarted.java) |
| Wacht rijen maken | [Aan de slag met Azure Queue service in Java](https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java) |
| Bericht/wachtrij verwijderen | [Aan de slag met Azure Queue service in Java](https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java) |
| Bericht bekijken | [Aan de slag met Azure Queue service in Java](https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java) |
| Wachtrij-ACL/meta gegevens/statistieken | [Aan de slag met Azure Queue service in Java](https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueAdvanced.java) |
| Eigenschappen van Queue-service | [Aan de slag met Azure Queue service in Java](https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueAdvanced.java) |
| Bericht bijwerken | [Aan de slag met Azure Queue service in Java](https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java)
|
## <a name="table-samples-v11"></a>Tabel voorbeelden (V11)

| **Scenario** | **Voorbeeldcode** |
|--------------|-----------------|
| Tabel maken | [Aan de slag met de Azure-tabelservice in Java](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java) |
| Entiteit/tabel verwijderen | [Aan de slag met de Azure-tabelservice in Java](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java) |
| Entiteit invoegen/samen voegen/vervangen | [Aan de slag met de Azure-tabelservice in Java](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java) |
| Entiteiten opvragen | [Aan de slag met de Azure-tabelservice in Java](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java) |
| Query tabellen | [Aan de slag met de Azure-tabelservice in Java](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java) |
| Tabel-ACL/eigenschappen | [Aan de slag met de Azure-tabelservice in Java](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableAdvanced.java) |
| Entiteit bijwerken | [Aan de slag met de Azure-tabelservice in Java](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java) |
## <a name="azure-code-samples-library"></a>Bibliotheek voor Azure-code voorbeelden

Als u de volledige voorbeeld bibliotheek wilt weer geven, gaat u naar de [Azure code samples](https://azure.microsoft.com/resources/samples/?service=storage) -bibliotheek, die voor beelden bevat voor Azure Storage die u lokaal kunt downloaden en uitvoeren. De voorbeeld bibliotheek code bevat voorbeeld code in. zip-indeling. U kunt ook bladeren en de GitHub-opslag plaats voor elk voor beeld klonen.

[!INCLUDE [storage-java-samples-include](../../../includes/storage-java-samples-include.md)]

## <a name="getting-started-guides"></a>Aan de slag-hand leidingen

Bekijk de volgende hand leidingen als u op zoek bent naar instructies voor het installeren en aan de slag met de Azure Storage-client bibliotheken.

* [Aan de slag met Azure Blob service in Java](../blobs/storage-quickstart-blobs-java.md)
* [Aan de slag met Azure Queue service in Java](../queues/storage-java-how-to-use-queue-storage.md)
* [Aan de slag met de Azure-tabelservice in Java](../../cosmos-db/table-storage-how-to-use-java.md)
* [Aan de slag met Azure File Service in Java](../files/storage-java-how-to-use-file-storage.md)

## <a name="next-steps"></a>Volgende stappen

Voor informatie over voor beelden voor andere talen:

* .NET: voor [beelden Azure Storage met .net](storage-samples-dotnet.md)
* Java script/node. js: [Azure Storage voor beelden met behulp van Java script](storage-samples-javascript.md)
* Python: [Azure Storage voor beelden met behulp van python](storage-samples-python.md)
* Alle andere talen: [Azure Storage](storage-samples.md) -voor beelden
