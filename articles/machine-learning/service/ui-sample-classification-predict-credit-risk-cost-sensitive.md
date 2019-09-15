---
title: '#4 voor beeld van visuele interface: Classificatie om het krediet risico te voors pellen (kosten gevoelig)'
titleSuffix: Azure Machine Learning
description: In dit artikel wordt beschreven hoe u een complex machine learning experiment bouwt met behulp van de visuele interface. U leert hoe u aangepaste python-scripts implementeert en hoe u meerdere modellen vergelijkt om de beste optie te kiezen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: 977ff101b0f697a48b3e5595834c98fef0f1119a
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/14/2019
ms.locfileid: "70997033"
---
# <a name="sample-4---classification-predict-credit-risk-cost-sensitive"></a>Voor beeld 4-classificatie: Voor speld krediet risico (kosten gevoelig)

In dit artikel wordt beschreven hoe u een complex machine learning experiment bouwt met behulp van de visuele interface. U leert hoe u aangepaste logica implementeert met behulp van python-scripts en meerdere modellen vergelijkt om de beste optie te kiezen.

In dit voor beeld wordt een classificatie gebruikt om het krediet risico te voors pellen met behulp van informatie over krediet aanvragen, zoals krediet geschiedenis, leeftijd en aantal credit cards. U kunt echter de concepten in dit artikel Toep assen om uw eigen machine learning problemen op te lossen.

Als u net aan de slag gaat met machine learning, kunt u het [basis monster](ui-sample-classification-predict-credit-risk-basic.md) van de classificatie het eerst bekijken.

Hier volgt de voltooide grafiek voor dit experiment:

