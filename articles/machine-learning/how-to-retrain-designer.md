---
title: Modellen opnieuw trainen met behulp van Azure Machine Learning Designer (preview)
titleSuffix: Azure Machine Learning
description: Meer informatie over het opnieuw trainen van modellen met gepubliceerde pijp lijnen in Azure Machine Learning Designer (preview).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: keli19
author: likebupt
ms.date: 04/06/2020
ms.openlocfilehash: fb0122833dabe4030232efb3ca07557ce1826aff
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/08/2020
ms.locfileid: "82981898"
---
# <a name="retrain-models-with-azure-machine-learning-designer-preview"></a>Modellen opnieuw trainen met de Azure Machine Learning-ontwerpfunctie (preview)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

In dit artikel leert u hoe u Azure Machine Learning Designer kunt gebruiken om een machine learning model opnieuw te trainen. U gebruikt gepubliceerde pijp lijnen om uw werk stroom te automatiseren en para meters in te stellen voor het trainen van uw model op nieuwe gegevens. 

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Train een machine learning model.
> * Een pijplijn parameter maken.
> * Publiceer uw trainings pijplijn.
> * Train uw model opnieuw met nieuwe para meters.

## <a name="prerequisites"></a>Vereisten

* Een Azure Machine Learning-werk ruimte met de Enter prise-SKU.
* Voltooi deel 1 van deze instructie-to-reeks, zet [gegevens om in de ontwerp functie](how-to-designer-transform-data.md).

In dit artikel wordt ervan uitgegaan dat u basis kennis hebt van het bouwen van pijp lijnen in de ontwerp functie. Voor een begeleide Inleiding voltooit u de [zelf studie](tutorial-designer-automobile-price-train-score.md). 

### <a name="sample-pipeline"></a>Voorbeeld pijplijn

