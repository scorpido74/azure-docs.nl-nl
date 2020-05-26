---
author: v-demjoh
ms.service: cognitive-services
ms.topic: include
ms.date: 05/13/2020
ms.author: v-demjoh
ms.openlocfilehash: a9beaea70dd3c8ce852344b11a50c5f20e5f8ca2
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/22/2020
ms.locfileid: "83806357"
---
## <a name="enable-microphone"></a>Microfoon inschakelen

Sluit uw PC-microfoon aan en schakel deze in en schakel alle apps uit die mogelijk ook gebruikmaken van de microfoon. Sommige computers hebben een ingebouwde microfoon, terwijl anderen een Bluetooth-apparaat moeten configureren.

## <a name="run-the-speech-cli"></a>De speech CLI uitvoeren

U bent nu klaar om de speech CLI uit te voeren om spraak van uw microfoon te herkennen.

1. **Start uw app** -vanaf de opdracht regel, ga naar de map met het binaire bestand met spraak-CLI en typ het volgende:
    ```bash
    spx recognize --microphone
    ```

    > [!NOTE]
    > De spraak-CLI wordt standaard ingesteld op Engels. U kunt een andere taal kiezen [in de tabel met spraak-naar-tekst](../../../../language-support.md).
    > Voeg bijvoorbeeld toe `--source de-DE` om Duitse spraak te herkennen.

2. **Begin met herkenning** : praat in de microfoon. U ziet transcriptie van uw woorden in realtime. De speech CLI stopt na een periode van stilte of wanneer u op CTRL + C drukt.
