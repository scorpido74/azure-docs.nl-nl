---
title: 'Voor beeld van een Visual-Interface #5: classificatie voor het voors pellen van verloop en verlangen + up-sell'
titleSuffix: Azure Machine Learning
description: In deze voorbeeld pijplijn van de Visual-Interface wordt een binaire classifier-voor spelling van verloop, een algemene taak voor Customer Relationship Management (CRM) weer gegeven.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 09/23/2019
ms.openlocfilehash: 82639779dde08bb1f71fb75dba62038dbf34d1b6
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693556"
---
# <a name="sample-5---classification-predict-churn-appetency-and-up-selling"></a>Voor beeld 5-classificatie: voor spel verloop, verlangen en up-sell 

Meer informatie over het bouwen van een complexe machine learning pijp lijn zonder dat u een regel code hoeft te schrijven met behulp van de visuele interface.

Deze pijplijn treinen hebben drie, met **twee klassen gestimuleerde** classificaties voor de beslissings structuur voor het voors pellen van veelvoorkomende taken voor CRM-systemen (Customer Relationship Management): verloop, verlangen en up-selling. De gegevens waarden en labels zijn gesplitst over meerdere gegevens bronnen en worden versleuteld naar anoniem maken klant gegevens, maar we kunnen wel nog steeds de visuele interface gebruiken om gegevens sets te combi neren en een model te trainen met behulp van de verborgen waarden.

Omdat u probeert de vraag "welke?" te beantwoorden Dit wordt een probleem met de classificatie genoemd, maar u kunt ook dezelfde logica Toep assen die in dit voor beeld wordt weer gegeven om elk type machine learning probleem aan te pakken, of het nu gaat om een regressie, classificatie, Clustering, enzovoort.

Hier is de voltooide grafiek voor deze pijp lijn:

![Pipeline-grafiek](./media/how-to-ui-sample-classification-predict-churn/pipeline-graph.png)

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Selecteer de knop **openen** voor het voor beeld 5-pijp lijn.

    ![De pijp lijn openen](media/how-to-ui-sample-classification-predict-churn/open-sample5.png)

## <a name="data"></a>Gegevens

De gegevens voor deze pijp lijn zijn afkomstig uit KDDe Cup 2009. Het bevat 50.000 rijen en kolommen met 230-functies. De taak is het voors pellen van verloop, verlangen en het verkopen van klanten die gebruikmaken van deze functies. Zie de [KDD-website](https://www.kdd.org/kdd-cup/view/kdd-cup-2009)voor meer informatie over de gegevens en de taak.

## <a name="pipeline-summary"></a>Pijplijn overzicht

In deze voorbeeld pijplijn van de Visual-Interface ziet u een veelvoorkomende taak voor de voor spelling van het verloop, de verlangen en de verkoop van klanten met CRM (Customer Relationship Management).

Ten eerste, een eenvoudige gegevens verwerking.

- De onbewerkte gegevensset heeft veel ontbrekende waarden. Gebruik de module **clean Missing Data** om de ontbrekende waarden te vervangen door 0.

    ![De gegevensset opschonen](./media/how-to-ui-sample-classification-predict-churn/cleaned-dataset.png)

- De functies en de bijbehorende labels voor de verloop-, verlangen-en verkoop activiteiten bevinden zich in verschillende gegevens sets. Gebruik de module **add columns** om de label kolommen toe te voegen aan de functie kolommen. De eerste kolom, **Kol1**, is de kolom Label. De rest van de kolommen, **Var1**, **Var2**, enzovoort, zijn de functie kolommen.

    ![De kolom gegevensset toevoegen](./media/how-to-ui-sample-classification-predict-churn/added-column1.png)

- Gebruik de **gesplitste gegevens** module om de gegevensset te splitsen in Train-en test sets.

- Gebruik vervolgens de gestimuleerde binaire classificatie van de beslissings structuur met de standaard parameters voor het bouwen van de Voorspellings modellen. Bouw één model per taak, dat wil zeggen, één model waarmee u de verkoop, verlangen en het verloop kunt voors pellen.

## <a name="results"></a>Resultaten

Visualiseer de uitvoer van de module **Evaluate model** om de prestaties van het model in de testset te bekijken. Voor de Upsell-taak laat de ROC-curve zien dat het model beter werkt dan een wille keurig model. Het gebied onder de curve (AUC) is 0,857. Bij drempel waarde van 0,5 is de nauw keurigheid 0,7, het intrekken 0,463 en de F1-Score is 0,545.

![De resultaten evalueren](./media/how-to-ui-sample-classification-predict-churn/evaluate-result.png)

 U kunt de schuif regelaar **drempelwaarde** verplaatsen en de wijziging van metrische gegevens voor de binaire classificatie taak bekijken.

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

Bekijk de andere voor beelden die beschikbaar zijn voor de visuele interface:

- [Voor beeld 1-regressie: de prijs van een auto voors pellen](how-to-ui-sample-regression-predict-automobile-price-basic.md)
- [Voor beeld 2-regressie: vergelijkings algoritmen voor de voor spelling van prijzen voor auto Mobile](how-to-ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Voor beeld 3-classificatie: krediet risico voors pellen](how-to-ui-sample-classification-predict-credit-risk-basic.md)
- [Voor beeld 4-classificatie: krediet risico voors pellen (kosten gevoelig)](how-to-ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [Voor beeld 6: classificatie: voor spel vertraging van de vlucht](how-to-ui-sample-classification-predict-flight-delay.md)
- [Voor beeld 7-tekst classificatie: Books revisies](how-to-ui-sample-text-classification.md)
