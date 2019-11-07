---
title: Door gebeurtenis gestuurde machine learning werk stromen maken
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van Event grid met Azure Machine Learning om op gebeurtenissen gebaseerde oplossingen in te scha kelen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: shipatel
author: shivp950
ms.reviewer: larryfr
ms.date: 11/04/2019
ms.openlocfilehash: b10b848918fda7a1f271a4e617b1706971f103d7
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73622219"
---
# <a name="create-event-driven-machine-learning-workflows-preview"></a>Door gebeurtenis gestuurde machine learning werk stromen maken (preview)

[Azure Event grid](https://docs.microsoft.com/azure/event-grid/) ondersteunt Azure machine learning-service gebeurtenissen. U kunt bijvoorbeeld gebeurtenissen gebruiken van de uitvoering van volt ooien, model registratie, model implementatie en gegevensdrijf detectie bereik voor een werk ruimte.

Zie [Azure machine learning Integration with Event grid](concept-event-grid-integration.md) and the [Azure machine learning Event grid schema](/azure/event-grid/event-schema-machine-learning)(Engelstalig) voor meer informatie.

Gebruik Event Grid om algemene scenario's in te scha kelen, zoals:

* Pijp lijnen activeren voor het opnieuw trainen
* Gebeurtenissen streamen van Azure Machine Learning naar verschillende eind punten

## <a name="prerequisites"></a>Vereisten

* Inzender of eigenaar toegang tot de werk ruimte van de Azure machine learning-service waarvoor u gebeurtenissen gaat maken.
* Selecteer een eind punt voor de gebeurtenis-handler, zoals een webhook of event hub. Zie voor meer informatie [gebeurtenis-handlers](https://docs.microsoft.com/azure/event-grid/event-handlers). 

## <a name="register-resource-providers"></a>Resource providers registreren

Als u vóór november 1 2019 Azure Event Grid of Machine Learning hebt gebruikt, moet u de resource providers mogelijk opnieuw registreren voordat u de stappen in dit document kunt volgen. Als u de providers opnieuw wilt registreren, gebruikt u de volgende stappen:

1. Ga naar de Azure Portal en selecteer __abonnementen__. Selecteer het abonnement waarmee u wilt werken.
1. Selecteer __resource providers__en zoek naar __EventGrid__.
1. Selecteer de vermelding __micro soft. EventGrid__ en selecteer __opnieuw registreren__.

    ![opnieuw registreren-resource provider](media/how-to-use-event-grid/re-register-resource-provider.png)

1. Zoek naar __MachineLearningServices__, selecteer de vermelding en selecteer __opnieuw registreren__.

> [!TIP]
> Als u niet over de juiste machtigingen beschikt om deze stappen uit te voeren, vraagt u uw abonnements beheerder deze te doen.

## <a name="configure-machine-learning-events-using-the-azure-portal"></a>machine learning gebeurtenissen configureren met behulp van de Azure Portal

1. Open de [Azure Portal](https://portal.azure.com) en ga naar uw Azure machine learning-werk ruimte.

1. Selecteer in de linker balk __gebeurtenissen__ en selecteer vervolgens **gebeurtenis abonnementen**. 

    ![Select-Events-in-Workspace. png](media/how-to-use-event-grid/select-event.png)

1. Selecteer het gebeurtenis type dat u wilt gebruiken. De volgende scherm afbeelding is bijvoorbeeld geselecteerd __model geregistreerd__, __model geïmplementeerd__, __uitvoering voltooid__en gegevensset- __drift gedetecteerd__:

    ![Add-Event-type](media/how-to-use-event-grid/add-event-type.png)

1. Selecteer het eind punt waarnaar u de gebeurtenis wilt publiceren. In de volgende scherm afbeelding is __Event hub__ het geselecteerde eind punt:

    ![Select-event-handler](media/how-to-use-event-grid/select-event-handler.png)

Nadat u de selectie hebt bevestigd, klikt u op __maken__. Na de configuratie worden deze gebeurtenissen naar uw eind punt gepusht.

## <a name="set-up-azure-event-grid-using-cli"></a>Azure Event Grid instellen met CLI

U kunt de nieuwste versie van [Azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)installeren of de Azure Cloud shell gebruiken die wordt meegeleverd als onderdeel van uw Azure-abonnement.

Als u de uitbrei ding Event Grid wilt installeren, gebruikt u de volgende opdracht vanuit de CLI:

```azurecli-interactive
az add extension --name eventgrid
```

In het volgende voor beeld ziet u hoe u een Azure-abonnement selecteert en vervolgens een nieuw gebeurtenis abonnement maakt voor Azure Machine Learning:

```azurecli-interactive
# Select the Azure subscription that contains the workspace
az account set --subscription "<name or ID of the subscription>"

# Subscribe to the machine learning workspace.
az eventgrid event-subscription create \
  --name {eventGridFilterName} \
  --source-resource-id "/subscriptions/{subId}/resourceGroups/{rgName}/ \providers/Microsoft.MachineLearningServices/workspaces/{wsName}" \
  --endpoint {event handler endpoint} \
  --included-event-types Microsoft.MachineLearningServices.ModelRegistered \
  --subject-begins-with "models/mymodelname"
```

## <a name="sample-scenarios"></a>Voorbeeldscenario's

### <a name="use-a-logic-app-to-send-email-alerts"></a>Een logische app gebruiken om e-mail waarschuwingen te verzenden

Gebruik [Azure Logic apps](https://docs.microsoft.com/azure/logic-apps/) voor het configureren van e-mail berichten voor al uw gebeurtenissen. Pas aan met voor waarden en geef ontvangers op om samen werking en bewustzijn in verschillende teams samen te stellen.

1. Ga in het Azure Portal naar uw werk ruimte Azure Machine Learning en selecteer het tabblad gebeurtenissen in de balk aan de linkerkant. Selecteer hier __Logic apps__. 

    ![Select-Logic-AP](media/how-to-use-event-grid/select-logic-ap.png)

1. Meld u aan bij de gebruikers interface van de logische app en selecteer Machine Learning service als onderwerps type. 

    ![Select-topic-type](media/how-to-use-event-grid/select-topic-type.png)

1. Selecteer voor welke gebeurtenis (en) u een melding wilt ontvangen. Bijvoorbeeld de volgende scherm afbeelding __RunCompleted__.

    ![Select-Event-runcomplete](media/how-to-use-event-grid/select-event-runcomplete.png)

1. U kunt ook filters toevoegen om de logische app alleen te activeren voor een subset van gebeurtenis typen. In de volgende scherm afbeelding wordt een __voor voegsel filter__ van __/datadriftID/runs/__ gebruikt.

    ![filter-gebeurtenissen](media/how-to-use-event-grid/filtering-events.png)

1. Voeg vervolgens een stap toe om deze gebeurtenis te gebruiken en te zoeken naar e-mail. Er zijn verschillende e-mail accounts die u kunt gebruiken om gebeurtenissen te ontvangen. U kunt ook voor waarden configureren op wanneer u een e-mail bericht wilt verzenden.

    ![Select-email-Action](media/how-to-use-event-grid/select-email-action.png)

1. Selecteer __een E-mail verzenden__ en vul de para meters in. In het onderwerp kunt u het __gebeurtenis type__ en __onderwerp__ toevoegen om gebeurtenissen te filteren. U kunt ook een koppeling naar de werkruimte pagina toevoegen voor uitvoeringen in de hoofd tekst van het bericht. 

    ![e-mail configureren-hoofd tekst](media/how-to-use-event-grid/configure-email-body.png)

1. Als u deze actie wilt opslaan, selecteert u **Opslaan als** in de linkerbovenhoek van de pagina. Bevestig het maken van deze actie vanaf de rechter balk die wordt weer gegeven.

    ![bevestigings logica-app-maken](media/how-to-use-event-grid/confirm-logic-app-create.png)


### <a name="use-a-logic-app-to-trigger-retraining-workflows-when-data-drift-occurs"></a>Een logische app gebruiken om werk stromen voor het opnieuw trainen te activeren wanneer gegevens drift plaatsvindt

Modellen gaan verouderd in de loop van de tijd en blijven niet nuttig in de context waarin deze wordt uitgevoerd in. Een manier om te bepalen of het model opnieuw moet worden getraind, is het detecteren van gegevens drift. 

In dit voor beeld ziet u hoe u Event grid gebruikt met een Azure Logic-app om de training te activeren. In het voor beeld wordt een Azure Data Factory pijp lijn geactiveerd wanneer gegevens overdracht plaatsvindt tussen de training van een model en het leveren van data sets.

Voordat u begint, moet u de volgende acties uitvoeren:

* Een gegevensset-monitor instellen voor het [detecteren van gegevens drift]( https://aka.ms/datadrift) in een werk ruimte
* Een gepubliceerde [Azure Data Factory pijp lijn](https://docs.microsoft.com/azure/data-factory/)maken.

In dit voor beeld wordt een eenvoudige Data Factory-pijp lijn gebruikt om bestanden te kopiëren naar een BLOB Store en een gepubliceerde Machine Learning-pijp lijn uit te voeren. Zie een [Machine Learning stap instellen in azure Data Factory](https://docs.microsoft.com/azure/data-factory/transform-data-machine-learning-service) voor meer informatie over dit scenario.

![ADF-mlpipeline-fase](media/how-to-use-event-grid/adf-mlpipeline-stage.png)

1. Begin met het maken van de logische app. Ga naar de [Azure Portal](https://portal.azure.com), zoek naar Logic apps en selecteer maken.

    ![Zoek logica-app](media/how-to-use-event-grid/search-for-logic-app.png)

1. Vul de vereiste gegevens in. Gebruik hetzelfde abonnement en dezelfde resource groep als uw Azure Data Factory pijp lijn en Azure Machine Learning werk ruimte om de ervaring te vereenvoudigen.

    ![instellen-Logic-app-voor-ADF](media/how-to-use-event-grid/set-up-logic-app-for-adf.png)

1. Wanneer u de logische app hebt gemaakt, selecteert u __Wanneer een event grid resource gebeurtenis optreedt__. 

    ![Select-Event-grid-trigger](media/how-to-use-event-grid/select-event-grid-trigger.png)

1. Meld u aan en vul de Details voor de gebeurtenis in. Stel de __resource naam__ in op de naam van de werk ruimte. Stel het __gebeurtenis type__ in op __DatasetDriftDetected__.

    ![aanmelden en toevoegen-gebeurtenis](media/how-to-use-event-grid/login-and-add-event.png)

1. Voeg een nieuwe stap toe en zoek naar __Azure Data Factory__. Selecteer __een pijplijn uitvoering maken__. 

    ![Create-adfpipeline-run](media/how-to-use-event-grid/create-adfpipeline-run.png)

1. Meld u aan en geef de gepubliceerde Azure Data Factory-pijp lijn op om uit te voeren.

    ![op te geven-ADF-pijp lijn](media/how-to-use-event-grid/specify-adf-pipeline.png)

1. Sla de logische app op en maak deze op met behulp van de knop **Opslaan** in de linkerbovenhoek van de pagina. Als u uw app wilt weer geven, gaat u naar uw werk ruimte in de [Azure Portal](https://portal.azure.com) en klikt u op **gebeurtenissen**.

    ![weer geven-logica-app-webhook](media/how-to-use-event-grid/show-logic-app-webhook.png)

Nu wordt de data factory-pijp lijn geactiveerd wanneer drift optreedt. Bekijk details over uw data drift-uitvoering en machine learning pijp lijn op de [nieuwe werkruimte Portal](https://ml.azure.com). 

![weer gave-in-werk ruimte](media/how-to-use-event-grid/view-in-workspace.png)


### <a name="use-azure-functions-to-deploy-a-model-based-on-tags"></a>Azure Functions gebruiken om een model te implementeren op basis van Tags

Een Azure Machine Learning model object bevat para meters waarmee u implementaties kunt draaien, zoals model naam, versie, tag en eigenschap. De registratie gebeurtenis voor het model kan een eind punt activeren en u kunt een Azure-functie gebruiken om een model te implementeren op basis van de waarde van die para meters.

Zie de [https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid](https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid) -opslag plaats voor een voor beeld en volg de stappen in het **Leesmij** -bestand.

## <a name="next-steps"></a>Volgende stappen

* Zie voor meer informatie over beschik bare gebeurtenissen het [Azure machine learning-gebeurtenis schema](/azure/event-grid/event-schema-machine-learning)