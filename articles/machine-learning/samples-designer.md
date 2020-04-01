---
title: Voorbeeld van ontwerppijplijnen
titleSuffix: Azure Machine Learning
description: Gebruik voorbeelden in Azure Machine Learning-ontwerper om uw machine learning-pijplijnen te starten.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 03/29/2020
ms.openlocfilehash: f9a8b0a4c51024d91e517db2f6ae10a4dba62384
ms.sourcegitcommit: 0553a8b2f255184d544ab231b231f45caf7bbbb0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2020
ms.locfileid: "80389338"
---
# <a name="designer-sample-pipelines"></a>Voorbeeldpijplijnen voor ontwerpers

Gebruik de ingebouwde voorbeelden in Azure Machine Learning-ontwerper om snel aan de slag te gaan met het bouwen van uw eigen machine learning-pijplijnen. De [GitHub-repository](https://github.com/Azure/MachineLearningDesigner) van Azure Machine Learning-ontwerper bevat gedetailleerde documentatie om u te helpen een aantal veelvoorkomende machine learning-scenario's te begrijpen.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://aka.ms/AMLFree)aan.
* Een Azure Machine Learning-werkruimte met de Enterprise SKU.


## <a name="how-to-use-sample-pipelines"></a>Voorbeeldpijplijnen gebruiken

De ontwerper slaat een kopie van de voorbeeldpijplijnen op in uw werkruimte in uw studio. U de pijplijn bewerken om deze aan te passen aan uw behoeften en deze op te slaan als uw eigen pijplijn. Gebruik ze als uitgangspunt om uw projecten een vliegende start te geven.

### <a name="open-a-sample-pipeline"></a>Een voorbeeldpijplijn openen

1. Meld u aan bij <a href="https://ml.azure.com?tabs=jre" target="_blank">ml.azure.com</a>en selecteer de werkruimte waarmee u wilt werken.

1. Selecteer **Ontwerper**.

1. Selecteer een voorbeeldpijplijn onder de sectie **Nieuwe pijplijn.**

    Selecteer **Meer voorbeelden weergeven** voor een volledige lijst met voorbeelden.

### <a name="submit-a-pipeline-run"></a>Een pijplijnrun verzenden

Als u een pijplijn wilt uitvoeren, moet u eerst het standaard gegevensdoel instellen om de pijplijn op uit te voeren.

1. Selecteer in het deelvenster **Instellingen** rechts van het canvas de optie **Rekendoel selecteren**.

1. Selecteer in het dialoogvenster dat wordt weergegeven een bestaand rekendoel of maak een nieuw doel. Selecteer **Opslaan**.

1. Selecteer **Verzenden** boven aan het canvas om een pijplijnrun in te dienen.

Afhankelijk van de voorbeeldpijplijn en rekeninstellingen kan het enige tijd duren voordat de uitvoering en de berekeningsinstellingen zijn voltooid. De standaardcompute-instellingen hebben een minimale nodegrootte van 0, wat betekent dat de ontwerper resources moet toewijzen nadat deze niet is inactief. Herhaalde pijplijnuitvoeringen nemen minder tijd in beslag omdat de compute resources al zijn toegewezen. Daarnaast gebruikt de ontwerper in de cache resultaten voor elke module om de efficiëntie verder te verbeteren.


### <a name="review-the-results"></a>De resultaten bekijken

Nadat de pijplijn is uitgevoerd, u de pijplijn bekijken en de uitvoer voor elke module bekijken voor meer informatie.

Gebruik de volgende stappen om module-uitgangen weer te geven:

1. Selecteer een module in het canvas.

1. Selecteer **Uitvoer + logboeken**in het deelvenster moduledetails rechts van het canvas. Selecteer het ![pictogram](./media/tutorial-designer-automobile-price-train-score/visualize-icon.png) grafiekpictogram visualiseren om de resultaten van elke module te zien. 

Gebruik de voorbeelden als uitgangspunt voor enkele van de meest voorkomende machine learning-scenario's.

## <a name="regression-samples"></a>Regressiemonsters

Meer informatie over de ingebouwde regressievoorbeelden.

| Voorbeeldtitel | Beschrijving | 
| --- | --- |
| [Voorbeeld 1: Regressie - Automobiele prijsvoorspelling (Basic)](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-regression-automobile-price-basic.md) | Voorspel autoprijzen met behulp van lineaire regressie. |
| [Voorbeeld 2: Regressie - Automobiele prijsvoorspelling (geavanceerd)](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-regression-automobile-price-compare-algorithms.md) | Voorspel autoprijzen met behulp van beslissingsbos en verhoogde beslissingen voor de regressies van de beslissingsbomen. Vergelijk modellen om het beste algoritme te vinden.

## <a name="classification-samples"></a>Indelingsmonsters

Meer informatie over de ingebouwde classificatievoorbeelden. U meer informatie over de voorbeelden zonder documentatie links door het openen van de monsters en het bekijken van de module opmerkingen in plaats daarvan.

| Voorbeeldtitel | Beschrijving | 
| --- | --- |
| [Voorbeeld 3: Binaire classificatie met functieselectie - Inkomensvoorspelling](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-predict-income.md) | Voorspel inkomsten als hoog of laag, met behulp van een twee-klasse gestimuleerd beslissingboom. Gebruik pearsoncorrelatie om functies te selecteren.
| [Voorbeeld 4: Binaire classificatie met aangepast Python-script - Kredietrisicovoorspelling](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-credit-risk-cost-sensitive.md) | Classificeer kredietaanvragen als hoog of laag risico. Gebruik de module Python Script uitvoeren om uw gegevens te wegen.
| [Voorbeeld 5: Binaire classificatie - Voorspelling van klantrelatie](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-churn.md) | Voorspel klantverloop met behulp van twee-klasse gestimuleerde beslissingsbomen. Gebruik SMOTE om bevooroordeelde gegevens te bemonsteren.
| [Voorbeeld 7: Tekstclassificatie - Wikipedia SP 500-gegevensset](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-text-classification.md) | Classificeren van bedrijfstypen uit Wikipedia-artikelen met multiclass logistieke regressie. |
| Voorbeeld 12: Classificatie van meerdere klassen - Letterherkenning | Maak een ensemble van binaire classificaties om geschreven letters te classificeren. |

## <a name="recommender-samples"></a>Voorbeelden van recommenders

Meer informatie over de ingebouwde recommender-voorbeelden. U meer informatie over de voorbeelden zonder documentatie links door het openen van de monsters en het bekijken van de module opmerkingen in plaats daarvan.

| Voorbeeldtitel | Beschrijving | 
| --- | --- |
| Voorbeeld 10: Aanbeveling - Tweets over filmwaardering | Bouw een filmrecommender engine van filmtitels en rating. |

## <a name="utility-samples"></a>Utility monsters

Meer informatie over de voorbeelden die gebruikmaken en functies voor machine learning demonstreren. U meer informatie over de voorbeelden zonder documentatie links door het openen van de monsters en het bekijken van de module opmerkingen in plaats daarvan.

| Voorbeeldtitel | Beschrijving | 
| --- | --- |
| [Voorbeeld 6: Gebruik aangepast R-script - Voorspelling van vluchtvertraging](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-flight-delay.md) |
| Voorbeeld 8: Cross Validation for Binary Classification - Adult Income Prediction | Gebruik kruisvalidatie om een binaire classificatie voor volwassen inkomsten te bouwen.
| Voorbeeld 9: Permutatie Functie Belang | Gebruik het belang van de permutatiefunctie om belangrijke scores voor de testgegevensset te berekenen. 
| Voorbeeld 11: Parameters afstemmen voor binaire classificatie - Voorspelling van volwassen inkomsten | Gebruik Tune Model Hyperparameters om optimale hyperparameters te vinden om een binaire classificatie te bouwen. |

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het bouwen en implementeren van machine learning-modellen met [Tutorial: Voorspel de prijs van auto's met de ontwerper](tutorial-designer-automobile-price-train-score.md)
