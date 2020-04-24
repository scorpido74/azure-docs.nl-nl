---
title: Gebeurtenis Azure Batch groep verwijderen voltooid
description: Verwijzing voor de gebeurtenis voor het verwijderen van een batch-pool. Deze gebeurtenis wordt verzonden als het verwijderen van een groep is voltooid.
ms.topic: article
ms.date: 04/20/2017
ms.author: labrenne
ms.openlocfilehash: d28223c79f96f35a6ee11f98e0f09f21d7db4451
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82115938"
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

|Element|Type|Opmerkingen|
|-------------|----------|-----------|
|`id`|Tekenreeks|De ID van de pool.|
|`startTime`|DateTime|Het tijdstip waarop de groep is verwijderd.|
|`endTime`|DateTime|Het tijdstip waarop de groep is verwijderd.|

## <a name="remarks"></a>Opmerkingen
Zie [een groep uit een account verwijderen](https://docs.microsoft.com/rest/api/batchservice/delete-a-pool-from-an-account)voor meer informatie over statussen en fout codes voor het wijzigen van de grootte van de pool.
