---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/27/2020
ms.author: trbye
ms.openlocfilehash: 8d70d282ffb1a39f9ffb3eb6addf694ac9d0d060
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2020
ms.locfileid: "81422366"
---
In deze quickstart gebruikt u de [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) en de Language Understanding-service (LUIS) om intenties te herkennen van audiogegevens die zijn vastgelegd vanuit een microfoon. In het bijzonder gebruikt u de Speech SDK om spraak vast te leggen en een vooraf ingebouwd domein van LUIS om intenties voor huisautomatisering te identificeren, zoals het in- en uitschakelen van een lampje. 

Na het voldoen aan verschillende voorwaarden, zijn er slechts enkele stappen nodig om de spraak en de intenties van de microfoon te identificeren:

> [!div class="checklist"]
>
> * Maak een `SpeechConfig`-object op basis van uw abonnementssleutel en -regio.
> * Maak een `IntentRecognizer`-object met het bovenstaande `SpeechConfig`-object.
> * Gebruik het `IntentRecognizer`-object om het herkenningsproces voor één uiting te starten.
> * Inspecteer de geretourneerde `IntentRecognitionResult`.
