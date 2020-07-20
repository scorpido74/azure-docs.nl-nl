---
title: 'Zelfstudie: ML-modellen implementeren met de ontwerpfunctie (preview)'
titleSuffix: Azure Machine Learning
description: In deze zelfstudie wordt uitgelegd hoe u een predictive analytics-oplossing bouwt in de Azure Machine Learning-ontwerpfunctie (preview). Ontdek hoe u een machine learning-model traint, een score geeft en implementeert met behulp van slepen-en-neerzettenmodules.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 06/28/2020
ms.custom: designer
ms.openlocfilehash: 1d0cd074f6e4ad836bc5102f3f6edc61b75215e4
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/09/2020
ms.locfileid: "86199963"
---
# <a name="tutorial-deploy-a-machine-learning-model-with-the-designer-preview"></a>Zelfstudie: Een machine Learning-model implementeren met de ontwerpfunctie (preview)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

U kunt het voorspellende model dat is ontwikkeld in [deel 1 van de zelfstudie](tutorial-designer-automobile-price-train-score.md) implementeren zodat anderen het ook kunnen gebruiken. In deel een hebt u het model getraind. Nu gaat u nieuwe voorspellingen genereren op basis van gebruikersinvoer. In dit deel van de zelfstudie komen deze onderwerpen aan bod:

> [!div class="checklist"]
> * Een realtime deductiepijplijn maken.
> * Een deductiecluster maken.
> * Het realtime-eindpunt implementeren.
> * Het realtime-eindpunt testen.

## <a name="prerequisites"></a>Vereisten

Voltooi [deel 1 van de zelfstudie](tutorial-designer-automobile-price-train-score.md) om te leren hoe u een machine learning-model kunt trainen en een score kunt geven in de ontwerpfunctie.

## <a name="create-a-real-time-inference-pipeline"></a>Een realtime deductiepijplijn maken

Voor het implementeren van uw pijplijn moet u de trainingspijplijn eerst converteren naar een realtime deductiepijplijn. Tijdens dit proces worden trainingsmodules verwijderd en de invoer en uitvoer van webservices toegevoegd voor het afhandelen van aanvragen.

### <a name="create-a-real-time-inference-pipeline"></a>Een realtime deductiepijplijn maken

1. Selecteer boven het pijplijncanvas **Deductiepijplijn maken** > **Realtime deductiepijplijn**.

    :::image type="content" source="./media/tutorial-designer-automobile-price-deploy/tutorial2-create-inference-pipeline.png"alt-text="Schermopname waarin wordt weergegeven waar de knop pijplijn maken zich bevindt":::

    Uw pijplijn ziet er nu als volgt uit: 

   ![Schermopname van de verwachte configuratie van de pijplijn nadat deze is voorbereid voor implementatie](./media/tutorial-designer-automobile-price-deploy/real-time-inference-pipeline.png)

    Wanneer u **Deductiepijplijn maken** selecteert, gebeuren er verschillende dingen:
    
    * Het getrainde model wordt opgeslagen als een **Gegevensset**module in het palet van de module. U vindt deze onder **Mijn gegevenssets**.
    * Trainingsmodules zoals **Model trainen** en **Gegevens splitsen** worden verwijderd.
    * Het opgeslagen getrainde model wordt weer toegevoegd aan de pijplijn.
    * De modules **Webservice-invoer** en **Webservice-uitvoer** worden toegevoegd. Deze modules laten zien waar gebruikersgegevens de pijplijn binnenkomen en waar gegevens worden geretourneerd.

    > [!NOTE]
    > Standaard verwacht de **Webservice-invoer** hetzelfde gegevensschema als de trainingsgegevens die zijn gebruikt voor het maken van de voorspellende pijplijn. In dit scenario is de prijs in het schema opgenomen. De prijs wordt echter niet gebruikt als een factor tijdens de voorspelling.
    >

