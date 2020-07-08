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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "77202097"
---
U kunt binden aan de volgende typen om blobs te schrijven:

* `TextWriter`
* `out string`
* `out Byte[]`
* `CloudBlobStream`
* `Stream`
* `CloudBlobContainer`<sup>i</sup>
* `CloudBlobDirectory`
* `ICloudBlob`<sup>twee</sup>
* `CloudBlockBlob`<sup>twee</sup>
* `CloudPageBlob`<sup>twee</sup>
* `CloudAppendBlob`<sup>twee</sup>

<sup>1</sup> vereist de binding in `direction` *function.js* in of `FileAccess.Read` in een C#-klassen bibliotheek. U kunt echter het container object gebruiken dat de runtime biedt om schrijf bewerkingen uit te voeren, zoals blobs uploaden naar de container.

<sup>2</sup> vereist de binding ' InOut ' `direction` in *function.js* in `FileAccess.ReadWrite` of in een C#-klassebibliotheek.

Als u probeert verbinding te maken met een van de typen opslag-SDK en er een fout bericht wordt weer gegeven, moet u ervoor zorgen dat u een verwijzing naar [de juiste versie van de Storage SDK](../articles/azure-functions/functions-bindings-storage-blob.md#azure-storage-sdk-version-in-functions-1x)hebt.

Gebruik in async-functies de retour waarde of `IAsyncCollector` in plaats van een `out` para meter.

Binding met `string` of `Byte[]` wordt alleen aanbevolen als de Blob-grootte klein is, omdat de gehele blob-inhoud in het geheugen wordt geladen. Over het algemeen is het raadzaam om een of-type te gebruiken `Stream` `CloudBlockBlob` . Zie voor meer informatie gelijktijdigheid [en geheugen gebruik](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#concurrency-and-memory-usage) eerder in dit artikel.
