---
title: bestand opnemen
description: bestand opnemen
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 08/02/2019
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: 4a879c4041fe317955a07eda9dd8a3ef9f542275
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87056054"
---
U kunt de volgende typen parameters gebruiken voor de activeringsblob:

* `Stream`
* `TextReader`
* `string`
* `Byte[]`
* `ICloudBlob`<sup>1</sup>
* `CloudBlockBlob`<sup>1</sup>
* `CloudPageBlob`<sup>1</sup>
* `CloudAppendBlob`<sup>1</sup>

<sup>1</sup> vereist 'inout'-binding `direction` in *function.json* of `FileAccess.ReadWrite` in een C#-klassebibliotheek.

Als u probeert verbinding te maken met een van de typen Storage-SDK en er een foutbericht wordt weergegeven, moet u ervoor zorgen dat u een verwijzing hebt naar [de juiste Storage-SDK-versie](../articles/azure-functions/functions-bindings-storage-blob.md#azure-storage-sdk-version-in-functions-1x).

Binding met `string` of `Byte[]` wordt alleen aanbevolen als de blob-grootte klein is, omdat de gehele blob-inhoud in het geheugen wordt geladen. Over het algemeen is het raadzaam om een type `Stream` of `CloudBlockBlob` te gebruiken. Zie voor meer informatie [Gelijktijdigheid en geheugengebruik](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#concurrency-and-memory-usage) verderop in dit artikel.