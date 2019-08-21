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
ms.openlocfilehash: 557e34e8a6bddd36a92b0e212bda3609baa14407
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69642160"
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

<sup>1</sup> vereist de binding `direction` ' InOut ' in *Function. json* of `FileAccess.ReadWrite` in een C# klassen bibliotheek.

Als u probeert verbinding te maken met een van de typen opslag-SDK en er een fout bericht wordt weer gegeven, moet u ervoor zorgen dat u een verwijzing naar [de juiste versie van de Storage SDK](#azure-storage-sdk-version-in-functions-1x)hebt.

Binding met `string`, `Byte[]`, of poco wordt alleen aanbevolen als de Blob-grootte klein is, omdat de gehele blob-inhoud in het geheugen wordt geladen. Over het algemeen is het raadzaam om een `Stream` of `CloudBlockBlob` -type te gebruiken. Zie gelijktijdigheid [en geheugen gebruik](#trigger---concurrency-and-memory-usage) verderop in dit artikel voor meer informatie.