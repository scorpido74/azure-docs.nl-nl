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
ms.openlocfilehash: 3788fdb954917f28f64a0dfe035bed4ded0932d5
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/27/2020
ms.locfileid: "69642134"
---
De BLOB-trigger biedt verschillende eigenschappen van meta gegevens. Deze eigenschappen kunnen worden gebruikt als onderdeel van binding expressies in andere bindingen of als para meters in uw code. Deze waarden hebben dezelfde semantiek als het type [CloudBlob](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.cloudblob?view=azure-dotnet) .

|Eigenschap  |Type  |Beschrijving  |
|---------|---------|---------|
|`BlobTrigger`|`string`|Het pad naar de activerings-blob.|
|`Uri`|`System.Uri`|De URI van de BLOB voor de primaire locatie.|
|`Properties` |[BlobProperties](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.blobproperties)|De systeem eigenschappen van de blob. |
|`Metadata` |`IDictionary<string,string>`|De door de gebruiker gedefinieerde meta gegevens voor de blob.|

In het volgende C#-script en Java script-voor beelden wordt bijvoorbeeld het pad naar de activerings-BLOB vastgelegd, met inbegrip van de container:

```csharp
public static void Run(string myBlob, string blobTrigger, ILogger log)
{
    log.LogInformation($"Full blob path: {blobTrigger}");
} 
```
