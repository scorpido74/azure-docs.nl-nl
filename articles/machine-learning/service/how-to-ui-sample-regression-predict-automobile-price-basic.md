---
title: 'Voor beeld van een Visual-Interface #1: regressie om de prijs te voors pellen'
titleSuffix: Azure Machine Learning
description: Meer informatie over het bouwen van een machine learning model voor het voors pellen van de prijs van een auto, zonder dat u een regel code hoeft te schrijven.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: a91f1446d8aab3db36499a9b5707d48d387b6081
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/19/2019
ms.locfileid: "71131556"
---
# <a name="sample-1---regression-predict-price"></a>Voor beeld 1-regressie: Prijs voorspellen

Meer informatie over het bouwen van een machine learning regressie model zonder een enkele regel code te schrijven met behulp van de visuele interface.

In dit experiment wordt een regressor hierop van een **beslissings forest** voor speld om de prijs van een auto te voors pellen op basis van de technische functies zoals merk, model, paarden kracht en grootte. Omdat u probeert de vraag "hoeveel?" te beantwoorden Dit wordt een regressie probleem genoemd. U kunt echter dezelfde basis stappen in dit voor beeld Toep assen om elk type machine learning probleem op te doen, of het nu gaat om een regressie, classificatie, Clustering, enzovoort.

De belangrijkste stappen van een trainings machine learning model zijn:

1. De gegevens ophalen
1. De gegevens vooraf verwerken
1. Het model trainen
1. Het model evalueren

Hier volgt de uiteindelijke, voltooide grafiek van het experiment waarmee we aan de slag gaan. We bieden de motivering voor alle modules, zodat u op uw eigen wijze vergelijk bare beslissingen kunt nemen.

![Grafiek van het experiment](media/how-to-ui-sample-regression-predict-automobile-price-basic/overall-graph.png)

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Selecteer de knop **openen** voor het voor beeld van 1 experiment:

    ![Het experiment openen](media/how-to-ui-sample-regression-predict-automobile-price-basic/open-sample1.png)

## <a name="get-the-data"></a>De gegevens ophalen

In dit voor beeld wordt de gegevensset **Auto Mobile price data (RAW)** gebruikt, die afkomstig is uit de ICB machine learning-opslag plaats. De gegevensset bevat 26 kolommen die informatie bevatten over auto's, waaronder merk, model, prijs, voertuig functies (zoals het aantal flessen), MPG en een risico score voor verzekeringen. Het doel van dit experiment is om de prijs van de auto te voors pellen.

## <a name="pre-process-the-data"></a>De gegevens vooraf verwerken

De belangrijkste taken voor gegevens voorbereiding zijn het opschonen van gegevens, integratie, trans formatie, reductie en onderscheidings of kwantisatiefouten. In de visuele interface kunt u modules vinden voor het uitvoeren van deze bewerkingen en andere taken die vooraf worden verwerkt in de **gegevens transformatie** groep in het linkerdeel venster.

Gebruik de module **select columns in dataset** om normale verliezen met veel ontbrekende waarden uit te sluiten. Vervolgens gebruikt u **clean Missing Data** om de rijen met ontbrekende waarden te verwijderen. Zo kunt u een schone set trainings gegevens maken.

![Vooraf verwerkte gegevens](./media/how-to-ui-sample-regression-predict-automobile-price-basic/data-processing.png)

## <a name="train-the-model"></a>Het model trainen

Problemen met machine learning kunnen variëren. Veelvoorkomende machine learning taken omvatten classificatie-, cluster-, regressie-en aanbevolen systemen, die elk een ander algoritme kunnen vereisen. Uw keuze van algoritme is vaak afhankelijk van de vereisten van de use-case. Nadat u een algoritme hebt gekozen, moet u de para meters afstemmen om een nauw keuriger model te trainen. Vervolgens moet u alle modellen evalueren op basis van metrische gegevens, zoals nauw keurigheid, Intelligibility en efficiëntie.

Omdat het doel van dit experiment is om de prijzen van auto's te voors pellen, en omdat de kolom Label (prijs) reële getallen bevat, is een regressie model een goede keuze. Gezien het feit dat het aantal functies relatief klein is (minder dan 100) en deze functies niet verspreid zijn, is de beslissings grens waarschijnlijk niet lineair. Daarom gebruiken we de regressie van het **besluitvormings forest** voor dit experiment.

Gebruik de module **Splits data** om de invoer gegevens wille keurig te verdelen, zodat de trainings gegevensset 70% van de oorspronkelijke gegevens bevat en de test gegevensset 30% van de oorspronkelijke gegevens bevat.

## <a name="test-evaluate-and-compare"></a>Testen, evalueren en vergelijken

Splits de gegevensset en gebruik verschillende gegevens sets om het model te trainen en te testen, zodat de evaluatie van het model meer doel gericht is.

Nadat het model is getraind, kunt u het **score model** gebruiken en **model modules evalueren** om voorspelde resultaten te genereren en de modellen te evalueren.

Met het **score model** worden voor spellingen gegenereerd voor de test gegevensset met behulp van het getrainde model. Als u het resultaat wilt controleren, selecteert u de uitvoer poort van het **score model** en selecteert u vervolgens **visualiseren**.

![Resultaat van Score](./media/how-to-ui-sample-regression-predict-automobile-price-basic/score-result.png)

Geef de scores door aan de module **Evaluate model** om metrische gegevens over de evaluatie te genereren. Als u het resultaat wilt controleren, selecteert u de uitvoer poort van het **Evalueer model** en selecteert u vervolgens **visualiseren**.

![Resultaat evalueren](./media/how-to-ui-sample-regression-predict-automobile-price-basic/evaluate-result.png)

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

Bekijk de andere voor beelden die beschikbaar zijn voor de visuele interface:

- [Voor beeld 2-regressie: Algoritmen voor het voors pellen van prijzen vergelijken](how-to-ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Voor beeld 3-classificatie: Krediet risico voors pellen](how-to-ui-sample-classification-predict-credit-risk-basic.md)
- [Voor beeld 4-classificatie: Voor speld krediet risico (kosten gevoelig)](how-to-ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [Voor beeld 5-classificatie: Verloop voors pellen](how-to-ui-sample-classification-predict-churn.md)
- [Voor beeld 6: classificatie: Vlucht vertragingen voors pellen](how-to-ui-sample-classification-predict-flight-delay.md)
