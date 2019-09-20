---
title: 'Voor beeld van een Visual-Interface #3: regressie voor prijs-en vergelijkings algoritmen'
titleSuffix: Azure Machine Learning
description: In dit artikel wordt beschreven hoe u een complex machine learning experiment bouwt zonder een enkele regel code te schrijven met behulp van de visuele interface. Meer informatie over het trainen en vergelijken van meerdere regressie modellen voor het voors pellen van de prijs van een auto op basis van technische functies
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: c40d76b87ca7437e25c567176b0309f08f3ca9f2
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/19/2019
ms.locfileid: "71131569"
---
# <a name="sample-2---regression-predict-price-and-compare-algorithms"></a>Voor beeld 2-regressie: Prijs voors pellen en algoritmen vergelijken

Meer informatie over hoe u een complex machine learning experiment bouwt zonder een enkele regel code te schrijven met behulp van de visuele interface. Dit voor beeld van treinen en vergelijkt meerdere regressie modellen om de prijs van een auto te voors pellen op basis van de technische functies. We bieden de reden voor de keuzes die in dit experiment zijn gemaakt zodat u uw eigen machine learning problemen kunt aanpakken.

Als u net aan de slag gaat met machine learning, kunt u de [basis versie](how-to-ui-sample-regression-predict-automobile-price-basic.md) van dit experiment bekijken.

Hier volgt de voltooide grafiek voor dit experiment:

[![Grafiek van het experiment](media/how-to-ui-sample-regression-predict-automobile-price-compare-algorithms/graph.png)](media/how-to-ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Selecteer de knop **openen** voor het voor beeld-2 experiment:

    ![Het experiment openen](media/how-to-ui-sample-regression-predict-automobile-price-compare-algorithms/open-sample2.png)

## <a name="experiment-summary"></a>Experiment overzicht

Gebruik de volgende stappen om het machine learning-experiment te bouwen:

1. De gegevens ophalen.
1. De gegevens vooraf verwerken.
1. Train het model.
1. Test, evalueer en vergelijk de modellen.

## <a name="get-the-data"></a>De gegevens ophalen

In dit voor beeld wordt de gegevensset **Auto Mobile price data (RAW)** gebruikt, die afkomstig is uit de ICB machine learning-opslag plaats. Deze gegevensset bevat 26 kolommen die informatie bevatten over auto's, waaronder merk, model, prijs, voertuig functies (zoals het aantal flessen), MPG en een risico score voor verzekeringen.

## <a name="pre-process-the-data"></a>De gegevens vooraf verwerken

De belangrijkste taken voor gegevens voorbereiding zijn het opschonen van gegevens, integratie, trans formatie, reductie en onderscheidings of kwantisatiefouten. In de Visual-Interface kunt u modules vinden voor het uitvoeren van deze bewerkingen en andere taken voor het verwerken van gegevens in de **gegevens transformatie** groep in het linkerdeel venster.

Gebruik de module **select columns in dataset** om normale verliezen met veel ontbrekende waarden uit te sluiten. Vervolgens gebruiken we **clean Missing Data** om de rijen met ontbrekende waarden te verwijderen. Zo kunt u een schone set trainings gegevens maken.

![Vooraf verwerkte gegevens](media/how-to-ui-sample-regression-predict-automobile-price-compare-algorithms/data-processing.png)

## <a name="train-the-model"></a>Het model trainen

Problemen met machine learning kunnen variëren. Veelvoorkomende machine learning taken omvatten classificatie-, cluster-, regressie-en aanbevolen systemen, die elk een ander algoritme kunnen vereisen. Uw keuze van algoritme is vaak afhankelijk van de vereisten van de use-case. Nadat u een algoritme hebt gekozen, moet u de para meters afstemmen om een nauw keuriger model te trainen. Vervolgens moet u alle modellen evalueren op basis van metrische gegevens, zoals nauw keurigheid, Intelligibility en efficiëntie.

Omdat het doel van dit experiment is om de prijzen van auto's te voors pellen, en omdat de kolom Label (prijs) reële getallen bevat, is een regressie model een goede keuze. Gezien het feit dat het aantal functies relatief klein is (minder dan 100) en deze functies niet verspreid zijn, is de beslissings grens waarschijnlijk niet lineair.

Omdat het doel van dit experiment is om de prijzen van auto's te voors pellen, en omdat de kolom Label (prijs) reële getallen bevat, is een regressie model een goede keuze. Gezien het feit dat het aantal functies relatief klein is (minder dan 100) en deze functies niet verspreid zijn, is de beslissings grens waarschijnlijk niet lineair.

Om de prestaties van verschillende algoritmen te vergelijken, gebruiken we twee niet-lineaire algoritmen, de regressie regressie van de **beslissings structuur** en de herleiding van het **besluitvormings forest**om modellen te bouwen. Beide algoritmen hebben para meters die u kunt wijzigen, maar in dit voor beeld worden de standaard waarden voor dit experiment gebruikt.

Gebruik de module **Splits data** om de invoer gegevens wille keurig te verdelen, zodat de trainings gegevensset 70% van de oorspronkelijke gegevens bevat en de test gegevensset 30% van de oorspronkelijke gegevens bevat.

## <a name="test-evaluate-and-compare-the-models"></a>De modellen testen, evalueren en vergelijken

U gebruikt twee verschillende sets wille keurig gekozen gegevens om het model te trainen en vervolgens te testen, zoals beschreven in de vorige sectie. Splits de gegevensset en gebruik verschillende gegevens sets om het model te trainen en te testen, zodat de evaluatie van het model meer doel gericht is.

Nadat het model is getraind, gebruikt u het **score model** en **evalueert u model** modules om voorspelde resultaten te genereren en de modellen te evalueren. Met het **score model** worden voor spellingen gegenereerd voor de test gegevensset met behulp van het getrainde model. Geef vervolgens de scores op om het **model te evalueren** en de metrische gegevens over de evaluatie te genereren.

In dit experiment gebruikt u twee exemplaren van het **Evaluate-model** om twee combi Naties van modellen te vergelijken.

Vergelijk eerst twee algoritmen in de trainings gegevensset.
Vergelijk vervolgens twee algoritmen op de gegevensset testen.

Dit zijn de resultaten:

![De resultaten vergelijken](media/how-to-ui-sample-regression-predict-automobile-price-compare-algorithms/result.png)

Deze resultaten geven aan dat het model dat is gebouwd met een **versterkte regressie voor de beslissings structuur** , een lager uitstaande fout heeft dan het model dat is opgebouwd op basis van de regressie van het **beslissings forest**.

Beide algoritmen hebben een lagere fout in de trainings gegevensset dan op de ongelijke test gegevensset.

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

Bekijk de andere voor beelden die beschikbaar zijn voor de visuele interface:

- [Voor beeld 1-regressie: De prijs van een auto voors pellen](how-to-ui-sample-regression-predict-automobile-price-basic.md)
- [Voor beeld 3-classificatie: Krediet risico voors pellen](how-to-ui-sample-classification-predict-credit-risk-basic.md)
- [Voor beeld 4-classificatie: Voor speld krediet risico (kosten gevoelig)](how-to-ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [Voor beeld 5-classificatie: Verloop voors pellen](how-to-ui-sample-classification-predict-churn.md)
- [Voor beeld 6: classificatie: Vlucht vertragingen voors pellen](how-to-ui-sample-classification-predict-flight-delay.md)
