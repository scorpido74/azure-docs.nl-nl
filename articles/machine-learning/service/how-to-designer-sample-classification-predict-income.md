---
title: 'Ontwerp functie: classificeren, voor beeld van inkomen'
titleSuffix: Azure Machine Learning
description: In dit voor beeld wordt een classificatie zonder code gemaakt om de inkomsten met Azure Machine Learning Designer te voors pellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: peterlu
ms.date: 11/04/2019
ms.openlocfilehash: 527db89be85cc5b095d33ba89c776a077119f08a
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196056"
---
# <a name="build-a-classifier--use-feature-selection-to-predict-income-with-azure-machine-learning-designer"></a>Bouw een classificatie & gebruik functie selectie om inkomsten te voors pellen met Azure Machine Learning Designer

**Voor beeld van Designer (preview) 3**

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku.md)]

Meer informatie over het bouwen van een machine learning classificatie zonder het schrijven van één regel code met behulp van de ontwerp functie (preview). In dit voor beeld wordt een **geboostte beslissings structuur met twee klassen** getraind om inkomsten te voors pellen (> = 50.000 of < = 50.000).

Omdat de vraag ' die ene? ' beantwoordt Dit wordt een probleem met de classificatie genoemd. U kunt echter hetzelfde fundamentele proces Toep assen om elk type machine learning probleem aan te pakken, of het nu gaat om een regressie, classificatie, Clustering, enzovoort.

Hier volgt de laatste pijplijn grafiek voor dit voor beeld:

![Grafiek van de pijp lijn](media/how-to-ui-sample-classification-predict-income/overall-graph.png)

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Klik op het voor beeld 3 om het te openen.



## <a name="data"></a>Gegevens

De gegevensset bevat 14 functies en één label kolom. Er zijn meerdere typen functies, waaronder numerieke en categorische. In het volgende diagram ziet u een fragment uit de gegevensset: ![gegevens](media/how-to-ui-sample-classification-predict-income/data.png)



## <a name="pipeline-summary"></a>Pijplijn overzicht

Volg deze stappen om de pijp lijn te maken:

1. Sleep de module inkomsten van de binary-gegevensset voor volwassenen naar het pijp lijn-canvas.
1. Voeg een **Splits gegevens** module toe om de trainings-en test sets te maken. Stel de Fractie van rijen in de eerste uitvoer gegevensset in op 0,7. Met deze instelling geeft u op dat 70% van de gegevens wordt uitgevoerd naar de linker poort van de module en de rest naar de juiste poort. We gebruiken de gegevensset links voor training en de juiste voor test doeleinden.
1. Voeg de module **op basis van filter functies** toe om vijf functies te selecteren door PearsonCorreclation. 
1. Voeg een uitmuntende **beslissings structuur** module toe om een gestimuleerde beslissings structuur classificatie te initialiseren.
1. Een **Train model** -module toevoegen. Verbind de classificatie van de vorige stap naar de links invoer poort van het **Train-model**. Verbind de gefilterde gegevensset van de module functie selectie op basis van een trainings gegevensset.  Het **Train-model** traint de classificatie.
1. Voeg kolom transformatie selecteren toe en pas de transformatie module toe op het Toep assen van dezelfde trans formatie (gefilterde functie selectie) om de gegevensset te testen.
![apply-trans formatie](media/how-to-ui-sample-classification-predict-income/transformation.png)
1. Voeg de module **score model** toe en verbind de module **Train model** . Voeg vervolgens de testset (de uitvoer van de module voor het Toep assen van een trans formatie toe om de functie selectie te testen die u wilt instellen) naar het **score model**. Het **score model** maakt de voor spellingen. U kunt de uitvoer poort selecteren om de voor spellingen en de positieve klassen kansen te bekijken.


    Deze pijp lijn heeft twee Score modules, het één aan de rechter kant heeft een kolom met het label uitgesloten voordat de voor spelling wordt gemaakt. Dit is voor bereid voor het implementeren van een real-time eind punt, omdat de webservice-invoer alleen kenmerken bevat die geen label zijn. 

1. Voeg een module **Evaluate model** toe en verbind de gescoorde gegevensset met de linker invoer poort. Als u de evaluatie resultaten wilt weer geven, selecteert u de uitvoer poort van de module **Evaluate model** en selecteert u **visualiseren**.

## <a name="results"></a>Resultaten

![De resultaten evalueren](media/how-to-ui-sample-classification-predict-income/evaluate-result.png)

In de resultaten van de evaluatie kunt u zien dat de bochten zoals ROC, precisie terughalen en verwar ring. 

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

Bekijk de andere voor beelden die beschikbaar zijn voor de ontwerp functie:

- [Voor beeld 1-regressie: de prijs van een auto voors pellen](how-to-designer-sample-regression-automobile-price-basic.md)
- [Voor beeld 2-regressie: vergelijkings algoritmen voor de voor spelling van prijzen voor auto Mobile](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [Voor beeld 4-classificatie: krediet risico voors pellen (kosten gevoelig)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [Voor beeld 5-classificatie: voor spel verloop](how-to-designer-sample-classification-churn.md)
- [Voor beeld 6: classificatie: voor spel vertraging van de vlucht](how-to-designer-sample-classification-flight-delay.md)
- [Voor beeld 7-tekst classificatie: Wikipedia SP 500-gegevensset](how-to-designer-sample-text-classification.md)
