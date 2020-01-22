---
title: 'Ontwerp: voor beeld van prijzen voors pellen (Basic)'
titleSuffix: Azure Machine Learning
description: Bouw een ML regressie model voor het voors pellen van de prijs van een auto, zonder een enkele regel code te schrijven met Azure Machine Learning Designer.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.date: 12/25/2019
ms.openlocfilehash: 18a9f9d49dce96b037f10a95a458e52d020fe6c7
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76311067"
---
# <a name="use-regression-to-predict-car-prices-with-azure-machine-learning-designer"></a>Regressie gebruiken om de prijzen van auto's te voors pellen met Azure Machine Learning Designer

**Voor beeld van Designer 1**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Meer informatie over het bouwen van een machine learning regressie model zonder dat u een regel code hoeft te schrijven met behulp van de ontwerp functie.

Deze pijp lijn traint een **lineaire regressor hierop** om de prijs van een auto te voors pellen op basis van technische functies zoals merk, model, paarden kracht en grootte. Omdat u probeert de vraag "hoeveel?" te beantwoorden Dit wordt een regressie probleem genoemd. U kunt echter dezelfde basis stappen in dit voor beeld Toep assen om elk type machine learning probleem op te doen, of het nu gaat om een regressie, classificatie, Clustering, enzovoort.

De belangrijkste stappen van een trainings machine learning model zijn:

1. De gegevens ophalen
1. De gegevens vooraf verwerken
1. Het model trainen
1. Het model evalueren

Hier is de uiteindelijke, voltooide grafiek van de pijp lijn. Dit artikel bevat een motivering voor alle modules, zodat u op uw eigen wijze vergelijk bare beslissingen kunt nemen.

![Grafiek van de pijp lijn](./media/how-to-designer-sample-regression-automobile-price-basic/overall-graph.png)

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [aml-ui-prereq](../../includes/aml-ui-prereq.md)]

4. Klik op het voor beeld 1 om het te openen Marketplace.


## <a name="get-the-data"></a>De gegevens ophalen

In dit voor beeld wordt de gegevensset **Auto Mobile price data (RAW)** gebruikt, die afkomstig is uit de ICB machine learning-opslag plaats. De gegevensset bevat 26 kolommen die informatie bevatten over auto's, waaronder merk, model, prijs, voertuig functies (zoals het aantal flessen), MPG en een risico score voor verzekeringen. Het doel van dit voor beeld is om de prijs van de auto te voors pellen.

## <a name="pre-process-the-data"></a>De gegevens vooraf verwerken

De belangrijkste taken voor gegevens voorbereiding zijn het opschonen van gegevens, integratie, trans formatie, reductie en onderscheidings of kwantisatiefouten. In de ontwerp functie kunt u modules vinden voor het uitvoeren van deze bewerkingen en andere taken die vooraf worden verwerkt in de **gegevens transformatie** groep in het linkerdeel venster.

Gebruik de module **select columns in dataset** om normale verliezen met veel ontbrekende waarden uit te sluiten. Vervolgens gebruikt u **clean Missing Data** om de rijen met ontbrekende waarden te verwijderen. Zo kunt u een schone set trainings gegevens maken.

![Vooraf verwerkte gegevens](./media/how-to-designer-sample-regression-automobile-price-basic/data-processing.png)

## <a name="train-the-model"></a>Het model trainen

Problemen met machine learning kunnen variëren. Veelvoorkomende machine learning taken omvatten classificatie-, cluster-, regressie-en aanbevolen systemen, die elk een ander algoritme kunnen vereisen. Uw keuze van algoritme is vaak afhankelijk van de vereisten van de use-case. Nadat u een algoritme hebt gekozen, moet u de para meters afstemmen om een nauw keuriger model te trainen. Vervolgens moet u alle modellen evalueren op basis van metrische gegevens, zoals nauw keurigheid, Intelligibility en efficiëntie.

Omdat het doel van dit voor beeld is om de prijzen van auto's te voors pellen, en omdat de kolom Label (prijs) reële getallen bevat, is een regressie model een goede keuze. Gezien het feit dat het aantal functies relatief klein is (minder dan 100) en deze functies niet verspreid zijn, is de beslissings grens waarschijnlijk niet lineair. Daarom gebruiken we de regressie van het **beslissings forest** voor deze pijp lijn.

Gebruik de module **Splits data** om de invoer gegevens wille keurig te verdelen, zodat de trainings gegevensset 70% van de oorspronkelijke gegevens bevat en de test gegevensset 30% van de oorspronkelijke gegevens bevat.

## <a name="test-evaluate-and-compare"></a>Testen, evalueren en vergelijken

Splits de gegevensset en gebruik verschillende gegevens sets om het model te trainen en te testen, zodat de evaluatie van het model meer doel gericht is.

Nadat het model is getraind, kunt u het **score model** gebruiken en **model modules evalueren** om voorspelde resultaten te genereren en de modellen te evalueren.

Met het **score model** worden voor spellingen gegenereerd voor de test gegevensset met behulp van het getrainde model. Als u het resultaat wilt controleren, selecteert u de uitvoer poort van het **score model** en selecteert u vervolgens **visualiseren**.

![Resultaat van Score](./media/how-to-designer-sample-regression-automobile-price-basic/sample1-score-1225.png)

Geef de scores door aan de module **Evaluate model** om metrische gegevens over de evaluatie te genereren. Als u het resultaat wilt controleren, selecteert u de uitvoer poort van het **Evalueer model** en selecteert u vervolgens **visualiseren**.

![Resultaat evalueren](./media/how-to-designer-sample-regression-automobile-price-basic/sample1-evaluate-1225.png)

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

Bekijk de andere voor beelden die beschikbaar zijn voor de ontwerp functie:

- [Voor beeld 2-regressie: vergelijkings algoritmen voor de voor spelling van prijzen voor auto Mobile](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [Voor beeld 3: classificatie met functie selectie: inkomen voor spelling](how-to-designer-sample-classification-predict-income.md)
- [Voor beeld 4-classificatie: krediet risico voors pellen (kosten gevoelig)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [Voor beeld 5-classificatie: voor spel verloop](how-to-designer-sample-classification-churn.md)
- [Voor beeld 6: classificatie: voor spel vertraging van de vlucht](how-to-designer-sample-classification-flight-delay.md)
- [Voor beeld 7-tekst classificatie: Wikipedia SP 500-gegevensset](how-to-designer-sample-text-classification.md)