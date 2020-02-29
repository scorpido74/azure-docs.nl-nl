---
title: 'Ontwerp: voor beeld van prijzen voors pellen (Geavanceerd)'
titleSuffix: Azure Machine Learning
description: Bouw & meerdere ML regressie modellen vergelijkt om de prijs van een auto te voors pellen op basis van technische functies met Azure Machine Learning Designer.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.date: 12/25/2019
ms.openlocfilehash: a80a1567c84ff3c2eda8ad22391aa862bb7d9d82
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/28/2020
ms.locfileid: "77915823"
---
# <a name="train--compare-multiple-regression-models-to-predict-car-prices-with-azure-machine-learning-designer"></a>Train & meerdere regressie modellen vergelijken om de prijzen van auto's te voors pellen met Azure Machine Learning Designer

**Voor beeld van Designer (preview) 2**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Meer informatie over het bouwen van een machine learning pijp lijn zonder het schrijven van één regel code met behulp van de ontwerp functie (preview). Dit voor beeld van treinen en vergelijkt meerdere regressie modellen om de prijs van een auto te voors pellen op basis van de technische functies. We bieden de reden voor de keuzes die u in deze pijp lijn hebt gemaakt zodat u uw eigen machine learning problemen kunt aanpakken.

Als u net aan de slag gaat met machine learning, bekijkt u de [basis versie](how-to-designer-sample-regression-automobile-price-basic.md) van deze pijp lijn.

Hier is de voltooide grafiek voor deze pijp lijn:

[Grafiek van de pijp lijn ![](./media/how-to-designer-sample-regression-automobile-price-compare-algorithms/graph.png)](./media/how-to-designer-sample-regression-automobile-price-compare-algorithms/graph.png#lightbox)

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [aml-ui-prereq](../../includes/aml-ui-prereq.md)]

4. Klik op voor beeld 2 om het te openen. 

## <a name="pipeline-summary"></a>Pijplijn overzicht

Gebruik de volgende stappen om de machine learning-pijp lijn te bouwen:

1. De gegevens ophalen.
1. De gegevens vooraf verwerken.
1. Train het model.
1. Test, evalueer en vergelijk de modellen.

## <a name="get-the-data"></a>De gegevens ophalen

In dit voor beeld wordt de gegevensset **Auto Mobile price data (RAW)** gebruikt, die afkomstig is uit de ICB machine learning-opslag plaats. Deze gegevensset bevat 26 kolommen die informatie bevatten over auto's, waaronder merk, model, prijs, voertuig functies (zoals het aantal flessen), MPG en een risico score voor verzekeringen.

## <a name="pre-process-the-data"></a>De gegevens vooraf verwerken

De belangrijkste taken voor gegevens voorbereiding zijn het opschonen van gegevens, integratie, trans formatie, reductie en onderscheidings of kwantisatiefouten. In de ontwerp functie kunt u modules vinden voor het uitvoeren van deze bewerkingen en andere taken die vooraf worden verwerkt in de **gegevens transformatie** groep in het linkerdeel venster.

Gebruik de module **select columns in dataset** om normale verliezen met veel ontbrekende waarden uit te sluiten. Vervolgens gebruiken we **clean Missing Data** om de rijen met ontbrekende waarden te verwijderen. Zo kunt u een schone set trainings gegevens maken.

![Vooraf verwerkte gegevens](./media/how-to-designer-sample-regression-automobile-price-compare-algorithms/data-processing.png)

## <a name="train-the-model"></a>Het model trainen

Problemen met machine learning kunnen variëren. Veelvoorkomende machine learning taken omvatten classificatie-, cluster-, regressie-en aanbevolen systemen, die elk een ander algoritme kunnen vereisen. Uw keuze van algoritme is vaak afhankelijk van de vereisten van de use-case. Nadat u een algoritme hebt gekozen, moet u de para meters afstemmen om een nauw keuriger model te trainen. Vervolgens moet u alle modellen evalueren op basis van metrische gegevens, zoals nauw keurigheid, Intelligibility en efficiëntie.

Het doel van deze pijp lijn is om auto Mobile-prijzen te voors pellen, en omdat de kolom Label (prijs) reële getallen bevat, is een regressie model een goede keuze.

Om de prestaties van verschillende algoritmen te vergelijken, gebruiken we twee niet-lineaire algoritmen, de regressie regressie van de **beslissings structuur** en de herleiding van het **besluitvormings forest**om modellen te bouwen. Beide algoritmen hebben para meters die u kunt wijzigen, maar in dit voor beeld wordt gebruikgemaakt van de standaard waarden voor deze pijp lijn.

Gebruik de module **Splits data** om de invoer gegevens wille keurig te verdelen, zodat de trainings gegevensset 70% van de oorspronkelijke gegevens bevat en de test gegevensset 30% van de oorspronkelijke gegevens bevat.

## <a name="test-evaluate-and-compare-the-models"></a>De modellen testen, evalueren en vergelijken

U gebruikt twee verschillende sets wille keurig gekozen gegevens om het model te trainen en vervolgens te testen, zoals beschreven in de vorige sectie. Splits de gegevensset en gebruik verschillende gegevens sets om het model te trainen en te testen, zodat de evaluatie van het model meer doel gericht is.

Nadat het model is getraind, gebruikt u het **score model** en **evalueert u model** modules om voorspelde resultaten te genereren en de modellen te evalueren. Met het **score model** worden voor spellingen gegenereerd voor de test gegevensset met behulp van het getrainde model. Geef vervolgens de scores op om het **model te evalueren** en de metrische gegevens over de evaluatie te genereren.



Dit zijn de resultaten:

![De resultaten vergelijken](./media/how-to-designer-sample-regression-automobile-price-compare-algorithms/result.png)

Deze resultaten geven aan dat het model dat is gebouwd met een **versterkte regressie voor de beslissings structuur** , een lager uitstaande fout heeft dan het model dat is opgebouwd op basis van de regressie van het **beslissings forest**.



## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

Bekijk de andere voor beelden die beschikbaar zijn voor de ontwerp functie:

- [Voor beeld 1-regressie: de prijs van een auto voors pellen](how-to-designer-sample-regression-automobile-price-basic.md)
- [Voor beeld 3: classificatie met functie selectie: inkomen voor spelling](how-to-designer-sample-classification-predict-income.md)
- [Voor beeld 4-classificatie: krediet risico voors pellen (kosten gevoelig)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [Voor beeld 5-classificatie: voor spel verloop](how-to-designer-sample-classification-churn.md)
- [Voor beeld 6: classificatie: voor spel vertraging van de vlucht](how-to-designer-sample-classification-flight-delay.md)
- [Voor beeld 7-tekst classificatie: Wikipedia SP 500-gegevensset](how-to-designer-sample-text-classification.md)
