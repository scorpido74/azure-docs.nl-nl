---
title: Complete gebeurtenis azure batchgroep verwijderen
description: Referentie voor batchgroep verwijdert volledige gebeurtenis. Deze gebeurtenis wordt uitgezonden wanneer een bewerking voor het verwijderen van een groep is voltooid.
services: batch
author: LauraBrenner
manager: evansma
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: labrenne
ms.openlocfilehash: d317d7395a8246c109073a72338b55960cb50954
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023613"
---
# <a name="pool-delete-complete-event"></a>Gebeurtenis pool verwijderen voltooid

 Deze gebeurtenis wordt uitgezonden wanneer een bewerking voor het verwijderen van een groep is voltooid.

 In het volgende voorbeeld ziet u de hoofdtekst van een volledige gebeurtenis voor het verwijderen van een groep verwijderen.

```
{
    "id": "myPool1",
    "startTime": "2016-09-09T22:13:48.579Z",
    "endTime": "2016-09-09T22:14:08.836Z"
}
```

|Element|Type|Opmerkingen|
|-------------|----------|-----------|
|`id`|Tekenreeks|De ID van het zwembad.|
|`startTime`|DateTime|Het moment dat de groep is verwijderd.|
|`endTime`|DateTime|De tijd dat de groep wordt verwijderd.|

## <a name="remarks"></a>Opmerkingen
Zie Een groep verwijderen uit een account voor meer informatie over statussen en foutcodes voor het wijzigen van het formaat [van de groep.](https://docs.microsoft.com/rest/api/batchservice/delete-a-pool-from-an-account)
