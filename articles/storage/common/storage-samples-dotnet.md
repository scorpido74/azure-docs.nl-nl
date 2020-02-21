---
title: Voor beelden Azure Storage met .NET | Microsoft Docs
description: Voorbeeld code en toepassingen voor Azure Storage weer geven, downloaden en uitvoeren. Ontdek aan de slag met voor beelden voor blobs, wacht rijen, tabellen en bestanden, met behulp van de .NET Storage-client bibliotheken.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 02/13/2020
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.openlocfilehash: a4c9188c8f1315a3af452cd0c1fb5cf45ab82081
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/20/2020
ms.locfileid: "77486114"
---
# <a name="azure-storage-samples-using-v12-net-client-libraries"></a>Azure Storage voor beelden met behulp van V12 .NET-client bibliotheken

De volgende tabel bevat een overzicht van onze voor beelden van opslag plaatsen en de scenario's die in elk voor beeld worden behandeld. Klik op de koppelingen om de bijbehorende voorbeeld code in GitHub weer te geven.

> [!NOTE]
> In deze voor beelden wordt de nieuwste Azure Storage .NET V12-bibliotheek gebruikt. Zie [Azure Blob Storage-voor beelden voor .net](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started) in de GitHub-opslag plaats voor een verouderde V11-code.

## <a name="blob-samples"></a>BLOB-voor beelden

### <a name="authentication"></a>Authentication

