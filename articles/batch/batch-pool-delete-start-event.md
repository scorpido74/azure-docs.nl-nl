---
title: Gebeurtenis Azure Batch groep verwijderen | Microsoft Docs
description: Verwijzing voor de gebeurtenis voor het verwijderen van een batch-pool.
services: batch
author: ju-shim
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: jushiman
ms.openlocfilehash: d582e2b04d203484632a1781d1819f612de41fe7
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "76026727"
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
