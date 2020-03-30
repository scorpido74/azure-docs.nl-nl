---
title: De begingebeurtenis voor het wijzigen van het formaat van de Azure Batch-groep
description: Referentie voor batchgroep wijzigt de begingebeurtenis voor het formaat van de groep. In het voorbeeld ziet u de hoofdtekst van een begingebeurtenis voor het wijzigen van het formaat van een groep voor het wijzigen van het formaat van 0 naar 2 knooppunten met een handmatigformaat.
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
ms.openlocfilehash: 1866e51da30fe5ed148d019c8720755e99757df7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023579"
---
# <a name="pool-resize-start-event"></a>Gebeurtenis formaat pool wijzigen starten

 Deze gebeurtenis wordt uitgezonden wanneer het formaat van een groep is gewijzigd. Aangezien het formaat van de groep een asynchrone gebeurtenis is, u verwachten dat een volledige gebeurtenis voor het vergroten van de grootte van een groep wordt uitgevoerd zodra de bewerking voor het formaat van de grootte is voltooid.

 In het volgende voorbeeld ziet u de hoofdtekst van een begingebeurtenis voor het wijzigen van het formaat van een groep voor het wijzigen van het formaat van 0 naar 2 knooppunten met een handmatigformaat.

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
|`id`|Tekenreeks|De ID van het zwembad.|
|`nodeDeallocationOption`|Tekenreeks|Hiermee geeft u op wanneer knooppunten uit de groep kunnen worden verwijderd als de grootte van de groep afneemt.<br /><br /> Mogelijke waarden zijn:<br /><br /> **opnieuw in de wachtrij** : Beëindig lopende taken en zet ze opnieuw in de wachtrij. De taken worden opnieuw uitgevoerd wanneer de taak is ingeschakeld. Verwijder knooppunten zodra taken zijn beëindigd.<br /><br /> **beëindigen** – Lopende taken beëindigen. De taken worden niet opnieuw uitgevoerd. Verwijder knooppunten zodra taken zijn beëindigd.<br /><br /> **taakvoltooiing** : Laat momenteel uitvoeren van taken uitvoeren toe. Plan geen nieuwe taken tijdens het wachten. Verwijder knooppunten wanneer alle taken zijn voltooid.<br /><br /> **Bewaarde gegevens** - Laat momenteel taken uitvoeren om te voltooien en wacht vervolgens tot alle bewaartermijnen voor taakgegevens zijn verlopen. Plan geen nieuwe taken tijdens het wachten. Verwijder knooppunten wanneer alle taakbewaartermijnen zijn verstreken.<br /><br /> De standaardwaarde is opnieuw in de wachtrij.<br /><br /> Als de grootte van de groep toeneemt, wordt de waarde ingesteld op **ongeldig**.|
|`currentDedicatedNodes`|Int32|Het aantal rekenknooppunten dat momenteel aan de groep is toegewezen.|
|`targetDedicatedNodes`|Int32|Het aantal compute nodes dat wordt aangevraagd voor de groep.|
|`currentLowPriorityNodes`|Int32|Het aantal rekenknooppunten dat momenteel aan de groep is toegewezen.|
|`targetLowPriorityNodes`|Int32|Het aantal compute nodes dat wordt aangevraagd voor de groep.|
|`enableAutoScale`|Booleaanse waarde|Hiermee geeft u op of de grootte van de groep zich na verloop van tijd automatisch aanpast.|
|`isAutoPool`|Booleaanse waarde|Hiermee geeft u op of de groep is gemaakt via het AutoPool-mechanisme van een taak.|
