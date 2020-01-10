---
title: Azure Storage voor beelden met behulp van python | Microsoft Docs
description: Voorbeeld code en toepassingen voor Azure Storage weer geven, downloaden en uitvoeren. Ontdek aan de slag met voor beelden voor blobs, wacht rijen, tabellen en bestanden, met behulp van de python Storage-client bibliotheken.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/06/2019
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.openlocfilehash: 7f694ee51989023a3e7a72f40700edcbb6a97bae
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2020
ms.locfileid: "75747984"
---
# <a name="azure-storage-samples-using-python"></a>Azure Storage voor beelden met behulp van python

In de volgende tabellen vindt u een overzicht van onze voor beelden van opslag plaatsen en de scenario's die in elk voor beeld worden behandeld. Klik op de koppelingen om de bijbehorende voorbeeld code in GitHub weer te geven.

> [!NOTE]
> Deze voor beelden gebruiken de Azure Storage python v 2.1-bibliotheek. Zie voor [beelden](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob/samples) in de GitHub-opslag plaats voor V12-code.

## <a name="blob-samples-v21"></a>Voor beelden van blobs (v 2.1)

| **Scenario** | **Voorbeeldcode** |
|--------------|-----------------|
| BLOB toevoegen | [Aan de slag met Azure Blob service in python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L166) |
| Blok-blob | [Aan de slag met Azure Blob service in python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L77) |
| Clientversleuteling | [Sleutels voor opslag accounts beheren in een Azure-sleutel waarde met python](https://github.com/Azure-Samples/key-vault-python-storage-accounts) |
| Blob kopiëren | [Aan de slag met Azure Blob service in python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_advanced_samples.py#L102) |
| Container maken | [Aan de slag met Azure Blob service in python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L91) |
| Blob verwijderen | [Aan de slag met Azure Blob service in python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L114) |
| Container verwijderen | [Aan de slag met Azure Blob service in python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L118) |
| Meta gegevens/eigenschappen/statistieken voor blobs | [Aan de slag met Azure Blob service in python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_advanced_samples.py#L298) |
| Container-ACL/meta gegevens/eigenschappen | [Aan de slag met Azure Blob service in python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_advanced_samples.py#L268) |
| Paginabereiken ophalen | [Aan de slag met Azure Blob service in python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L151) |
| BLOB/container lease | [Aan de slag met Azure Blob service in python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_advanced_samples.py#L377) |
| BLOB/container weer geven | [Aan de slag met Azure Blob service in python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L103) |
| Pagina-BLOB | [Aan de slag met Azure Blob service in python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L124) |
| SAS | [Shared Access Signature in python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_advanced_samples.py#L145) |
| Service-eigenschappen | [Aan de slag met Azure Blob service in python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_advanced_samples.py#L540) |
| Moment opname-BLOB | [Aan de slag met Azure Blob service in python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L214) |

## <a name="file-samples-v21"></a>Bestands voorbeelden (v 2.1)

| **Scenario** | **Voorbeeldcode** |
|--------------|-----------------|
| Shares/Directory's/bestanden maken | [Aan de slag met Azure File Service in python](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_basic_samples.py#L71) |
| Shares/mappen/bestanden verwijderen | [Aan de slag met Azure File Service in python](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_basic_samples.py#L170) |
| Mapeigenschappen/meta gegevens | [Aan de slag met Azure File Service in python](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_advanced_samples.py#L175) |
| Bestanden downloaden | [Aan de slag met Azure File Service in python](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_basic_samples.py#L138) |
| Bestands eigenschappen/meta gegevens/statistieken | [Aan de slag met Azure File Service in python](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_advanced_samples.py#L193) |
| Eigenschappen van bestands service | [Aan de slag met Azure File Service in python](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_advanced_samples.py#L125) |
| Mappen en bestanden weer geven | [Aan de slag met Azure File Service in python](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_basic_samples.py#L153) |
| Shares weer geven | [Aan de slag met Azure File Service in python](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_advanced_samples.py#L82) |
| Eigenschappen/meta gegevens/statistieken delen | [Aan de slag met Azure File Service in python](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_advanced_samples.py#L144) |

## <a name="queue-samples-v21"></a>Voor beelden van de wachtrij (v 2.1)

| **Scenario** | **Voorbeeldcode** |
|--------------|-----------------|
| Bericht toevoegen | [Aan de slag met Azure Queue service in python](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_basic_samples.py#L94) |
| Clientversleuteling | [Sleutels voor opslag accounts beheren in Azure Key Vault met python](https://github.com/Azure-Samples/key-vault-python-storage-accounts) |
| Wacht rijen maken | [Aan de slag met Azure Queue service in python](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_basic_samples.py#L75) |
| Bericht/wachtrij verwijderen | [Aan de slag met Azure Queue service in python](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_basic_samples.py#L144) |
| Bericht bekijken | [Aan de slag met Azure Queue service in python](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_basic_samples.py#L110) |
| Wachtrij-ACL/meta gegevens/statistieken | [Aan de slag met Azure Queue service in python](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_advanced_samples.py#L148) |
| Eigenschappen van Queue-service | [Aan de slag met Azure Queue service in python](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_advanced_samples.py#L128) |
| Bericht bijwerken | [Aan de slag met Azure Queue service in python](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_basic_samples.py#L120) |

## <a name="table-samples-v21"></a>Tabel voorbeelden (v 2.1)

| **Scenario** | **Voorbeeldcode** |
|--------------|-----------------|
| Tabel maken | [Aan de slag met Azure Table service in python](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py#L46) |
| Entiteit/tabel verwijderen | [Aan de slag met Azure Table service in python](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py#L79) |
| Entiteit invoegen/samen voegen/vervangen | [Aan de slag met Azure Table service in python](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py#L57) |
| Entiteiten opvragen | [Aan de slag met Azure Table service in python](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py#L62) |
| Query tabellen | [Aan de slag met Azure Table service in python](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py) |
| Tabel-ACL/eigenschappen | [Aan de slag met Azure Table service in python](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_advanced_samples.py#L138) |
| Entiteit bijwerken | [Aan de slag met Azure Table service in python](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py#L68) |

## <a name="azure-code-samples-library"></a>Bibliotheek voor Azure-code voorbeelden

Als u de volledige voorbeeld bibliotheek wilt weer geven, gaat u naar de [Azure code samples](https://azure.microsoft.com/resources/samples/?service=storage) -bibliotheek, die voor beelden bevat voor Azure Storage die u lokaal kunt downloaden en uitvoeren. De voorbeeld bibliotheek code bevat voorbeeld code in. zip-indeling. U kunt ook bladeren en de GitHub-opslag plaats voor elk voor beeld klonen.

[!INCLUDE [storage-python-samples-include](../../../includes/storage-python-samples-include.md)]

## <a name="getting-started-guides"></a>Aan de slag-hand leidingen

Bekijk de volgende hand leidingen als u op zoek bent naar instructies voor het installeren en aan de slag met de Azure Storage-client bibliotheken.

* [Aan de slag met Azure Blob service in python](../blobs/storage-quickstart-blobs-python.md)
* [Aan de slag met Azure Queue service in python](../queues/storage-python-how-to-use-queue-storage.md)
* [Aan de slag met Azure Table service in python](../../cosmos-db/table-storage-how-to-use-python.md)
* [Aan de slag met Azure File Service in python](../files/storage-python-how-to-use-file-storage.md)

## <a name="next-steps"></a>Volgende stappen

Voor informatie over voor beelden voor andere talen:

* .NET: voor [beelden Azure Storage met .net](storage-samples-dotnet.md)
* Java: [Azure Storage voor beelden met behulp van Java](storage-samples-java.md)
* Java script/node. js: [Azure Storage voor beelden met behulp van Java script](storage-samples-javascript.md)
* Alle andere talen: [Azure Storage](storage-samples.md) -voor beelden
