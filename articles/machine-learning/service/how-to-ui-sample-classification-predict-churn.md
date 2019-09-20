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
ms.openlocfilehash: 260d94ddf2572979e819ee89dfcbd315ef3c4769
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/19/2019
ms.locfileid: "71131933"
---
# <a name="sample-5---classification-predict-churn-appetency-and-up-selling"></a>Voor beeld 5-classificatie: Verloop, verlangen en up-sell voors pellen 

Meer informatie over hoe u een complex machine learning experiment bouwt zonder een enkele regel code te schrijven met behulp van de visuele interface.

In dit experiment worden drie, met **twee klassen gestimuleerde** classificaties voor de beslissings structuur voor het voors pellen van veelvoorkomende taken voor CRM-systemen (Customer Relationship Management): verloop, verlangen en up-selling. De gegevens waarden en labels zijn gesplitst over meerdere gegevens bronnen en worden versleuteld naar anoniem maken klant gegevens. we kunnen echter nog steeds de visuele interface gebruiken om gegevens sets te combi neren en een model te trainen met behulp van de versleutelde waarden.

Omdat u probeert de vraag "welke?" te beantwoorden Dit wordt een probleem met de classificatie genoemd, maar u kunt dezelfde logica in dit project Toep assen om elk type machine learning probleem op te doen, of het nu gaat om een regressie, classificatie, Clustering, enzovoort.

Hier volgt de voltooide grafiek voor dit experiment:

![Grafiek experimenteren](./media/how-to-ui-sample-classification-predict-churn/experiment-graph.png)

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Selecteer de knop **openen** voor het voor beeld 5-experiment.

    ![Het experiment openen](media/how-to-ui-sample-classification-predict-churn/open-sample5.png)

## <a name="data"></a>Data

De gegevens voor dit experiment zijn afkomstig uit KDDe Cup 2009. Het bevat 50.000 rijen en kolommen met 230-functies. De taak is het voors pellen van verloop, verlangen en het verkopen van klanten die gebruikmaken van deze functies. Zie de [KDD-website](https://www.kdd.org/kdd-cup/view/kdd-cup-2009)voor meer informatie over de gegevens en de taak.

## <a name="experiment-summary"></a>Experiment overzicht

In dit voorbeeld experiment van de Visual-Interface ziet u een veelvoorkomende taak voor de voor spelling van een gemeen schappelijke geclassificeerd verloop, verlangen en verkoop, een algemene taken voor Customer Relationship Management (CRM).

Voer eerst een eenvoudige gegevens verwerking uit.

- De onbewerkte gegevensset bevat veel ontbrekende waarden. Gebruik de module **clean Missing Data** om de ontbrekende waarden te vervangen door 0.

    ![De gegevensset opschonen](./media/how-to-ui-sample-classification-predict-churn/cleaned-dataset.png)

- De functies en de bijbehorende labels voor de verloop-, verlangen-en verkoop activiteiten bevinden zich in verschillende gegevens sets. Gebruik de module **add columns** om de label kolommen toe te voegen aan de functie kolommen. De eerste kolom, **Kol1**, is de kolom Label. De rest van de kolommen, **Var1**, **Var2**, enzovoort, zijn de functie kolommen.

    ![De kolom gegevensset toevoegen](./media/how-to-ui-sample-classification-predict-churn/added-column1.png)

- Gebruik de **gesplitste gegevens** module om de gegevensset te splitsen in Train-en test sets.

    Gebruik vervolgens de gestimuleerde binaire classificatie van de beslissings structuur met de standaard parameters voor het bouwen van de Voorspellings modellen. Bouw één model per taak, dat wil zeggen, één model waarmee u de verkoop, verlangen en het verloop kunt voors pellen.

## <a name="results"></a>Resultaten

Visualiseer de uitvoer van de module **Evaluate model** om de prestaties van het model in de testset te bekijken. Voor de Upsell-taak laat de ROC-curve zien dat het model beter werkt dan een wille keurig model. Het gebied onder de curve (AUC) is 0,857. Bij drempel waarde van 0,5 is de nauw keurigheid 0,7, het intrekken 0,463 en de F1-Score is 0,545.

![De resultaten evalueren](./media/how-to-ui-sample-classification-predict-churn/evaluate-result.png)

 U kunt de schuif regelaar **drempelwaarde** verplaatsen en de wijziging van metrische gegevens voor de binaire classificatie taak bekijken.

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

Bekijk de andere voor beelden die beschikbaar zijn voor de visuele interface:

- [Voor beeld 1-regressie: De prijs van een auto voors pellen](how-to-ui-sample-regression-predict-automobile-price-basic.md)
- [Voor beeld 2-regressie: Algoritmen voor het voors pellen van prijzen vergelijken](how-to-ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Voor beeld 3-classificatie: Krediet risico voors pellen](how-to-ui-sample-classification-predict-credit-risk-basic.md)
- [Voor beeld 4-classificatie: Voor speld krediet risico (kosten gevoelig)](how-to-ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [Voor beeld 6: classificatie: Vlucht vertragingen voors pellen](how-to-ui-sample-classification-predict-flight-delay.md)
