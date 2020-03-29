---
title: Trein app - LUIS
titleSuffix: Azure Cognitive Services
description: Training is het proces van het onderwijzen van uw Language Understanding (LUIS) app-versie om het natuurlijke taalbegrip te verbeteren. Train uw LUIS-app na updates van het model, zoals het toevoegen, bewerken, labelen of verwijderen van entiteiten, intenties of uitingen.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: diberry
ms.openlocfilehash: 1da8ab3015730c6b3e1962301a34b1ad43b1aad6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219861"
---
# <a name="train-your-active-version-of-the-luis-app"></a>Uw actieve versie van de LUIS-app trainen 

Training is het proces van het onderwijzen van uw Language Understanding (LUIS) app om het natuurlijke taalbegrip te verbeteren. Train uw LUIS-app na updates van het model, zoals het toevoegen, bewerken, labelen of verwijderen van entiteiten, intenties of uitingen. 

Het [trainen](luis-concept-test.md) en testen van een app is een iteratief proces. Nadat u uw LUIS-app hebt getraind, test u deze met voorbeelduitingen om te zien of de intenties en entiteiten correct worden herkend. Als dit niet het geval is, moet u de LUIS-app bijwerken, trainen en opnieuw testen. 

Training wordt toegepast op de actieve versie in het LUIS-portaal. 

## <a name="how-to-train-interactively"></a>Interactief trainen

Als u het iteratieve proces in de [LUIS-portal](https://www.luis.ai)wilt starten, moet u eerst uw LUIS-app minstens één keer trainen. Zorg ervoor dat elke intentie ten minste één uiting heeft voor de training.

1. Toegang tot uw app door de naam ervan te selecteren op de pagina **Mijn apps.** 

1. Selecteer in uw app **Trainen** in het bovenste deelvenster. 

1. Wanneer de training is voltooid, verschijnt er een melding boven aan de browser.

## <a name="training-date-and-time"></a>Trainingsdatum en -tijd

Trainingsdatum en -tijd zijn GMT + 2. 

## <a name="train-with-all-data"></a>Trainen met alle gegevens

Training maakt gebruik van een klein percentage van de negatieve bemonstering. Als u alle gegevens wilt gebruiken in plaats van de kleine negatieve bemonstering, gebruikt u de [API](#version-settings-api-use-of-usealltrainingdata).

### <a name="version-settings-api-use-of-usealltrainingdata"></a>Versie-instellingen API gebruik van UseAllTrainingData

Gebruik de API voor `UseAllTrainingData` [versie-instellingen](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) met de set true om deze functie uit te schakelen. 

## <a name="unnecessary-training"></a>Onnodige training

U hoeft niet te trainen na elke verandering. Training moet worden gedaan nadat een groep wijzigingen zijn toegepast op het model, en de volgende stap die u wilt doen is om te testen of te publiceren. Als u niet hoeft te testen of publiceren, is training niet nodig. 

## <a name="training-with-the-rest-apis"></a>Training met de REST API's

Training in het LUIS-portaal is een enkele stap om op de **treinknop te** drukken. Training met de REST API's is een proces in twee stappen. De eerste is om [training aan](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) te vragen bij HTTP POST. Vraag vervolgens de [trainingsstatus](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46) aan met HTTP Get. 

Om te weten wanneer de training is voltooid, moet je de status peilen totdat alle modellen met succes zijn opgeleid. 

## <a name="next-steps"></a>Volgende stappen

* [Interactief testen](luis-interactive-test.md)
* [Batchgewijs testen](luis-how-to-batch-test.md)
