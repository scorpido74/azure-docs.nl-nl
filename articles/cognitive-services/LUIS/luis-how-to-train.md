---
title: App-LUIS trainen
titleSuffix: Azure Cognitive Services
description: Training is het proces van het aanwijzen van uw Language Understanding-App-versie (LUIS) om de duidelijkheid van natuurlijke taal te verbeteren. Train uw LUIS-app na het bijwerken van het model, zoals het toevoegen, bewerken, labelen of verwijderen van entiteiten, intenties of uitingen.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 11/15/2019
ms.author: diberry
ms.openlocfilehash: 7511d7379e7b51b19e3436ed7cef53fb914b80ac
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/03/2020
ms.locfileid: "84343068"
---
# <a name="train-your-active-version-of-the-luis-app"></a>Train uw actieve versie van de LUIS-app

Training is het proces van het aanwijzen van uw Language Understanding-app (LUIS) om de uitleg van natuurlijke taal te verbeteren. Train uw LUIS-app na het bijwerken van het model, zoals het toevoegen, bewerken, labelen of verwijderen van entiteiten, intenties of uitingen.

Training en [testen](luis-concept-test.md) van een app is een iteratief proces. Nadat u uw LUIS-app hebt getraind, test u deze met voor beeld-uitingen om te zien of de intenties en entiteiten goed zijn herkend. Als dat niet het geval is, moet u updates voor de LUIS-app, Train en test opnieuw uitvoeren.

Training wordt toegepast op de actieve versie in de LUIS-Portal.

## <a name="how-to-train-interactively"></a>Interactief trainen

Als u het iteratieve proces in de [Luis-Portal](https://www.luis.ai)wilt starten, moet u eerst uw Luis-app ten minste één keer trainen. Zorg ervoor dat elke intentie ten minste één utterance heeft vóór de training.

1. U hebt toegang tot uw app door de naam ervan te selecteren op de pagina **mijn apps** .

1. In uw app selecteert u **trainen** in het bovenste paneel.

1. Wanneer de training is voltooid, wordt boven aan de browser een melding weer gegeven.

## <a name="training-date-and-time"></a>Datum en tijd van training

De trainings datum en-tijd zijn GMT + 2.

## <a name="train-with-all-data"></a>Train met alle gegevens

Training maakt gebruik van een klein percentage van negatieve steek proeven. Als u alle gegevens wilt gebruiken in plaats van de kleine negatieve steek proef, gebruikt u de [API](#version-settings-api-use-of-usealltrainingdata).

### <a name="version-settings-api-use-of-usealltrainingdata"></a>API-gebruik van versie-instellingen van UseAllTrainingData

Gebruik de [API versie-instellingen](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) met de `UseAllTrainingData` waarde True om deze functie uit te scha kelen.

## <a name="unnecessary-training"></a>Onnodige training

U hoeft niet te trainen na elke wijziging. De training moet worden uitgevoerd nadat een groep wijzigingen op het model is toegepast en de volgende stap die u wilt uitvoeren, is om te testen of te publiceren. Als u niet hoeft te testen of publiceren, is training niet nodig.

## <a name="training-with-the-rest-apis"></a>Training met de REST-Api's

Training in de LUIS-Portal is één stap van het drukken op de **trein** knop. Training met de REST-Api's is een proces dat uit twee stappen bestaat. Eerst moet u [training aanvragen](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) met http post. Vraag vervolgens de [trainings status](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46) aan met HTTP Get.

Als u wilt weten wanneer de training is voltooid, moet u de status navragen totdat alle modellen zijn getraind.

## <a name="next-steps"></a>Volgende stappen

* [Interactief testen](luis-interactive-test.md)
* [Batchgewijs testen](luis-how-to-batch-test.md)
