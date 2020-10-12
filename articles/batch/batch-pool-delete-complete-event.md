---
title: Gebeurtenis Azure Batch groep verwijderen voltooid
description: Verwijzing voor de gebeurtenis voor het verwijderen van een batch-pool. Deze gebeurtenis wordt verzonden als het verwijderen van een groep is voltooid.
ms.topic: reference
ms.date: 04/20/2017
ms.openlocfilehash: f00d32fd5e4eb49fddf4975a9b64ce792d0226dd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85962455"
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

|Element|Type|Notities|
|-------------|----------|-----------|
|`id`|Tekenreeks|De ID van de pool.|
|`startTime`|DateTime|Het tijdstip waarop de groep is verwijderd.|
|`endTime`|DateTime|Het tijdstip waarop de groep is verwijderd.|

## <a name="remarks"></a>Opmerkingen
Zie [een groep uit een account verwijderen](/rest/api/batchservice/delete-a-pool-from-an-account)voor meer informatie over statussen en fout codes voor het wijzigen van de grootte van de pool.
