---
title: 'Snelstartgids: spraak, intenties en entiteiten herkennen-spraak service'
titleSuffix: Azure Cognitive Services
description: Nader te bepalen
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/28/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: a87ed9355a5939393fd5e20f395cc96f35e7f150
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/04/2019
ms.locfileid: "74816083"
---
In deze Quick Start gebruikt u de [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) voor het interactief herkennen van spraak van audio gegevens die zijn vastgelegd van een microfoon. Nadat u aan enkele vereisten hebt voldaan, heeft het herkennen van spraak van een microfoon slechts vier stappen:
> [!div class="checklist"]
>
> * Een ````SpeechConfig````-object maken op basis van uw abonnements sleutel en-regio.
> * Maak een ````IntentRecognizer````-object met behulp van het ````SpeechConfig````-object.
> * Gebruik het ````IntentRecognizer````-object om het herkennings proces voor één utterance te starten.
> * Controleer de geretourneerde ````IntentRecognitionResult````.
