---
title: Modellen opnieuw trainen met Azure Machine Learning-ontwerper (voorbeeld)
titleSuffix: Azure Machine Learning
description: Meer informatie over het omscholen van modellen met gepubliceerde pijplijnen in Azure Machine Learning designer (preview).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: keli19
author: likebupt
ms.date: 02/24/2020
ms.openlocfilehash: 264b169eefde18880f50feae2554aa3ca7037b1f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79368159"
---
# <a name="retrain-models-with-azure-machine-learning-designer-preview"></a>Modellen opnieuw trainen met de Azure Machine Learning-ontwerpfunctie (preview)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

In dit artikel leert u hoe u Azure Machine Learning-ontwerper gebruiken om een machine learning-model om te scholen. Ontdek hoe u gepubliceerde pijplijnen gebruiken om machine learning-workflows te automatiseren voor omscholing.

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Train een machine learning model.
> * Maak een parameter voor pijplijnen.
> * Publiceer uw trainingspijplijn.
> * Train je model om.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://aka.ms/AMLFree)aan.
* Een Azure Machine Learning-werkruimte met de Enterprise SKU.

Dit artikel gaat ervan uit dat u basiskennis hebt van het bouwen van pijpleidingen in de ontwerper. Voor een begeleide kennismaking met de ontwerper, voltooi de [tutorial](tutorial-designer-automobile-price-train-score.md). 

### <a name="sample-pipeline"></a>Voorbeeldpijplijn

De pijplijn die in dit artikel wordt gebruikt, is een gewijzigde versie van de pijplijn die wordt gevonden in [voorbeeld 3: inkomensvoorspelling.](how-to-designer-sample-classification-predict-income.md) Het gebruikt de module [Gegevens importeren](algorithm-module-reference/import-data.md) in plaats van de voorbeeldgegevensset om u te laten zien hoe u een model trainen met behulp van uw eigen gegevens.

![Schermafbeelding van de gewijzigde voorbeeldpijplijn met een vak waarin de module Gegevens importeren wordt gemarkeerd](./media/how-to-retrain-designer/modified-sample-pipeline.png)

## <a name="train-a-machine-learning-model"></a>Train een machine learning model

Om een model om te scholen, hebt u een eerste model nodig. In deze sectie leert u hoe u een model trainen en toegang krijgen tot het opgeslagen model met behulp van de ontwerper.

1. Selecteer de module **Gegevens importeren.**
1. Geef in het deelvenster Eigenschappen een gegevensbron op.

   ![Schermafbeelding van een voorbeeldconfiguratie van de module Gegevens importeren](./media/how-to-retrain-designer/import-data-settings.png)

   In dit voorbeeld worden de gegevens opgeslagen in een [Azure-gegevensarchief.](how-to-access-data.md) Als u nog geen datastore hebt, u er nu een maken door **Nieuw gegevensarchief te**selecteren.

1. Geef het pad naar uw gegevens op. U ook **Pad bladeren** selecteren om naar uw gegevensarchief te bladeren. 
1. Selecteer **Uitvoeren** boven aan het canvas.
    
   > [!NOTE]
   > Als u al een standaardcompute voor dit pijplijnconcept hebt ingesteld, wordt de pijplijn automatisch uitgevoerd. Anders u de aanwijzingen in het instellingenvenster volgen om er nu een in te stellen.

### <a name="find-your-trained-model"></a>Vind uw getrainde model

De ontwerper slaat alle pijplijnuitvoer, inclusief getrainde modellen, op in het standaardopslagaccount. U ook rechtstreeks toegang krijgen tot het getrainde model in de ontwerper:

1. Wacht tot de pijplijn is voltooid.
1. Selecteer de module **Train Model**.
1. Selecteer **Uitvoer+logboeken**in het instellingenvenster .
1. Selecteer het pictogram **Uitvoer weergeven** en volg de instructie in het pop-upvenster om het getrainde model te vinden.