1. Selecteer **Verzenden**en gebruik hetzelfde rekendoel en experiment dat u in deel 1 hebt gebruikt.

    Als dit de eerste keer is dat u de pijplijn uitvoert, kan het maximaal 20 minuten duren voor uw pijplijn helemaal is uitgevoerd. De standaardrekeninstellingen hebben een minimale knooppuntgrootte van 0, wat betekent dat de ontwerpfunctie na inactiviteit resources moet toewijzen. Herhaalde pijplijnuitvoeringen kosten minder tijd, omdat de rekenresources al zijn toegewezen. Bovendien gebruikt de ontwerpfunctie in de cache opgeslagen resultaten voor elke module om de efficiëntie verder te verbeteren.

1. Selecteer **Implementeren**.

## <a name="create-an-inferencing-cluster"></a>Een deductiecluster maken

In het dialoogvenster dat wordt weergegeven kunt u willekeurige Azure Kubernetes Service (AKS)-clusters selecteren om uw model naar te implementeren. Als u geen AKS-cluster hebt, gebruikt u de volgende stappen om er een te maken.

1. Selecteer **Compute** in het dialoogvenster dat wordt weergegeven om naar de pagina **Compute** te gaan.

1. Selecteer **Deductieclusters** >  **+ Nieuw** op het navigatielint.

    ![Schermopname waarin wordt weergegeven waar het deelvenster nieuwe deductiecluster zich bevindt](./media/tutorial-designer-automobile-price-deploy/new-inference-cluster.png)
   
1. Configureer een nieuwe Kubernetes Service in het deelvenster Deductiecluster.

1. Geef *aks-compute* op als **Compute-naam**.
    
1. Selecteer een regio in de buurt die beschikbaar is voor de **Regio**.

1. Selecteer **Maken**.

    > [!NOTE]
    > Het duurt ongeveer 15 minuten om een AKS-service te maken. U kunt de inrichtingsstatus bekijken op de pagina **Deductieclusters**.
    >

## <a name="deploy-the-real-time-endpoint"></a>Het realtime-eindpunt implementeren

Wanneer de inrichting van uw AKS-service is voltooid, gaat u terug naar de realtime deductiepijplijn om de implementatie te voltooien.

1. Selecteer **Implementeren** boven het canvas.

1. Selecteer **Nieuw realtime-eindpunt implementeren**. 

1. Selecteer de AKS-cluster die u hebt gemaakt.

1. Selecteer **Implementeren**.
    
    :::image type="content" source="./media/tutorial-designer-automobile-price-deploy/setup-endpoint.png"alt-text="Schermopname waarin het instellen van een nieuw realtime-eindpunt wordt weergegeven":::

    Nadat de implementatie is voltooid, wordt een melding boven het canvas weergegeven. Dit kan enkele minuten duren.

## <a name="test-the-real-time-endpoint"></a>Het realtime-eindpunt testen

Nadat de implementatie is voltooid, kunt u uw realtime-eindpunt testen door naar de pagina **Eindpunten** te gaan.

1. Selecteer op de pagina **Eindpunten** het eindpunt dat u hebt geïmplementeerd.

    ![Schermopname met het tabblad realtime-eindpunten met het nieuw gemaakte eindpunt gemarkeerd](./media/tutorial-designer-automobile-price-deploy/endpoints.png)

1. Selecteer **Testen**.

1. U kunt de testgegevens handmatig invoeren of de automatisch ingevulde voorbeeldgegevens gebruiken en **Testen** selecteren.

    In de portal wordt een testaanvraag naar het eindpunt verzonden en worden de resultaten weergegeven. Er wordt een prijswaarde gegenereerd voor de invoergegevens, maar deze wordt niet gebruikt om de voorspellingswaarde te genereren.

    ![Schermopname met het testen van het realtime-eindpunt en het scorelabel voor prijs gemarkeerd](./media/tutorial-designer-automobile-price-deploy/test-endpoint.png)

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u de belangrijkste stappen geleerd voor het maken, implementeren en gebruiken van een machine learning-model in de ontwerpfunctie. Zie de andere voorbeeldpijplijnen voor meer informatie over hoe u de ontwerpfunctie kunt gebruiken om andere soorten problemen op te lossen.

> [!div class="nextstepaction"]
> [Voorbeelden ontwerpfunctie](samples-designer.md)
