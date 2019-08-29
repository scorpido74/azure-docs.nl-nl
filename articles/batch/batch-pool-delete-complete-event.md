---
title: Gebeurtenis Azure Batch groep verwijderen voltooid | Microsoft Docs
description: Verwijzing voor de gebeurtenis voor het verwijderen van een batch-pool.
services: batch
author: laurenhughes
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: lahugh
ms.openlocfilehash: 3ad48fdba298a10d94a32e31e432c0c88b35b658
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70094844"
---
# <a name="pool-delete-complete-event"></a>Gebeurtenis pool verwijderen voltooid

 Deze gebeurtenis wordt verzonden als het verwijderen van een groep is voltooid.

 In het volgende voor beeld ziet u de hoofd tekst van de gebeurtenis groep verwijderen voltooid.

```
{
    "id": "myPool1",
    "startTime": "2016-09-09T22:13:48.579Z",
    "endTime": "2016-09-09T22:14:08.836Z"
}
```

|Element|type|Opmerkingen|
|-------------|----------|-----------|
|id|Tekenreeks|De id van de pool.|
|startTime|DateTime|Het tijdstip waarop de groep is verwijderd.|
|endTime|DateTime|Het tijdstip waarop de groep is verwijderd.|

## <a name="remarks"></a>Opmerkingen
Zie [een groep uit een account verwijderen](https://docs.microsoft.com/rest/api/batchservice/delete-a-pool-from-an-account)voor meer informatie over statussen en fout codes voor het wijzigen van de grootte van de pool.