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
ms.openlocfilehash: 414381d42c52e7489fbd6aaacf0f079cdd978ebe
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69642095"
---
U kunt binden aan de volgende typen om blobs te schrijven:

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

<sup>1</sup> vereist binding `direction` in *functie. json* of `FileAccess.Read` in een C# klassen bibliotheek. U kunt echter het container object gebruiken dat de runtime biedt om schrijf bewerkingen uit te voeren, zoals blobs uploaden naar de container.

<sup>2</sup> vereist een ' InOut ' `direction` binding in *Function. json* of `FileAccess.ReadWrite` in C# een klassen bibliotheek.

Als u probeert verbinding te maken met een van de typen opslag-SDK en er een fout bericht wordt weer gegeven, moet u ervoor zorgen dat u een verwijzing naar [de juiste versie van de Storage SDK](#azure-storage-sdk-version-in-functions-1x)hebt.

In het async-functies, gebruikt u de geretourneerde waarde of `IAsyncCollector` in plaats van een `out` parameter.

Binding met `string` of `Byte[]` wordt alleen aanbevolen als de Blob-grootte klein is, omdat de gehele blob-inhoud in het geheugen wordt geladen. Over het algemeen is het raadzaam om een `Stream` of `CloudBlockBlob` -type te gebruiken. Zie voor meer informatie gelijktijdigheid [en geheugen gebruik](#trigger---concurrency-and-memory-usage) eerder in dit artikel.