:::row:::
   :::column span="":::
      [Verifiëren met behulp van een connection string](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample02_Auth.cs#L27)
   :::column-end:::
   :::column span="":::
      [Verifiëren met een gedeelde sleutel referentie](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample02_Auth.cs#L91)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Verifiëren met Azure-identiteit](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample01a_HelloWorld.cs#L210)
   :::column-end:::
   :::column span="":::
      [Verifiëren met een Active Directory-token](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample02_Auth.cs#L177)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Anoniem toegang krijgen tot een open bare BLOB](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample02_Auth.cs#L55)
   :::column-end:::
:::row-end:::

### <a name="batching"></a>Batchverwerking

:::row:::
   :::column span="":::
      [Meerdere blobs in één aanvraag verwijderen](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample03b_BatchingAsync.cs#L22)
   :::column-end:::
   :::column span="":::
      [Meerdere BLOB-toegangs lagen instellen in één aanvraag](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample03b_BatchingAsync.cs#L56)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Nauw keurig beheer van een batch-aanvraag](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample03b_BatchingAsync.cs#L90)
   :::column-end:::
   :::column span="":::
      [Fouten van een mislukte subbewerking ondervangen](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample03b_BatchingAsync.cs#L136)
   :::column-end:::
:::row-end:::

### <a name="blob"></a>Blob

:::row:::
   :::column span="":::
      [Een bestand uploaden naar een BLOB](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample01b_HelloWorldAsync.cs#L21)
   :::column-end:::
   :::column span="":::
      [Een BLOB downloaden naar een bestand](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample01b_HelloWorldAsync.cs#L66)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Een installatie kopie downloaden](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample01b_HelloWorldAsync.cs#L109)
   :::column-end:::
   :::column span="":::
      [Alle blobs in een container weer geven](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample01b_HelloWorldAsync.cs#L128)
   :::column-end:::
:::row-end:::

### <a name="troubleshooting"></a>Problemen oplossen
:::row:::
   :::column span="2":::
      [Een herstel bare fout activeren met behulp van een container-client](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample01b_HelloWorldAsync.cs#L166)
   :::column-end:::
:::row-end:::

## <a name="data-lake-storage-gen2-samples"></a>Data Lake Storage Gen2-voor beelden

### <a name="authentication"></a>Authentication

:::row:::
   :::column span="":::
      [Anoniem toegang krijgen tot een openbaar bestand](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample02_Auth.cs#L28)
   :::column-end:::
   :::column span="":::
      [Verifiëren met een gedeelde sleutel referentie](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample02_Auth.cs#L79)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Verifiëren met behulp van een Shared Access Signature (SAS)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample02_Auth.cs#L114)
   :::column-end:::
   :::column span="":::
      [Verifiëren met een Active Directory-token](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample02_Auth.cs#L164)
   :::column-end:::
:::row-end:::

### <a name="file-system"></a>Bestandssysteem
:::row:::
   :::column span="":::
      [Een bestand maken met een File System-client](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L22)
   :::column-end:::
   :::column span="":::
      [Eigenschappen voor een bestand en een map ophalen](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L560)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [De naam van een bestand en een map wijzigen](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L511)
   :::column-end:::
:::row-end:::

### <a name="directory"></a>Directory

:::row:::
   :::column span="":::
      [Een map maken](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L93)
   :::column-end:::
   :::column span="":::
      [Een bestand maken met een Directory-client](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L55)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Mappen weer geven](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L275)
   :::column-end:::
   :::column span="":::
      [Door bestanden en mappen bladeren](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L318)
   :::column-end:::
:::row-end:::

### <a name="file"></a>File
:::row:::
   :::column span="":::
      [Bestand uploaden](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L126)
   :::column-end:::
   :::column span="":::
      [Uploaden door toe te voegen aan een bestand](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L169)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Een bestand downloaden](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L224)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Een toegangs beheer lijst voor een bestand instellen en ophalen](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L468)
   :::column-end:::
   :::column span="":::
      [Machtigingen voor een bestand instellen en ophalen](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L426)
   :::column-end:::
:::row-end:::

### <a name="troubleshooting"></a>Problemen oplossen

:::row:::
   :::column span="2":::
      [Een herstel bare fout activeren](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L389)
   :::column-end:::
:::row-end:::

## <a name="azure-files-samples"></a>Azure Files-voor beelden

### <a name="authentication"></a>Authentication

:::row:::
   :::column span="":::
      [Verifiëren met behulp van een connection string](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.Shares/samples/Sample02_Auth.cs#L24)
   :::column-end:::
   :::column span="":::
      [Verifiëren met een gedeelde sleutel referentie](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.Shares/samples/Sample02_Auth.cs#L52)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Verifiëren met behulp van een Shared Access Signature (SAS))](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.Shares/samples/Sample02_Auth.cs#L86)
   :::column-end:::
:::row-end:::

### <a name="file-shares"></a>Bestandsshares

:::row:::
   :::column span="":::
      [Een share maken en een bestand uploaden](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.Shares/samples/Sample01b_HelloWorldAsync.cs#L21)
   :::column-end:::
   :::column span="":::
      [Een bestand downloaden](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.Shares/samples/Sample01b_HelloWorldAsync.cs#L68)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Door bestanden en mappen bladeren](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.Shares/samples/Sample01b_HelloWorldAsync.cs#L107)
   :::column-end:::
:::row-end:::

### <a name="troubleshooting"></a>Problemen oplossen

:::row:::
   :::column span="2":::
      [Een herstel bare fout activeren met behulp van een share-client](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.Shares/samples/Sample01b_HelloWorldAsync.cs#L141)
   :::column-end:::
:::row-end:::

## <a name="queue-samples"></a>Wachtrij voorbeelden

### <a name="authentication"></a>Authentication

:::row:::
   :::column span="":::
      [Verifiëren met behulp van Azure Active Directory](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample01b_HelloWorldAsync.cs#L167)
   :::column-end:::
   :::column span="":::
      [Verifiëren met behulp van een connection string](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample02_Auth.cs#L24)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Verifiëren met een gedeelde sleutel referentie](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample02_Auth.cs#L52)
   :::column-end:::
   :::column span="":::
      [Verifiëren met behulp van een Shared Access Signature (SAS))](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample02_Auth.cs#L86)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Verifiëren met een Active Directory-token](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample02_Auth.cs#L140)
   :::column-end:::
:::row-end:::

### <a name="queue"></a>Wachtrij

:::row:::
   :::column span="2":::
      [Een wachtrij maken en een bericht toevoegen](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample01b_HelloWorldAsync.cs#L24)
   :::column-end:::
:::row-end:::

### <a name="message"></a>Bericht

:::row:::
   :::column span="":::
      [Berichten ontvangen en verwerken](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample01b_HelloWorldAsync.cs#L61)
   :::column-end:::
   :::column span="":::
      [Berichten bekijken](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample01b_HelloWorldAsync.cs#L90)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Berichten ontvangen en time-out voor bijwerken van zicht baarheid](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample01b_HelloWorldAsync.cs#L115)
   :::column-end:::
:::row-end:::

### <a name="troubleshooting"></a>Problemen oplossen 
:::row:::
   :::column span="2":::
      [Een herstel bare fout activeren met behulp van een wachtrij-client](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample01b_HelloWorldAsync.cs#L188)
   :::column-end:::
:::row-end:::

## <a name="table-samples-v11"></a>Tabel voorbeelden (V11)

:::row:::
   :::column span="":::
      [Tabel maken](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/Common.cs#L40)
   :::column-end:::
   :::column span="":::
      [Entiteit/tabel verwijderen](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Entiteit invoegen/samen voegen/vervangen](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/SamplesUtils.cs#L41)
   :::column-end:::
   :::column span="":::
      [Entiteiten opvragen](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/AdvancedSamples.cs#L672)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Query tabellen](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs)
   :::column-end:::
   :::column span="":::
      [Tabel-ACL/eigenschappen](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/AdvancedSamples.cs#L224)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Entiteit bijwerken](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs#L51)
   :::column-end:::
:::row-end:::

## <a name="azure-code-sample-libraries"></a>Voorbeeld bibliotheken van Azure-code

Als u de volledige .NET-voorbeeld bibliotheken wilt weer geven, gaat u naar:

* [Voor beelden van Azure Blob-code](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples)
* [Voor beelden van Azure Data Lake code](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples)
* [Voor beelden van Azure Files code](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.Shares/samples)
* [Voor beelden van Azure-wachtrij code](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples)

U kunt de GitHub-opslag plaats voor elke tape wisselaar door bladeren en klonen.

## <a name="getting-started-guides"></a>Aan de slag-hand leidingen

Bekijk de volgende hand leidingen als u op zoek bent naar instructies voor het installeren en aan de slag met de Azure Storage-client bibliotheken.

* [Aan de slag met Azure Blob service in .NET](../blobs/storage-quickstart-blobs-dotnet.md)
* [Aan de slag met Azure Queue service in .NET](../queues/storage-quickstart-queues-dotnet.md)
* [Aan de slag met Azure Table service in .NET](../../cosmos-db/tutorial-develop-table-dotnet.md)
* [Aan de slag met Azure File Service in .NET](../files/storage-dotnet-how-to-use-files.md)

## <a name="next-steps"></a>Volgende stappen

Voor informatie over voor beelden voor andere talen:

* Java: [Azure Storage voor beelden met behulp van Java](storage-samples-java.md)
* Python: [Azure Storage voor beelden met behulp van python](storage-samples-python.md)
* Java script/node. js: [Azure Storage voor beelden met behulp van Java script](storage-samples-javascript.md)
* Alle andere talen: [Azure Storage](storage-samples.md) -voor beelden
