---
title: 'Zelfstudie: Een machine learning-model implementeren met de ontwerper'
titleSuffix: Azure Machine Learning
description: In deze zelfstudie ziet u hoe u een oplossing voor voorspellende analyses bouwen in Azure Machine Learning-ontwerper (preview). Train, score en implementeer een machine learning-model met behulp van drag-and-drop-modules.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: c3ca37fd47b6551a95f9a491053ec7863acd1eeb
ms.sourcegitcommit: 0553a8b2f255184d544ab231b231f45caf7bbbb0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2020
ms.locfileid: "80389389"
---
# <a name="tutorial-deploy-a-machine-learning-model-with-the-designer-preview"></a>Zelfstudie: Een machine learning-model implementeren met de ontwerper (voorbeeld)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

U het voorspellende model implementeren dat in [deel één van de zelfstudie](tutorial-designer-automobile-price-train-score.md) is ontwikkeld om anderen een kans te geven om het te gebruiken. In deel één heb je je model getraind. Nu is het tijd om nieuwe voorspellingen te genereren op basis van gebruikersinvoer. In dit deel van de zelfstudie komen deze onderwerpen aan bod:

> [!div class="checklist"]
> * Maak een real-time inferentiepijplijn.
> * Maak een inferencing cluster.
> * Implementeer het realtime eindpunt.
> * Test het real-time eindpunt.

## <a name="prerequisites"></a>Vereisten

Voltooi [deel een van de tutorial](tutorial-designer-automobile-price-train-score.md) om te leren hoe je een machine learning-model in de ontwerper trainen en scoren.

## <a name="create-a-real-time-inference-pipeline"></a>Een real-time inferencepijplijn maken

Als u uw pijplijn wilt implementeren, moet u de trainingspijplijn eerst omzetten in een realtime inferentiepijplijn. Dit proces verwijdert trainingsmodules en voegt webservice-ingangen en uitvoer toe om aanvragen te verwerken.

### <a name="create-a-real-time-inference-pipeline"></a>Een real-time inferencepijplijn maken

1. Selecteer boven het pijplijncanvas de optie **Inference pipeline** > **real-time inference pipeline maken**.

    ![Schermafbeelding van de plaats waar u de knop Pijplijn maken vinden](./media/tutorial-designer-automobile-price-deploy/tutorial2-create-inference-pipeline.png)

    Uw pijplijn moet er nu als volgt uitzien: 

   ![Schermafbeelding van de verwachte configuratie van de pijplijn na de voorbereiding op implementatie](./media/tutorial-designer-automobile-price-deploy/real-time-inference-pipeline.png)

    Wanneer u **De inferencepijplijn maken**selecteert, gebeuren er verschillende dingen:
    
    * Het getrainde model wordt opgeslagen als **een Dataset-module** in het modulepalet. Je het vinden onder **Mijn gegevenssets.**
    * Trainingsmodules zoals **Train Model** en **Split Data** worden verwijderd.
    * Het opgeslagen getrainde model wordt weer in de pijplijn toegevoegd.
    * **Modules voor webserviceinvoer** en **webservice-uitvoer** worden toegevoegd. Deze modules geven aan waar gebruikersgegevens de pijplijn binnenkomen en waar gegevens worden geretourneerd.

    > [!NOTE]
    > Standaard verwacht de **webserviceinvoer** hetzelfde gegevensschema als de trainingsgegevens die worden gebruikt om de voorspellende pijplijn te maken. In dit scenario is de prijs opgenomen in het schema. Echter, prijs wordt niet gebruikt als een factor tijdens de voorspelling.
    >

1. Selecteer **Verzenden**en gebruik hetzelfde rekendoel en experiment dat u in deel één hebt gebruikt.

    Als dit de eerste run is, kan het tot 20 minuten duren voordat de pijplijn is voltooid. De standaardcompute-instellingen hebben een minimale nodegrootte van 0, wat betekent dat de ontwerper resources moet toewijzen nadat deze niet is inactief. Herhaalde pijplijnuitvoeringen nemen minder tijd in beslag omdat de compute resources al zijn toegewezen. Daarnaast gebruikt de ontwerper in de cache resultaten voor elke module om de efficiëntie verder te verbeteren.

1. Selecteer **Implementeren**.

## <a name="create-an-inferencing-cluster"></a>Een inferencingcluster maken

In het dialoogvenster dat wordt weergegeven, u kiezen uit bestaande AKS-clusters (Azure Kubernetes Service) om uw model te implementeren. Als u geen AKS-cluster hebt, gebruikt u de volgende stappen om er een te maken.

1. Selecteer **Berekenen** in het dialoogvenster dat naar de **compute-pagina** wordt weergegeven.

1. Selecteer op het navigatielint De optie **Inference Clusters** > **+ Nieuw**.

    ![Schermafbeelding van hoe u naar het nieuwe deelvenster gevolgtrekkingen gaan](./media/tutorial-designer-automobile-price-deploy/new-inference-cluster.png)

1. Configureer in het deelvenster Gevolgtrekkingen een nieuwe Kubernetes-service.

1. Voer *aks-compute in* voor de **compute-naam**.
    
1. Selecteer een regio in de buurt die beschikbaar is voor de **regio.**

1. Selecteer **Maken**.

    > [!NOTE]
    > Het duurt ongeveer 15 minuten om een nieuwe AKS-service te maken. U de ininrichtingsstatus controleren op de pagina **Inference Clusters.**
    >

## <a name="deploy-the-real-time-endpoint"></a>Het realtime eindpunt implementeren

Nadat uw AKS-service klaar is met inrichten, gaat u terug naar de realtime inferencing-pijplijn om de implementatie te voltooien.

1. Selecteer **Implementeren** boven het canvas.

1. Selecteer **Nieuw realtime eindpunt implementeren selecteren**. 

1. Selecteer het AKS-cluster dat u hebt gemaakt.

1. Selecteer **Implementeren**.

    ![Schermafbeelding van het instellen van een nieuw realtime eindpunt](./media/tutorial-designer-automobile-price-deploy/setup-endpoint.png)

    Er wordt een melding voor succes boven het canvas weergegeven nadat de implementatie is voltooid. Het kan een paar minuten duren.

## <a name="test-the-real-time-endpoint"></a>Test het realtime eindpunt

Nadat de implementatie is voltooid, u uw realtime eindpunt testen door naar de pagina **Eindpunten** te gaan.

1. Selecteer op de pagina **Eindpunten** het eindpunt dat u hebt geïmplementeerd.

    ![Schermafbeelding van het tabblad realtime eindpunten waarop het onlangs gemaakte eindpunt is gemarkeerd](./media/tutorial-designer-automobile-price-deploy/endpoints.png)

1. Selecteer **Testen**.

1. U handmatig testgegevens invoeren of de automatisch ingevulde voorbeeldgegevens gebruiken en **Testen selecteren.**

    De portal dient een testaanvraag in bij het eindpunt en toont de resultaten. Hoewel een prijswaarde wordt gegenereerd voor de invoergegevens, wordt deze niet gebruikt om de voorspellingswaarde te genereren.

    ![Schermafbeelding van het testen van het realtime eindpunt met het gemarkeerde label voor de prijs](./media/tutorial-designer-automobile-price-deploy/test-endpoint.png)

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u de belangrijkste stappen geleerd in het maken, implementeren en consumeren van een machine learning-model in de ontwerper. Zie onze andere voorbeeldpijplijnen voor meer informatie over hoe u de ontwerper gebruiken om andere soorten problemen op te lossen.

> [!div class="nextstepaction"]
> [Designer voorbeelden](samples-designer.md)
