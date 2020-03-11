---
title: Voorbeeld pijplijnen
titleSuffix: Azure Machine Learning
description: Gebruik voor beelden in Azure Machine Learning Designer om uw machine learning pijp lijnen te starten.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 03/10/2020
ms.openlocfilehash: 82476b9cc8d92c815df602496ed3dcb33014a4fd
ms.sourcegitcommit: b8d0d72dfe8e26eecc42e0f2dbff9a7dd69d3116
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2020
ms.locfileid: "79037400"
---
# <a name="designer-sample-pipelines"></a>Voorbeeld pijplijnen voor Designer

Gebruik de ingebouwde voor beelden in Azure Machine Learning Designer om snel aan de slag te gaan met het bouwen van uw machine learning pijp lijnen. De GitHub- [opslag plaats](https://github.com/Azure/MachineLearningDesigner) van Azure machine learning Designer bevat gedetailleerde documentatie waarmee u een aantal algemene machine learning scenario's kunt begrijpen.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://aka.ms/AMLFree) aan.
* Een Azure Machine Learning-werk ruimte met de Enter prise-SKU.


## <a name="how-to-use-sample-pipelines"></a>Voorbeeld pijplijnen gebruiken

De ontwerp functie slaat een kopie van de voorbeeld pijplijnen op in uw studio-werk ruimte. U kunt de pijp lijn bewerken om deze aan uw behoeften aan te passen en deze als uw eigen toepassing op te slaan. Gebruik deze als uitgangs punt om uw projecten op de slag te brengen.

1. Meld u aan bij <a href="https://ml.azure.com?tabs=jre" target="_blank">ml.Azure.com</a>en selecteer de werk ruimte waarmee u wilt werken.

1. Selecteer **ontwerp functie**.

1. Selecteer een voorbeeld pijplijn in het gedeelte **nieuwe pijp lijn** .

    Selecteer **meer voor beelden weer geven** voor een volledige lijst met voor beelden.

## <a name="regression-samples"></a>Regressie voorbeelden

Meer informatie over de ingebouwde regressie voorbeelden.

| Voorbeeldtitel | Beschrijving | 
| --- | --- |
| [Voor beeld 1: regressie-auto-prijs voorspelling (basis)](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-regression-automobile-price-basic.md) | Prijs van de auto voors pellen met behulp van lineaire regressie. |
| [Voor beeld 2: regressie-prijs voorspellingen voor auto (Geavanceerd)](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-regression-automobile-price-compare-algorithms.md) | De prijzen van auto's voors pellen met behulp van het besluitvormings forest en de versterkte beslissings structuur regressors gevonden Vergelijk modellen om het beste algoritme te vinden.

## <a name="classification-samples"></a>Classificatie voorbeelden

Meer informatie over de ingebouwde classificatie voorbeelden. U vindt meer informatie over de voor beelden zonder documentatie koppelingen door de voor beelden te openen en in plaats daarvan de module opmerkingen weer te geven.

| Voorbeeldtitel | Beschrijving | 
| --- | --- |
| [Voor beeld 3: binaire classificatie met functie selectie-inkomen voor spelling](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-predict-income.md) | Baten als hoog of laag voors pellen met behulp van een versterkte beslissings structuur met twee klassen. Gebruik Pearson-correlatie om onderdelen te selecteren.
| [Voor beeld 4: binaire classificatie met aangepast python-script-credit risico voor spelling](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-credit-risk-cost-sensitive.md) | Het classificeren van tegoed aanvragen als hoog of laag risico. Gebruik de script module python uitvoeren om uw gegevens te verlichten.
| [Voor beeld 5: binaire classificatie-relatie tussen klant en voor spelling](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-churn.md) | Het klant verloop voors pellen met behulp van geboostte beslissings structuren van twee klasse. Gebruik SMOTE om een voor beeld van zuivere gegevens te berekenen.
| [Voor beeld 7: tekst classificatie-Wikipedia SP 500-gegevensset](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-text-classification.md) | Bedrijfs typen classificeren vanuit Wikipedia-artikelen met een logistiek regressie van meer klasse. |
| Voor beeld 12: classificatie met meervoudige klassen-letter herkenning | Maak een ensemble van binaire classificaties om geschreven letters te classificeren. |

## <a name="recommender-samples"></a>Voor beelden van aanbevelingen

Meer informatie over de ingebouwde aanbevolen voor beelden. U vindt meer informatie over de voor beelden zonder documentatie koppelingen door de voor beelden te openen en in plaats daarvan de module opmerkingen weer te geven.

| Voorbeeldtitel | Beschrijving | 
| --- | --- |
| Voor beeld 10: aanbeveling-film classificatie tweets | Bouw een film aanbevolen engine van film titels en classificatie. |

## <a name="utility-samples"></a>Voor beelden van hulpprogram ma's

Meer informatie over de voor beelden van machine learning-hulpprogram ma's en-functies. U vindt meer informatie over de voor beelden zonder documentatie koppelingen door de voor beelden te openen en in plaats daarvan de module opmerkingen weer te geven.

| Voorbeeldtitel | Beschrijving | 
| --- | --- |
| [Voor beeld 6: aangepaste R-script gebruiken voor spelling vertraging](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-flight-delay.md) |
| Voor beeld 8: Kruis validatie voor binaire classificatie-volwassenen inkomen voor spelling | Gebruik Kruis validatie om een binaire classificatie te maken voor volwassenen inkomen.
| Voor beeld 9: permutatie functie urgentie | Gebruik het belang van de permutatie functie om urgentie scores voor de test gegevensset te berekenen. 
| Voor beeld 11: para meters voor binaire classificatie afstemmen-volwassene baten voor spelling | Gebruik Tune model Hyper parameters om optimale Hyper parameters te vinden om een binaire classifier te bouwen. |

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het bouwen en implementeren van machine learning modellen met [de zelf studie: prijs van auto Mobile met de ontwerper](tutorial-designer-automobile-price-train-score.md)
