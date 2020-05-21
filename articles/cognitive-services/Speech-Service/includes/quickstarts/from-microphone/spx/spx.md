---
author: v-demjoh
ms.service: cognitive-services
ms.topic: include
ms.date: 05/13/2020
ms.author: v-demjoh
ms.openlocfilehash: 9f1da13efc9f75d14fdf2d158e8b7547d4a9fa94
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83715296"
---
## <a name="enable-microphone"></a>Microfoon inschakelen

Sluit uw PC-microfoon aan en schakel deze in en schakel alle apps uit die mogelijk ook gebruikmaken van de microfoon. Sommige computers hebben een ingebouwde microfoon, terwijl anderen een Bluetooth-apparaat moeten configureren.

## <a name="run-the-spx-tool"></a>Het hulp programma SPX uitvoeren

U kunt nu het hulp programma SPX uitvoeren om spraak van uw microfoon te herkennen.

1. **Start uw app** -vanaf de opdracht regel, ga naar de map met het binaire bestand van het hulp programma SPX en typ het volgende:
    ```bash
    spx recognize --microphone
    ```

    > [!NOTE]
    > Het hulp programma SPX wordt standaard ingesteld op Engels. U kunt een andere taal kiezen [in de tabel met spraak-naar-tekst](../../../../language-support.md).
    > Voeg bijvoorbeeld toe `--source de-DE` om Duitse spraak te herkennen.

2. **Begin met herkenning** : praat in de microfoon. U ziet transcriptie van uw woorden in realtime. Het hulp programma SPX wordt gestopt na een periode van stilte of wanneer u op CTRL + C drukt.
