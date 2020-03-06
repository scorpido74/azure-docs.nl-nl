---
title: App-LUIS trainen
titleSuffix: Azure Cognitive Services
description: Training is het proces van uw app-versie van de Language Understanding (LUIS) voor het verbeteren van de natuurlijke taal begrijpen lessen. Uw LUIS-app na updates voor het model zoals toevoegen, bewerken, labels of verwijderen van entiteiten, intents of uitingen trainen.
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
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78361100"
---
# <a name="train-your-active-version-of-the-luis-app"></a>Train uw actieve versie van de LUIS-app 

Training is het proces van uw app Language Understanding (LUIS) voor het verbeteren van de natuurlijke taal begrijpen lessen. Uw LUIS-app na updates voor het model zoals toevoegen, bewerken, labels of verwijderen van entiteiten, intents of uitingen trainen. 

Training en [testen](luis-concept-test.md) van een app is een iteratief proces. Nadat u uw LUIS-app trainen, kunt u deze testen met voorbeeldgegevens uitingen om te controleren of de intenties en entiteiten correct worden herkend. Als ze niet zijn, moet u opnieuw updates voor de LUIS-app, trainen en testen. 

Training wordt toegepast op de actieve versie in de LUIS-portal. 

## <a name="how-to-train-interactively"></a>Hoe u met het trainen van interactief

Als u het iteratieve proces in de [Luis-Portal](https://www.luis.ai)wilt starten, moet u eerst uw Luis-app ten minste één keer trainen. Zorg ervoor dat elke bedoeling heeft ten minste één utterance voordat een training.

1. U hebt toegang tot uw app door de naam ervan te selecteren op de pagina **mijn apps** . 

1. In uw app selecteert u **trainen** in het bovenste paneel. 

1. Wanneer de training is voltooid, wordt boven aan de browser een melding weer gegeven.

## <a name="training-date-and-time"></a>Datum en tijd van training

De trainings datum en-tijd zijn GMT + 2. 

## <a name="train-with-all-data"></a>Met alle gegevens van de trein

Training maakt gebruik van een klein percentage van het negatieve samplen bijhouden. Als u alle gegevens wilt gebruiken in plaats van de kleine negatieve steek proef, gebruikt u de [API](#version-settings-api-use-of-usealltrainingdata).

### <a name="version-settings-api-use-of-usealltrainingdata"></a>API-gebruik van versie-instellingen van UseAllTrainingData

Gebruik de [API Version Settings](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) met de `UseAllTrainingData` ingesteld op True om deze functie uit te scha kelen. 

## <a name="unnecessary-training"></a>Onnodige training

U hoeft niet te trainen na elke één wijziging. Training moet worden uitgevoerd nadat een groep van de wijzigingen worden toegepast op het model en de volgende stap die u wilt doen, is te testen of te publiceren. Als u niet hoeft te testen of te publiceren, training niet nodig. 

## <a name="training-with-the-rest-apis"></a>Training met de REST API 's

Training in de LUIS-Portal is één stap van het drukken op de **trein** knop. Training met de REST API's is een proces in twee stappen. Eerst moet u [training aanvragen](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) met http post. Vraag vervolgens de [trainings status](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46) aan met HTTP Get. 

Als u wilt weten wanneer de training is voltooid, moet u de status te peilen totdat alle modellen met succes zijn getraind. 

## <a name="next-steps"></a>Volgende stappen

* [Interactieve tests](luis-interactive-test.md)
* [Batch testen](luis-how-to-batch-test.md)
