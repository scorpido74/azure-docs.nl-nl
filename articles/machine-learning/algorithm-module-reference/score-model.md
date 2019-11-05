---
title: 'Score model: module verwijzing'
titleSuffix: Azure Machine Learning
description: Leer hoe u de module score model in Azure Machine Learning kunt gebruiken om voor spellingen te genereren met behulp van een getrainde classificatie of een regressie model.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: e7ab953a7ac0907244ebaab70b3b86cbe6f0f4d9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73497640"
---
# <a name="score-model-module"></a>De module Score Model (Scoremodel)

In dit artikel wordt een module in Azure Machine Learning Designer (preview) beschreven.

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

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning. 