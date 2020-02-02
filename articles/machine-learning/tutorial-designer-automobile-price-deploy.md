---
title: 'Zelf studie: een machine learning model implementeren met de ontwerp functie'
titleSuffix: Azure Machine Learning
description: In deze zelf studie wordt uitgelegd hoe u een predictive analytics oplossing bouwt in Azure Machine Learning Designer (preview). Train, Score en implementeer een machine learning model met behulp van slepen en neerzetten.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 6fc6479ccda77bff84319d3244b2bd4a0599dc47
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/01/2020
ms.locfileid: "76934003"
---
# <a name="tutorial-deploy-a-machine-learning-model-with-the-designer-preview"></a>Zelf studie: een machine learning model implementeren met de ontwerp functie (preview-versie)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

U kunt het voorspellende model dat is ontwikkeld in [deel één van de zelf studie](tutorial-designer-automobile-price-train-score.md) implementeren om anderen de kans te geven deze te gebruiken. In deel één hebt u het model getraind. Nu is het tijd om nieuwe voor spellingen te genereren op basis van gebruikers invoer. In dit deel van de zelfstudie komen deze onderwerpen aan bod:

> [!div class="checklist"]
> * Maak een real-time pipeline-pijp lijn.
> * Maak een Afleidings cluster.
> * Implementeer het real-time eind punt.
> * Het real-time eind punt testen.

## <a name="prerequisites"></a>Vereisten

Voltooi [deel één van de zelf studie](tutorial-designer-automobile-price-train-score.md) voor meer informatie over het trainen en beoordelen van een machine learning model in de ontwerp functie.

## <a name="create-a-real-time-inference-pipeline"></a>Een real-time pijp lijn maken

Als u uw pijp lijn wilt implementeren, moet u eerst de trainings pijplijn omzetten in een realtime pipeline-pijp lijn. Dit proces verwijdert trainings modules en voegt de invoer en uitvoer van webservices toe om aanvragen af te handelen.

### <a name="create-a-real-time-inference-pipeline"></a>Een real-time pijp lijn maken

1. Selecteer boven het pijplijn canvas **pijp lijn maken** > **real-time pipeline-pijp lijn**.

    ![Scherm afbeelding waarin wordt weer gegeven waar u de knop pijp lijn maken kunt vinden](./media/tutorial-designer-automobile-price-deploy/create-inference-pipeline.png)

    Uw pijp lijn moet er nu als volgt uitzien: 

   ![Scherm opname van de verwachte configuratie van de pijp lijn nadat deze is voor bereid voor implementatie](./media/tutorial-designer-automobile-price-deploy/real-time-inference-pipeline.png)

    Wanneer u de **pijp lijn**voor het maken van een devan-interferentie selecteert, gebeurt het volgende:
    
    * Het getrainde model wordt opgeslagen als een module **gegevensset** in het palet van de module. U kunt het vinden onder **mijn gegevens sets**.
    * Trainings modules zoals **Train model** en **gesplitste gegevens** worden verwijderd.
    * Het opgeslagen getrainde model wordt weer toegevoegd aan de pijp lijn.
    * De **Web Service-invoer** en de **webservice-uitvoer** modules worden toegevoegd. Deze modules laten zien waar gebruikers gegevens de pijp lijn invoeren en waar gegevens worden geretourneerd.

    > [!NOTE]
    > Standaard zal de invoer van de **webservice** hetzelfde gegevens schema verwachten als de trainings gegevens die worden gebruikt voor het maken van de voorspellende pijp lijn. In dit scenario wordt de prijs in het schema opgenomen. De prijs wordt echter niet gebruikt als een factor tijdens de voor spelling.
    >

1. Selecteer **uitvoeren**en gebruik hetzelfde reken doel en experiment dat u in deel één hebt gebruikt.

1. Selecteer **Implementeren**.

## <a name="create-an-inferencing-cluster"></a>Een Afleidings cluster maken

In het dialoog venster dat wordt weer gegeven, kunt u een wille keurige bestaande Azure Kubernetes service-cluster (AKS) selecteren om uw model te implementeren. Als u geen AKS-cluster hebt, gebruikt u de volgende stappen om er een te maken.

1. Selecteer **berekenen** in het dialoog venster dat wordt weer gegeven om naar de **Compute** -pagina te gaan.

1. Selecteer op het navigatie lint de optie **clusters** af >  **+ Nieuw**.

    ![Scherm afbeelding die laat zien hoe het nieuwe deel venster voor het afwijzen van een cluster wordt weer gegeven](./media/tutorial-designer-automobile-price-deploy/new-inference-cluster.png)

1. Configureer een nieuwe Kubernetes-service in het deel venster cluster uitstellen.

1. Voer *AKS-Compute* in voor de naam van de **berekening**.
    
1. Selecteer een regio in de buurt die beschikbaar is voor de **regio**.

1. Selecteer **Maken**.

    > [!NOTE]
    > Het duurt ongeveer 15 minuten om een nieuwe AKS-service te maken. U kunt de inrichtings status controleren op de pagina Afleidings **clusters** .
    >

## <a name="deploy-the-real-time-endpoint"></a>Het real-time-eind punt implementeren

Wanneer de inrichting van uw AKS-service is voltooid, gaat u terug naar de real-time pipeline-pijp lijn om de implementatie te volt ooien.

1. Selecteer boven het canvas **implementeren** .

1. Selecteer **Nieuw real-time eind punt implementeren**. 

1. Selecteer het AKS-cluster dat u hebt gemaakt.

1. Selecteer **Implementeren**.

    ![Scherm afbeelding die laat zien hoe u een nieuw real-time eind punt instelt](./media/tutorial-designer-automobile-price-deploy/setup-endpoint.png)

    Nadat de implementatie is voltooid, wordt een melding boven het canvas weer gegeven. Het kan een paar minuten duren.

## <a name="test-the-real-time-endpoint"></a>Het real-time eind punt testen

Nadat de implementatie is voltooid, kunt u uw real-time eind punt testen door naar de pagina met **eind punten** te gaan.

1. Selecteer op de pagina **eind punten** het eind punt dat u hebt geïmplementeerd.

    ![Scherm afbeelding van het tabblad realtime eind punten waarop het onlangs gemaakte eind punt is gemarkeerd](./media/tutorial-designer-automobile-price-deploy/endpoints.png)

1. Selecteer **testen**.

1. U kunt de test gegevens hand matig invoeren of de automatisch gevulde voorbeeld gegevens gebruiken en **testen**selecteren.

    In de portal wordt een test aanvraag naar het eind punt verzonden en worden de resultaten weer gegeven. Hoewel een prijs waarde wordt gegenereerd voor de invoer gegevens, wordt deze niet gebruikt om de Voorspellings waarde te genereren.

    ![Scherm afbeelding die laat zien hoe u het realtime-eind punt kunt testen met het gescoorde label voor de gemarkeerde prijs](./media/tutorial-designer-automobile-price-deploy/test-endpoint.png)

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u de belangrijkste stappen geleerd voor het maken, implementeren en gebruiken van een machine learning model in de ontwerp functie. Voor meer informatie over hoe u de Designer kunt gebruiken om andere soorten problemen op te lossen, raadpleegt u onze andere voorbeeld pijplijnen.

> [!div class="nextstepaction"]
> [Voor beeld van classificatie van credit risico](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
