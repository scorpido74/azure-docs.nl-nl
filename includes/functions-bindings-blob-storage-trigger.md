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
ms.openlocfilehash: fa3888d28c52625684676036def7e2920b77b5ca
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77202110"
---
U kunt de volgende parameter typen voor de activerings-BLOB gebruiken:

* `Stream`
* `TextReader`
* `string`
* `Byte[]`
* Een POCO serialiseerbaar als JSON
* `ICloudBlob`<sup>1</sup>
* `CloudBlockBlob`<sup>1</sup>
* `CloudPageBlob`<sup>1</sup>
* `CloudAppendBlob`<sup>1</sup>

<sup>1</sup> vereist de binding van ' inout ' `direction` in *Function. json* of `FileAccess.ReadWrite` C# in een klassen bibliotheek.

Als u probeert verbinding te maken met een van de typen opslag-SDK en er een fout bericht wordt weer gegeven, moet u ervoor zorgen dat u een verwijzing naar [de juiste versie van de Storage SDK](../articles/azure-functions/functions-bindings-storage-blob.md#azure-storage-sdk-version-in-functions-1x)hebt.

Binding met `string`, `Byte[]`of POCO wordt alleen aanbevolen als de grootte van de BLOB klein is, omdat de gehele blob-inhoud in het geheugen wordt geladen. Over het algemeen is het raadzaam om een `Stream` of `CloudBlockBlob` type te gebruiken. Zie gelijktijdigheid [en geheugen gebruik](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#concurrency-and-memory-usage) verderop in dit artikel voor meer informatie.