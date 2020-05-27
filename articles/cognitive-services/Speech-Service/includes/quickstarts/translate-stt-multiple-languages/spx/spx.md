---
author: v-demjoh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 05/18/2020
ms.author: v-demjoh
ms.openlocfilehash: c73ee93d89a350763e8ced490187e1c47f8918de
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/22/2020
ms.locfileid: "83806557"
---
## <a name="run-the-speech-cli"></a>De speech CLI uitvoeren

U bent nu klaar om de speech CLI uit te voeren om spraak naar tekst in twee verschillende talen om te zetten.

Ga vanaf de opdracht regel naar de map die het binaire bestand speech CLI bevat en typ het volgende:

```bash
spx translate --microphone --target de-DE --target es-MX
```

Met de spraak-CLI wordt het gesp roken van de natuurlijke taal omgezet in tekst die in het Duits en Spaans (Mexico) is gedrukt.
Druk op ENTER om het hulp programma te stoppen.

> [!NOTE]
> De spraak-CLI wordt standaard ingesteld op Engels. U kunt een andere taal kiezen [in de tabel met spraak-naar-tekst](../../../../language-support.md).
> Voeg bijvoorbeeld toe `--source ja-JP` om Japanse spraak herkenning te herkennen.
