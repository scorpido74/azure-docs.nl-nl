---
title: Startgebeurtenis azure batchgroep verwijderen
description: Referentie voor batchgroep verwijderen startgebeurtenis. Deze gebeurtenis wordt uitgezonden wanneer een bewerking voor het verwijderen van een groep is gestart.
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
ms.openlocfilehash: 24a68c6656bd13f0c353d53870a51cdc940fd141
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022202"
---
# <a name="pool-delete-start-event"></a>Gebeurtenis pool verwijderen starten

 Deze gebeurtenis wordt uitgezonden wanneer een bewerking voor het verwijderen van een groep is gestart. Aangezien de groep verwijderen een asynchrone gebeurtenis is, u verwachten dat een volledige gebeurtenis voor het verwijderen van een groep wordt verwijderd zodra de verwijderingsbewerking is voltooid.

 In het volgende voorbeeld ziet u de hoofdtekst van een begingebeurtenis voor het verwijderen van een groep verwijderen.

```
{
    "id": "myPool1"
}
```

|Element|Type|Opmerkingen|
|-------------|----------|-----------|
|`id`|Tekenreeks|De ID van het zwembad.|
