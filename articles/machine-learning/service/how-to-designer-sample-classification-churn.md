---
title: 'Ontwerp: voor beeld van het verloop van voor spel'
titleSuffix: Azure Machine Learning
description: Volg dit voor beeld van de classificatie om verloop met Azure Machine Learning Designer te voors pellen & gestimuleerde beslissings structuren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 11/04/2019
ms.openlocfilehash: 1fe3598fd15424ab2593e3d236146c7566493743
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225128"
---
# <a name="use-boosted-decision-tree-to-predict-churn-with-azure-machine-learning-designer"></a>Versterkte beslissings structuur gebruiken om verloop te voors pellen met Azure Machine Learning Designer

**Voor beeld van Designer (preview) 5**

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku.md)]

Meer informatie over het bouwen van een complexe machine learning pijp lijn zonder dat u een regel code hoeft te schrijven met behulp van de ontwerp functie (preview).

Deze pijp lijn treinen 2 **gestimuleerde classificaties voor de beslissings structuur van twee klassen** voor het voors pellen van algemene taken voor CRM-systemen (Customer Relationship Management)-klant verloop. De gegevens waarden en labels zijn gesplitst over meerdere gegevens bronnen en worden versleuteld naar anoniem maken klant gegevens. we kunnen de Designer echter nog steeds gebruiken om gegevens sets te combi neren en een model te trainen met behulp van de verborgen waarden.

Omdat u probeert de vraag "welke?" te beantwoorden Dit wordt een probleem met de classificatie genoemd, maar u kunt ook dezelfde logica Toep assen die in dit voor beeld wordt weer gegeven om elk type machine learning probleem aan te pakken, of het nu gaat om een regressie, classificatie, Clustering, enzovoort.

Hier is de voltooide grafiek voor deze pijp lijn:

![Pipeline-grafiek](./media/how-to-designer-sample-classification-predict-churn/pipeline-graph.png)

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Klik op voor beeld 5 om het te openen. 

## <a name="data"></a>Gegevens

De gegevens voor deze pijp lijn zijn afkomstig uit KDDe Cup 2009. Het bevat 50.000 rijen en kolommen met 230-functies. De taak is het voors pellen van verloop, verlangen en het verkopen van klanten die gebruikmaken van deze functies. Zie de [KDD-website](https://www.kdd.org/kdd-cup/view/kdd-cup-2009)voor meer informatie over de gegevens en de taak.

## <a name="pipeline-summary"></a>Pijplijn overzicht

Deze voorbeeld pijplijn in de ontwerp functie bevat een veelvoorkomende taak voor de voor spelling van het verloop, de verlangen en de verkoop van de gemeen schappelijke taken voor Customer Relationship Management (CRM).

Ten eerste, een eenvoudige gegevens verwerking.

- De onbewerkte gegevensset heeft veel ontbrekende waarden. Gebruik de module **clean Missing Data** om de ontbrekende waarden te vervangen door 0.

    ![De gegevensset opschonen](./media/how-to-designer-sample-classification-predict-churn/cleaned-dataset.png)

- De functies en het bijbehorende verloop bevinden zich in verschillende gegevens sets. Gebruik de module **add columns** om de label kolommen toe te voegen aan de functie kolommen. De eerste kolom, **Kol1**, is de kolom Label. Vanuit het visualisatie resultaat kunnen we zien dat de gegevensset niet in balans is. Er zijn meer negatieve (-1) voor beelden dan positieve voor beelden (+ 1). We gebruiken de **SMOTE** -module om later gepaarde cases te verg Roten.

    ![De kolom gegevensset toevoegen](./media/how-to-designer-sample-classification-predict-churn/added-column1.png)



- Gebruik de **gesplitste gegevens** module om de gegevensset te splitsen in Train-en test sets.

- Gebruik vervolgens de gestimuleerde binaire classificatie van de beslissings structuur met de standaard parameters voor het bouwen van de Voorspellings modellen. Bouw één model per taak, dat wil zeggen, één model waarmee u de verkoop, verlangen en het verloop kunt voors pellen.

- In het rechter deel van de pijp lijn gebruiken we de **SMOTE** -module om het percentage positieve voor beelden te verhogen. Het percentage SMOTE wordt ingesteld op 100 om de positieve voor beelden te verdubbelen. Meer informatie over de werking van de SMOTE-module met [SMOTE-module reference0](../././algorithm-module-reference/SMOTE.md).

## <a name="results"></a>Resultaten

Visualiseer de uitvoer van de module **Evaluate model** om de prestaties van het model in de testset te bekijken. 

![De resultaten evalueren](./media/how-to-designer-sample-classification-predict-churn/evaluate-result.png)

 U kunt de schuif regelaar **drempelwaarde** verplaatsen en de wijziging van metrische gegevens voor de binaire classificatie taak bekijken. 

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

Bekijk de andere voor beelden die beschikbaar zijn voor de ontwerp functie:

- [Voor beeld 1-regressie: de prijs van een auto voors pellen](how-to-designer-sample-regression-automobile-price-basic.md)
- [Voor beeld 2-regressie: vergelijkings algoritmen voor de voor spelling van prijzen voor auto Mobile](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [Voor beeld 3: classificatie met functie selectie: inkomen voor spelling](how-to-designer-sample-classification-predict-income.md)
- [Voor beeld 4-classificatie: krediet risico voors pellen (kosten gevoelig)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [Voor beeld 6: classificatie: voor spel vertraging van de vlucht](how-to-designer-sample-classification-flight-delay.md)
- [Voor beeld 7-tekst classificatie: Wikipedia SP 500-gegevensset](how-to-designer-sample-text-classification.md)
