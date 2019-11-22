---
title: Batch voorspellingen uitvoeren met behulp van Azure Machine Learning Designer (preview)
titleSuffix: Azure Machine Learning
description: Meer informatie over het trainen van een model en het instellen van een batch voorspelling-pijp lijn met behulp van de ontwerp functie. Implementeer de pijp lijn als een webservice met para meters die kan worden geactiveerd vanuit elke HTTP-bibliotheek.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.reviewer: trbye
ms.author: trbye
author: trevorbye
ms.date: 11/19/2019
ms.custom: Ignite2019
ms.openlocfilehash: a44ac821271cc07a790c380287391f41650ced19
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74276742"
---
# <a name="run-batch-predictions-using-azure-machine-learning-designer"></a>Batch voorspellingen uitvoeren met behulp van Azure Machine Learning Designer
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

In deze procedure leert u hoe u de ontwerp functie kunt gebruiken om een model te trainen en een batch Voorspellings pijplijn en-webservice in te stellen. Met batch-voor spelling kunt u getrainde modellen op basis van een doorlopende en on-demand Score instellen voor grote gegevens sets, optioneel geconfigureerd als een webservice die kan worden geactiveerd vanuit elke HTTP-bibliotheek. 

Voor het instellen van batch Score Services met behulp van de SDK raadpleegt u de bijbehorende [procedures](how-to-run-batch-predictions.md).

In deze procedure leert u de volgende taken:

> [!div class="checklist"]
> * Een Basic ML-experiment in een pijp lijn maken
> * Een door para meters batch-verwerkings pijplijn maken
> * Pijp lijnen hand matig of vanuit een REST-eind punt beheren en uitvoeren

## <a name="prerequisites"></a>Vereisten

