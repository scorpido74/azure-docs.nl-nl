---
title: 'Snelstartgids: aangepaste opdrachten gebruiken met aangepaste spraak (preview)-spraak service'
titleSuffix: Azure Cognitive Services
description: In dit artikel geeft u de uitvoer stem van een toepassing met aangepaste opdrachten op.
services: cognitive-services
author: anhoang
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: anhoang
ms.openlocfilehash: c2b9b4d51e89975d988ed94bf85695bd8a1cc770
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/06/2020
ms.locfileid: "82872466"
---
# <a name="quickstart-use-custom-commands-with-custom-voice-preview"></a>Snelstartgids: aangepaste opdrachten gebruiken met aangepaste spraak (preview-versie)

In het [vorige artikel](./quickstart-custom-speech-commands-create-parameters.md)hebben we een nieuw project met aangepaste opdrachten gemaakt om te reageren op opdrachten met para meters.

In dit artikel selecteren we een aangepaste uitvoer stem voor de toepassing die we hebben gemaakt.

## <a name="select-a-custom-voice"></a>Een aangepaste stem selecteren

1. Open het project dat [we eerder hebben gemaakt](./quickstart-custom-speech-commands-create-parameters.md)
1. **Instellingen** selecteren in het linkerdeel venster
1. **Aangepaste spraak** selecteren in het middelste deel venster
1. Selecteer de gewenste aangepaste of open bare stem in de tabel
1. Selecteer **Opslaan**

> [!div class="mx-imgBorder"]
> ![Voorbeeld zinnen met para meters](media/custom-speech-commands/select-custom-voice.png)

> [!NOTE]
> - Voor **open bare stemmen**zijn **Neural typen** alleen beschikbaar voor bepaalde regio's. Zie [standaard en Neural stemmen per regio/eind punt](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices)om de beschik baarheid te controleren.
> - Voor **aangepaste stemmen**kunnen ze worden gemaakt op de pagina aangepast spraak project. Zie [aan de slag met aangepaste spraak](./how-to-custom-voice.md).

De toepassing reageert nu op de geselecteerde stem, in plaats van de standaard stem.

## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [Quick Start: verbinding maken met een aangepaste opdracht toepassing met de spraak-SDK (preview)](./quickstart-custom-speech-commands-speech-sdk.md)

