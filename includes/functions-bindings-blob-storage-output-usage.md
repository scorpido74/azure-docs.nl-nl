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
ms.openlocfilehash: 0c0ab0e62a5d951f0bc0e237f44cf55c5b8e16cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77202097"
---
U zich binden aan de volgende typen om blobs te schrijven:

* `TextWriter`
* `out string`
* `out Byte[]`
* `CloudBlobStream`
* `Stream`
* `CloudBlobContainer`<sup>1</sup>
* `CloudBlobDirectory`
* `ICloudBlob`<sup>2</sup>
* `CloudBlockBlob`<sup>2</sup>
* `CloudPageBlob`<sup>2</sup>
* `CloudAppendBlob`<sup>2</sup>

<sup>1</sup> Vereist "in" binding `direction` in `FileAccess.Read` *function.json* of in een C# klasse bibliotheek. U echter het containerobject gebruiken dat de runtime biedt om schrijfbewerkingen uit te voeren, zoals het uploaden van blobs naar de container.

<sup>2</sup> Vereist "inout" binding `direction` in `FileAccess.ReadWrite` *function.json* of in een C# klasse bibliotheek.

Als u probeert te binden aan een van de SDK-typen opslag en een foutbericht ontvangt, moet u ervoor zorgen dat u een verwijzing hebt naar [de juiste Storage SDK-versie.](../articles/azure-functions/functions-bindings-storage-blob.md#azure-storage-sdk-version-in-functions-1x)

Gebruik in async-functies de `IAsyncCollector` retourwaarde `out` of in plaats van een parameter.

Binding `string` aan `Byte[]` of wordt alleen aanbevolen als de blobgrootte klein is, omdat de volledige blob-inhoud in het geheugen wordt geladen. Over het algemeen verdient het `Stream` de `CloudBlockBlob` voorkeur om een of type te gebruiken. Zie [Gelijktijdigheid en geheugengebruik](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#concurrency-and-memory-usage) eerder in dit artikel voor meer informatie.
