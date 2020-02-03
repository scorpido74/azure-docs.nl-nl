---
title: 'Ontwerp functie: voor beeld van krediet risico voors pellen'
titleSuffix: Azure Machine Learning
description: Bouw een classificatie en gebruik aangepaste python-scripts om het krediet risico te voors pellen met behulp van Azure Machine Learning Designer.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.date: 12/25/2019
ms.openlocfilehash: ed8ee9b1c711ee0056377154379b8df56e0785df
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964597"
---
# <a name="build-a-classifier--use-python-scripts-to-predict-credit-risk-using-azure-machine-learning-designer"></a>Een classificatie bouwen & python-scripts gebruiken om het krediet risico te voors pellen met behulp van Azure Machine Learning Designer

**Voor beeld van Designer (preview) 4**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

In dit artikel wordt beschreven hoe u een complexe machine learning pijp lijn bouwt met behulp van de ontwerp functie (preview). U leert hoe u aangepaste logica implementeert met behulp van python-scripts en meerdere modellen vergelijkt om de beste optie te kiezen.

In dit voor beeld wordt een classificatie gebruikt om het krediet risico te voors pellen met behulp van informatie over krediet aanvragen, zoals krediet geschiedenis, leeftijd en aantal credit cards. U kunt echter de concepten in dit artikel Toep assen om uw eigen machine learning problemen op te lossen.

Hier is de voltooide grafiek voor deze pijp lijn:

[Grafiek van de pijp lijn ![](./media/how-to-designer-sample-classification-credit-risk-cost-sensitive/graph.png)](./media/how-to-designer-sample-classification-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [aml-ui-prereq](../../includes/aml-ui-prereq.md)]

4. Klik op voor beeld 4 om het te openen.

## <a name="data"></a>Gegevens

