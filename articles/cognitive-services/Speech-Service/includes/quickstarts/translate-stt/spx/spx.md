---
author: v-demjoh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 05/18/2020
ms.author: v-demjoh
ms.openlocfilehash: 57383668ef025b46d0bae1f98c5ed5cd75417c63
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83715008"
---
## <a name="run-the-spx-tool"></a>Het hulp programma SPX uitvoeren

U kunt nu het hulp programma SPX uitvoeren om spraak te vertalen naar tekst in een andere taal.

Ga vanaf de opdracht regel naar de map die het binaire bestand van het hulp programma SPX bevat en typ het volgende:

```bash
spx translate --microphone --target de-DE
```

Het hulp programma SPX zorgt voor de vertaling van gesp roken tekst in natuurlijke taal in het Duits.
Druk op ENTER om het hulp programma te stoppen.

> [!NOTE]
> Het hulp programma SPX wordt standaard ingesteld op Engels. U kunt een andere taal kiezen [in de tabel met spraak-naar-tekst](../../../../language-support.md).
> Voeg bijvoorbeeld toe `--source ja-JP` om Japanse spraak herkenning te herkennen.
