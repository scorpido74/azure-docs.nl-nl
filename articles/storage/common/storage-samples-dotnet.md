---
title: Azure Storage-voorbeelden met .NET | Microsoft Documenten
description: Voorbeeldcode en -toepassingen voor Azure Storage weergeven, downloaden en uitvoeren. Ontdek aan de slag met voorbeelden voor blobs, wachtrijen, tabellen en bestanden met behulp van de .NET-opslagclientbibliotheken.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 02/13/2020
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.openlocfilehash: a4c9188c8f1315a3af452cd0c1fb5cf45ab82081
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77486114"
---
# <a name="azure-storage-samples-using-v12-net-client-libraries"></a>Azure Storage-voorbeelden met v12 .NET-clientbibliotheken

De volgende tabel geeft een overzicht van onze monsters repository en de scenario's die in elk monster. Klik op de koppelingen om de bijbehorende voorbeeldcode in GitHub weer te geven.

> [!NOTE]
> Deze voorbeelden maken gebruik van de nieuwste Azure Storage .NET v12-bibliotheek. Zie Azure Blob Storage [Samples voor .NET voor](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started) oudere v11-code in de GitHub-opslagplaats.

## <a name="blob-samples"></a>Blob-voorbeelden

### <a name="authentication"></a>Authentication