[![Grafiek van het experiment](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png)](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Selecteer de knop **openen** voor het experiment Sample 4:

    ![Het experiment openen](media/ui-sample-classification-predict-credit-risk-cost-sensitive/open-sample4.png)

## <a name="data"></a>Data

We gebruiken de Duitse creditcard gegevensset uit de UC Irvine-opslag plaats. Deze gegevensset bevat 1.000 voor beelden met 20 functies en één label. Elk voor beeld vertegenwoordigt een persoon. De 20 functies omvatten de numerieke en categorische-functies. Zie de [website van UCI](https://archive.ics.uci.edu/ml/datasets/Statlog+%28German+Credit+Data%29) voor meer informatie over de gegevensset. De laatste kolom is het label, dat het krediet risico aanduidt en slechts twee mogelijke waarden heeft: hoog credit risico = 2 en laag krediet risico = 1.

## <a name="experiment-summary"></a>Experiment overzicht

In dit experiment vergelijken we twee verschillende benaderingen voor het genereren van modellen om dit probleem op te lossen:

- Training met de oorspronkelijke gegevensset.
- Training met een gerepliceerde gegevensset.

Met beide benaderingen evalueren we de modellen met behulp van de test gegevensset met replicatie om ervoor te zorgen dat de resultaten worden uitgelijnd met de functie cost. We testen twee classificaties met beide benaderingen: **Ondersteuning voor vector machines met twee klassen** en een **Geboostte beslissings structuur van twee**klasse.

De kosten voor een slechtere classificatie van een voor beeld met een laag risico zijn 1 en de kosten voor een slechtere voor beeld van een hoog risico zijn 5. We gebruiken een **python-script** module voor het uitvoeren van een account voor deze niet-geclassificeerde kosten.

Hier volgt de grafiek van het experiment:

[![Grafiek van het experiment](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png)](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="data-processing"></a>Gegevensverwerking

We beginnen met het gebruik van de module **meta gegevens editor** om kolom namen toe te voegen om de standaard kolom namen te vervangen door meer herken bare namen die zijn opgehaald uit de beschrijving van de gegevensset op de ICB-site. We geven de nieuwe kolom namen op als door komma's gescheiden waarden in het veld **nieuwe kolom** naam van de **meta gegevens editor**.

Vervolgens genereren we de trainings-en test sets die worden gebruikt voor het ontwikkelen van het risico Voorspellings model. We splitsen de oorspronkelijke gegevensset in trainings-en test sets van dezelfde grootte met behulp van de module voor het **splitsen van gegevens** . Om sets van gelijke grootte te maken, stellen we het **gedeelte van de rijen in de eerste uitvoer gegevensset in** op 0,5.

### <a name="generate-the-new-dataset"></a>De nieuwe gegevensset genereren

Omdat de kosten voor underestimating-risico hoog zijn, stellen we de kosten voor een ongelijke classificatie in, zoals:

- Voor scenario's met een hoog risico die verkeerd zijn geclassificeerd als laag risico: 5
- Voor cases met een laag risico die verkeerd zijn geclassificeerd als hoog risico: 1

We genereren een nieuwe gegevensset om deze kosten functie weer te geven. In de nieuwe gegevensset wordt elk voor beeld met een hoog risico vijf keer gerepliceerd, maar het aantal voor beelden met een laag risico wordt niet gewijzigd. We splitsen de gegevens in trainingen en test gegevens sets vóór replicatie om te voor komen dat dezelfde rij in beide sets wordt weer gegeven.

Als u de gegevens met een hoog risico wilt repliceren, nemen we deze python-code op in een script module voor het uitvoeren van een **python** :

```Python
import pandas as pd

def azureml_main(dataframe1 = None, dataframe2 = None):

    df_label_1 = dataframe1[dataframe1.iloc[:, 20] == 1]
    df_label_2 = dataframe1[dataframe1.iloc[:, 20] == 2]

    result = df_label_1.append([df_label_2] * 5, ignore_index=True)
    return result,
```

De **script** module voor het uitvoeren van python repliceert zowel de gegevens sets training als test.

### <a name="feature-engineering"></a>Functie-engineering

Voor de **computer algoritme met twee klassen ondersteuning zijn** genormaliseerde gegevens vereist. Daarom gebruiken we de module **normaliseren gegevens** om de bereiken van alle numerieke functies te normaliseren met `tanh` een trans formatie. Met `tanh` een trans formatie worden alle numerieke functies geconverteerd naar waarden binnen een bereik van 0 tot en met 1, waarbij de algehele verdeling van waarden wordt behouden.

De **ondersteunings vector-machine module met twee klassen ondersteunt** teken reeks functies en converteert deze naar categorische-functies en vervolgens naar binaire functies met de waarde 0 of 1. Deze functies hoeven dus niet te worden genormaliseerd.

## <a name="models"></a>Modellen

Omdat we twee classificaties hebben toegepast, een SVM ( **Support Vector machine) met twee klassen** en een **Geboostte beslissings structuur van twee klassen**, en er ook twee gegevens sets worden gebruikt, genereren we een totaal van vier modellen:

- SVM is getraind met de oorspronkelijke gegevens.
- SVM heeft getraind met gerepliceerde gegevens.
- Gestimuleerde beslissings structuur die is getraind met de oorspronkelijke gegevens.
- Gestimuleerde beslissings structuur die is getraind met gerepliceerde gegevens.

We gebruiken de standaard experimentele werk stroom om de modellen te maken, te trainen en te testen:

1. Initialiseer de leer algoritmen met behulp van een **Vector machine met twee klassen** en een **Geboostte beslissings structuur van twee klassen**.
1. **Train model** gebruiken om het algoritme toe te passen op de gegevens en het daad werkelijke model te maken.
1. Gebruik het **score model** voor het produceren van scores met behulp van de test voorbeelden.

Het volgende diagram toont een deel van dit experiment, waarin de oorspronkelijke en gerepliceerde trainings sets worden gebruikt om twee verschillende SVM-modellen te trainen. **Train model** is verbonden met de Trainingsset en het **score model** is verbonden met de testset.

![Grafiek experimenteren](media/ui-sample-classification-predict-credit-risk-cost-sensitive/score-part.png)

In de evaluatie fase van het experiment berekenen we de nauw keurigheid van elk van de vier modellen. Voor dit experiment gebruiken we het **Evaluate-model** om voor beelden te vergelijken met dezelfde classificatie kosten.

In de module **Evaluate model** kunnen de prestatie gegevens voor Maxi maal twee gescoorde modellen worden berekend. We gebruiken daarom één exemplaar van het **Evalueer model** om de twee SVM-modellen en een ander exemplaar van het **Evalueer model** te evalueren om de twee versterkte beslissings structuur modellen te evalueren.

U ziet dat de gerepliceerde test-gegevensset wordt gebruikt als invoer voor het **score model**. Met andere woorden, de uiteindelijke nauw keurige scores zijn de kosten voor het ophalen van de labels fout.

## <a name="combine-multiple-results"></a>Meerdere resultaten combi neren

De module **Evaluate model** produceert een tabel met één rij die verschillende metrische gegevens bevat. Als u één set nauw keurige resultaten wilt maken, gebruiken we eerst **rijen toevoegen** om de resultaten te combi neren in één tabel. Vervolgens gebruiken we het volgende python-script in de **script module python uitvoeren** om de model naam en de Oefen benadering toe te voegen voor elke rij in de tabel met resultaten:

```Python
import pandas as pd

def azureml_main(dataframe1 = None, dataframe2 = None):

    new_cols = pd.DataFrame(
            columns=["Algorithm","Training"],
            data=[
                ["SVM", "weighted"],
                ["SVM", "unweighted"],
                ["Boosted Decision Tree","weighted"],
                ["Boosted Decision Tree","unweighted"]
            ])

    result = pd.concat([new_cols, dataframe1], axis=1)
    return result,
```

## <a name="results"></a>Resultaten

Als u de resultaten van het experiment wilt weer geven, klikt u met de rechter muisknop op de Visuale-uitvoer van de laatste **select columns in dataset** -module.

![Uitvoer visualiseren](media/ui-sample-classification-predict-credit-risk-cost-sensitive/result.png)

De eerste kolom bevat het machine learning algoritme dat is gebruikt voor het genereren van het model.
De tweede kolom geeft het type van de Trainingsset aan.
De derde kolom bevat de waarde voor de kosten gevoelige nauw keurigheid.

Vanuit deze resultaten kunt u zien dat de beste nauw keurigheid wordt geboden door het model dat is gemaakt met **twee klassen Support Vector machine** en getraind in de gegevensset van de gerepliceerde training.

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

Bekijk de andere voor beelden die beschikbaar zijn voor de visuele interface:

- [Voor beeld 1-regressie: De prijs van een auto voors pellen](ui-sample-regression-predict-automobile-price-basic.md)
- [Voor beeld 2-regressie: Algoritmen voor het voors pellen van prijzen vergelijken](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Voor beeld 3-classificatie: Krediet risico voors pellen](ui-sample-classification-predict-credit-risk-basic.md)
- [Voor beeld 5-classificatie: Verloop voors pellen](ui-sample-classification-predict-churn.md)
- [Voor beeld 6: classificatie: Vlucht vertragingen voors pellen](ui-sample-classification-predict-flight-delay.md)
