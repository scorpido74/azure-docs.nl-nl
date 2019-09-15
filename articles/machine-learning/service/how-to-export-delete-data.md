---
title: Exporteren of verwijderen van gegevens in de werkruimte
titleSuffix: Azure Machine Learning
description: Meer informatie over hoe u uw werk ruimte exporteert of verwijdert met de Azure Portal, CLI, SDK en geverifieerde REST-Api's.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: ph-com
ms.author: pahusban
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: 18e2ab18dac214e73eaf6ad7dfcb9dbbab0b5cf5
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/15/2019
ms.locfileid: "71002840"
---
# <a name="export-or-delete-your-machine-learning-service-workspace-data"></a>Exporteren of uw gegevens in de werkruimte voor Machine Learning-service verwijderen 

U kunt in Azure Machine Learning, exporteren of verwijderen van uw gegevens in de werkruimte met de geverifieerde REST-API. In dit artikel leest u hoe.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="control-your-workspace-data"></a>Uw gegevens in de werkruimte beheren
In-product gegevens die zijn opgeslagen door Azure Machine Learning, kunnen worden geëxporteerd en verwijderd via de Azure Portal, CLI, SDK en geverifieerde REST-Api's. Telemetriegegevens zijn toegankelijk via de Privacy van de Azure-portal. 

In Azure Machine Learning bestaan persoonlijke gegevens uit gebruikers gegevens in uitvoerings geschiedenis documenten en telemetrie-records van sommige gebruikers interacties met de service.

## <a name="delete-workspace-data-with-the-rest-api"></a>Verwijderen van gegevens in de werkruimte met de REST-API 

Als u wilt verwijderen van gegevens, kunnen de volgende API-aanroepen worden gemaakt met de bewerking HTTP DELETE. Deze zijn gemachtigd door een `Authorization: Bearer <arm-token>` header in de aanvraag, waar `<arm-token>` is het AAD-toegangstoken voor de `https://management.core.windows.net/` eindpunt.  

Zie voor meer informatie over dit token ophalen en Azure-eindpunten worden aangeroepen, [Azure REST API-documentatie](https://docs.microsoft.com/rest/api/azure/).  

Vervang de tekst in de volgende voorbeelden {} met de exemplaarnamen om te bepalen van de gekoppelde resource.

### <a name="delete-an-entire-workspace"></a>Een volledige-werkruimte verwijderen

Gebruik deze aanroep om te verwijderen van een hele werkruimte.  
> [!WARNING]
> Alle gegevens worden verwijderd en de werkruimte wordt niet langer worden gebruikt.

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}?api-version=2018-03-01-preview

### <a name="delete-models"></a>Verwijderen van modellen

Gebruik deze aanroep voor een lijst van modellen en hun-id's:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models?api-version=2018-03-01-preview

Afzonderlijke modellen kunnen worden verwijderd met:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models/{id}?api-version=2018-03-01-preview

### <a name="delete-assets"></a>Assets verwijderen

Gebruik deze aanroep voor een lijst van assets en hun-id's:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets?api-version=2018-03-01-preview

Afzonderlijke elementen kunnen worden verwijderd met:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets/{id}?api-version=2018-03-01-preview

### <a name="delete-images"></a>Verwijderen van installatiekopieën

Gebruik deze aanroep voor een lijst van afbeeldingen en hun-id's:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images?api-version=2018-03-01-preview

Afzonderlijke afbeeldingen kunnen worden verwijderd met:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images/{id}?api-version=2018-03-01-preview

### <a name="delete-services"></a>Services verwijderen

Gebruik deze aanroep voor een lijst met services en de id's:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services?api-version=2018-03-01-preview

Afzonderlijke services kunnen worden verwijderd met:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services/{id}?api-version=2018-03-01-preview

## <a name="export-service-data-with-the-rest-api"></a>Exporteren van de servicegegevens met de REST-API

Als u wilt exporteren, kunnen de volgende API-aanroepen worden gemaakt met de HTTP GET-bewerking. Deze zijn gemachtigd door een `Authorization: Bearer <arm-token>` header in de aanvraag, waar `<arm-token>` het AAD-toegangstoken voor het eindpunt is `https://management.core.windows.net/`  

Zie voor meer informatie over dit token ophalen en Azure-eindpunten worden aangeroepen, [Azure REST API-documentatie](https://docs.microsoft.com/rest/api/azure/).   

Vervang de tekst in de volgende voorbeelden {} met de exemplaarnamen om te bepalen van de gekoppelde resource.

### <a name="export-workspace-information"></a>Werkruimtegegevens exporteren

Gebruik deze aanroep voor een lijst van alle werkruimten:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces?api-version=2018-03-01-preview

Informatie over een afzonderlijke werkruimte kan worden verkregen door:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}?api-version=2018-03-01-preview

### <a name="export-compute-information"></a>Compute-gegevens exporteren

