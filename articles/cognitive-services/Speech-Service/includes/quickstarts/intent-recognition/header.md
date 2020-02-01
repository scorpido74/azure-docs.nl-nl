---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/27/2020
ms.author: dapine
ms.openlocfilehash: d59a55a9b6d57d90ac6ae893b05e1064c11eb58b
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76900551"
---
In deze Snelstartgids gebruikt u de [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) en de Language Understanding-service (Luis) om de intenties van audio gegevens die zijn vastgelegd van een microfoon te herkennen. Met name maakt u gebruik van de Speech SDK voor het vastleggen van spraak en een vooraf gebouwd domein van LUIS om intenties voor Start Automation te identificeren, zoals het in-en uitschakelen van een licht. 

Na het voldoen aan enkele vereisten is het herkennen van spraak en het identificeren van intenties van een microfoon slechts een paar stappen:

> [!div class="checklist"]
>
> * Een `SpeechConfig`-object maken op basis van uw abonnements sleutel en-regio.
> * Maak een `IntentRecognizer`-object met behulp van het `SpeechConfig`-object.
> * Gebruik het `IntentRecognizer`-object om het herkennings proces voor één utterance te starten.
> * Controleer de geretourneerde `IntentRecognitionResult`.
