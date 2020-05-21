---
author: v-demjoh
ms.service: cognitive-services
ms.topic: include
ms.date: 05/13/2020
ms.author: v-demjoh
ms.openlocfilehash: 9ff21105e61be59b0000d86455ee19ba6f0d1aeb
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83714658"
---
## <a name="find-a-file-that-contains-speech"></a>Een bestand zoeken dat spraak bevat

Het hulp programma SPX kan spraak herkennen in veel bestands indelingen en natuurlijke talen. Voor deze Quick Start kunt u een WAV-bestand (16kHz of 8kHz, 16-bits en mono PCM) gebruiken dat Engelse spraak bevat.

1. Down load <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/whatstheweatherlike.wav" download="whatstheweatherlike" target="_blank">whatstheweatherlike. WAV <span class="docon docon-download x-hidden-focus"></span> </a>.
2. Kopieer het `whatstheweatherlike.wav` bestand naar dezelfde map als het binaire bestand van het SPX-hulp programma.

## <a name="run-the-spx-tool"></a>Het hulp programma SPX uitvoeren

U kunt nu het hulp programma SPX uitvoeren om spraak te herkennen die in het geluids bestand is gevonden.

Ga vanaf de opdracht regel naar de map die het binaire bestand van het hulp programma SPX bevat en typ het volgende:

```bash
spx recognize --file whatstheweatherlike.wav
```

> [!NOTE]
> Het hulp programma SPX wordt standaard ingesteld op Engels. U kunt een andere taal kiezen [in de tabel met spraak-naar-tekst](../../../../language-support.md).
> Voeg bijvoorbeeld toe `--source de-DE` om Duitse spraak te herkennen.

Het hulp programma SPX geeft een tekst transcriptie van de spraak op het scherm. Vervolgens wordt het hulp programma SPX gesloten.
