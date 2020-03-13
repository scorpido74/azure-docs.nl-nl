---
title: Azure Machine Learning gebeurtenissen gebruiken
titleSuffix: Azure Machine Learning
description: In dit artikel leest u hoe u Azure Event Grid kunt gebruiken om gebeurtenissen die door Azure Machine Learning zijn gegenereerd, te abonneren, te reageren en af te melden.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: andyxu
author: gogowings
ms.date: 11/04/2019
ms.openlocfilehash: d8a975487c68a21b2c8b6fa2f07d86c312243f12
ms.sourcegitcommit: d322d0a9d9479dbd473eae239c43707ac2c77a77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/12/2020
ms.locfileid: "79139042"
---
# <a name="consume-azure-machine-learning-events-preview"></a>Azure Machine Learning gebeurtenissen gebruiken (preview-versie)

Azure Machine Learning beheert de volledige levens cyclus van machine learning proces, waaronder model training, model implementatie en bewaking. Met Azure Machine Learning gebeurtenissen kunnen toepassingen reageren op gebeurtenissen tijdens de machine learning levenscyclus, zoals het volt ooien van de uitvoering van de training, de registratie en implementatie van modellen en de detectie van gegevens drift, met behulp van moderne serverloze Architecture. 

Deze gebeurtenissen worden gepubliceerd via [Azure Event grid](https://azure.microsoft.com/services/event-grid/). Met Azure Portal, Power shell of Azure CLI kunnen klanten eenvoudig gebeurtenissen abonneren door [een of meer gebeurtenis typen op te geven en filter voorwaarden](/azure/event-grid/event-filtering). Klanten hebben ook de keuze om een breed scala aan gebeurtenis-handlers te bouwen, zoals Azure Functions, Azure Logic Apps of algemene webhooks. Azure Machine Learning, samen met Azure Event Grid, biedt een Maxi maal beschikbaar, betrouwbaar en fout tolerant gebeurtenis bezorgings platform waarmee u op gebeurtenissen gebaseerde toepassingen kunt bouwen.

Zie [gebeurtenis gerichte machine learning werk stromen maken (preview)](how-to-use-event-grid.md)voor meer informatie over het gebruik van Azure Machine Learning met Event grid.

## <a name="the-event-model"></a>Het gebeurtenis model 

Azure Event Grid leest gebeurtenissen van bronnen, zoals Azure Machine Learning en andere Azure-Services. Deze gebeurtenissen worden vervolgens verzonden naar gebeurtenis-handlers zoals Azure Event Hubs, Azure Functions, Logic Apps en anderen. In het volgende diagram ziet u hoe Event Grid bronnen en handlers verbindt, maar dit is geen uitgebreide lijst met ondersteunde integraties.

![Azure Event Grid functionele model](./media/concept-event-grid-integration/azure-event-grid-functional-model.png)

Zie [Wat is Event grid?](/azure/event-grid/overview)voor meer informatie over gebeurtenis bronnen en gebeurtenis-handlers.

## <a name="azure-machine-learning-event-types"></a>Azure Machine Learning gebeurtenis typen

Azure Machine Learning biedt gebeurtenissen in de verschillende punten van machine learning levenscyclus: 

| Gebeurtenis type | Beschrijving |
| ---------- | ----------- |
| `Microsoft.MachineLearningServices.RunCompleted` | Deze gebeurtenis treedt op wanneer de uitvoering van een machine learning experiment is voltooid |
| `Microsoft.MachineLearningServices.ModelRegistered` | Deze gebeurtenis treedt op wanneer een machine learning model in de werk ruimte wordt geregistreerd |
| `Microsoft.MachineLearningServices.ModelDeployed` | Deze gebeurtenis treedt op wanneer een implementatie van een Afleidings service met een of meer modellen is voltooid |
| `Microsoft.MachineLearningServices.DatasetDriftDetected` | Deze gebeurtenis treedt op wanneer een taak voor de detectie van gegevens drift voor twee gegevens sets is voltooid |
| `Microsoft.MachineLearningServices.RunStatusChanged` | Deze gebeurtenis treedt op wanneer een uitvoerings status wordt gewijzigd. dit wordt momenteel alleen geactiveerd wanneer de uitvoerings status is ingesteld op mislukt |

## <a name="subscribe-to-machine-learning-events"></a>Abonneren op Machine Learning gebeurtenissen

Abonnementen voor Azure Machine Learning gebeurtenissen worden beveiligd door middel van op rollen gebaseerd toegangs beheer (RBAC). Alleen [Inzender of eigenaar](how-to-assign-roles.md#default-roles) van een werk ruimte kan gebeurtenis abonnementen maken, bijwerken en verwijderen.

Gebeurtenis abonnementen kunnen worden gefilterd op basis van verschillende voor waarden. Filters kunnen worden toegepast op gebeurtenis abonnementen tijdens het [maken](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest) van het gebeurtenis abonnement of [op een later tijdstip](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest). 

### <a name="filter-by-event-type"></a>Filteren op gebeurtenis type
Met een gebeurtenis abonnement kunt u een of meer Azure Machine Learning gebeurtenis typen opgeven.

### <a name="filter-by-event-subject"></a>Filteren op gebeurtenis onderwerp
Azure Event Grid ondersteunt onderwerps __filters op basis van__ en __eindigt met__ overeenkomsten, zodat gebeurtenissen met een overeenkomend onderwerp aan de abonnee worden geleverd. Verschillende machine learning gebeurtenissen hebben een andere onderwerps indeling.

| Gebeurtenis type | Onderwerps indeling | Voorbeeld onderwerp |
| ---------- | ----------- | ----------- |
| `Microsoft.MachineLearningServices.RunCompleted` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` |
| `Microsoft.MachineLearningServices.ModelRegistered` | `models/{modelName}:{modelVersion}` | `models/sklearn_regression_model:3` |
| `Microsoft.MachineLearningServices.ModelDeployed` | `endpoints/{serviceId}` | `endpoints/my_sklearn_aks` |
| `Microsoft.MachineLearningServices.DatasetDriftDetected` | `datadrift/{data.DataDriftId}/run/{data.RunId}` | `datadrift/4e694bf5-712e-4e40-b06a-d2a2755212d4/run/my_driftrun1_1550564444_fbbcdc0f` |
| `Microsoft.MachineLearningServices.RunStatusChanged` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` | 

### <a name="advanced-filtering"></a>Geavanceerd filteren

Azure Event Grid biedt ook ondersteuning voor geavanceerde filtering op basis van een gepubliceerd gebeurtenis schema. Details van Azure Machine Learning gebeurtenis schema vindt u in [Azure Event grid-gebeurtenis schema voor Azure machine learning](../event-grid/event-schema-machine-learning.md).

Voor beelden van geavanceerde filters die u kunt uitvoeren zijn:

* Voor `Microsoft.MachineLearningServices.ModelRegistered` gebeurtenis: de tag-waarde van het model filter

    ```
    --advanced-filter data.ModelTags.key1 StringIn ('value1')
    ```

Zie [gebeurtenissen filteren voor Event grid voor](https://docs.microsoft.com/azure/event-grid/how-to-filter-events)meer informatie over het Toep assen van filters.

## <a name="consume-machine-learning-events"></a>Machine Learning gebeurtenissen gebruiken

Toepassingen die Machine Learning gebeurtenissen verwerken, moeten een aantal aanbevolen procedures volgen:

> [!div class="checklist"]
> * Omdat meerdere abonnementen kunnen worden geconfigureerd voor het routeren van gebeurtenissen naar dezelfde gebeurtenis-handler, is het belang rijk om te voor komen dat gebeurtenissen afkomstig zijn uit een bepaalde bron, maar om het onderwerp van het bericht te controleren om ervoor te zorgen dat het afkomstig is van de machine learning werk ruimte die u verwacht.
> * Controleer ook of de Event type is ingesteld als een voor bereiding op het proces en ga er niet van uit dat alle gebeurtenissen die u ontvangt, de verwachte typen zijn.
> * Wanneer berichten in aflopende volg orde en na enige vertraging kunnen aankomen, gebruikt u de ETAG-velden om te begrijpen of uw informatie over objecten nog steeds actueel is.  Gebruik ook de sequencer-velden om de volg orde van gebeurtenissen op een bepaald object te begrijpen.
> * Velden negeren die u niet begrijpt. Met deze procedure kunt u de nieuwe functies die in de toekomst kunnen worden toegevoegd, flexibeler maken.
> * Mislukte of geannuleerde Azure Machine Learning bewerkingen activeren geen gebeurtenis. Als bijvoorbeeld een model implementatie mislukt, wordt micro soft. MachineLearningServices. ModelDeployed niet geactiveerd. Overweeg deze fout modus bij het ontwerpen van uw toepassingen. U kunt altijd Azure Machine Learning SDK, CLI of portal gebruiken om de status van een bewerking te controleren en inzicht te krijgen in de oorzaken van de fout.

Met Azure Event Grid kunnen klanten ongekoppelde bericht-handlers bouwen, die kunnen worden geactiveerd door Azure Machine Learning-gebeurtenissen. Enkele voor beelden van bericht-handlers zijn:
* Azure Functions
* Azure Logic Apps
* Azure Event Hubs
* Azure Data Factory pijp lijn
* Generieke webhooks, die kunnen worden gehost op het Azure-platform of elders

## <a name="next-steps"></a>Volgende stappen

Meer informatie over Event Grid en het geven van Azure Machine Learning gebeurtenissen een try:

- [Over Event Grid](../event-grid/overview.md)
- [Azure Event Grid-gebeurtenis schema voor Azure Machine Learning](../event-grid/event-schema-machine-learning.md)
- [Door gebeurtenis gestuurde werk stromen maken met Azure Machine Learning](how-to-use-event-grid.md)
