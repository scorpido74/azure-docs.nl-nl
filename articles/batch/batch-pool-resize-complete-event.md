---
title: Gebeurtenis voor het wijzigen van de grootte van de groep Azure Batch
description: Verwijzing voor de voltooiings gebeurtenis voor het wijzigen van de batch pool. Bekijk een voor beeld van een pool die groter is gemaakt en is voltooid.
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
ms.openlocfilehash: 0d4d07fe5762905633487cfd541b1c039ee1c841
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "76026655"
---
# <a name="pool-resize-complete-event"></a>Gebeurtenis formaat pool wijzigen voltooid

 Deze gebeurtenis wordt verzonden als het wijzigen van de grootte van een groep is voltooid of mislukt.

 In het volgende voor beeld ziet u de hoofd tekst van een gebeurtenis voor het wijzigen van de grootte van een pool voor een groep die groter is gemaakt en is voltooid.

```
{
    "id": "myPool",
    "nodeDeallocationOption": "invalid",
        "currentDedicatedNodes": 10,
        "targetDedicatedNodes": 10,
    "currentLowPriorityNodes": 5,
        "targetLowPriorityNodes": 5,
    "enableAutoScale": false,
    "isAutoPool": false,
    "startTime": "2016-09-09T22:13:06.573Z",
    "endTime": "2016-09-09T22:14:01.727Z",
    "resultCode": "Success",
    "resultMessage": "The operation succeeded"
}
```

|Element|Type|Opmerkingen|
|-------------|----------|-----------|
|`id`|Tekenreeks|De ID van de pool.|
|`nodeDeallocationOption`|Tekenreeks|Hiermee geeft u op wanneer knoop punten uit de pool kunnen worden verwijderd als de pool grootte afneemt.<br /><br /> Mogelijke waarden zijn:<br /><br /> opnieuw **in wachtrij plaatsen** : lopende taken worden beëindigd en opnieuw in de wachtrij worden geplaatst. De taken worden opnieuw uitgevoerd wanneer de taak is ingeschakeld. Knoop punten verwijderen zodra de taken zijn beëindigd.<br /><br /> **beëindigen** : actieve taken worden beëindigd. De taken worden niet opnieuw uitgevoerd. Knoop punten verwijderen zodra de taken zijn beëindigd.<br /><br /> **taskcompletion** : Hiermee staat u toe dat taken die momenteel worden uitgevoerd, worden voltooid. Geen nieuwe taken plannen tijdens het wachten. Knoop punten verwijderen wanneer alle taken zijn voltooid.<br /><br /> **Retaineddata** : Hiermee staat u toe dat actieve taken worden voltooid en wacht u totdat alle Bewaar perioden voor de taak gegevens verlopen. Geen nieuwe taken plannen tijdens het wachten. Knoop punten verwijderen wanneer alle Bewaar perioden van taken zijn verlopen.<br /><br /> De standaard waarde is opnieuw in de wachtrij.<br /><br /> Als de pool grootte wordt verhoogd, wordt de waarde ingesteld op **ongeldig**.|
|`currentDedicatedNodes`|Int32|Het aantal toegewezen reken knooppunten dat momenteel aan de pool is toegewezen.|
|`targetDedicatedNodes`|Int32|Het aantal toegewezen reken knooppunten dat voor de pool wordt aangevraagd.|
|`currentLowPriorityNodes`|Int32|Het aantal reken knooppunten met lage prioriteit dat momenteel aan de pool is toegewezen.|
|`targetLowPriorityNodes`|Int32|Het aantal reken knooppunten met lage prioriteit dat voor de groep wordt aangevraagd.|
|`enableAutoScale`|Bool|Hiermee geeft u op of de pool grootte automatisch in de loop van de tijd wordt aangepast.|
|`isAutoPool`|Bool|Hiermee geeft u op of de groep is gemaakt via het mechanisme voor de groep van een taak.|
|`startTime`|Datum/tijd|Het tijdstip waarop de grootte van de pool wordt gestart.|
|`endTime`|Datum/tijd|Het tijdstip waarop de groep is voltooid.|
|`resultCode`|Tekenreeks|Het resultaat van de grootte.|
|`resultMessage`|Tekenreeks| Een gedetailleerd bericht over het resultaat.<br /><br /> Als de grootte is voltooid, wordt aangegeven dat de bewerking is geslaagd.|
