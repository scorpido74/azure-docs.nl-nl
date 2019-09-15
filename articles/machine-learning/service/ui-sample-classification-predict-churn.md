---
title: '#5 voor beeld van visuele interface: Classificatie voor het voors pellen van verloop en verlangen + up-sell'
titleSuffix: Azure Machine Learning
description: In dit voorbeeld experiment van de Visual-Interface ziet u de binaire classifier-voor spelling van het verloop, een algemene taak voor Customer Relationship Management (CRM).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: 054ac3ea5224a4ffea2e7bae9da329ba7bc81233
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/14/2019
ms.locfileid: "70997139"
---
# <a name="sample-5---classification-predict-churn-appetency-and-up-selling"></a>Voor beeld 5-classificatie: Verloop, verlangen en up-sell voors pellen 

Meer informatie over hoe u een complex machine learning experiment bouwt zonder een enkele regel code te schrijven met behulp van de visuele interface.

In dit experiment worden drie, met **twee klassen gestimuleerde** classificaties voor de beslissings structuur voor het voors pellen van veelvoorkomende taken voor CRM-systemen (Customer Relationship Management): verloop, verlangen en up-selling. De gegevens waarden en labels zijn gesplitst over meerdere gegevens bronnen en worden versleuteld naar anoniem maken klant gegevens. we kunnen echter nog steeds de visuele interface gebruiken om gegevens sets te combi neren en een model te trainen met behulp van de versleutelde waarden.

Omdat we proberen de vraag "welke?" te beantwoorden Dit wordt een probleem met de classificatie genoemd. U kunt echter dezelfde stappen in dit experiment Toep assen om elk type machine learning probleem op te doen, of het nu gaat om een regressie, classificatie, Clustering, enzovoort.

Hier volgt de voltooide grafiek voor dit experiment:

![Grafiek experimenteren](./media/ui-sample-classification-predict-churn/experiment-graph.png)

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Selecteer de knop **openen** voor het voor beeld 5-experiment.

    ![Het experiment openen](media/ui-sample-classification-predict-churn/open-sample5.png)

## <a name="data"></a>Data

De gegevens die we voor dit experiment gebruiken, zijn afkomstig uit KDDe Cup 2009. De gegevensset heeft 50.000 rijen en kolommen van 230. De taak is het voors pellen van verloop, verlangen en het verkopen van klanten die gebruikmaken van deze functies. Zie de [KDD-website](https://www.kdd.org/kdd-cup/view/kdd-cup-2009)voor meer informatie over de gegevens en de taak.

## <a name="experiment-summary"></a>Experiment overzicht

In dit voorbeeld experiment van de Visual-Interface ziet u een veelvoorkomende taak voor de voor spelling van een gemeen schappelijke geclassificeerd verloop, verlangen en verkoop, een algemene taken voor Customer Relationship Management (CRM).

Eerst gaan we een eenvoudige gegevens verwerking uitvoeren.

- De onbewerkte gegevensset bevat veel ontbrekende waarden. We gebruiken de module **clean Missing Data** om de ontbrekende waarden te vervangen door 0.

    ![De gegevensset opschonen](./media/ui-sample-classification-predict-churn/cleaned-dataset.png)

- De functies en de bijbehorende labels voor de verloop-, verlangen-en verkoop activiteiten bevinden zich in verschillende gegevens sets. We gebruiken de module **Columns toevoegen** om de label kolommen toe te voegen aan de functie kolommen. De eerste kolom, **Kol1**, is de kolom Label. De rest van de kolommen, **Var1**, **Var2**, enzovoort, zijn de functie kolommen.

    ![De kolom gegevensset toevoegen](./media/ui-sample-classification-predict-churn/added-column1.png)

- We gebruiken de **gesplitste gegevens** module om de gegevensset te splitsen in Train-en test sets.

    Vervolgens gebruiken we de gestimuleerde binaire classificatie van de beslissings structuur met de standaard parameters voor het bouwen van de Voorspellings modellen. We bouwen één model per taak, dat wil zeggen, één model voor het voors pellen van de verkoop, verlangen en het verloop.

## <a name="results"></a>Resultaten

Visualiseer de uitvoer van de module **Evaluate model** om de prestaties van het model in de testset te bekijken. Voor de Upsell-taak laat de ROC-curve zien dat het model beter werkt dan een wille keurig model. Het gebied onder de curve (AUC) is 0,857. Bij drempel waarde van 0,5 is de nauw keurigheid 0,7, het intrekken 0,463 en de F1-Score is 0,545.

![De resultaten evalueren](./media/ui-sample-classification-predict-churn/evaluate-result.png)

 U kunt de schuif regelaar **drempelwaarde** verplaatsen en de wijziging van metrische gegevens voor de binaire classificatie taak bekijken.

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

Bekijk de andere voor beelden die beschikbaar zijn voor de visuele interface:

- [Voor beeld 1-regressie: De prijs van een auto voors pellen](ui-sample-regression-predict-automobile-price-basic.md)
- [Voor beeld 2-regressie: Algoritmen voor het voors pellen van prijzen vergelijken](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Voor beeld 3-classificatie: Krediet risico voors pellen](ui-sample-classification-predict-credit-risk-basic.md)
- [Voor beeld 4-classificatie: Voor speld krediet risico (kosten gevoelig)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [Voor beeld 6: classificatie: Vlucht vertragingen voors pellen](ui-sample-classification-predict-flight-delay.md)
