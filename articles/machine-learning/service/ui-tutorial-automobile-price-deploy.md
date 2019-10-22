---
title: 'Zelf studie: een machine learning model implementeren met de visuele interface'
titleSuffix: Azure Machine Learning
description: Meer informatie over het bouwen van een predictive analytics oplossing in de Azure Machine Learning Visual-Interface. Train, Score en implementeer een machine learning model met behulp van slepen-en-neerzetten.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 10/09/2019
ms.openlocfilehash: ff3ffe4b68d7b5d74ee3a84ca9c59a13d445f43b
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693011"
---
# <a name="tutorial-deploy-a-machine-learning-model-with-the-visual-interface"></a>Zelf studie: een machine learning model implementeren met de visuele interface

Om anderen een kans te bieden het voorspellende model te gebruiken dat is ontwikkeld in [deel één van de zelf studie](ui-tutorial-automobile-price-train-score.md), kunt u dit implementeren als een real-time-eind punt. In deel 1 hebt u het model getraind. Nu is het tijd om nieuwe voor spellingen te genereren op basis van gebruikers invoer. In dit gedeelte van de zelf studie doet u het volgende:

> [!div class="checklist"]
> * Een real-time eind punt implementeren
> * Een Afleidings cluster maken
> * Een real-time eind punt testen

## <a name="prerequisites"></a>Vereisten

Voltooi [deel één van de zelf studie](ui-tutorial-automobile-price-train-score.md) om te leren hoe u een machine learning model kunt trainen en beoordelen in de visuele interface.

## <a name="deploy-a-real-time-endpoint"></a>Een real-time eind punt implementeren

Als u uw pijp lijn wilt implementeren, moet u het volgende doen:

1. Converteer de trainings pijplijn naar een real-time pipeline-pijp lijn, waardoor trainings modules worden verwijderd en invoer en uitvoer worden toegevoegd voor het afleiden van aanvragen.
1. Implementeer de pijp lijn voor de deinterferentie.

### <a name="create-a-real-time-inference-pipeline"></a>Een real-time pijp lijn maken

1. Selecteer aan de bovenkant van het pijp lijn-canvas de optie **pijp lijn maken**  > **realtime-pijp lijn** .

    Wanneer u **voorspellende pijp lijn maken**selecteert, gebeuren er diverse dingen:
    
    * Het getrainde model wordt opgeslagen als een module **gegevensset** in het palet van de module. U kunt het vinden onder **mijn gegevens sets**.
    * Modules worden, zoals **Train model** en **gesplitste gegevens**, verwijderd die zijn gebruikt voor de training.
    * Het opgeslagen getrainde model wordt weer toegevoegd aan de pijp lijn.
    * De **Web Service-invoer** en de **webservice-uitvoer** modules worden toegevoegd. Deze modules identificeren waar gebruikers gegevens het model invoeren en waar gegevens worden geretourneerd.

    > [!Note]
    > De **trainings pijplijn** wordt opgeslagen op het nieuwe tabblad boven aan het pijp lijn-canvas. Het kan ook worden gevonden als een gepubliceerde pijp lijn in de visuele interface.
    >

    Uw pijp lijn moet er nu als volgt uitzien:  

   ![Scherm opname van de verwachte configuratie van de pijp lijn nadat deze is voor bereid voor implementatie](./media/ui-tutorial-automobile-price-deploy/predictive-graph.png)

1. Selecteer **uitvoeren** en gebruik hetzelfde reken doel en experiment dat u in deel 1 hebt gebruikt.

1. Selecteer de module **score model** .

1. Selecteer in het deel venster Eigenschappen de optie **uitvoer**  > **visualiseren** om te controleren of het model nog steeds werkt. U kunt zien dat de oorspronkelijke gegevens samen met de voorspelde prijs worden weer gegeven ("gescoorde labels").

1. Selecteer **Implementeren**.

### <a name="create-an-inferencing-cluster"></a>Een Afleidings cluster maken

In het dialoog venster dat wordt weer gegeven, kunt u uit bestaande Azure Kubernetes Service-clusters (AKS) in uw werk ruimte selecteren om uw model te implementeren. Als u geen AKS-cluster hebt, gebruikt u de volgende stappen om er een te maken.

1. Selecteer **berekenen** in het dialoog venster om naar de **Compute** -pagina te gaan.

1. Selecteer in het navigatie lint de optie **clusters** af >  **+ Nieuw**.

    ![Scherm afbeelding die laat zien hoe u naar het nieuwe deel venster voor uitgaand cluster navigeert](./media/ui-tutorial-automobile-price-deploy/new-inference-cluster.png)

1. Configureer een nieuwe Kubernetes-service in het deel venster cluster uitstellen.

1. Voer ' AKS-Compute ' in voor de naam van de **berekening**.
    
1. Selecteer een beschik bare **regio**in de buurt.

1. Selecteer **Maken**.

    > [!Note]
    > Het duurt ongeveer 15 minuten om een nieuwe AKS-service te maken. U kunt de inrichtings status controleren op de pagina Afleidings **clusters**
    >

### <a name="deploy-the-real-time-endpoint"></a>Het real-time-eind punt implementeren

Wanneer de inrichting van uw AKS-service is voltooid, gaat u terug naar de real-time pipeline-pijp lijn om de implementatie te volt ooien.

1. Selecteer boven het canvas **implementeren** .

1. Selecteer **Nieuw real-time eind punt implementeren**. 

1. Selecteer het AKS-cluster dat u hebt gemaakt.

1. Selecteer **Implementeren**.

    [! Scherm afbeelding die laat zien hoe u een nieuw realtime-eind punt kunt instellen](./media/ui-tutorial-automobile-price-deploy/setup-endpoint.png)

    Wanneer de implementatie is voltooid, wordt er een melding weer gegeven boven het canvas. Dit kan enkele minuten duren.

## <a name="test-the-real-time-endpoint"></a>Het real-time eind punt testen

U kunt uw real-time eind punt testen door te navigeren naar de pagina **eind punten** in het navigatie deel venster werk ruimte aan de linkerkant.

1. Selecteer op de pagina **eind punten** het eind punt dat u hebt geïmplementeerd.

    ![Scherm afbeelding van het tabblad realtime eind punten waarop het onlangs gemaakte eind punt is gemarkeerd](./media/ui-tutorial-automobile-price-deploy/web-services.png)

1. Selecteer **testen**.

1. Invoer test gegevens of gebruik de door gegeven gegevens en selecteer **testen**.

    De test aanvraag wordt verzonden naar het eind punt en de resultaten worden weer gegeven op de pagina. Hoewel een prijs waarde wordt gegenereerd voor de invoer gegevens, wordt deze niet gebruikt om de Voorspellings waarde te genereren.

    ![Scherm afbeelding die laat zien hoe u het realtime-eind punt kunt testen met het gescoorde label voor de gemarkeerde prijs](./media/ui-tutorial-automobile-price-deploy/test-endpoint.png)

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u de belangrijkste stappen geleerd bij het maken, implementeren en gebruiken van een machine learning model in de visuele interface. Voor meer informatie over hoe u de visuele interface kunt gebruiken om andere soorten problemen op te lossen, raadpleegt u onze andere voorbeeld pijplijnen.

> [!div class="nextstepaction"]
> [Voor beeld van classificatie van credit risico](how-to-ui-sample-classification-predict-credit-risk-cost-sensitive.md)
