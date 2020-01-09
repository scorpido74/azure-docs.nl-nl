---
title: 'Snelstartgids: spraak, intenties en entiteiten herkennen-spraak service'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 1/02/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: a833d39ab91cd803f066d707306a6ff648d37e8f
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/04/2020
ms.locfileid: "75660476"
---
In deze Snelstartgids gebruikt u de [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) en de Language Understanding-service (Luis) om de intenties van audio gegevens die zijn vastgelegd van een microfoon te herkennen. Met name maakt u gebruik van de Speech SDK voor het vastleggen van spraak en een vooraf gebouwd domein van LUIS om intenties voor Start Automation te identificeren, zoals het in-en uitschakelen van een licht. 

Na het voldoen aan enkele vereisten is het herkennen van spraak en het identificeren van intenties van een microfoon slechts een paar stappen:

> [!div class="checklist"]
>
> * Een ````SpeechConfig````-object maken op basis van uw abonnements sleutel en-regio.
> * Maak een ````IntentRecognizer````-object met behulp van het ````SpeechConfig````-object.
> * Gebruik het ````IntentRecognizer````-object om het herkennings proces voor één utterance te starten.
> * Controleer de geretourneerde ````IntentRecognitionResult````.
