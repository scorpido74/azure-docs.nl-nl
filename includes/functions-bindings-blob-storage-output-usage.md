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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
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

<sup>1</sup> vereist binding `direction` in *functie. json* of `FileAccess.Read` in een C#-klassebibliotheek. U kunt echter het container object gebruiken dat de runtime biedt om schrijf bewerkingen uit te voeren, zoals blobs uploaden naar de container.

<sup>2</sup> vereist een ' InOut ' `direction` -binding in *Function. json* of `FileAccess.ReadWrite` in een C#-klassen bibliotheek.

Als u probeert verbinding te maken met een van de typen opslag-SDK en er een fout bericht wordt weer gegeven, moet u ervoor zorgen dat u een verwijzing naar [de juiste versie van de Storage SDK](../articles/azure-functions/functions-bindings-storage-blob.md#azure-storage-sdk-version-in-functions-1x)hebt.

Gebruik in async-functies de retour waarde of `IAsyncCollector` in plaats van `out` een para meter.

Binding met `string` of `Byte[]` wordt alleen aanbevolen als de Blob-grootte klein is, omdat de gehele blob-inhoud in het geheugen wordt geladen. Over het algemeen is het raadzaam om een `Stream` of `CloudBlockBlob` -type te gebruiken. Zie voor meer informatie gelijktijdigheid [en geheugen gebruik](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#concurrency-and-memory-usage) eerder in dit artikel.
