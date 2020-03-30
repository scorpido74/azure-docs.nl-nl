---
title: Batchvoorspellingen uitvoeren met de Azure Machine Learning-ontwerpfunctie (preview)
titleSuffix: Azure Machine Learning
description: Meer informatie over het trainen van een model en het instellen van een batchvoorspellingspijplijn met behulp van de ontwerper. Implementeer de pijplijn als een geparameteriseerde webservice, die vanuit elke HTTP-bibliotheek kan worden geactiveerd.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: peterlu
author: peterclu
ms.date: 02/24/2020
ms.custom: Ignite2019
ms.openlocfilehash: 01d69bffcf2c17abceba8ba2e0893360bead8b12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477218"
---
# <a name="run-batch-predictions-using-azure-machine-learning-designer-preview"></a>Batchvoorspellingen uitvoeren met de Azure Machine Learning-ontwerpfunctie (preview)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

In dit artikel leert u hoe u de ontwerper gebruiken om een batchvoorspellingspijplijn te maken. Met batchvoorspelling u continu grote gegevenssets on-demand scoren met behulp van een webservice die vanuit elke HTTP-bibliotheek kan worden geactiveerd.

In deze how-to leert u de volgende taken uit te voeren:

> [!div class="checklist"]
> * Een batch-gevolgtrekkingspijplijn maken en publiceren
> * Een eindpunt van een pijplijn verbruiken
> * Eindpuntversies beheren

Zie de bijbehorende [how-to.](how-to-run-batch-predictions.md)

## <a name="prerequisites"></a>Vereisten

Deze how-to gaat ervan uit dat je al een trainingspijplijn hebt. Voor een begeleide kennismaking met de ontwerper, voltooi [deel een van de ontwerper tutorial](tutorial-designer-automobile-price-train-score.md). 

## <a name="create-a-batch-inference-pipeline"></a>Een batch-inferencepijplijn maken

Uw trainingspijplijn moet ten minste één keer worden uitgevoerd om een inferencing pipeline te kunnen maken.

1. Ga naar het tabblad **Ontwerper** in uw werkruimte.

1. Selecteer de trainingspijplijn die het model traint dat u wilt gebruiken om voorspelling te doen.

1. **Verzend** de pijplijn.

    ![De pijplijn verzenden](./media/how-to-run-batch-predictions-designer/run-training-pipeline.png)

Nu de trainingspijplijn is uitgevoerd, u een batch-inferencepijplijn maken.

1. Selecteer naast **Verzenden**de nieuwe vervolgkeuzelijst **Inferencepipeline maken**.

1. Selecteer **batch-inferencepijplijn**.

    ![Batch-inferencepipeline maken](./media/how-to-run-batch-predictions-designer/create-batch-inference.png)
    
Het resultaat is een standaard batch-inference pijplijn. 

### <a name="add-a-pipeline-parameter"></a>Een parameter voor een pijplijn toevoegen

Als u voorspellingen wilt maken over nieuwe gegevens, u handmatig een andere gegevensset in deze ontwerpweergave van de pijplijn verbinden of een parameter voor uw gegevensset maken. Met parameters u het gedrag van het batch-inferencingproces wijzigen tijdens runtime.

In deze sectie maakt u een gegevenssetparameter om een andere gegevensset op te geven om voorspellingen op te doen.

1. Selecteer de gegevenssetmodule.

1. Rechts van het canvas verschijnt een deelvenster. Selecteer onder aan het deelvenster de optie **Instellen als parameter voor pijplijn**.
   
    Voer een naam voor de parameter in of accepteer de standaardwaarde.

## <a name="publish-your-batch-inferencing-pipeline"></a>Uw batch-inferencing-pijplijn publiceren

Nu bent u klaar om de inferencing pipeline te implementeren. Dit zal de pijplijn implementeren en beschikbaar maken voor anderen om te gebruiken.

1. Selecteer de knop **Publiceren**.

1. Vouw in het dialoogvenster dat wordt weergegeven de vervolgkeuzelijst uit voor **PipelineEndpoint**en selecteer **Nieuw pipelineEndpoint**.

