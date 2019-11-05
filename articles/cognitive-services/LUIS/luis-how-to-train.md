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
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 259ea23c05f0c0a138ad54b6efd11aad2061cf7a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73500220"
---
# <a name="train-your-active-version-of-the-luis-app"></a>Train uw actieve versie van de LUIS-app 

Training is het proces van het aanwijzen van uw Language Understanding-app (LUIS) om de uitleg van natuurlijke taal te verbeteren. Train uw LUIS-app na het bijwerken van het model, zoals het toevoegen, bewerken, labelen of verwijderen van entiteiten, intenties of uitingen. 

Training en [testen](luis-concept-test.md) van een app is een iteratief proces. Nadat u uw LUIS-app hebt getraind, test u deze met voor beeld-uitingen om te zien of de intenties en entiteiten goed zijn herkend. Als dat niet het geval is, moet u updates voor de LUIS-app, Train en test opnieuw uitvoeren. 

Training wordt toegepast op de actieve versie in de LUIS-Portal. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="how-to-train-interactively"></a>Interactief trainen

Als u het iteratieve proces in de [Luis-Portal](https://www.luis.ai)wilt starten, moet u eerst uw Luis-app ten minste één keer trainen. Zorg ervoor dat elke intentie ten minste één utterance heeft vóór de training.

1. U hebt toegang tot uw app door de naam ervan te selecteren op de pagina **mijn apps** . 

2. In uw app selecteert u **trainen** in het bovenste paneel. 

3. Wanneer de training is voltooid, wordt boven aan de browser een groene meldings balk weer gegeven.

<!-- The following note refers to what might cause the error message "Training failed: FewLabels for model: <ModelName>" -->

>[!NOTE]
>Als uw app een of meer intenties bevat die geen voorbeeld uitingen bevatten, kunt u uw app niet trainen. Voeg uitingen toe voor al uw intenties. Zie voor meer informatie [toevoegen voor beeld uitingen](luis-how-to-add-example-utterances.md).

## <a name="training-date-and-time"></a>Datum en tijd van training

De trainings datum en-tijd zijn GMT + 2. 

## <a name="train-with-all-data"></a>Train met alle gegevens

Training maakt gebruik van een klein percentage van negatieve steek proeven. 

Als u alle gegevens wilt gebruiken in plaats van de kleine negatieve steek proef, gebruikt u de [API](#version-settings-api-use-of-usealltrainingdata).

<!--

 or the [LUIS portal setting](#luis-portal-setting-to-use-all-training-data)

### LUIS portal setting to use all training data

!!!IGNITE


-->

### <a name="version-settings-api-use-of-usealltrainingdata"></a>API-gebruik van versie-instellingen van UseAllTrainingData

Gebruik de [API Version Settings](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) met de `UseAllTrainingData` ingesteld op True om deze functie uit te scha kelen. 

## <a name="unnecessary-training"></a>Onnodige training

U hoeft niet te trainen na elke wijziging. De training moet worden uitgevoerd nadat een groep wijzigingen op het model is toegepast en de volgende stap die u wilt uitvoeren, is om te testen of te publiceren. Als u niet hoeft te testen of publiceren, is training niet nodig. 

## <a name="training-with-the-rest-apis"></a>Training met de REST-Api's

Training in de LUIS-Portal is één stap van het drukken op de **trein** knop. Training met de REST-Api's is een proces dat uit twee stappen bestaat. Eerst moet u [training aanvragen](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) met http post. Vraag vervolgens de [trainings status](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46) aan met HTTP Get. 

Als u wilt weten wanneer de training is voltooid, moet u de status navragen totdat alle modellen zijn getraind. 

## <a name="next-steps"></a>Volgende stappen

* [Aanbevolen uitingen label met LUIS](luis-how-to-review-endpoint-utterances.md) 
* [Functies gebruiken om de prestaties van uw LUIS-app te verbeteren](luis-how-to-add-features.md) 
