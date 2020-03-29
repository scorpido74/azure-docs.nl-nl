---
title: Werkruimtegegevens exporteren of verwijderen
titleSuffix: Azure Machine Learning
description: Meer informatie over het exporteren of verwijderen van uw werkruimte met de Azure Machine Learning-studio, CLI, SDK en geverifieerde REST-API's.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: lobrien
ms.author: laobri
ms.date: 03/06/2020
ms.custom: seodec18
ms.openlocfilehash: 4abef0146b4bf0cfaa254d196b0ca68f0d8ac883
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218289"
---
# <a name="export-or-delete-your-machine-learning-service-workspace-data"></a>Uw Machine Learning-servicewerkruimtegegevens exporteren of verwijderen

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In Azure Machine Learning u uw werkruimtegegevens exporteren of verwijderen met de geverifieerde REST-API. Dit artikel vertelt je hoe.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="control-your-workspace-data"></a>Uw werkruimtegegevens beheren

In-productgegevens die zijn opgeslagen door Azure Machine Learning zijn beschikbaar voor export en verwijdering via Azure Machine Learning-studio, CLI, SDK en geverifieerde REST-API's. Telemetriegegevens zijn toegankelijk via de Azure Privacy-portal. 

In Azure Machine Learning bestaan persoonlijke gegevens uit gebruikersgegevens in run history documents en telemetrierecords van sommige gebruikersinteracties met de service.

## <a name="delete-workspace-data-with-the-rest-api"></a>Werkruimtegegevens verwijderen met de REST-API

Om gegevens te verwijderen, kunnen de volgende API-aanroepen worden uitgevoerd met het http delete-werkwoord. Deze zijn geautoriseerd door `Authorization: Bearer <arm-token>` een koptekst `<arm-token>` in de aanvraag te `https://management.core.windows.net/` hebben, waar is het AAD-toegangstoken voor het eindpunt.  

Zie [REST-resources](how-to-manage-rest.md) en [Azure REST-API-documentatie](https://docs.microsoft.com/rest/api/azure/)beheren voor meer informatie over het aanvragen van dit token en het aanroepen van Azure-eindpunten.  

Vervang in de volgende voorbeelden {} de tekst door de instantienamen die de bijbehorende resource bepalen.

### <a name="delete-an-entire-workspace"></a>Een hele werkruimte verwijderen

Gebruik deze aanroep om een hele werkruimte te verwijderen.  
> [!WARNING]
> Alle informatie wordt verwijderd en de werkruimte is niet meer bruikbaar.

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}?api-version=2019-11-01

### <a name="delete-models"></a>Modellen verwijderen

Gebruik deze oproep om een lijst met modellen en hun geïdentificeerde gegevens te krijgen:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models?api-version=2019-11-01

Afzonderlijke modellen kunnen worden verwijderd met:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models/{id}?api-version=2019-11-01

### <a name="delete-assets"></a>Elementen verwijderen

Gebruik deze oproep om een lijst met activa en hun geïdentificeerde gegevens te krijgen:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets?api-version=2019-11-01

Afzonderlijke elementen kunnen worden verwijderd met:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets/{id}?api-version=2019-11-01

### <a name="delete-images"></a>Installatiekopieën verwijderen

Gebruik deze oproep om een lijst met afbeeldingen en hun geïdentificeerde gegevens te krijgen:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images?api-version=2019-11-01

Afzonderlijke afbeeldingen kunnen worden verwijderd met:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images/{id}?api-version=2019-11-01

### <a name="delete-services"></a>Services verwijderen

Gebruik deze oproep om een lijst met services en hun geïdentificeerde gegevens te krijgen:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services?api-version=2019-11-01

Afzonderlijke services kunnen worden verwijderd met:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services/{id}?api-version=2019-11-01

## <a name="export-service-data-with-the-rest-api"></a>Servicegegevens exporteren met de REST API

Om gegevens te exporteren, kunnen de volgende API-aanroepen worden uitgevoerd met het HTTP GET-werkwoord. Deze zijn geautoriseerd door `Authorization: Bearer <arm-token>` een koptekst `<arm-token>` in de aanvraag te hebben, waar is het AAD-toegangstoken voor het eindpunt`https://management.core.windows.net/`  

Zie [REST gebruiken om ML-resources](how-to-manage-rest.md) en [Azure REST API-documentatie](https://docs.microsoft.com/rest/api/azure/)te beheren voor meer informatie over het aanvragen van azure-eindpunten en het aanroepen van Azure-eindpunten.   

Vervang in de volgende voorbeelden {} de tekst door de instantienamen die de bijbehorende resource bepalen.

### <a name="export-workspace-information"></a>Werkruimtegegevens exporteren

Gebruik deze oproep om een lijst met alle werkruimten op te vragen:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces?api-version=2019-11-01

Informatie over een individuele werkruimte kan worden verkregen door:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}?api-version=2019-11-01

### <a name="export-compute-information"></a>Compute-gegevens exporteren

