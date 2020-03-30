---
title: Azure Batch-groep wijzigt het formaat van de volledige gebeurtenis
description: Referentie voor batchgroep wijzigt de volledige gebeurtenis van de groep. Zie een voorbeeld van een groep die groter is geworden en met succes is voltooid.
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
ms.openlocfilehash: e2c66471ad9fe8d917d1ffddceb6e01c339d62dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022219"
---
# <a name="pool-resize-complete-event"></a>Gebeurtenis formaat pool wijzigen voltooid

 Deze gebeurtenis wordt uitgezonden wanneer het formaat van een groep is voltooid of is mislukt.

 In het volgende voorbeeld ziet u de hoofdtekst van een volledige gebeurtenis voor het vergroten van het formaat van een groep voor een groep die groter is geworden en voltooid.

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
|`id`|Tekenreeks|De ID van het zwembad.|
|`nodeDeallocationOption`|Tekenreeks|Hiermee geeft u op wanneer knooppunten uit de groep kunnen worden verwijderd als de grootte van de groep afneemt.<br /><br /> Mogelijke waarden zijn:<br /><br /> **opnieuw in de wachtrij** : Beëindig lopende taken en zet ze opnieuw in de wachtrij. De taken worden opnieuw uitgevoerd wanneer de taak is ingeschakeld. Verwijder knooppunten zodra taken zijn beëindigd.<br /><br /> **beëindigen** – Lopende taken beëindigen. De taken worden niet opnieuw uitgevoerd. Verwijder knooppunten zodra taken zijn beëindigd.<br /><br /> **taakvoltooiing** : Laat momenteel uitvoeren van taken uitvoeren toe. Plan geen nieuwe taken tijdens het wachten. Verwijder knooppunten wanneer alle taken zijn voltooid.<br /><br /> **Bewaarde gegevens** - Laat momenteel taken uitvoeren om te voltooien en wacht vervolgens tot alle bewaartermijnen voor taakgegevens zijn verlopen. Plan geen nieuwe taken tijdens het wachten. Verwijder knooppunten wanneer alle taakbewaartermijnen zijn verstreken.<br /><br /> De standaardwaarde is opnieuw in de wachtrij.<br /><br /> Als de grootte van de groep toeneemt, wordt de waarde ingesteld op **ongeldig**.|
|`currentDedicatedNodes`|Int32|Het aantal toegewezen basisknooppunten dat momenteel aan de groep is toegewezen.|
|`targetDedicatedNodes`|Int32|Het aantal speciale compute nodes dat wordt aangevraagd voor de groep.|
|`currentLowPriorityNodes`|Int32|Het aantal compute nodes met lage prioriteit dat momenteel aan de groep is toegewezen.|
|`targetLowPriorityNodes`|Int32|Het aantal computenodes met lage prioriteit dat wordt aangevraagd voor de groep.|
|`enableAutoScale`|Booleaanse waarde|Hiermee geeft u op of de grootte van de groep zich na verloop van tijd automatisch aanpast.|
|`isAutoPool`|Booleaanse waarde|Hiermee geeft u op of de groep is gemaakt via het AutoPool-mechanisme van een taak.|
|`startTime`|DateTime|Het tijdstip dat het zwembad het formaat wijzigt.|
|`endTime`|DateTime|Het formaat van het zwembad is voltooid.|
|`resultCode`|Tekenreeks|Het resultaat van het formaat.|
|`resultMessage`|Tekenreeks| Een gedetailleerd bericht over het resultaat.<br /><br /> Als het formaat van het formaat is voltooid, wordt vermeld dat de bewerking is geslaagd.|
