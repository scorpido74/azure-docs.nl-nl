---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 08/11/2020
ms.author: trbye
ms.openlocfilehash: b68025c1b33a94f03e2d84693a7d6407a18abd88
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91332432"
---
In deze quickstart maakt u kennis met algemene ontwerppatronen voor het uitvoeren van een spraak-naar-tekstsynthese met behulp van de Speech-SDK. Eerst voert u een basisconfiguratie en -synthese uit en gaat u verder met geavanceerdere voorbeelden voor aangepaste toepassingsontwikkeling zoals:

* Antwoorden krijgen als stromen in het geheugen
* De uitgevoerde frequentie- en bitsnelheid aanpassen
* Synthese-aanvragen indienen met behulp van SSML (Speech Synthesis Markup Language)
* Neurale stemmen gebruiken

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u een Azure-account en een abonnement op de Speech-service hebt. Als u geen account en abonnement hebt, [kunt u de Speech-service gratis uitproberen](../../../overview.md#try-the-speech-service-for-free).

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