In dit voor beeld wordt gebruikgemaakt van de Duitse creditcard gegevensset uit de UC Irvine-opslag plaats. Het bevat 1.000 voor beelden met 20 functies en één label. Elk voor beeld vertegenwoordigt een persoon. De 20 functies omvatten de numerieke en categorische-functies. Zie de [website van icb's](https://archive.ics.uci.edu/ml/datasets/Statlog+%28German+Credit+Data%29)voor meer informatie over de gegevensset. De laatste kolom is het label, dat het krediet risico aanduidt en slechts twee mogelijke waarden heeft: hoog credit risico = 2 en laag krediet risico = 1.

## <a name="pipeline-summary"></a>Pijplijn overzicht

In deze pijp lijn vergelijkt u twee verschillende benaderingen voor het genereren van modellen om dit probleem op te lossen:

- Training met de oorspronkelijke gegevensset.
- Training met een gerepliceerde gegevensset.

Met beide benaderingen evalueert u de modellen met behulp van de test gegevensset met replicatie om ervoor te zorgen dat de resultaten worden uitgelijnd met de functie cost. Test twee classificaties met beide benaderingen: **twee klasse-ondersteunings vector machines** en **twee klassen met een Geboostte beslissings structuur**.

De kosten voor een slechtere classificatie van een voor beeld met een laag risico zijn 1 en de kosten voor een slechtere voor beeld van een hoog risico zijn 5. We gebruiken een **python-script** module voor het uitvoeren van een account voor deze niet-geclassificeerde kosten.

Hier volgt de grafiek van de pijp lijn:

[Grafiek van de pijp lijn ![](./media/how-to-designer-sample-classification-credit-risk-cost-sensitive/graph.png)](./media/how-to-designer-sample-classification-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="data-processing"></a>Gegevensverwerking

Begin met het toevoegen van de **meta gegevens editor** module om kolom namen toe te voegen om de standaard kolom namen te vervangen door meer herken bare namen die zijn opgehaald uit de beschrijving van de gegevensset op de ICB-site. Geef de nieuwe kolom namen op als door komma's gescheiden waarden in het veld **nieuwe kolom** naam van de **meta gegevens editor**.

Genereer vervolgens de trainings-en test sets die worden gebruikt voor het ontwikkelen van het risico Voorspellings model. Splits de oorspronkelijke gegevensset in trainings-en test sets van dezelfde grootte met behulp van de module **Split data** . Als u sets van gelijke grootte wilt maken, stelt u het **gedeelte van de rijen in de eerste optie voor de uitvoer gegevensset in** op 0,7.

### <a name="generate-the-new-dataset"></a>De nieuwe gegevensset genereren

Omdat de kosten voor underestimating-risico hoog zijn, stelt u de kosten voor een onzekere classificatie in, zoals:

- Voor scenario's met een hoog risico die verkeerd zijn geclassificeerd als laag risico: 5
- Voor scenario's met een laag risico die zijn geclassificeerd als hoog risico: 1

Genereer een nieuwe gegevensset om deze kosten functie weer te geven. In de nieuwe gegevensset wordt elk voor beeld met een hoog risico vijf keer gerepliceerd, maar het aantal voor beelden met een laag risico wordt niet gewijzigd. Splits de gegevens in trainingen en test gegevens sets vóór replicatie om te voor komen dat dezelfde rij in beide sets wordt weer gegeven.

Als u de gegevens met een hoog risico wilt repliceren, plaatst u deze python-code in een script module voor het uitvoeren van een **python** :

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

Voor de **computer algoritme met twee klassen ondersteuning zijn** genormaliseerde gegevens vereist. Gebruik daarom de module **normaliseren gegevens** om de bereiken van alle numerieke functies te normaliseren met een `tanh` trans formatie. Met een `tanh` trans formatie worden alle numerieke functies geconverteerd naar waarden binnen een bereik van 0 tot en met 1, terwijl de algehele verdeling van waarden wordt behouden.

De **ondersteunings vector-machine module met twee klassen ondersteunt** teken reeks functies en converteert deze naar categorische-functies en vervolgens naar binaire functies met de waarde nul of een. U hoeft deze functies dus niet te normaliseren.

## <a name="models"></a>Modellen

Omdat u twee classificaties hebt toegepast, een SVM ( **Support Vector machine) met twee klassen** en een **Geboostte beslissings structuur van twee klassen**, en twee gegevens sets, genereert u een totaal van vier modellen:

- SVM is getraind met de oorspronkelijke gegevens.
- SVM heeft getraind met gerepliceerde gegevens.
- Gestimuleerde beslissings structuur die is getraind met de oorspronkelijke gegevens.
- Gestimuleerde beslissings structuur die is getraind met gerepliceerde gegevens.

In dit voor beeld wordt de standaard data Science-werk stroom gebruikt om de modellen te maken, te trainen en te testen:

1. Initialiseer de leer algoritmen met behulp van een **Vector machine met twee klassen** en een **Geboostte beslissings structuur van twee klassen**.
1. **Train model** gebruiken om het algoritme toe te passen op de gegevens en het daad werkelijke model te maken.
1. Gebruik het **score model** voor het produceren van scores met behulp van de test voorbeelden.

Het volgende diagram toont een deel van deze pijp lijn, waarin de oorspronkelijke en gerepliceerde trainings sets worden gebruikt om twee verschillende SVM-modellen te trainen. **Train model** is verbonden met de Trainingsset en het **score model** is verbonden met de testset.

![Pipeline-grafiek](./media/how-to-designer-sample-classification-credit-risk-cost-sensitive/score-part.png)

In de evaluatie fase van de pijp lijn berekent u de nauw keurigheid van elk van de vier modellen. Voor deze pijp lijn gebruikt u **model evalueren** om voor beelden te vergelijken met dezelfde indelings kosten.

In de module **Evaluate model** kunnen de prestatie gegevens voor Maxi maal twee gescoorde modellen worden berekend. U kunt dus één exemplaar van het **Evaluate-model** gebruiken om de twee SVM-modellen en een ander exemplaar van het **Evalueer model** te evalueren om de twee versterkte beslissings structuur modellen te evalueren.

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

Als u de resultaten van de pijp lijn wilt weer geven, klikt u met de rechter muisknop op de Visuale-uitvoer van de laatste **select columns in dataset** -module.

![Uitvoer visualiseren](media/how-to-designer-sample-classification-credit-risk-cost-sensitive/sample4-lastselect-1225.png)

De eerste kolom bevat het machine learning algoritme dat is gebruikt voor het genereren van het model.

De tweede kolom geeft het type van de Trainingsset aan.

De derde kolom bevat de waarde voor de kosten gevoelige nauw keurigheid.

Vanuit deze resultaten kunt u zien dat de beste nauw keurigheid wordt geboden door het model dat is gemaakt met **twee klassen Support Vector machine** en getraind in de gegevensset van de gerepliceerde training.

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

Bekijk de andere voor beelden die beschikbaar zijn voor de ontwerp functie:

- [Voor beeld 1-regressie: de prijs van een auto voors pellen](how-to-designer-sample-regression-automobile-price-basic.md)
- [Voor beeld 2-regressie: vergelijkings algoritmen voor de voor spelling van prijzen voor auto Mobile](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [Voor beeld 3: classificatie met functie selectie: inkomen voor spelling](how-to-designer-sample-classification-predict-income.md)
- [Voor beeld 5-classificatie: voor spel verloop](how-to-designer-sample-classification-churn.md)
- [Voor beeld 6: classificatie: voor spel vertraging van de vlucht](how-to-designer-sample-classification-flight-delay.md)
- [Voor beeld 7-tekst classificatie: Wikipedia SP 500-gegevensset](how-to-designer-sample-text-classification.md)
