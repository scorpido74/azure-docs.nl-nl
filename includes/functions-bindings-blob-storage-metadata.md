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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "69642134"
---
De blobtrigger biedt verschillende metagegevenseigenschappen. Deze eigenschappen kunnen worden gebruikt als onderdeel van bindende expressies in andere bindingen of als parameters in uw code. Deze waarden hebben dezelfde semantiek als het [CloudBlob-type.](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.cloudblob?view=azure-dotnet)

|Eigenschap  |Type  |Beschrijving  |
|---------|---------|---------|
|`BlobTrigger`|`string`|Het pad naar de activerende blob.|
|`Uri`|`System.Uri`|De URI van de blob voor de primaire locatie.|
|`Properties` |[BlobEigenschappen](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.blobproperties)|De systeemeigenschappen van de blob. |
|`Metadata` |`IDictionary<string,string>`|De door de gebruiker gedefinieerde metagegevens voor de blob.|

In de volgende C#-script- en JavaScript-voorbeelden wordt bijvoorbeeld het pad naar de triggerende blob bijlogboeken geplaatst, inclusief de container:

```csharp
public static void Run(string myBlob, string blobTrigger, ILogger log)
{
    log.LogInformation($"Full blob path: {blobTrigger}");
} 
```