Alle compute-doelen die is gekoppeld aan een werkruimte kunnen worden verkregen door:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroup/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes?api-version=2018-03-01-preview

Informatie over een enkele compute-doel kan worden verkregen door:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroup/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes/{computeName}?api-version=2018-03-01-preview

### <a name="export-run-history-data"></a>Uitvoeringsgeschiedenisgegevens exporteren
Gebruik deze aanroep voor een lijst van alle experimenten en hun informatie:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments 

Alle uitvoeringen voor een bepaalde experiment kunnen worden verkregen door:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/runs 

Uitvoeringsgeschiedenis items kunnen worden verkregen door:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/runs/{runId} 

Alle uitvoeren metrische gegevens voor een experiment kan worden verkregen door:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/metrics 

Één uitvoeren metrische waarde kan worden verkregen door:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/metrics/{metricId}
    
### <a name="export-artifacts"></a>Exporteren van artefacten

Gebruik deze aanroep voor een lijst van artefacten en hun paden:

    https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/artifacts/origins/ExperimentRun/containers/{runId}
    
### <a name="export-notifications"></a>Exporteren van meldingen

Gebruik deze aanroep voor een lijst met opgeslagen taken:     

    https://{location}.experiments.azureml.net/notification/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/tasks

Meldingen voor een enkele taak kunnen worden verkregen door:

    https://{location}.experiments.azureml.net/notification/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}tasks/{taskId}

### <a name="export-data-stores"></a>Opgeslagen gegevens exporteren

Gebruik deze aanroep voor een lijst van gegevensarchieven:

    https://{location}.experiments.azureml.net/datastore/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/datastores

Afzonderlijke gegevensarchieven kunnen worden verkregen door:

    https://{location}.experiments.azureml.net/datastore/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/datastores/{name}

### <a name="export-models"></a>Modellen exporteren

Gebruik deze aanroep voor een lijst van modellen en hun-id's:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models?api-version=2018-03-01-preview

Afzonderlijke modellen kunnen worden verkregen door:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models/{id}?api-version=2018-03-01-preview

### <a name="export-assets"></a>Exporteren van assets

Gebruik deze aanroep voor een lijst van assets en hun-id's:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets?api-version=2018-03-01-preview

Afzonderlijke elementen kunnen worden verkregen door:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets/{id}?api-version=2018-03-01-preview

### <a name="export-images"></a>Exporteren van afbeeldingen

Gebruik deze aanroep voor een lijst van afbeeldingen en hun-id's:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images?api-version=2018-03-01-preview

Afzonderlijke afbeeldingen kunnen worden verkregen door:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images/{id}?api-version=2018-03-01-preview

### <a name="export-services"></a>Exporteren van services

Gebruik deze aanroep voor een lijst met services en de id's:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services?api-version=2018-03-01-preview

Afzonderlijke services kunnen worden verkregen door:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services/{id}?api-version=2018-03-01-preview

### <a name="export-pipeline-experiments"></a>Pijplijn experimenten exporteren

Afzonderlijke experimenten kunnen worden verkregen door:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Experiments/{experimentId}

### <a name="export-pipeline-graphs"></a>Pijplijn grafieken exporteren

Afzonderlijke grafieken kunnen worden verkregen door:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Graphs/{graphId}

### <a name="export-pipeline-modules"></a>Exporteren-Pijplijnmodules

Modules kunnen worden verkregen door:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Modules/{id}

### <a name="export-pipeline-templates"></a>Pijplijn sjablonen exporteren

Sjablonen kunnen worden verkregen door:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Templates/{templateId}

### <a name="export-pipeline-data-sources"></a>Exporteren van de pijplijn-gegevensbronnen

Gegevensbronnen kunnen worden verkregen door:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/DataSources/{id}

## <a name="delete-visual-interface-assets"></a>Elementen van de Visual-Interface verwijderen

Verwijder afzonderlijke assets in de visuele interface waar u uw experiment hebt gemaakt:

1. Selecteer aan de linkerkant het type activum dat u wilt verwijderen.

    ![Assets verwijderen](media/how-to-export-delete-data.md/delete-experiment.png)

1. Selecteer in de lijst de afzonderlijke activa die u wilt verwijderen.

1. Selecteer aan de onderkant **verwijderen**.

## <a name="export-visual-interface-data"></a>Gegevens van de visuele interface exporteren

In de visuele interface waar u uw experiment hebt gemaakt, exporteert u de gegevens die u hebt toegevoegd:

1. Selecteer aan de linkerkant **gegevens**.

1. Selecteer bovenaan **mijn gegevens sets** of voor **beelden** om de gegevens te vinden die u wilt exporteren.

    ![Gegevens downloaden](media/how-to-export-delete-data.md/download-data.png)

1. Selecteer in de lijst de afzonderlijke gegevens sets die u wilt exporteren.

1. Selecteer onderaan **downloaden**.