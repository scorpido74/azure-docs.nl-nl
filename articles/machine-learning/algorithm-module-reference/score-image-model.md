---
title: Afbeeldings model voor Score
titleSuffix: Azure Machine Learning
description: Meer informatie over hoe u de module Score image model in Azure Machine Learning kunt gebruiken om voor spellingen te genereren met behulp van een getraind afbeeldings model.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: 021572aef673cf88f7744a0a210ef794c739448b
ms.sourcegitcommit: 0a5bb9622ee6a20d96db07cc6dd45d8e23d5554a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/05/2020
ms.locfileid: "84450715"
---
# <a name="score-image-model"></a>Afbeeldings model voor Score

In dit artikel wordt een module in Azure Machine Learning Designer (preview) beschreven.

Gebruik deze module om voor spellingen te genereren met behulp van een getraind installatie kopie model op gegevens van de invoer afbeelding.

## <a name="how-to-configure-score-image-model"></a>Score image model configureren

1. Voeg de module **Score image model** toe aan de pijp lijn.

2. Koppel een getraind afbeeldings model en een gegevensset met gegevens over de invoer afbeelding. 

    De gegevens moeten van het type ImageDirectory zijn. Raadpleeg [Convert to image directory](convert-to-image-directory.md) module voor meer informatie over het ophalen van een map met installatie kopieën. Het schema van de invoer gegevensset moet ook algemeen overeenkomen met het schema van de gegevens die worden gebruikt om het model te trainen.

3. Verzend de pijp lijn.

## <a name="results"></a>Resultaten

Nadat u een set scores hebt gegenereerd met een [Score afbeeldings model](score-image-model.md), kunt u deze module en de gescoorde gegevensset verbinden om het [model te evalueren](evaluate-model.md), om een set metrische gegevens te genereren die worden gebruikt voor het evalueren van de nauw keurigheid van het model (prestaties). 

### <a name="publish-scores-as-a-web-service"></a>Scores publiceren als een webservice

Een gemeen schappelijk gebruik van scores is het retour neren van de uitvoer als onderdeel van een voorspellende webservice. Zie [deze zelf studie](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy) voor meer informatie over het implementeren van een real-time-eind punt op basis van een pijp lijn in azure machine learning Designer.

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning. 
