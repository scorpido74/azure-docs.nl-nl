---
title: Gebeurtenis voor het wijzigen van het formaat van de groep Azure Batch | Microsoft Docs
description: Verwijzing voor de voltooiings gebeurtenis voor het wijzigen van de batch pool.
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
ms.openlocfilehash: a11dec8998a77153cd10b6caf72f5885c69b70c3
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70094769"
---
# <a name="pool-resize-complete-event"></a>Gebeurtenis formaat pool wijzigen voltooid

 Deze gebeurtenis wordt verzonden als het wijzigen van de grootte van een groep is voltooid of mislukt.

 In het volgende voor beeld ziet u de hoofd tekst van een gebeurtenis voor het wijzigen van de grootte van een pool voor een groep die groter is gemaakt en is voltooid.

```
{
    "id": "p_1_0_01503750-252d-4e57-bd96-d6aa05601ad8",
    "nodeDeallocationOption": "invalid",
    "currentDedicated": 4,
    "targetDedicated": 4,
    "enableAutoScale": false,
    "isAutoPool": false,
    "startTime": "2016-09-09T22:13:06.573Z",
    "endTime": "2016-09-09T22:14:01.727Z",
    "result": "Success",
    "resizeError": "The operation succeeded"
}
```

|Element|type|Opmerkingen|
|-------------|----------|-----------|
|id|Tekenreeks|De id van de pool.|
|nodeDeallocationOption|Tekenreeks|Hiermee geeft u op wanneer knoop punten uit de pool kunnen worden verwijderd als de pool grootte afneemt.<br /><br /> Mogelijke waarden zijn:<br /><br /> opnieuw **in wachtrij plaatsen** : lopende taken worden beëindigd en opnieuw in de wachtrij worden geplaatst. De taken worden opnieuw uitgevoerd wanneer de taak is ingeschakeld. Knoop punten verwijderen zodra de taken zijn beëindigd.<br /><br /> **beëindigen** : actieve taken worden beëindigd. De taken worden niet opnieuw uitgevoerd. Knoop punten verwijderen zodra de taken zijn beëindigd.<br /><br /> **taskcompletion** : Hiermee staat u toe dat taken die momenteel worden uitgevoerd, worden voltooid. Geen nieuwe taken plannen tijdens het wachten. Knoop punten verwijderen wanneer alle taken zijn voltooid.<br /><br /> **Retaineddata** : Hiermee staat u toe dat actieve taken worden voltooid en wacht u totdat alle Bewaar perioden voor de taak gegevens verlopen. Geen nieuwe taken plannen tijdens het wachten. Knoop punten verwijderen wanneer alle Bewaar perioden van taken zijn verlopen.<br /><br /> De standaard waarde is opnieuw in de wachtrij.<br /><br /> Als de pool grootte wordt verhoogd, wordt de waarde ingesteld op **ongeldig**.|
|currentDedicated|Int32|Het aantal reken knooppunten dat momenteel aan de pool is toegewezen.|
|targetDedicated|Int32|Het aantal reken knooppunten dat is aangevraagd voor de groep.|
|enableAutoScale|Bool|Hiermee geeft u op of de pool grootte automatisch in de loop van de tijd wordt aangepast.|
|isAutoPool|Bool|Hiermee geeft u op of de groep is gemaakt via het mechanisme voor de groep van een taak.|
|startTime|DateTime|Het tijdstip waarop de grootte van de pool wordt gestart.|
|endTime|DateTime|Het tijdstip waarop de groep is voltooid.|
|resultCode|Tekenreeks|Het resultaat van de grootte.|
|resultMessage|Tekenreeks|De fout bij het wijzigen van het formaat bevat de details van het resultaat.<br /><br /> Als de grootte is voltooid, wordt aangegeven dat de bewerking is geslaagd.|