1. Als u nog geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer de [gratis of betaalde versie van de Azure machine learning](https://aka.ms/AMLFree).

1. Een [werk ruimte](tutorial-1st-experiment-sdk-setup.md)maken.

1. Meld u aan bij [Azure machine learning Studio](https://ml.azure.com/).

In deze procedure wordt ervan uitgegaan dat u basis kennis hebt van het bouwen van een eenvoudige pijp lijn in de ontwerp functie. Voltooi de [zelf studie](tutorial-designer-automobile-price-train-score.md)voor een begeleide Inleiding tot de ontwerp functie. 

## <a name="create-a-pipeline"></a>Een pijplijn maken

Voor het maken van een pijp lijn voor een batch-deinterferentie, hebt u eerst een machine learning experiment nodig. Als u er een wilt maken, gaat u naar het tabblad **ontwerpen** in uw werk ruimte en maakt u een nieuwe pijp lijn door de optie **gemakkelijk te gebruiken vooraf gedefinieerde modules** te selecteren.

![Introductie pagina ontwerpen](media/how-to-run-batch-predictions-designer/designer-batch-scoring-1.png)

Hier volgt een eenvoudig machine learning model voor demonstratie doeleinden. De gegevens zijn een geregistreerde gegevensset die is gemaakt op basis van de Azure open data sets diabetes-gegevens. Zie de [sectie How-to](how-to-create-register-datasets.md#create-datasets-with-azure-open-datasets) voor het registreren van gegevens sets van Azure open gegevens sets. De gegevens worden gesplitst in trainings-en validatie sets en een versterkte beslissings structuur wordt getraind en gescoord. De pijp lijn moet ten minste één keer worden uitgevoerd om een interferentie pijplijn te kunnen maken. Klik op de knop **uitvoeren** om de pijp lijn uit te voeren.

![Eenvoudig experiment maken](media/how-to-run-batch-predictions-designer/designer-batch-scoring-2.png)

## <a name="create-a-batch-inference-pipeline"></a>Een pijp lijn voor een batch-deinterferentie maken

Nu de pijp lijn is uitgevoerd, is er een nieuwe optie beschikbaar naast **uitvoeren** en **publiceren** aangeroepen **pijp lijn maken**. Klik op de vervolg keuzelijst en selecteer **batch-interferentie pijplijn**.

![Pijp lijn voor batch-deinterferentie maken](media/how-to-run-batch-predictions-designer/designer-batch-scoring-5.png)

Het resultaat is een standaard pijp lijn voor batch-deinterferentie. Dit omvat een knoop punt voor de originele installatie van de pijplijn-experiment, een knoop punt voor onbewerkte gegevens voor waardering en een knoop punt om de onbewerkte gegevens te beoordelen op basis van uw oorspronkelijke pijp lijn.

![Pijp lijn voor de standaard batch-deinterferentie](media/how-to-run-batch-predictions-designer/designer-batch-scoring-6.png)

U kunt andere knoop punten toevoegen om het gedrag van het proces voor het afwijzen van batches te wijzigen. In dit voor beeld voegt u een knoop punt toe voor wille keurige steek proeven van de invoer gegevens vóór de score. Maak een **partitie en** een voor beeld van een knoop punt en plaats deze tussen de onbewerkte gegevens en Score knooppunten. Klik vervolgens op de **partitie en** het voor beeld-knoop punt om toegang te krijgen tot de instellingen en para meters.

![Nieuw knoop punt](media/how-to-run-batch-predictions-designer/designer-batch-scoring-7.png)

De *frequentie van de sampling* -para meter bepaalt het percentage van de oorspronkelijke gegevensset waaruit een wille keurig voor beeld moet worden genomen. Dit is een para meter die nuttig is om regel matig aan te passen, zodat u deze als een pijplijn parameter inschakelt. Pijplijn parameters kunnen tijdens runtime worden gewijzigd en kunnen worden opgegeven in een Payload-object wanneer de pijp lijn opnieuw wordt uitgevoerd vanuit een REST-eind punt. 

Als u dit veld wilt inschakelen als een pijplijn parameter, klikt u op het beletsel teken boven het veld en klikt u vervolgens op **toevoegen aan pijplijn parameter**. 

![Voorbeeld instellingen](media/how-to-run-batch-predictions-designer/designer-batch-scoring-8.png)

Geef vervolgens een naam en standaard waarde op voor de para meter. De naam wordt gebruikt om de para meter te identificeren en op te geven in een REST-aanroep.

![Pijplijnparameter](media/how-to-run-batch-predictions-designer/designer-batch-scoring-9.png)

## <a name="deploy-batch-inferencing-pipeline"></a>Pijp lijn voor het afnemen van batches implementeren

U bent nu klaar om de pijp lijn te implementeren. Klik op de knop **implementeren** . Hiermee wordt de interface geopend om een eind punt in te stellen. Klik op de vervolg keuzelijst en selecteer **nieuwe PipelineEndpoint**.

![Pijp lijn implementeren](media/how-to-run-batch-predictions-designer/designer-batch-scoring-10.png)

Geef het eind punt een naam en een optionele beschrijving. Onder aan de onderkant ziet u de para meter `sample-rate` die u hebt geconfigureerd met een standaard waarde van 0,8. Wanneer u klaar bent, klikt u op **implementeren**.

![Installatie-eind punt](media/how-to-run-batch-predictions-designer/designer-batch-scoring-11.png)

## <a name="manage-endpoints"></a>Eindpunten beheren 

Nadat de implementatie is voltooid, gaat u naar het tabblad **eind punten** en klikt u op de naam van het eind punt dat u zojuist hebt gemaakt.

![Eindpunt koppeling](media/how-to-run-batch-predictions-designer/designer-batch-scoring-12.png)

In dit scherm worden alle gepubliceerde pijp lijnen onder het specifieke eind punt weer gegeven. Klik op uw pijp lijn voor het deuwren.

![Pijp lijn afleiding](media/how-to-run-batch-predictions-designer/designer-batch-scoring-13.png)

De pagina Details van de pijp lijn bevat gedetailleerde uitvoerings geschiedenis en connection string informatie voor de pijp lijn. Klik op de knop **uitvoeren** om een hand matige uitvoering van de pijp lijn te maken.

![Pijp lijn Details](media/how-to-run-batch-predictions-designer/designer-batch-scoring-14.png)

In Setup uitvoeren kunt u een beschrijving voor de uitvoering opgeven en de waarde voor eventuele pijplijn parameters wijzigen. Voer deze keer de representatie pijp lijn opnieuw uit met een sample frequentie van 0,9. Klik op **uitvoeren** om de pijp lijn uit te voeren.

![Pijplijn uitvoering](media/how-to-run-batch-predictions-designer/designer-batch-scoring-15.png)

Het tabblad **verbruik** bevat het rest-eind punt voor het opnieuw uitvoeren van de pijp lijn. Als u een rest-aanroep wilt uitvoeren, hebt u een OAuth 2,0 Bearer-type verificatie-header nodig. Raadpleeg de volgende [sectie zelf studie](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint) voor meer informatie over het instellen van verificatie voor uw werk ruimte en het maken van een para meter rest-aanroep.

## <a name="next-steps"></a>Volgende stappen

Volg de [zelf studie](tutorial-designer-automobile-price-train-score.md) over ontwerpen om een regressie model te trainen en te implementeren.
