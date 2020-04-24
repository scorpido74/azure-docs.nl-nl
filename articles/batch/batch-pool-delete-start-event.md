---
title: Gebeurtenis Azure Batch groep verwijderen
description: Verwijzing voor de gebeurtenis voor het verwijderen van een batch-pool. Deze gebeurtenis wordt verzonden als het verwijderen van een groep is gestart.
ms.topic: article
ms.date: 04/20/2017
ms.author: labrenne
ms.openlocfilehash: 38e419e549006d3fde2f1694e0d40e620cd438e5
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82115921"
---
# <a name="pool-delete-start-event"></a>Gebeurtenis pool verwijderen starten

 Deze gebeurtenis wordt verzonden als het verwijderen van een groep is gestart. Omdat het verwijderen van de groep een asynchrone gebeurtenis is, kunt u verwachten dat de groep verwijderen voltooid moet worden verzonden zodra de bewerking delete is voltooid.

 In het volgende voor beeld ziet u de hoofd tekst van de gebeurtenis groep verwijderen (start).

```
{
    "id": "myPool1"
}
```

|Element|Type|Opmerkingen|
|-------------|----------|-----------|
|`id`|Tekenreeks|De ID van de pool.|
