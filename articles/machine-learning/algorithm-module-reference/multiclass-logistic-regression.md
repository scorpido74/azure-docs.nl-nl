---
title: 'Geocategorie logistiek regressie: Module verwijzing'
titleSuffix: Azure Machine Learning service
description: Meer informatie over het gebruik van de module logistiek-regressie voor meerdere klassen in Azure Machine Learning service voor het maken van een logistiek regressie model dat kan worden gebruikt voor het voors pellen van verschillende waarden.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: d51bc48944204b4c7c50790949927849869f26fc
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128639"
---
# <a name="multiclass-logistic-regression-module"></a>Module logistiek-regressie voor multi klasse

In dit artikel wordt een module van de Visual Interface (preview) voor de Azure Machine Learning-service beschreven.

Met deze module kunt u een logistiek regressie model maken dat kan worden gebruikt om meerdere waarden te voors pellen.

Classificatie met behulp van logistiek regressie is een geclassificeerde leer methode en vereist daarom een gegevensset met een label. U traint het model door het model en de gelabelde gegevensset op te geven als invoer voor een module, zoals [Train model](./train-model.md). Het getrainde model kan vervolgens worden gebruikt om waarden voor nieuwe invoer voorbeelden te voors pellen.

Azure Machine Learning biedt ook een [logistiek regressie module van twee klassen](./two-class-logistic-regression.md) , die geschikt is voor de classificatie van binaire of dichotomous variabelen.

## <a name="about-multiclass-logistic-regression"></a>Over een logistiek regressie voor multi klasse

Logistiek regressie is een bekende methode in statistieken die wordt gebruikt om de kans op een resultaat te voors pellen en populair is voor classificatie taken. Het algoritme voor spelt de kans op een gebeurtenis door gegevens aan een logistiek functie toe te passen. 

In een logistiek-regressie met meerdere klassen kan de classificatie worden gebruikt voor het voors pellen van meervoudige resultaten.

## <a name="configure-a-multiclass-logistic-regression"></a>Een logistiek-regressie met meer klasse configureren

1. Voeg de module **logistiek-regressie** voor multi klasse toe aan het experiment.

2. Geef op hoe u wilt dat het model wordt getraind door de optie **trainer modus maken** in te stellen.

    + **Eén para meter**: Gebruik deze optie als u weet hoe u het model wilt configureren en een specifieke set waarden als argumenten kunt opgeven.

    + **Parameter bereik**: Gebruik deze optie als u niet zeker weet wat de beste para meters zijn en u een parameter sweep wilt gebruiken.

3. **Optimalisatie tolerantie**, geeft u de drempel waarde voor optimalisatie convergentie op. Als de verbetering tussen herhalingen kleiner is dan de drempel waarde, wordt het algoritme gestopt en wordt het huidige model geretourneerd.

4. **L1 regularisatie Weight**, **L2-regularisatie gewicht**: Typ een waarde die moet worden gebruikt voor de regularisatie-para meters L1 en L2. Een andere waarde dan nul wordt aanbevolen voor beide.

    Regularisatie is een methode voor het voor komen van overmontage door het bestraffen van modellen met extreme coëfficiënt waarden. Regularisatie werkt door de sanctie toe te voegen die is gekoppeld aan coëfficiënt waarden voor de fout van de hypo these. Een nauw keurig model met extreme coëfficiënt waarden wordt meer bestraft, maar een minder nauw keurig model met meer conservatieve waarden zou minder worden bestraft.

     L1 en L2-regularisatie hebben verschillende effecten en worden gebruikt. L1 kan worden toegepast op sparse modellen, wat handig is bij het werken met zeer dimensionale gegevens. In tegens telling tot L2-regularisatie is de voor keur voor gegevens die niet sparse zijn.  Dit algoritme ondersteunt een lineaire combi natie van L1-en L2-regularisatie-waarden: `x = L1` dat `y = L2`wil `ax + by = c` zeggen, als en, de lineaire reeks van de regularisatie-voor waarden definieert.

     Er zijn verschillende lineaire combi Naties van L1-en L2-termen ontwikkeld voor logistieke regressie modellen, zoals [elastische net regularisatie](https://wikipedia.org/wiki/Elastic_net_regularization).

6. **Wille keurig aantal Seed**: Typ een geheel getal dat moet worden gebruikt als seed voor het algoritme als u wilt dat de resultaten herhaaldelijk worden uitgevoerd. Anders wordt een waarde van de systeem klok gebruikt als seed, waardoor er iets andere resultaten kunnen worden geproduceerd in uitvoeringen van hetzelfde experiment.

8. Verbind een gegevensset met een label en een van de trein modules:

    + Als u de **modus trainer maken** instelt op **één para meter**, gebruikt u de module [Train model](./train-model.md) .

9. Voer het experiment uit.

## <a name="results"></a>Resultaten

Nadat de training is voltooid, ziet u een samen vatting van de para meters van het model, samen met de functie gewichten die zijn geleerd van training, klikt u met de rechter muisknopop de uitvoer van de module [Train model](./train-model.md) en selecteert u visualiseren.


## <a name="next-steps"></a>Volgende stappen

Bekijk de [set beschik bare modules](module-reference.md) voor Azure machine learning service. 