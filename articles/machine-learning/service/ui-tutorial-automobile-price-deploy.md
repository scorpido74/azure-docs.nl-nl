---
title: 'Zelfstudie: Een machine learning model met de visuele interface implementeren'
titleSuffix: Azure Machine Learning service
description: Meer informatie over het bouwen van een predictive analytics oplossing in de Visual Interface van de Azure Machine Learning-service. Train, Score en implementeer een machine learning model met behulp van slepen-en-neerzetten. Deze zelf studie is deel twee van een reeks van twee delen voor het voors pellen van prijzen voor auto's met lineaire regressie.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 07/11/2019
ms.openlocfilehash: 64062aeab9a807d2aee7f4bca05d4019a3d9b736
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/10/2019
ms.locfileid: "70858629"
---
# <a name="tutorial-deploy-a-machine-learning-model-with-the-visual-interface"></a>Zelfstudie: Een machine learning model met de visuele interface implementeren

Om anderen een kans te bieden het voorspellende model te gebruiken dat is ontwikkeld in [deel één van de zelf studie](ui-tutorial-automobile-price-train-score.md), kunt u dit implementeren als een Azure-webservice. Tot nu toe hebt u experimenteren met het trainen van uw model. Nu is het tijd om nieuwe voor spellingen te genereren op basis van gebruikers invoer. In dit gedeelte van de zelf studie doet u het volgende:

> [!div class="checklist"]
> * Een model voorbereiden voor implementatie
> * Een webservice implementeren
> * Een webservice testen
> * Een webservice beheren
> * De webservice gebruiken

## <a name="prerequisites"></a>Vereisten

Voltooi [deel één van de zelf studie](ui-tutorial-automobile-price-train-score.md) om te leren hoe u een machine learning model kunt trainen en beoordelen in de visuele interface.

## <a name="prepare-for-deployment"></a>Voorbereiden voor implementatie

Voordat u uw experiment als een webservice implementeert, moet u uw *trainings experiment* eerst omzetten in een *voorspellend experiment*.

1. Selecteer **Predictive experiment*** onder aan het canvas voor experimenteren.

    ![GIF-animatie waarin de automatische conversie van een trainings experiment naar een voorspellend experiment wordt weer gegeven](./media/ui-tutorial-automobile-price-deploy/deploy-web-service.gif)

    Wanneer u **voorspellend experiment maken**selecteert, gebeuren er diverse dingen:
    
    * Het getrainde model wordt opgeslagen als een **getrainde model** module in het palet van de module. U kunt deze vinden onder **getrainde modellen**.
    * Modules die zijn gebruikt voor trainingen, worden verwijderd; met name:
      * Model trainen
      * Gegevens splitsen
      * Model evalueren
    * Het opgeslagen getrainde model wordt weer toegevoegd aan het experiment.
    * De **Web Service-invoer** en de **webservice-uitvoer** modules worden toegevoegd. Deze modules identificeren waar de gebruikers gegevens het model invoeren en waar gegevens worden geretourneerd.

    Het **trainings experiment** wordt nog steeds opgeslagen op de nieuwe tabbladen boven aan het canvas van het experiment.

1. **Voer het experiment uit**.

1. Selecteer de uitvoer van de module **score model** en selecteer **resultaten weer geven** om te controleren of het model nog werkt. U kunt zien dat de oorspronkelijke gegevens worden weer gegeven, samen met de voorspelde prijs ("gescoorde labels").

Uw experiment moet er nu als volgt uitzien:  

![Scherm opname van de verwachte configuratie van het experiment nadat het is voor bereid voor implementatie](./media/ui-tutorial-automobile-price-deploy/predictive-graph.png)

## <a name="deploy-the-web-service"></a>De webservice implementeren

1. Selecteer **Web service implementeren** onder het canvas.

1. Selecteer het **reken doel** dat u wilt uitvoeren voor de webservice.

    Op dit moment ondersteunt de visuele interface alleen de implementatie van AKS-reken doelen (Azure Kubernetes service). U kunt kiezen uit beschik bare AKS Compute-doelen in uw machine learning service-werk ruimte of een nieuwe AKS-omgeving configureren met behulp van de stappen in de dialoog venster die wordt weer gegeven.

    ![Scherm afbeelding met een mogelijke configuratie voor een nieuw reken doel](./media/ui-tutorial-automobile-price-deploy/deploy-compute.png)

