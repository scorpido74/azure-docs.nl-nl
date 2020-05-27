---
author: v-demjoh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 05/18/2020
ms.author: v-demjoh
ms.openlocfilehash: bd48618a520f547c72bfba7ff04ae6a249d0a673
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/22/2020
ms.locfileid: "83806326"
---
## <a name="run-the-speech-cli"></a>De speech CLI uitvoeren

Nu bent u klaar om de speech CLI uit te voeren om spraak van tekst in een nieuw audio bestand te kunnen samen werken.

Ga vanaf de opdracht regel naar de map die het binaire bestand speech CLI bevat en typ het volgende:

```bash
spx synthesize --text "The speech synthesizer greets you!" --audio output greetings.wav
```

De spraak-CLI produceert in het audio bestand natuurlijke taal in het Engels `greetings.wav` .
In Windows kunt u het audio bestand afspelen door in te voeren `start greetings.wav` .
