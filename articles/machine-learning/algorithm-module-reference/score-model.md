---
title: 'Score model: module verwijzing'
titleSuffix: Azure Machine Learning service
description: Meer informatie over het gebruik van de module score model in Azure Machine Learning service om voor spellingen te genereren met behulp van een getrainde classificatie of een regressie model.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: f046b41d02ac392d003a9ecd15f25b43e5449cd0
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/21/2019
ms.locfileid: "72692678"
---
# <a name="score-model-module"></a>De module Score Model (Scoremodel)

In dit artikel wordt een module van de Visual Interface (preview) voor de Azure Machine Learning-service beschreven.

Gebruik deze module om voor spellingen te genereren met behulp van een getrainde classificatie of een regressie model.

## <a name="how-to-use"></a>Gebruiksinstructies

1. Voeg de module **score model** toe aan de pijp lijn.

2. Koppel een getraind model en een gegevensset die nieuwe invoer gegevens bevat. 

    De gegevens moeten een indeling hebben die compatibel is met het type getrainde model dat u gebruikt. Het schema van de invoer gegevensset moet ook algemeen overeenkomen met het schema van de gegevens die worden gebruikt om het model te trainen.

3. Voer de pijplijn uit.

## <a name="results"></a>Resultaten

Nadat u een set scores hebt gegenereerd met het [score model](./score-model.md):

+ Genereren van een set metrische gegevens die worden gebruikt voor het evalueren van de nauw keurigheid van het model (prestaties).  u kunt de gescoorde gegevensset verbinden om het [model te evalueren](./evaluate-model.md). 
+ Klik met de rechter muisknop op de module en selecteer **visualiseren** om een voor beeld van de resultaten weer te geven.
+ Sla de resultaten op in een gegevensset.

De Score of voorspelde waarde kan in veel verschillende indelingen worden opgegeven, afhankelijk van het model en uw invoer gegevens:

- Voor classificatie modellen voert het [score model](./score-model.md) een voorspelde waarde uit voor de klasse, evenals de waarschijnlijkheid van de voorspelde waarde.
- Voor regressie modellen genereert het [score model](./score-model.md) alleen de voorspelde numerieke waarde.
- Voor afbeeldings classificatie modellen kan de score de klasse van het object in de afbeelding zijn of een Booleaanse waarde die aangeeft of een bepaalde functie is gevonden.

## <a name="publish-scores-as-a-web-service"></a>Scores publiceren als een webservice

Een gemeen schappelijk gebruik van scores is het retour neren van de uitvoer als onderdeel van een voorspellende webservice. Zie voor meer informatie deze zelf studie over het maken van een webservice op basis van een pijp lijn in Azure Machine Learning:


## <a name="next-steps"></a>Volgende stappen

Bekijk de [set beschik bare modules](module-reference.md) voor Azure machine learning service. 