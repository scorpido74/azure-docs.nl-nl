---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/27/2020
ms.author: trbye
ms.openlocfilehash: 8d70d282ffb1a39f9ffb3eb6addf694ac9d0d060
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81422366"
---
In deze Snelstartgids gebruikt u de [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) en de Language Understanding-service (Luis) om de intenties van audio gegevens die zijn vastgelegd van een microfoon te herkennen. Met name maakt u gebruik van de Speech SDK voor het vastleggen van spraak en een vooraf gebouwd domein van LUIS om intenties voor Start Automation te identificeren, zoals het in-en uitschakelen van een licht. 

Na het voldoen aan enkele vereisten is het herkennen van spraak en het identificeren van intenties van een microfoon slechts een paar stappen:

> [!div class="checklist"]
>
> * Maak een `SpeechConfig` object op basis van uw abonnements sleutel en-regio.
> * Maak een `IntentRecognizer` object met behulp van het `SpeechConfig` bovenstaande object.
> * Gebruik het `IntentRecognizer` -object om het herkennings proces voor één utterance te starten.
> * Inspecteer `IntentRecognitionResult` de geretourneerde.
