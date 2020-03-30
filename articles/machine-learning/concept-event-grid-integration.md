---
title: Azure Machine Learning-gebeurtenissen gebruiken
titleSuffix: Azure Machine Learning
description: Lees in dit artikel hoe u Azure Event Grid gebruiken om u te abonneren, te reageren en u af te melden voor gebeurtenissen die zijn gegenereerd door Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: andyxu
author: gogowings
ms.date: 11/04/2019
ms.openlocfilehash: d8a975487c68a21b2c8b6fa2f07d86c312243f12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79139042"
---
# <a name="consume-azure-machine-learning-events-preview"></a>Azure Machine Learning-gebeurtenissen gebruiken (voorbeeld)

Azure Machine Learning beheert de volledige levenscyclus van het machine learning-proces, inclusief modeltraining, modelimplementatie en bewaking. Azure Machine Learning-gebeurtenissen stellen toepassingen in staat om te reageren op gebeurtenissen tijdens de levenscyclus van machine learning, zoals het voltooien van trainingsruns, de registratie en implementatie van modellen en de detectie van gegevensdrift, door moderne serverloze Platforms. 

Deze gebeurtenissen worden gepubliceerd via [Azure Event Grid](https://azure.microsoft.com/services/event-grid/). Met Azure portal, Powershell of Azure CLI kunnen klanten eenvoudig een abonnement nemen door [een of meer gebeurtenistypen op te geven en de voorwaarden te filteren.](/azure/event-grid/event-filtering) Klanten hebben ook de keuze om een breed scala aan gebeurtenishandlers te bouwen, zoals Azure-functies, Azure Logic Apps of generieke webhooks. Azure Machine Learning biedt samen met Azure Event Grid een hoog beschikbaar, betrouwbaar en fouttolerant gebeurtenisleveringsplatform voor u om gebeurtenisgestuurde toepassingen te bouwen.

Zie [Gebeurtenisgestuurde machine learning-werkstromen (voorbeeld) maken](how-to-use-event-grid.md)voor informatie over het gebruik van Azure Machine Learning met gebeurtenisraster.

## <a name="the-event-model"></a>Het gebeurtenismodel 

Azure Event Grid leest gebeurtenissen uit bronnen, zoals Azure Machine Learning en andere Azure-services. Deze gebeurtenissen worden vervolgens verzonden naar gebeurtenishandlers zoals Azure Event Hubs, Azure Functions, Logic Apps en anderen. In het volgende diagram ziet u hoe Gebeurtenisraster bronnen en handlers met elkaar verbindt, maar geen uitgebreide lijst met ondersteunde integraties is.

![Multifunctioneel model Azure Event Grid](./media/concept-event-grid-integration/azure-event-grid-functional-model.png)

Zie [Wat is gebeurtenisraster?](/azure/event-grid/overview).

## <a name="azure-machine-learning-event-types"></a>Azure Machine Learning-gebeurtenistypen

Azure Machine Learning biedt gebeurtenissen op de verschillende punten van de machine learning-levenscyclus: 

| Gebeurtenistype | Beschrijving |
| ---------- | ----------- |
| `Microsoft.MachineLearningServices.RunCompleted` | Verhoogd wanneer een machine learning-experiment is voltooid |
| `Microsoft.MachineLearningServices.ModelRegistered` | Verhoogd wanneer een machine learning-model is geregistreerd in de werkruimte |
| `Microsoft.MachineLearningServices.ModelDeployed` | Verhoogd wanneer een implementatie van gevolgtrekkingsservice met een of meer modellen is voltooid |
| `Microsoft.MachineLearningServices.DatasetDriftDetected` | Verhoogd wanneer een detectietaak voor gegevensdrift voor twee gegevenssets is voltooid |
| `Microsoft.MachineLearningServices.RunStatusChanged` | Verhoogd wanneer een run status veranderd, momenteel alleen verhoogd wanneer een run status is 'mislukt' |

## <a name="subscribe-to-machine-learning-events"></a>Abonneer u op Machine Learning-evenementen

Abonnementen voor Azure Machine Learning-gebeurtenissen worden beschermd door rbac (role-based access control). Alleen [inzender of eigenaar](how-to-assign-roles.md#default-roles) van een werkruimte kan gebeurtenisabonnementen maken, bijwerken en verwijderen.

Gebeurtenisabonnementen kunnen worden gefilterd op basis van verschillende voorwaarden. Filters kunnen worden toegepast op gebeurtenisabonnementen tijdens het [maken](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest) van het gebeurtenisabonnement of [op een later tijdstip.](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest) 

### <a name="filter-by-event-type"></a>Filteren op gebeurtenistype
Een gebeurtenisabonnement kan een of meer Azure Machine Learning-gebeurtenistypen opgeven.

### <a name="filter-by-event-subject"></a>Filteren op gebeurtenisonderwerp
Azure Event Grid ondersteunt onderwerpfilters op basis __van begin__ en __eindigt met__ overeenkomsten, zodat gebeurtenissen met een overeenkomend onderwerp aan de abonnee worden geleverd. Verschillende machine learning-evenementen hebben een ander onderwerpformaat.

| Gebeurtenistype | Onderwerpindeling | Voorbeeldonderwerp |
| ---------- | ----------- | ----------- |
| `Microsoft.MachineLearningServices.RunCompleted` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` |
| `Microsoft.MachineLearningServices.ModelRegistered` | `models/{modelName}:{modelVersion}` | `models/sklearn_regression_model:3` |
| `Microsoft.MachineLearningServices.ModelDeployed` | `endpoints/{serviceId}` | `endpoints/my_sklearn_aks` |
| `Microsoft.MachineLearningServices.DatasetDriftDetected` | `datadrift/{data.DataDriftId}/run/{data.RunId}` | `datadrift/4e694bf5-712e-4e40-b06a-d2a2755212d4/run/my_driftrun1_1550564444_fbbcdc0f` |
| `Microsoft.MachineLearningServices.RunStatusChanged` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` | 

### <a name="advanced-filtering"></a>Geavanceerd filteren

Azure Event Grid ondersteunt ook geavanceerde filtering op basis van het gepubliceerde gebeurtenisschema. Details van het Azure Machine Learning-gebeurtenisschema zijn te vinden in [het gebeurtenisschema van Azure Event Grid voor Azure Machine Learning](../event-grid/event-schema-machine-learning.md).

Enkele geavanceerde filteringen die u uitvoeren, zijn:

* Als `Microsoft.MachineLearningServices.ModelRegistered` u de tagwaarde van het model wilt filteren:

    ```
    --advanced-filter data.ModelTags.key1 StringIn ('value1')
    ```

Zie [Gebeurtenissen filteren voor gebeurtenisraster](https://docs.microsoft.com/azure/event-grid/how-to-filter-events)voor meer informatie over het toepassen van filters.

## <a name="consume-machine-learning-events"></a>Machine Learning-gebeurtenissen gebruiken

Toepassingen die Machine Learning-gebeurtenissen verwerken, moeten een aantal aanbevolen procedures volgen:

> [!div class="checklist"]
> * Aangezien meerdere abonnementen kunnen worden geconfigureerd om gebeurtenissen naar dezelfde gebeurtenishandler te leiden, is het belangrijk om niet aan te nemen dat gebeurtenissen van een bepaalde bron zijn, maar om het onderwerp van het bericht te controleren om ervoor te zorgen dat het afkomstig is van de machine learning-werkruimte die u verwacht.
> * Controleer ook of het eventType een gebeurtenis is die u bereid bent te verwerken en ga er niet van uit dat alle gebeurtenissen die u ontvangt, de typen zijn die u verwacht.
> * Aangezien berichten niet in orde kunnen komen en na enige vertraging, gebruikt u de etag-velden om te begrijpen of uw informatie over objecten nog steeds up-to-date is.  Gebruik ook de sequencervelden om de volgorde van gebeurtenissen op een bepaald object te begrijpen.
> * Velden negeren die u niet begrijpt. Deze praktijk zal u helpen om u bestand te houden tegen nieuwe functies die in de toekomst kunnen worden toegevoegd.
> * Mislukte of geannuleerde Azure Machine Learning-bewerkingen leiden niet tot een gebeurtenis. Als een modelimplementatie bijvoorbeeld mislukt, wordt Microsoft.MachineLearningServices.ModelDeployed niet geactiveerd. Overweeg een dergelijke foutmodus bij het ontwerpen van uw toepassingen. U Azure Machine Learning SDK, CLI of portal altijd gebruiken om de status van een bewerking te controleren en de gedetailleerde foutredenen te begrijpen.

Azure Event Grid stelt klanten in staat om gekoppelde berichthandlers te bouwen, die kunnen worden geactiveerd door Azure Machine Learning-gebeurtenissen. Enkele opmerkelijke voorbeelden van berichthandlers zijn:
* Azure Functions
* Azure Logic Apps
* Azure Event Hubs
* Azure-pijplijn voor gegevensfabrieken
* Generieke webhooks, die kunnen worden gehost op het Azure-platform of elders

## <a name="next-steps"></a>Volgende stappen

Meer informatie over gebeurtenisraster en Azure Machine Learning-gebeurtenissen proberen:

- [Over Event Grid](../event-grid/overview.md)
- [Azure Event Grid-gebeurtenisschema voor Azure Machine Learning](../event-grid/event-schema-machine-learning.md)
- [Gebeurtenisgestuurde werkstromen maken met Azure Machine Learning](how-to-use-event-grid.md)