![Schermafbeelding van het downloaden van het getrainde model](./media/how-to-retrain-designer/trained-model-view-output.png)

## <a name="create-a-pipeline-parameter"></a>Een parameter voor pijplijnmaken

Voeg pijplijnparameters toe om variabelen dynamisch in te stellen bij uitvoering. Voeg voor deze pijplijn een parameter toe voor het trainingsgegevenspad, zodat u uw model omscholen op een nieuwe gegevensset.

1. Selecteer de module **Gegevens importeren.**
1. Selecteer in het instellingenvenster de ellipsen boven het veld **Pad.**
1. Selecteer **Toevoegen aan pijplijnparameter**.
1. Geef een parameternaam en een standaardwaarde op.

   > [!NOTE]
   > U de pijplijnparameters inspecteren en bewerken door het tandwielpictogram **Instellingen** naast de titel van uw pijplijnconcept te selecteren. 

![Schermafbeelding van het maken van een pijplijnparameter](media/how-to-retrain-designer/add-pipeline-parameter.png)

## <a name="publish-a-training-pipeline"></a>Een trainingspijplijn publiceren

Wanneer u een pijplijn publiceert, wordt een eindpunt van de pijplijn veroorzaakt. Met pijplijneindpunten u uw pijplijnen hergebruiken en beheren voor herhaalbaarheid en automatisering. In dit voorbeeld hebt u uw pijplijn ingesteld voor omscholing.

1. Selecteer **Publiceren** boven het ontwerpcanvas.
1. Selecteer of maak een eindpunt van de pijplijn.

   > [!NOTE]
   > U meerdere pijplijnen publiceren naar één eindpunt. Elke pijplijn in het eindpunt krijgt een versienummer, dat u opgeven wanneer u het eindpunt van de pijplijn aanroept.

1. Selecteer **Publiceren**.

## <a name="retrain-your-model"></a>Uw model omscholen

Nu u een gepubliceerde trainingspijplijn hebt, u deze gebruiken om uw model om te scholen met behulp van nieuwe gegevens. U runs vanaf een pijplijneindpunt indienen vanuit de Azure-portal of deze programmatisch indienen.

### <a name="submit-runs-by-using-the-designer"></a>Runs verzenden met behulp van de ontwerper

Gebruik de volgende stappen om een pijplijneindpunt van de ontwerper in te dienen:

1. Ga naar de pagina **Eindpunten.**
1. Selecteer het tabblad **Eindpunten van pijplijn.**
1. Selecteer het eindpunt van de pijplijn.
1. Selecteer het tabblad **Gepubliceerde pijplijnen.**
1. Selecteer de pijplijn die u wilt uitvoeren.
1. Selecteer **Indienen**.
1. In het dialoogvenster Setup u een nieuwe waarde opgeven voor de waarde van het invoergegevenspad. Deze waarde verwijst naar uw nieuwe gegevensset.

![Schermafbeelding van het instellen van een parameterpijplijn in de ontwerper](./media/how-to-retrain-designer/published-pipeline-run.png)

### <a name="submit-runs-by-using-code"></a>Voert uitvoeringen verzenden met behulp van code

U vindt het REST-eindpunt van een gepubliceerde pijplijn in het overzichtspaneel. Door het eindpunt aan te roepen, u de gepubliceerde pijplijn opnieuw trainen.

Om een REST-gesprek te voeren, hebt u een OAuth 2.0-verificatiekop van het type OAuth 2.0 nodig. Zie [Een Azure Machine Learning-pijplijn maken voor batchscores voor](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint)informatie over het instellen van verificatie op uw werkruimte en het maken van een parameterpunt rest.

## <a name="next-steps"></a>Volgende stappen

Volg de [ontwerp-zelfstudie](tutorial-designer-automobile-price-train-score.md) om een regressiemodel te trainen en te implementeren.