1. Geef een eindpuntnaam en optionele beschrijving op.

    Onder aan het dialoogvenster ziet u de parameter die u hebt geconfigureerd met een standaardwaarde van de gegevensset-id die tijdens de training wordt gebruikt.

1. Selecteer **Publiceren**.

![Een pijplijn publiceren](./media/how-to-run-batch-predictions-designer/publish-inference-pipeline.png)


## <a name="consume-an-endpoint"></a>Een eindpunt consumeren

Nu hebt u een gepubliceerde pijplijn met een gegevenssetparameter. De pijplijn gebruikt het getrainde model dat in de trainingspijplijn is gemaakt om de gegevensset te scoren die u als parameter hebt opgegeven.

### <a name="submit-a-pipeline-run"></a>Een pijplijnrun verzenden 

In deze sectie stelt u een handmatige pijplijnrun in en wijzigt u de parameter pijplijn om nieuwe gegevens te scoren. 

1. Nadat de implementatie is voltooid, gaat u naar de sectie **Eindpunten.**

1. Selecteer **Pijplijneindpunten**.

1. Selecteer de naam van het eindpunt dat u hebt gemaakt.

![Koppeling Eindpunt](./media/how-to-run-batch-predictions-designer/manage-endpoints.png)

1. Selecteer **Gepubliceerde pijplijnen**.

    In dit scherm worden alle gepubliceerde pijplijnen weergegeven die onder dit eindpunt zijn gepubliceerd.

1. Selecteer de pijplijn die u hebt gepubliceerd.

    Op de pagina met details van de pijplijn ziet u een gedetailleerde uitvoeringsgeschiedenis en verbindingstekenreeksgegevens voor uw pijplijn. 
    
1. Selecteer **Verzenden** om een handmatige uitvoering van de pijplijn te maken.

    ![Pijplijngegevens](./media/how-to-run-batch-predictions-designer/submit-manual-run.png)
    
1. Wijzig de parameter om een andere gegevensset te gebruiken.
    
1. Selecteer **Verzenden** om de pijplijn uit te voeren.

### <a name="use-the-rest-endpoint"></a>Het REST-eindpunt gebruiken

U vindt informatie over het consumeren van pijplijneindpunten en gepubliceerde pijplijn in de sectie **Eindpunten.**

U vindt het REST-eindpunt van een pijplijneindpunt in het deelvenster Run-overzicht. Door het eindpunt aan te roepen, verbruikt u de standaard gepubliceerde pijplijn.

U ook een gepubliceerde pijplijn gebruiken op de pagina **Gepubliceerde pijplijnen.** Selecteer een gepubliceerde pijplijn en zoek het REST-eindpunt ervan. 

![Resteindpuntdetails](./media/how-to-run-batch-predictions-designer/rest-endpoint-details.png)

Om een REST-gesprek te voeren, hebt u een OAuth 2.0-verificatiekop van het type OAuth 2.0 nodig. Zie de volgende [zelfstudiesectie](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint) voor meer informatie over het instellen van verificatie op uw werkruimte en het maken van een parameterpunt REST-aanroep.

## <a name="versioning-endpoints"></a>Eindpunten voor versiebeheer

De ontwerper wijst een versie toe aan elke volgende pijplijn die u publiceert naar een eindpunt. U de pijplijnversie opgeven die u als parameter wilt uitvoeren in uw REST-aanroep. Als u geen versienummer opgeeft, gebruikt de ontwerper de standaardpijplijn.

Wanneer u een pijplijn publiceert, u ervoor kiezen om er de nieuwe standaardpijplijn voor dat eindpunt van te maken.

![Standaardpijplijn instellen](./media/how-to-run-batch-predictions-designer/set-default-pipeline.png)

U ook een nieuwe standaardpijplijn instellen op het tabblad **Gepubliceerde pijplijnen** van uw eindpunt.

![Standaardpijplijn instellen](./media/how-to-run-batch-predictions-designer/set-new-default-pipeline.png)

## <a name="next-steps"></a>Volgende stappen

Volg de [ontwerp-zelfstudie](tutorial-designer-automobile-price-train-score.md) om een regressiemodel te trainen en te implementeren.
''