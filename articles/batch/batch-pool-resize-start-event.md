---
title: Gebeurtenis voor het wijzigen van de grootte van de groep Azure Batch
description: Verwijzing voor de begin gebeurtenis van de grootte van de batch-pool. Voor beeld toont de hoofd tekst van de start gebeurtenis voor het wijzigen van een pool voor een groep waarvan de grootte wordt gewijzigd van 0 naar 2 knoop punten met een hand matige grootte van het formaat.
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
ms.openlocfilehash: 89f4b04f4ef86ffa3978cadb997d6bfb8dae31c9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75449755"
---
# <a name="pool-resize-start-event"></a>Gebeurtenis formaat pool wijzigen starten

 Deze gebeurtenis wordt verzonden wanneer het formaat van een pool wordt gestart. Aangezien de grootte van de pool een asynchrone gebeurtenis is, kunt u verwachten dat een gebeurtenis voor het wijzigen van de pool grootte moet worden verzonden als de grootte van de bewerking is voltooid.

 In het volgende voor beeld ziet u de hoofd tekst van een groep voor het wijzigen van de grootte van een pool voor het wijzigen van de grootte van 0 naar 2 knoop punten met een hand matige grootte.

```
{
    "id": "myPool1",
    "nodeDeallocationOption": "Invalid",
    "currentDedicatedNodes": 0,
    "targetDedicatedNodes": 2,
    "currentLowPriorityNodes": 0,
    "targetLowPriorityNodes": 2,
    "enableAutoScale": false,
    "isAutoPool": false
}
```

|Element|Type|Opmerkingen|
|-------------|----------|-----------|
|`id`|Tekenreeks|De ID van de pool.|
|`nodeDeallocationOption`|Tekenreeks|Hiermee geeft u op wanneer knoop punten uit de pool kunnen worden verwijderd als de pool grootte afneemt.<br /><br /> Mogelijke waarden zijn:<br /><br /> opnieuw **in wachtrij plaatsen** : lopende taken worden beëindigd en opnieuw in de wachtrij worden geplaatst. De taken worden opnieuw uitgevoerd wanneer de taak is ingeschakeld. Knoop punten verwijderen zodra de taken zijn beëindigd.<br /><br /> **beëindigen** : actieve taken worden beëindigd. De taken worden niet opnieuw uitgevoerd. Knoop punten verwijderen zodra de taken zijn beëindigd.<br /><br /> **taskcompletion** : Hiermee staat u toe dat taken die momenteel worden uitgevoerd, worden voltooid. Geen nieuwe taken plannen tijdens het wachten. Knoop punten verwijderen wanneer alle taken zijn voltooid.<br /><br /> **Retaineddata** : Hiermee staat u toe dat actieve taken worden voltooid en wacht u totdat alle Bewaar perioden voor de taak gegevens verlopen. Geen nieuwe taken plannen tijdens het wachten. Knoop punten verwijderen wanneer alle Bewaar perioden van taken zijn verlopen.<br /><br /> De standaard waarde is opnieuw in de wachtrij.<br /><br /> Als de pool grootte wordt verhoogd, wordt de waarde ingesteld op **ongeldig**.|
|`currentDedicatedNodes`|Int32|Het aantal reken knooppunten dat momenteel aan de pool is toegewezen.|
|`targetDedicatedNodes`|Int32|Het aantal reken knooppunten dat is aangevraagd voor de groep.|
|`currentLowPriorityNodes`|Int32|Het aantal reken knooppunten dat momenteel aan de pool is toegewezen.|
|`targetLowPriorityNodes`|Int32|Het aantal reken knooppunten dat is aangevraagd voor de groep.|
|`enableAutoScale`|Bool|Hiermee geeft u op of de pool grootte automatisch in de loop van de tijd wordt aangepast.|
|`isAutoPool`|Bool|Hiermee geeft u op of de groep is gemaakt via het mechanisme voor de groep van een taak.|
