---
title: Gebeurtenisgestuurde machine learning-workflows maken
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van het gebeurtenisraster met Azure Machine Learning om oplossingsgerichte oplossingen in te schakelen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: shipatel
author: shivp950
ms.reviewer: larryfr
ms.date: 03/11/2020
ms.openlocfilehash: fe6125682f669e453100488b7e0afc4c49409588
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79129726"
---
# <a name="create-event-driven-machine-learning-workflows-preview"></a>Werkstromen voor gebeurtenisgestuurde machine learning maken (Voorbeeld)

[Azure Event Grid](https://docs.microsoft.com/azure/event-grid/) ondersteunt Azure Machine Learning-gebeurtenissen. U gebeurtenissen zoals status uitvoeren, voltooiing, modelregistratie, modelimplementatie en detectie van gegevensdrift in een werkruimte abonneren en gebruiken.

Zie [Azure Machine Learning-integratie met Gebeurtenisraster](concept-event-grid-integration.md) en het [azure Machine Learning-gebeurtenisrasterschema](/azure/event-grid/event-schema-machine-learning)voor meer informatie over gebeurtenistypen .

Gebruik Gebeurtenisraster om veelvoorkomende scenario's in te schakelen, zoals:

* E-mails verzenden op run-fout en voltooiing uitvoeren
* Een azure-functie gebruiken nadat een model is geregistreerd
* Gebeurtenissen streamen van Azure Machine Learning naar verschillende eindpunten
* Een ML-pijplijn activeren wanneer drift wordt gedetecteerd

> [!NOTE] 
> RunStatusChanged-gebeurtenissen worden momenteel alleen geactiveerd wanneer de runstatus **is mislukt**
>

## <a name="prerequisites"></a>Vereisten
* Inzender of eigenaar toegang tot de Azure Machine Learning-werkruimte waarvoor u gebeurtenissen maakt.

### <a name="configure-eventgrid-using-the-azure-portal"></a>EventGrid configureren met de Azure-portal

1. Open de [Azure-portal](https://portal.azure.com) en ga naar uw Azure Machine Learning-werkruimte.

1. Selecteer __gebeurtenissen__ op de linkerbalk en selecteer **vervolgens Gebeurtenisabonnementen**. 

    ![select-events-in-workspace.png](./media/how-to-use-event-grid/select-event.png)

1. Selecteer het gebeurtenistype dat u wilt consumeren. In de volgende schermafbeelding is bijvoorbeeld __model geselecteerd dat is geregistreerd,__ Model __geïmplementeerd,__ __Voltooid uitvoeren__en drift __van gegevenssets gedetecteerd:__

    ![add-event-type](./media/how-to-use-event-grid/add-event-type-updated.png)

1. Selecteer het eindpunt waarop u de gebeurtenis wilt publiceren. In de volgende schermafbeelding is __gebeurtenishub__ het geselecteerde eindpunt:

    ![select-event-handler](./media/how-to-use-event-grid/select-event-handler.png)

Nadat u uw selectie hebt bevestigd, klikt u op __Maken__. Na configuratie worden deze gebeurtenissen naar uw eindpunt gepusht.


### <a name="configure-eventgrid-using-the-cli"></a>EventGrid configureren met de CLI

U de nieuwste [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)installeren of de Azure Cloud Shell gebruiken die wordt geleverd als onderdeel van uw Azure-abonnement.

Als u de extensie Gebeurtenisraster wilt installeren, gebruikt u de volgende opdracht uit de CLI:

```azurecli-interactive
az add extension --name eventgrid
```

In het volgende voorbeeld wordt uitgelegd hoe u een Azure-abonnement selecteert en een nieuw gebeurtenisabonnement maakt voor Azure Machine Learning:

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

## <a name="filter-events"></a>Filtergebeurtenissen

Wanneer u uw gebeurtenissen instelt, u filters toepassen om alleen op specifieke gebeurtenisgegevens te activeren. In het onderstaande voorbeeld u voor gewijzigde gebeurtenissen voor runstatus filteren op runtypen. De gebeurtenis wordt alleen geactiveerd wanneer aan de criteria is voldaan. Raadpleeg het [azure Machine Learning-gebeurtenisrasterschema](/azure/event-grid/event-schema-machine-learning) voor meer informatie over gebeurtenisgegevens waarop u filteren. 

1. Ga naar de Azure-portal, selecteer een nieuw abonnement of een bestaand abonnement. 

1. Selecteer het tabblad Filters en blader omlaag naar Geavanceerde filters. Geef **in de sleutel** en **waarde** de eigenschapstypen op waarop u wilt filteren. Hier ziet u dat de gebeurtenis alleen wordt geactiveerd wanneer het runtype een pijplijnrun of pijplijnstap uitvoert.  

    :::image type="content" source="media/how-to-use-event-grid/select-event-filters.png" alt-text="filtergebeurtenissen":::

## <a name="sample-scenarios"></a>Voorbeeldscenario's

### <a name="use-a-logic-app-to-send-email-alerts"></a>Een Logische App gebruiken om e-mailwaarschuwingen te verzenden

Maak gebruik van [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/) om e-mails te configureren voor al uw evenementen. Pas aan met voorwaarden en geef ontvangers op om samenwerking en bewustzijn tussen teams mogelijk te maken die samenwerken.

1. Ga in de Azure-portal naar uw Azure Machine Learning-werkruimte en selecteer het tabblad Gebeurtenissen op de linkerbalk. Selecteer hier __Logic-apps__. 

    ![select-logic-ap](./media/how-to-use-event-grid/select-logic-ap.png)

1. Meld u aan bij de gebruikersinterface van de Logic App en selecteer machine learning-service als onderwerptype. 

    ![selecteren-onderwerp-type](./media/how-to-use-event-grid/select-topic-type.png)

1. Selecteer voor welke gebeurtenis(en) u een melding moet ontvangen. Bijvoorbeeld, de volgende screenshot __RunCompleted__.

    ![select-event-runcomplete](./media/how-to-use-event-grid/select-event-runcomplete.png)

1. U de filtermethode in de bovenstaande sectie gebruiken of filters toevoegen om alleen de logische app op een subset van gebeurtenistypen te activeren. In de volgende schermafbeelding wordt een __voorvoegselfilter__ van __/datadriftID/runs/__ gebruikt.

    ![filtergebeurtenissen](./media/how-to-use-event-grid/filtering-events.png)

1. Voeg vervolgens een stap toe om deze gebeurtenis te consumeren en zoek naar e-mail. Er zijn verschillende e-mailaccounts die u gebruiken om evenementen te ontvangen. U ook de voorwaarden configureren waarop u een e-mailwaarschuwing wilt verzenden.

    ![select-e-mail-actie](./media/how-to-use-event-grid/select-email-action.png)

1. Selecteer __Een e-mail verzenden__ en vul de parameters in. In het onderwerp kun je het __gebeurtenistype__ en __onderwerp__ opnemen om gebeurtenissen te filteren. U ook een koppeling naar de werkruimtepagina opnemen voor uitvoeringen in de berichttekst. 

    ![configureren-e-mail-body](./media/how-to-use-event-grid/configure-email-body.png)

1. Als u deze actie wilt opslaan, selecteert u **Opslaan als** in de linkerhoek van de pagina. Bevestig vanaf de rechterbalk de creatie van deze actie.

    ![confirm-logic-app-create](./media/how-to-use-event-grid/confirm-logic-app-create.png)


### <a name="use-a-logic-app-to-trigger-retraining-workflows-when-data-drift-occurs"></a>Een Logische app gebruiken om werkstromen om te scholen wanneer gegevensdrift optreedt

Modellen gaan muf na verloop van tijd, en niet nuttig blijven in de context waarin het wordt uitgevoerd. Een manier om te zien of het tijd is om het model om te scholen is het detecteren van gegevens drift. 

In dit voorbeeld ziet u hoe u gebeurtenisraster gebruiken met een Azure Logic App om omscholing. Het voorbeeld activeert een Azure Data Factory-pijplijn wanneer gegevensdrift optreedt tussen de trainings- en serveergegevenssets van een model.

Voer voordat u begint de volgende acties uit:

* Een gegevenssetmonitor instellen om [gegevensverschuiving]( https://aka.ms/datadrift) in een werkruimte te detecteren
* Maak een gepubliceerde [Azure Data Factory-pijplijn](https://docs.microsoft.com/azure/data-factory/).

In dit voorbeeld wordt een eenvoudige pijplijn voor Gegevensfabriek gebruikt om bestanden naar een blob-archief te kopiëren en een gepubliceerde Machine Learning-pijplijn uit te voeren. Zie voor meer informatie over dit scenario hoe u een [machine learning-stap in Azure Data Factory instelt](https://docs.microsoft.com/azure/data-factory/transform-data-machine-learning-service)

![adf-mlpipeline-fase](./media/how-to-use-event-grid/adf-mlpipeline-stage.png)

1. Begin met het maken van de logische app. Ga naar de [Azure-portal,](https://portal.azure.com)zoek naar Logische apps en selecteer maken.

    ![zoeklogica-app](./media/how-to-use-event-grid/search-for-logic-app.png)

1. Vul de gevraagde informatie in. Als u de ervaring wilt vereenvoudigen, gebruikt u dezelfde abonnements- en brongroep als uw Azure Data Factory Pipeline en Azure Machine Learning-werkruimte.

    ![set-up-logic-app-for-adf](./media/how-to-use-event-grid/set-up-logic-app-for-adf.png)

1. Zodra u de logische app hebt gemaakt, selecteert u __Wanneer een gebeurtenisrastergebeurtenis plaatsvindt__. 

    ![select-event-grid-trigger](./media/how-to-use-event-grid/select-event-grid-trigger.png)

1. Log in en vul de details in voor het evenement. Stel de __resourcenaam__ in op de naam van de werkruimte. Stel het __gebeurtenistype__ in op __DatasetDriftDetected__.

    ![login-en-add-event](./media/how-to-use-event-grid/login-and-add-event.png)

1. Voeg een nieuwe stap toe en zoek naar __Azure Data Factory__. Selecteer __Een pijplijnrun maken__. 

    ![create-adfpipeline-run](./media/how-to-use-event-grid/create-adfpipeline-run.png)

1. Log in en geef de gepubliceerde Azure Data Factory-pijplijn op die moet worden uitgevoerd.

    ![specify-adf-pipeline](./media/how-to-use-event-grid/specify-adf-pipeline.png)

1. Sla de logische app op en maak deze met de knop **Opslaan** linksboven op de pagina. Als u uw app wilt bekijken, gaat u naar uw werkruimte in de [Azure-portal](https://portal.azure.com) en klikt u op **Gebeurtenissen**.

    ![show-logic-app-webhook](./media/how-to-use-event-grid/show-logic-app-webhook.png)

Nu wordt de pijplijn van de gegevensfabriek geactiveerd wanneer er drift optreedt. Bekijk details over uw datadriftrun en machine learning-pijplijn op de [nieuwe werkruimteportal.](https://ml.azure.com) 

![view-in-workspace](./media/how-to-use-event-grid/view-in-workspace.png)

### <a name="use-azure-functions-to-deploy-a-model-based-on-tags"></a>Azure-functies gebruiken om een model te implementeren op basis van tags

Een Azure Machine Learning-modelobject bevat parameters waarop u implementaties draaien, zoals modelnaam, versie, tag en eigenschap. De modelregistratiegebeurtenis kan een eindpunt activeren en u een Azure-functie gebruiken om een model te implementeren op basis van de waarde van die parameters.

Zie bijvoorbeeld de [https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid](https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid) opslagplaats en volg de stappen in het **leesme-bestand.**

## <a name="next-steps"></a>Volgende stappen

* Zie het [Azure Machine Learning-gebeurtenisschema](/azure/event-grid/event-schema-machine-learning) voor meer informatie over beschikbare gebeurtenissen