:::row:::
   :::column span="":::
      [Verifiëren met een verbindingstekenreeks](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample02_Auth.cs#L27)
   :::column-end:::
   :::column span="":::
      [Verifiëren met behulp van een gedeelde sleutelreferentie](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample02_Auth.cs#L91)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Verifiëren met Azure Identity](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample01a_HelloWorld.cs#L210)
   :::column-end:::
   :::column span="":::
      [Verifiëren met een Active Directory-token](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample02_Auth.cs#L177)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Anoniem toegang tot een openbare blob](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample02_Auth.cs#L55)
   :::column-end:::
:::row-end:::

### <a name="batching"></a>Batchverwerking

:::row:::
   :::column span="":::
      [Meerdere blobs in één aanvraag verwijderen](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample03b_BatchingAsync.cs#L22)
   :::column-end:::
   :::column span="":::
      [Meerdere blobtoegangslagen in één aanvraag instellen](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample03b_BatchingAsync.cs#L56)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Fijnkorrelige controle in een batchaanvraag](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample03b_BatchingAsync.cs#L90)
   :::column-end:::
   :::column span="":::
      [Fouten vangen van een mislukte subbewerking](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample03b_BatchingAsync.cs#L136)
   :::column-end:::
:::row-end:::

### <a name="blob"></a>Blob

:::row:::
   :::column span="":::
      [Een bestand uploaden naar een blob](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample01b_HelloWorldAsync.cs#L21)
   :::column-end:::
   :::column span="":::
      [Een blob downloaden naar een bestand](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample01b_HelloWorldAsync.cs#L66)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Een afbeelding downloaden](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample01b_HelloWorldAsync.cs#L109)
   :::column-end:::
   :::column span="":::
      [Alle blobs in een container weergeven](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample01b_HelloWorldAsync.cs#L128)
   :::column-end:::
:::row-end:::

### <a name="troubleshooting"></a>Problemen oplossen
:::row:::
   :::column span="2":::
      [Een herstelbare fout activeren met een containerclient](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample01b_HelloWorldAsync.cs#L166)
   :::column-end:::
:::row-end:::

## <a name="data-lake-storage-gen2-samples"></a>Gegevens Lake Storage Gen2-monsters

### <a name="authentication"></a>Authentication

:::row:::
   :::column span="":::
      [Anoniem toegang tot een openbaar bestand](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample02_Auth.cs#L28)
   :::column-end:::
   :::column span="":::
      [Verifiëren met behulp van een gedeelde sleutelreferentie](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample02_Auth.cs#L79)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Verifiëren met een gedeelde toegangshandtekening (SAS)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample02_Auth.cs#L114)
   :::column-end:::
   :::column span="":::
      [Verifiëren met een Active Directory-token](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample02_Auth.cs#L164)
   :::column-end:::
:::row-end:::

### <a name="file-system"></a>Bestandssysteem
:::row:::
   :::column span="":::
      [Een bestand maken met een bestandssysteemclient](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L22)
   :::column-end:::
   :::column span="":::
      [Eigenschappen op een bestand en een map downloaden](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L560)
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
      [Een bestand maken met een mapclient](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L55)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Lijst mappen](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L275)
   :::column-end:::
   :::column span="":::
      [Bestanden en mappen doorkruisen](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L318)
   :::column-end:::
:::row-end:::

### <a name="file"></a>File
:::row:::
   :::column span="":::
      [Bestand uploaden](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L126)
   :::column-end:::
   :::column span="":::
      [Uploaden door een bestand toe te voegen](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L169)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Bestand downloaden](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L224)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Een lijst met bestandstoegangsbeheer instellen en downloaden](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L468)
   :::column-end:::
   :::column span="":::
      [Machtigingen van een bestand instellen en opvragen](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L426)
   :::column-end:::
:::row-end:::

### <a name="troubleshooting"></a>Problemen oplossen

:::row:::
   :::column span="2":::
      [Een herstelbare fout activeren](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L389)
   :::column-end:::
:::row-end:::

## <a name="azure-files-samples"></a>Voorbeelden van Azure-bestanden

### <a name="authentication"></a>Authentication

:::row:::
   :::column span="":::
      [Verifiëren met een verbindingstekenreeks](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.Shares/samples/Sample02_Auth.cs#L24)
   :::column-end:::
   :::column span="":::
      [Verifiëren met behulp van een gedeelde sleutelreferentie](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.Shares/samples/Sample02_Auth.cs#L52)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Verifiëren met behulp van een gedeelde toegangshandtekening (SAS))](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.Shares/samples/Sample02_Auth.cs#L86)
   :::column-end:::
:::row-end:::

### <a name="file-shares"></a>Bestandsshares

:::row:::
   :::column span="":::
      [Een share maken en een bestand uploaden](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.Shares/samples/Sample01b_HelloWorldAsync.cs#L21)
   :::column-end:::
   :::column span="":::
      [Bestand downloaden](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.Shares/samples/Sample01b_HelloWorldAsync.cs#L68)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Bestanden en mappen doorkruisen](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.Shares/samples/Sample01b_HelloWorldAsync.cs#L107)
   :::column-end:::
:::row-end:::

### <a name="troubleshooting"></a>Problemen oplossen

:::row:::
   :::column span="2":::
      [Een herstelbare fout activeren met een shareclient](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.Shares/samples/Sample01b_HelloWorldAsync.cs#L141)
   :::column-end:::
:::row-end:::

## <a name="queue-samples"></a>Wachtrijvoorbeelden

### <a name="authentication"></a>Authentication

:::row:::
   :::column span="":::
      [Verifiëren met Azure Active Directory](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample01b_HelloWorldAsync.cs#L167)
   :::column-end:::
   :::column span="":::
      [Verifiëren met een verbindingstekenreeks](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample02_Auth.cs#L24)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Verifiëren met behulp van een gedeelde sleutelreferentie](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample02_Auth.cs#L52)
   :::column-end:::
   :::column span="":::
      [Verifiëren met behulp van een gedeelde toegangshandtekening (SAS))](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample02_Auth.cs#L86)
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
      [Gluren bij berichten](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample01b_HelloWorldAsync.cs#L90)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Berichten ontvangen en een time-out voor zichtbaarheid bijwerken](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample01b_HelloWorldAsync.cs#L115)
   :::column-end:::
:::row-end:::

### <a name="troubleshooting"></a>Problemen oplossen 
:::row:::
   :::column span="2":::
      [Een herstelbare fout activeren met een wachtrijclient](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample01b_HelloWorldAsync.cs#L188)
   :::column-end:::
:::row-end:::

## <a name="table-samples-v11"></a>Tabelvoorbeelden (v11)

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
      [Entiteit invoegen/samenvoegen/vervangen](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/SamplesUtils.cs#L41)
   :::column-end:::
   :::column span="":::
      [Queryentiteiten](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/AdvancedSamples.cs#L672)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Querytabellen](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs)
   :::column-end:::
   :::column span="":::
      [Tabel ACL/eigenschappen](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/AdvancedSamples.cs#L224)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Entiteit bijwerken](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs#L51)
   :::column-end:::
:::row-end:::

## <a name="azure-code-sample-libraries"></a>Voorbeeldbibliotheken voor Azure-code

Ga naar:

* [Azure blobcodevoorbeelden](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples)
* [Codevoorbeelden azure Data Lake](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples)
* [Voorbeelden van Azure Files-code](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.Shares/samples)
* [Voorbeelden van Azure-wachtrijcode](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples)

U de GitHub-opslagplaats voor elke bibliotheek bladeren en klonen.

## <a name="getting-started-guides"></a>Aan de slag met hulplijnen

Bekijk de volgende handleidingen als u op zoek bent naar instructies over het installeren en aan de slag gaan met de Azure Storage Client Libraries.

* [Aan de slag met Azure Blob Service in .NET](../blobs/storage-quickstart-blobs-dotnet.md)
* [Aan de slag met Azure Queue Service in .NET](../queues/storage-quickstart-queues-dotnet.md)
* [Aan de slag met Azure Table Service in .NET](../../cosmos-db/tutorial-develop-table-dotnet.md)
* [Aan de slag met Azure File Service in .NET](../files/storage-dotnet-how-to-use-files.md)

## <a name="next-steps"></a>Volgende stappen

Voor informatie over monsters voor andere talen:

* Java: [Azure Storage-voorbeelden met Java](storage-samples-java.md)
* Python: [Azure Storage-voorbeelden met Python](storage-samples-python.md)
* JavaScript/Node.js: [Azure Storage-voorbeelden met JavaScript](storage-samples-javascript.md)
* Alle andere talen: [azure storage-voorbeelden](storage-samples.md)