Alle rekendoelen die aan een werkruimte zijn gekoppeld, kunnen worden verkregen door:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroup/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes?api-version=2019-11-01

Informatie over één rekendoel kan worden verkregen door:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroup/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes/{computeName}?api-version=2019-11-01

### <a name="export-run-history-data"></a>Run-geschiedenisgegevens exporteren

Gebruik deze oproep om een lijst te krijgen van alle experimenten en hun informatie:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments 

Alle runs voor een bepaald experiment kunnen worden verkregen door:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/runs 

Run history items kunnen worden verkregen door:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/runs/{runId} 

Alle run metrics voor een experiment kunnen worden verkregen door:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/metrics 

Eén run metric kan worden verkregen door:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/metrics/{metricId}

### <a name="export-artifacts"></a>Artefacten exporteren

Gebruik deze oproep om een lijst met artefacten en hun paden te krijgen:

    https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/artifacts/origins/ExperimentRun/containers/{runId}

### <a name="export-notifications"></a>Meldingen exporteren

Gebruik deze oproep om een lijst met opgeslagen taken op te halen:

    https://{location}.experiments.azureml.net/notification/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/tasks

Meldingen voor één taak kunnen worden verkregen door:

    https://{location}.experiments.azureml.net/notification/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}tasks/{taskId}

### <a name="export-data-stores"></a>Gegevenswinkels exporteren

Gebruik deze oproep om een lijst met gegevensarchieven te krijgen:

    https://{location}.experiments.azureml.net/datastore/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/datastores

Individuele gegevensopslag kan worden verkregen door:

    https://{location}.experiments.azureml.net/datastore/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/datastores/{name}

### <a name="export-models"></a>Exportmodellen

Gebruik deze oproep om een lijst met modellen en hun geïdentificeerde gegevens te krijgen:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models?api-version=2019-11-01

Individuele modellen kunnen worden verkregen door:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models/{id}?api-version=2019-11-01

### <a name="export-assets"></a>Exportactiva

Gebruik deze oproep om een lijst met activa en hun geïdentificeerde gegevens te krijgen:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets?api-version=2019-11-01

Individuele activa kunnen worden verkregen door:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets/{id}?api-version=2019-11-01

### <a name="export-images"></a>Afbeeldingen exporteren

Gebruik deze oproep om een lijst met afbeeldingen en hun geïdentificeerde gegevens te krijgen:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images?api-version=2019-11-01

Individuele beelden kunnen worden verkregen door:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images/{id}?api-version=2019-11-01

### <a name="export-services"></a>Exportdiensten

Gebruik deze oproep om een lijst met services en hun geïdentificeerde gegevens te krijgen:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services?api-version=2019-11-01

Individuele diensten kunnen worden verkregen door:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services/{id}?api-version=2019-11-01

### <a name="export-pipeline-experiments"></a>Pijplijnexperimenten exporteren

Individuele experimenten kunnen worden verkregen door:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Experiments/{experimentId}

### <a name="export-pipeline-graphs"></a>Pijplijngrafieken exporteren

Individuele grafieken kunnen worden verkregen door:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Graphs/{graphId}

### <a name="export-pipeline-modules"></a>Pijplijnmodules exporteren

Modules zijn te verkrijgen door:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Modules/{id}

### <a name="export-pipeline-templates"></a>Pijplijnsjablonen exporteren

Sjablonen kunnen worden verkregen door:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Templates/{templateId}

### <a name="export-pipeline-data-sources"></a>Gegevensbronnen voor pijplijnen exporteren

Gegevensbronnen kunnen worden verkregen door:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/DataSources/{id}

## <a name="delete-assets-in-the-designer"></a>Elementen in de ontwerper verwijderen

Verwijder afzonderlijke elementen in de ontwerper waar u uw experiment hebt gemaakt:

1. Ga naar designer

    ![Elementen verwijderen](./media/how-to-export-delete-data/delete-experiment.png)

1. Selecteer in de lijst het afzonderlijke pijplijnconcept dat u wilt verwijderen.

1. Selecteer **Verwijderen**.

### <a name="delete-datasets-in-the-designer"></a>Gegevenssets in de ontwerper verwijderen

Als u gegevenssets in de ontwerper wilt verwijderen, gebruikt u de Azure-portal of Storage Explorer om naar verbonden opslagaccounts te navigeren en daar gegevenssets te verwijderen. Als u gegevenssets in de ontwerper uithaalt, wordt alleen het referentiepunt in de opslag verwijderd.

## <a name="export-data-in-the-designer"></a>Gegevens exporteren in de ontwerper

Exporteergegevens die u hebt toegevoegd in de ontwerper waar u uw experiment hebt gemaakt:

1. Selecteer aan de linkerkant **Gegevenssets**.

1. Selecteer in de lijst de gegevensset die u wilt exporteren

    ![Gegevens downloaden](./media/how-to-export-delete-data/unregister-dataset.png)
