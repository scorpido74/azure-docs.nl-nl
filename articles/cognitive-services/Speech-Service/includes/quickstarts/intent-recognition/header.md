---
title: 'Snelstartgids: spraak, intenties en entiteiten herkennen-spraak service'
titleSuffix: Azure Cognitive Services
description: NOG TE BEPALEN
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/28/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: df4604560e05899461b11ec0788f72b27241f1b9
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74125458"
---
In deze Quick Start gebruikt u de [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) voor het interactief herkennen van spraak van audio gegevens die zijn vastgelegd van een microfoon. Nadat u aan enkele vereisten hebt voldaan, heeft het herkennen van spraak van een microfoon slechts vier stappen:
> [!div class="checklist"]
>
> * Een ````SpeechConfig````-object maken op basis van uw abonnements sleutel en-regio.
> * Maak een ````IntentRecognizer````-object met behulp van het ````SpeechConfig````-object.
> * Gebruik het ````IntentRecognizer````-object om het herkennings proces voor één utterance te starten.
> * Controleer de geretourneerde ````IntentRecognitionResult````.
