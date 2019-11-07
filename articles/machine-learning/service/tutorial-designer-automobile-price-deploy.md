---
title: 'Zelf studie: een machine learning model implementeren met de ontwerp functie'
titleSuffix: Azure Machine Learning
description: Meer informatie over het bouwen van een predictive analytics oplossing in Azure Machine Learning Designer (preview). Train, Score en implementeer een machine learning model met behulp van slepen-en-neerzetten.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: b5fa1557999ae851bccafbf8ee7c41f0b3614614
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73715915"
---
# <a name="tutorial-deploy-a-machine-learning-model-with-the-designer-preview"></a>Zelf studie: een machine learning model implementeren met de ontwerp functie (preview-versie)
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku.md)]

U kunt het voorspellende model dat is ontwikkeld in [deel één van de zelf studie](tutorial-designer-automobile-price-train-score.md) implementeren om anderen de kans te geven deze te gebruiken. In deel 1 hebt u het model getraind. Nu is het tijd om nieuwe voor spellingen te genereren op basis van gebruikers invoer. In dit deel van de zelfstudie komen deze onderwerpen aan bod:

> [!div class="checklist"]
> * Een real-time pijp lijn maken
> * Een Afleidings cluster maken
> * Een real-time eind punt implementeren
> * Een real-time eind punt testen

## <a name="prerequisites"></a>Vereisten

Voltooi [deel één van de zelf studie](tutorial-designer-automobile-price-train-score.md) voor meer informatie over het trainen en beoordelen van een machine learning model in de ontwerp functie.

## <a name="create-a-real-time-inference-pipeline"></a>Een real-time pijp lijn maken

Als u uw pijp lijn wilt implementeren, moet u eerst de trainings pijplijn omzetten in een realtime pipeline-pijp lijn. Dit proces verwijdert trainings modules en voegt invoer en uitvoer voor het afleiden van aanvragen toe.

### <a name="create-a-real-time-inference-pipeline"></a>Een real-time pijp lijn maken

1. Selecteer boven het pijplijn canvas **pijp lijn maken** > **realtime-pijp lijn** .

    Uw pijp lijn moet er nu als volgt uitzien:  

   ![Scherm opname van de verwachte configuratie van de pijp lijn nadat deze is voor bereid voor implementatie](./media/ui-tutorial-automobile-price-deploy/real-time-inference-pipeline.png)

    Wanneer u de **pijp lijn**voor het maken van een devan-interferentie selecteert, gebeurt het volgende:
    
    * Het getrainde model wordt opgeslagen als een module **gegevensset** in het palet van de module. U kunt het vinden onder **mijn gegevens sets**.
    * Trainings modules zoals **Train model** en **gesplitste gegevens** worden verwijderd.
    * Het opgeslagen getrainde model wordt weer toegevoegd aan de pijp lijn.
    * De **Web Service-invoer** en de **webservice-uitvoer** modules worden toegevoegd. Deze modules laten zien waar gebruikers gegevens het model invoeren en waar gegevens worden geretourneerd.

    > [!Note]
    > De **trainings pijplijn** wordt opgeslagen op het nieuwe tabblad boven aan het pijp lijn-canvas. Het kan ook worden gevonden als een gepubliceerde pijp lijn in de ontwerp functie.
    >

1. Selecteer **uitvoeren** en gebruik hetzelfde reken doel en experiment dat u in deel 1 hebt gebruikt.

1. Selecteer de module **score model** .

1. Selecteer in het deel venster Eigenschappen de optie **uitvoer** > **visualiseren** om te controleren of het model nog steeds werkt. U kunt zien dat de oorspronkelijke gegevens samen met de voorspelde prijs worden weer gegeven ("gescoorde labels").

1. Selecteer **Implementeren**.

## <a name="create-an-inferencing-cluster"></a>Een Afleidings cluster maken

In het dialoog venster dat wordt weer gegeven, kunt u een van de bestaande Azure Kubernetes Service-clusters (AKS) in gebruiken om uw model te implementeren. Als u geen AKS-cluster hebt, gebruikt u de volgende stappen om er een te maken.

1. Selecteer **berekenen** in het dialoog venster dat wordt weer gegeven om naar de **Compute** -pagina te gaan.

1. Selecteer in het navigatie lint de optie **clusters** af >  **+ Nieuw**.

    ![Scherm afbeelding die laat zien hoe u naar het nieuwe deel venster voor uitgaand cluster navigeert](./media/ui-tutorial-automobile-price-deploy/new-inference-cluster.png)

1. Configureer een nieuwe Kubernetes-service in het deel venster cluster uitstellen.

1. Voer ' AKS-Compute ' in voor de naam van de **berekening**.
    
1. Selecteer een beschik bare **regio**in de buurt.

1. Selecteer **Maken**.

    > [!Note]
    > Het duurt ongeveer 15 minuten om een nieuwe AKS-service te maken. U kunt de inrichtings status controleren op de pagina Afleidings **clusters**
    >

## <a name="deploy-the-real-time-endpoint"></a>Het real-time-eind punt implementeren

Wanneer de inrichting van uw AKS-service is voltooid, gaat u terug naar de real-time pipeline-pijp lijn om de implementatie te volt ooien.

1. Selecteer boven het canvas **implementeren** .

1. Selecteer **Nieuw real-time eind punt implementeren**. 

1. Selecteer het AKS-cluster dat u hebt gemaakt.

1. Selecteer **Implementeren**.

    ![Scherm afbeelding die laat zien hoe u een nieuw real-time eind punt instelt](./media/ui-tutorial-automobile-price-deploy/setup-endpoint.png)

    Wanneer de implementatie is voltooid, wordt er een melding weer gegeven boven het canvas. Dit kan enkele minuten duren.

## <a name="test-the-real-time-endpoint"></a>Het real-time eind punt testen

Zodra de implementatie is voltooid, kunt u uw real-time eind punt testen door te navigeren naar de pagina met **eind punten** .

1. Selecteer op de pagina **eind punten** het eind punt dat u hebt geïmplementeerd.

    ![Scherm afbeelding van het tabblad realtime eind punten waarop het onlangs gemaakte eind punt is gemarkeerd](./media/ui-tutorial-automobile-price-deploy/endpoints.png)

1. Selecteer **testen**.

1. U kunt de test gegevens hand matig invoeren of de automatisch gevulde voorbeeld gegevens gebruiken en **testen**selecteren.

    In de portal wordt een test aanvraag naar het eind punt verzonden en worden de resultaten weer gegeven. Hoewel een prijs waarde wordt gegenereerd voor de invoer gegevens, wordt deze niet gebruikt om de Voorspellings waarde te genereren.

    ![Scherm afbeelding die laat zien hoe u het realtime-eind punt kunt testen met het gescoorde label voor de gemarkeerde prijs](./media/ui-tutorial-automobile-price-deploy/test-endpoint.png)

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u de belangrijkste stappen geleerd bij het maken, implementeren en gebruiken van een machine learning model in de ontwerp functie. Voor meer informatie over hoe u de Designer kunt gebruiken om andere soorten problemen op te lossen, raadpleegt u onze andere voorbeeld pijplijnen.

> [!div class="nextstepaction"]
> [Voor beeld van classificatie van credit risico](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
