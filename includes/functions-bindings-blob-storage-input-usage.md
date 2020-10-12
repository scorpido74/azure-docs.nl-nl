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
ms.openlocfilehash: 512d05b245f1279a977ba61d5b4c4904fc0f6bf2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "77202123"
---
U kunt de volgende parameter typen gebruiken voor de BLOB-invoer binding:

* `Stream`
* `TextReader`
* `string`
* `Byte[]`
* `CloudBlobContainer`
* `CloudBlobDirectory`
* `ICloudBlob`<sup>1</sup>
* `CloudBlockBlob`<sup>1</sup>
* `CloudPageBlob`<sup>1</sup>
* `CloudAppendBlob`<sup>1</sup>

<sup>1</sup> vereist 'inout'-binding `direction` in *function.json* of `FileAccess.ReadWrite` in een C#-klassebibliotheek.

Als u probeert verbinding te maken met een van de typen Storage-SDK en er een foutbericht wordt weergegeven, moet u ervoor zorgen dat u een verwijzing hebt naar [de juiste Storage-SDK-versie](../articles/azure-functions/functions-bindings-storage-blob.md#azure-storage-sdk-version-in-functions-1x).

Binding met `string` of `Byte[]` wordt alleen aanbevolen als de Blob-grootte klein is, omdat de gehele blob-inhoud in het geheugen wordt geladen. Over het algemeen is het raadzaam om een type `Stream` of `CloudBlockBlob` te gebruiken. Zie voor meer informatie gelijktijdigheid [en geheugen gebruik](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#concurrency-and-memory-usage) eerder in dit artikel.
