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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77202123"
---
U de volgende parametertypen gebruiken voor de blobinvoerbinding:

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

<sup>1</sup> Vereist "inout" binding `direction` in `FileAccess.ReadWrite` *function.json* of in een C# klasse bibliotheek.

Als u probeert te binden aan een van de SDK-typen opslag en een foutbericht ontvangt, moet u ervoor zorgen dat u een verwijzing hebt naar [de juiste Storage SDK-versie.](../articles/azure-functions/functions-bindings-storage-blob.md#azure-storage-sdk-version-in-functions-1x)

Binding `string` aan `Byte[]` of wordt alleen aanbevolen als de blobgrootte klein is, omdat de volledige blob-inhoud in het geheugen wordt geladen. Over het algemeen verdient het `Stream` de `CloudBlockBlob` voorkeur om een of type te gebruiken. Zie [Gelijktijdigheid en geheugengebruik](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#concurrency-and-memory-usage) eerder in dit artikel voor meer informatie.
