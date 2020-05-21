---
author: v-demjoh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 05/18/2020
ms.author: v-demjoh
ms.openlocfilehash: 161138ba09baacdd2a3d18d35c930c645ee78103
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83715160"
---
## <a name="run-the-spx-tool"></a>Het hulp programma SPX uitvoeren

U kunt nu het hulp programma SPX uitvoeren om spraak van tekst in een nieuw audio bestand te bouwen.

Ga vanaf de opdracht regel naar de map die het binaire bestand van het hulp programma SPX bevat en typ het volgende:

```bash
spx synthesize --text "The speech synthesizer greets you!" --audio output greetings.wav
```

Het hulp programma SPX produceert in het audio bestand natuurlijke taal in het Engels `greetings.wav` .
In Windows kunt u het audio bestand afspelen door in te voeren `start greetings.wav` .
