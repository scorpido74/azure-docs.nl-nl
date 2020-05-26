---
author: v-demjoh
ms.service: cognitive-services
ms.topic: include
ms.date: 05/13/2020
ms.author: v-demjoh
ms.openlocfilehash: 398dfe36314f86614515168986023146d695e69b
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/22/2020
ms.locfileid: "83806195"
---
## <a name="find-a-file-that-contains-speech"></a>Een bestand zoeken dat spraak bevat

De speech CLI kan spraak herkennen in veel bestands indelingen en natuurlijke talen. Voor deze Quick Start kunt u een WAV-bestand (16kHz of 8kHz, 16-bits en mono PCM) gebruiken dat Engelse spraak bevat.

1. Down load <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/whatstheweatherlike.wav" download="whatstheweatherlike" target="_blank">whatstheweatherlike. WAV <span class="docon docon-download x-hidden-focus"></span> </a>.
2. Kopieer het `whatstheweatherlike.wav` bestand naar dezelfde map als het binaire bestand met de spraak-cli.

## <a name="run-the-speech-cli"></a>De speech CLI uitvoeren

U bent nu klaar om de speech CLI uit te voeren om spraak te herkennen die in het geluids bestand is gevonden.

Ga vanaf de opdracht regel naar de map die het binaire bestand speech CLI bevat en typ het volgende:

```bash
spx recognize --file whatstheweatherlike.wav
```

> [!NOTE]
> De spraak-CLI wordt standaard ingesteld op Engels. U kunt een andere taal kiezen [in de tabel met spraak-naar-tekst](../../../../language-support.md).
> Voeg bijvoorbeeld toe `--source de-DE` om Duitse spraak te herkennen.

De spraak-CLI geeft een tekst transcriptie van de spraak op het scherm. Vervolgens wordt de speech CLI gesloten.