De pijp lijn die in dit artikel wordt gebruikt, is een gewijzigde versie van voor [Beeld 3: inkomen voor spelling](samples-designer.md#classification). De pijp lijn gebruikt de module [gegevens importeren](algorithm-module-reference/import-data.md) in plaats van de voor beeld-gegevensset om te laten zien hoe u modellen traint met uw eigen gegevens.

![Scherm opname van de aangepaste voorbeeld pijplijn met een vak waarin de module import data is gemarkeerd](./media/how-to-retrain-designer/modified-sample-pipeline.png)

## <a name="create-a-pipeline-parameter"></a>Een pijplijn parameter maken

Maak pijplijn parameters om variabelen dynamisch in runtime in te stellen. Voor dit voor beeld wijzigt u het pad van de trainings gegevens van een vaste waarde naar een para meter, zodat u uw model op verschillende gegevens kunt trainen.

1. Selecteer de module **gegevens importeren** .

    > [!NOTE]
    > In dit voor beeld wordt de module gegevens importeren gebruikt om toegang te krijgen tot gegevens in een geregistreerd Data Store. U kunt vergelijk bare stappen volgen als u alternatieve patronen voor gegevens toegang gebruikt.

1. Selecteer uw gegevens bron in het detail venster van de module, rechts van het canvas.

1. Geef het pad op naar uw gegevens. U kunt ook **Bladeren naar pad** selecteren om door de bestands structuur te bladeren. 

1. Mouseover het veld **pad** en selecteer het beletsel teken boven het veld met het **pad** dat wordt weer gegeven.

    ![Scherm afbeelding die laat zien hoe u een pijplijn parameter maakt](media/how-to-retrain-designer/add-pipeline-parameter.png)

1. Selecteer **toevoegen aan pijplijn parameter**.

1. Geef een parameter naam en een standaard waarde op.

   > [!NOTE]
   > U kunt de pijplijn parameters inspecteren en bewerken door het tandwiel pictogram **instellingen** naast de titel van uw pijp lijn concept te selecteren. 

1. Selecteer **Opslaan**.

1. Verzend de pijplijn uitvoering.

## <a name="find-a-trained-model"></a>Een getraind model zoeken

De ontwerp functie slaat alle pijplijn uitvoer, inclusief getrainde modellen, op in het standaard opslag account voor de werk ruimte. U kunt getrainde modellen ook rechtstreeks openen in de ontwerp functie:

1. Wacht totdat de pijp lijn is uitgevoerd.
1. Selecteer de module **Train Model**.
1. Selecteer in het detail venster van de module, rechts van het canvas, **uitvoer en logboeken**.
1. U kunt uw model vinden in **andere uitvoer** samen met uitvoerings Logboeken.
1. U kunt ook het pictogram **uitvoer weer geven** selecteren. Hier kunt u de instructie in het dialoog venster volgen om rechtstreeks naar uw gegevens opslag te gaan. 

![Scherm afbeelding die laat zien hoe het getrainde model kan worden gedownload](./media/how-to-retrain-designer/trained-model-view-output.png)

## <a name="publish-a-training-pipeline"></a>Een trainings pijplijn publiceren

Publiceer een pijp lijn naar een pijp lijn-eind punt om uw pijp lijnen in de toekomst eenvoudig te hergebruiken. Een pijplijn eindpunt maakt een REST-eind punt om in de toekomst pijp lijn aan te roepen. In dit voor beeld kunt u met het eind punt van de pijp lijn de pijp lijn opnieuw gebruiken om een model op verschillende gegevens te trainen.

1. Selecteer boven het Designing-canvas **publiceren** .
1. Selecteer of maak een pijplijn eindpunt.

   > [!NOTE]
   > U kunt meerdere pijp lijnen publiceren naar een enkel eind punt. Elke pijp lijn in een bepaald eind punt krijgt een versie nummer, dat u kunt opgeven wanneer u het eind punt van de pijp lijn aanroept.

1. Selecteer **publiceren**.

## <a name="retrain-your-model"></a>Uw model opnieuw trainen

Nu u een gepubliceerde trainings pijplijn hebt, kunt u deze gebruiken om uw model op nieuwe gegevens te trainen. U kunt vanuit de studio-werk ruimte of via een programma uitvoeringen vanuit een pijplijn eindpunt verzenden.

### <a name="submit-runs-by-using-the-designer"></a>Uitvoeringen verzenden met behulp van de ontwerp functie

Voer de volgende stappen uit om een uit te voeren hulp programma voor het uitvoeren van een verwerkings punt uit de ontwerp functie te verzenden:

1. Ga naar de pagina **eind punten** in uw studio-werk ruimte.
1. Selecteer het tabblad **pijplijn eindpunten** . Selecteer vervolgens het eind punt van de pijp lijn.
1. Selecteer het tabblad **gepubliceerde pijp lijnen** . Selecteer vervolgens de pijplijn versie die u wilt uitvoeren.
1. Selecteer **Verzenden**.
1. In het dialoog venster Setup kunt u de parameter waarden voor de run opgeven. Voor dit voor beeld werkt u het gegevenspad bij om uw model te trainen met een niet-Amerikaanse gegevensset.

![Scherm afbeelding die laat zien hoe u een door para meters uitgestelde pijplijn uitvoering kunt instellen in de ontwerp functie](./media/how-to-retrain-designer/published-pipeline-run.png)

### <a name="submit-runs-by-using-code"></a>Uitvoeringen verzenden met behulp van code

U kunt het REST-eind punt van een gepubliceerde pijp lijn vinden in het deel venster Overzicht. Als u het eind punt aanroept, kunt u de gepubliceerde pijp lijn opnieuw trainen.

Als u een REST-aanroep wilt uitvoeren, hebt u een OAuth 2,0 Bearer-type verificatie-header nodig. Zie voor meer informatie over het instellen van verificatie voor uw werk ruimte en het maken van een geparametriseerde REST-aanroep een [Azure machine learning pijp lijn bouwen voor batch scores](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint).

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u een gepara meter trainings pijplijn-eind punt maakt met behulp van de ontwerp functie.

Voor een volledig overzicht van hoe u een model kunt implementeren voor het maken van voor spellingen, raadpleegt u de [zelf studie over ontwerpen](tutorial-designer-automobile-price-train-score.md) voor het trainen en implementeren van een regressie model.
