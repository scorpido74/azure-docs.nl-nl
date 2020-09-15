---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 09/08/2020
ms.author: trbye
ms.openlocfilehash: 9c2c46040dd741253e9a68855c4dca89c1dc9a9a
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/09/2020
ms.locfileid: "89570572"
---
[!INCLUDE [SPX Setup](../../spx-setup.md)]

## <a name="speech-to-text-from-microphone"></a>Spraak naar tekst vanuit een microfoon

Sluit de microfoon van uw pc aan en schakel deze in. Schakel alle apps uit die mogelijk ook gebruikmaken van de microfoon. Sommige computers hebben een ingebouwde microfoon, terwijl andere een Bluetooth-apparaat moeten configureren.

Nu bent u klaar om de Speech-CLI uit te voeren om tekst te herkennen uit uw microfoon. Gebruik de opdrachtregel om de map waarin het binair Speech CLI-bestand zich bevindt te veranderen en voer de volgende opdracht uit.

```bash
spx recognize --microphone
```

> [!NOTE]
> De Speech-CLI wordt standaard ingesteld op Engels. U kunt een andere taal [kiezen in de tabel met spraak-naar-tekst](../../../../language-support.md).
> Voeg bijvoorbeeld `--source de-DE` toe om Duitse spraak te herkennen.

Praat in de microfoon en u ziet transcriptie van uw woorden in realtime. De Speech-CLI stopt na een periode van stilte of wanneer u op Ctrl + C drukt.

## <a name="speech-to-text-from-audio-file"></a>Spraak naar tekst vanuit een audiobestand

De Speech-CLI kan spraak herkennen uit veel bestandsindelingen en natuurlijke talen. In dit voorbeeld kunt u een WAV-bestand (16 kHz of 8 kHz, 16-bits en mono PCM) gebruiken dat Engelse spraak bevat. Of als u een sne voorbeeld wilt, moet u de <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/whatstheweatherlike.wav" download="whatstheweatherlike" target="_blank">whatstheweatherlike.wav <span class="docon docon-download x-hidden-focus"></span></a>-bestand downloaden en kopiëren naar dezelfde map als het binaire bestand met de Speechspraak-CLI.

U bent nu klaar om de Speech-CLI uit te voeren om spraak te herkennen in het audiobestand door de volgende opdracht uit te voeren.

```bash
spx recognize --file whatstheweatherlike.wav
```

> [!NOTE]
> De Speech-CLI wordt standaard ingesteld op Engels. U kunt een andere taal [kiezen in de tabel met spraak-naar-tekst](../../../../language-support.md).
> Voeg bijvoorbeeld `--source de-DE` toe om Duitse spraak te herkennen.

De Speech-CLI geeft een transcriptie van tekst van de spraak op het scherm.