1. Selecteer **webservice implementeren**. U ziet de volgende melding wanneer de implementatie is voltooid. De implementatie kan enkele minuten duren.

    ![Scherm afbeelding met het bevestigings bericht voor een geslaagde implementatie.](./media/ui-tutorial-automobile-price-deploy/deploy-succeed.png)

## <a name="test-the-web-service"></a>De webservice testen

U kunt de webservices van uw Visual Interface testen en beheren door te navigeren naar het tabblad **webservices** .

1. Ga naar de sectie web service. U ziet de webservice die u hebt geïmplementeerd met de zelf studie voor het voors **pellen van een auto Mobile-prijs [voorspellend exp]** .

     ![Scherm opname van het tabblad webservice waarop de zojuist gemaakte webservice is gemarkeerd](./media/ui-tutorial-automobile-price-deploy/web-services.png)

1. Selecteer de naam van de webservice om meer details weer te geven.

1. Selecteer **testen**.

    [![Scherm opname van de pagina voor het testen van webservices](./media/ui-tutorial-automobile-price-deploy/web-service-test.png)](./media/ui-tutorial-automobile-price-deploy/web-service-test.png#lightbox)

1. Invoer test gegevens of gebruik de door gegeven gegevens en selecteer **testen**.

    De test aanvraag wordt verzonden naar de webservice en de resultaten worden weer gegeven op de pagina. Hoewel een prijs waarde wordt gegenereerd voor de invoer gegevens, wordt deze niet gebruikt om de Voorspellings waarde te genereren.

## <a name="consume-the-web-service"></a>De webservice gebruiken

Gebruikers kunnen nu API-aanvragen naar uw Azure-webservice verzenden en de resultaten ontvangen om de prijs van hun nieuwe auto's te voors pellen.

**Aanvraag/antwoord** -de gebruiker verzendt een of meer rijen met auto mobiele gegevens naar de service met behulp van een HTTP-protocol. De service reageert met een of meer sets met resultaten.

Op het tabblad **verbruik** van de pagina Details van webservice vindt u voor beelden van rest-aanroepen.

   ![Scherm afbeelding van een voor beeld van een REST-aanroep die gebruikers kunnen vinden op het tabblad verbruik](./media/ui-tutorial-automobile-price-deploy/web-service-consume.png)

Navigeer naar het tabblad **API-document** om meer API-details te vinden.

## <a name="manage-models-and-deployments"></a>Modellen en implementaties beheren

De modellen en web service-implementaties die u in de visuele interface maakt, kunnen ook worden beheerd vanuit de werk ruimte Azure Machine Learning service.

1. Open uw werk ruimte in de [Azure Portal](https://portal.azure.com/).  

1. Selecteer **modellen**in uw werk ruimte. Selecteer vervolgens het experiment dat u hebt gemaakt.

    ![Scherm afbeelding die laat zien hoe u kunt navigeren naar experimenten in de Azure Portal](./media/ui-tutorial-automobile-price-deploy/portal-models.png)

    Op deze pagina ziet u aanvullende informatie over het model.

1. Selecteer **implementaties**, hierin worden alle webservices weer geven die gebruikmaken van het model. Selecteer de naam van de webservice. deze gaat naar de detail pagina van de webservice. Op deze pagina kunt u meer gedetailleerde informatie over de webservice ophalen.

    [![Rapport voor gedetailleerde uitvoer van scherm afbeelding](./media/ui-tutorial-automobile-price-deploy/deployment-details.png)](./media/ui-tutorial-automobile-price-deploy/deployment-details.png#lightbox)

U kunt deze modellen en implementaties ook vinden in de secties **modellen** en **eind punten** van de [landings pagina van uw werk ruimte (preview)](https://ml.azure.com).

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u de belangrijkste stappen geleerd bij het maken, implementeren en gebruiken van een machine learning model in de visuele interface. Voor meer informatie over hoe u de visuele interface kunt gebruiken om andere soorten problemen op te lossen, raadpleegt u onze andere voor beelden van experimenten.

> [!div class="nextstepaction"]
> [Voor beeld van classificatie van credit risico](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
