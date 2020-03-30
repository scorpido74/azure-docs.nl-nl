---
title: 'Scoremodel: modulereferentie'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module Scoremodel in Azure Machine Learning om voorspellingen te genereren met behulp van een getraind classificatie- of regressiemodel.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 56d8cad05a42da8de680ade487dddee9a97aab3a
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80364177"
---
# <a name="score-model-module"></a>De module Score Model (Scoremodel)

In dit artikel wordt een module beschreven in Azure Machine Learning designer (preview).

Gebruik deze module om voorspellingen te genereren met behulp van een getraind classificatie- of regressiemodel.

## <a name="how-to-use"></a>Gebruiksinstructies

1. Voeg de module **Scoremodel** toe aan uw pijplijn.

2. Voeg een getraind model en een gegevensset met nieuwe invoergegevens toe. 

    De gegevens moeten in een formaat zijn dat compatibel is met het type getraind model dat u gebruikt. Het schema van de invoergegevensset moet over het algemeen ook overeenkomen met het schema van de gegevens die worden gebruikt om het model te trainen.

3. Verzend de pijplijn.

## <a name="results"></a>Resultaten

Nadat u een reeks scores hebt gegenereerd met [scoremodel:](./score-model.md)

+ Als u een set statistieken wilt genereren die worden gebruikt voor het evalueren van de nauwkeurigheid (prestaties) van het model, u de gescoorde gegevensset verbinden met [Model evalueren,](./evaluate-model.md) 
+ Klik met de rechtermuisknop op de module en selecteer **Visualiseren** om een voorbeeld van de resultaten te bekijken.
<!-- + To Save the results to a dataset. -->

De score, of voorspelde waarde, kan in veel verschillende formaten, afhankelijk van het model en uw invoergegevens:

- Voor classificatiemodellen [levert Score Model](./score-model.md) een voorspelde waarde voor de klasse en de waarschijnlijkheid van de voorspelde waarde.
- Voor regressiemodellen genereert [Score Model](./score-model.md) alleen de voorspelde numerieke waarde.


## <a name="publish-scores-as-a-web-service"></a>Scores publiceren als een webservice

Een veelvoorkomend gebruik van scoren is om de uitvoer terug te keren als onderdeel van een voorspellende webservice. Zie [deze zelfstudie](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy) over het implementeren van een realtime eindpunt op basis van een pijplijn in Azure Machine Learning designer voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set modules die beschikbaar zijn](module-reference.md) voor Azure Machine Learning. 