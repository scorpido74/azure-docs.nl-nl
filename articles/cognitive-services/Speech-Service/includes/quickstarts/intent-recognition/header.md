---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/27/2020
ms.author: dapine
ms.openlocfilehash: d59a55a9b6d57d90ac6ae893b05e1064c11eb58b
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76900551"
---
In deze quickstart gebruikt u de [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) en de Luis-service (Language Understanding) om intenties te herkennen uit audiogegevens die zijn vastgelegd vanuit een microfoon. U gebruikt de Spraak-SDK om spraak vast te leggen en een vooraf gebouwd domein van LUIS om intenties voor domotica te identificeren, zoals het in- en uitschakelen van een lampje. 

Na het voldoen aan een paar voorwaarden, het herkennen van spraak en het identificeren van intenties van een microfoon duurt slechts een paar stappen:

> [!div class="checklist"]
>
> * Maak `SpeechConfig` een object op basis van uw abonnementssleutel en regio.
> * Maak `IntentRecognizer` een object `SpeechConfig` met het object van bovenaf.
> * Start `IntentRecognizer` het herkenningsproces voor één utterance met het object.
> * Inspecteer de `IntentRecognitionResult` teruggekeerden.
