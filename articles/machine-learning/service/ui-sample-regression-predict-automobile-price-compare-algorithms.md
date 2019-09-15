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
ms.openlocfilehash: 2c45ccf55b1adde7150dee17fd562b24ee4777ba
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/14/2019
ms.locfileid: "70997104"
---
# <a name="sample-2---regression-predict-price-and-compare-algorithms"></a>Voor beeld 2-regressie: Prijs voors pellen en algoritmen vergelijken

Meer informatie over hoe u een complex machine learning experiment bouwt zonder een enkele regel code te schrijven met behulp van de visuele interface. Dit voor beeld van treinen en vergelijkt meerdere regressie modellen om de prijs van een auto te voors pellen op basis van de technische functies. We bieden de reden voor de keuzes die in dit experiment zijn gemaakt zodat u uw eigen machine learning problemen kunt aanpakken.

Als u net aan de slag gaat met machine learning, kunt u de [basis versie](ui-sample-regression-predict-automobile-price-basic.md) van dit experiment bekijken om een basis regressie-experiment te bekijken.

Hier volgt de voltooide grafiek voor dit experiment:

[![Grafiek van het experiment](media/ui-sample-regression-predict-automobile-price-compare-algorithms/graph.png)](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Selecteer de knop **openen** voor het voor beeld-2 experiment:

    ![Het experiment openen](media/ui-sample-regression-predict-automobile-price-compare-algorithms/open-sample2.png)

## <a name="experiment-summary"></a>Experiment overzicht

We gebruiken de volgende stappen om het experiment te bouwen:

1. De gegevens ophalen.
1. De gegevens vooraf verwerken.
1. Train het model.
1. Test, evalueer en vergelijk de modellen.

## <a name="get-the-data"></a>De gegevens ophalen

In dit experiment gebruiken we de gegevensset **Auto Mobile price data (RAW)** , die afkomstig is uit de ICB machine learning-opslag plaats. Deze gegevensset bevat 26 kolommen die informatie bevatten over auto's, waaronder merk, model, prijs, voertuig functies (zoals het aantal flessen), MPG en een risico score voor verzekeringen. Het doel van dit experiment is om de prijs van een auto te voors pellen.

## <a name="pre-process-the-data"></a>De gegevens vooraf verwerken

De belangrijkste taken voor gegevens voorbereiding zijn het opschonen van gegevens, integratie, trans formatie, reductie en onderscheidings of kwantisatiefouten. In de Visual-Interface kunt u modules vinden voor het uitvoeren van deze bewerkingen en andere taken voor het verwerken van gegevens in de **gegevens transformatie** groep in het linkerdeel venster.

In dit experiment gebruiken we de module **select columns in dataset** om normale verliezen met veel ontbrekende waarden uit te sluiten. Vervolgens gebruiken we **clean Missing Data** om de rijen met ontbrekende waarden te verwijderen. Zo kunt u een schone set trainings gegevens maken.

![Vooraf verwerkte gegevens](media/ui-sample-regression-predict-automobile-price-compare-algorithms/data-processing.png)

## <a name="train-the-model"></a>Het model trainen

Problemen met machine learning kunnen variëren. Veelvoorkomende machine learning taken omvatten classificatie-, cluster-, regressie-en aanbevolen systemen, die elk een ander algoritme kunnen vereisen. Uw keuze van algoritme is vaak afhankelijk van de vereisten van de use-case. Nadat u een algoritme hebt gekozen, moet u de para meters afstemmen om een nauw keuriger model te trainen. Vervolgens moet u alle modellen evalueren op basis van metrische gegevens, zoals nauw keurigheid, Intelligibility en efficiëntie.

Omdat het doel van dit experiment is om de prijzen van auto's te voors pellen, en omdat de kolom Label (prijs) reële getallen bevat, is een regressie model een goede keuze. Gezien het feit dat het aantal functies relatief klein is (minder dan 100) en deze functies niet verspreid zijn, is de beslissings grens waarschijnlijk niet lineair.

Om de prestaties van verschillende algoritmen te vergelijken, gebruiken we twee niet-lineaire algoritmen, de regressie regressie van de **beslissings structuur** en de herleiding van het **besluitvormings forest**om modellen te bouwen. Beide algoritmen hebben para meters die u kunt wijzigen, maar we gebruiken de standaard waarden voor dit experiment.

We gebruiken de **gesplitste gegevens** module om de invoer gegevens wille keurig te verdelen, zodat de trainings gegevensset 70% van de oorspronkelijke gegevens bevat en de test gegevensset bevat 30% van de oorspronkelijke gegevens.

## <a name="test-evaluate-and-compare-the-models"></a>De modellen testen, evalueren en vergelijken

We gebruiken twee verschillende sets wille keurig gekozen gegevens om het model te trainen en vervolgens te testen, zoals beschreven in de vorige sectie. We splitsen de gegevensset en gebruiken verschillende gegevens sets om het model te trainen en te testen, zodat de evaluatie van het model meer doel gericht is.

Nadat het model is getraind, gebruiken we het **score model** en **evalueren we model** modules om voorspelde resultaten te genereren en de modellen te evalueren. Met het **score model** worden voor spellingen gegenereerd voor de test gegevensset met behulp van het getrainde model. We geven vervolgens de scores voor het evalueren van het **model** door om evaluatie gegevens te genereren.

In dit experiment gebruiken we twee exemplaren van het **evaluatie model** om twee combi Naties van modellen te vergelijken.

Eerst vergelijken we twee algoritmen in de trainings gegevensset.
Ten tweede vergelijken we twee algoritmen op de gegevensset voor testen.
Dit zijn de resultaten:

![De resultaten vergelijken](media/ui-sample-regression-predict-automobile-price-compare-algorithms/result.png)

Deze resultaten geven aan dat het model dat is gebouwd met een **versterkte regressie voor de beslissings structuur** , een lager uitstaande fout heeft dan het model dat is opgebouwd op basis van de regressie van het **beslissings forest**.

Beide algoritmen hebben een lagere fout in de trainings gegevensset dan op de ongelijke test gegevensset.

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

Bekijk de andere voor beelden die beschikbaar zijn voor de visuele interface:

- [Voor beeld 1-regressie: De prijs van een auto voors pellen](ui-sample-regression-predict-automobile-price-basic.md)
- [Voor beeld 3-classificatie: Krediet risico voors pellen](ui-sample-classification-predict-credit-risk-basic.md)
- [Voor beeld 4-classificatie: Voor speld krediet risico (kosten gevoelig)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [Voor beeld 5-classificatie: Verloop voors pellen](ui-sample-classification-predict-churn.md)
- [Voor beeld 6: classificatie: Vlucht vertragingen voors pellen](ui-sample-classification-predict-flight-delay.md)
