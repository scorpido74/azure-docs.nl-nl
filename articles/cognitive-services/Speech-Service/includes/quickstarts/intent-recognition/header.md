---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/27/2020
ms.author: trbye
ms.openlocfilehash: 8d70d282ffb1a39f9ffb3eb6addf694ac9d0d060
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422366"
---
In deze quickstart gebruikt u de [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) en de Luis-service (Language Understanding) om intenties te herkennen uit audiogegevens die zijn vastgelegd vanuit een microfoon. U gebruikt de Spraak-SDK om spraak vast te leggen en een vooraf gebouwd domein van LUIS om intenties voor domotica te identificeren, zoals het in- en uitschakelen van een lampje. 

Na het voldoen aan een paar voorwaarden, het herkennen van spraak en het identificeren van intenties van een microfoon duurt slechts een paar stappen:

> [!div class="checklist"]
>
> * Maak `SpeechConfig` een object op basis van uw abonnementssleutel en regio.
> * Maak `IntentRecognizer` een object `SpeechConfig` met het object van bovenaf.
> * Start `IntentRecognizer` het herkenningsproces voor één utterance met het object.
> * Inspecteer de `IntentRecognitionResult` teruggekeerden.
