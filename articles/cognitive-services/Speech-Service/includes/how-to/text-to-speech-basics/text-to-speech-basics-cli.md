---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 08/11/2020
ms.author: trbye
ms.openlocfilehash: bee28c946242ecf227287fb36b5b03aa6defb1c2
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/13/2020
ms.locfileid: "88170121"
---
[!INCLUDE [SPX Setup](../../spx-setup.md)]

## <a name="synthesize-speech-to-a-speaker"></a>Spraaksynthese naar een luidspreker

Nu bent u klaar om de Speech CLI uit te voeren om tekst om te zetten in spraak. Gebruik de opdrachtregel om de map waarin het binair Speech CLI-bestand zich bevindt te veranderen. Voer vervolgens de volgende opdracht uit.

```bash
spx synthesize --text "The speech synthesizer greets you!"
```

De Speech CLI produceert natuurlijke taal in het Engels via de luidspreker van de computer.

## <a name="synthesize-speech-to-a-file"></a>Spraak synthetiseren naar een bestand

Voer de volgende opdracht uit om de uitvoer van uw luidspreker te veranderen naar een `.wav`-bestand.

```bash
spx synthesize --text "The speech synthesizer greets you!" --audio output greetings.wav
```

De Speech CLI produceert natuurlijke taal in het Engels en slaat die op in het `greetings.wav`-geluidsbestand.
In Windows kunt u het geluidsbestand afspelen door `start greetings.wav`in te voeren.