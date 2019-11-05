---
title: Werkruimte gegevens exporteren of verwijderen
titleSuffix: Azure Machine Learning
description: Meer informatie over hoe u uw werk ruimte exporteert of verwijdert met de Azure Machine Learning Studio, CLI, SDK en geverifieerde REST Api's.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: lobrien
ms.author: laobri
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: d653d1cd719ac59bf6c98bee588c41858052bc61
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489856"
---
# <a name="export-or-delete-your-machine-learning-service-workspace-data"></a>Uw Machine Learning service werkruimte gegevens exporteren of verwijderen 
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

In Azure Machine Learning kunt u uw werkruimte gegevens exporteren of verwijderen met de geverifieerde REST API. In dit artikel leest u hoe.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="control-your-workspace-data"></a>Uw werkruimte gegevens beheren
In-product gegevens die zijn opgeslagen door Azure Machine Learning, kunnen worden geëxporteerd en verwijderd via Azure Machine Learning Studio, CLI, SDK en geverifieerde REST-Api's. Telemetriegegevens kunnen worden geopend via de Azure-privacybeleid. 

In Azure Machine Learning bestaan persoonlijke gegevens uit gebruikers gegevens in uitvoerings geschiedenis documenten en telemetrie-records van sommige gebruikers interacties met de service.

## <a name="delete-workspace-data-with-the-rest-api"></a>Werkruimte gegevens verwijderen met de REST API 

Als u gegevens wilt verwijderen, kunnen de volgende API-aanroepen worden gemaakt met de HTTP-Verwijder bewerking. Deze worden geautoriseerd door een `Authorization: Bearer <arm-token>`-header te hebben in de aanvraag, waarbij `<arm-token>` het AAD-toegangs token voor het `https://management.core.windows.net/`-eind punt is.  

Zie [azure rest API-documentatie](https://docs.microsoft.com/rest/api/azure/)voor meer informatie over het ophalen van dit token en het aanroepen van Azure-eind punten.  

Vervang in de volgende voor beelden de tekst in {} door de instantie namen die de gekoppelde resource bepalen.

### <a name="delete-an-entire-workspace"></a>Een hele werk ruimte verwijderen

Gebruik deze aanroep om een hele werk ruimte te verwijderen.  
> [!WARNING]
> Alle gegevens worden verwijderd en de werk ruimte kan niet meer worden gebruikt.

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}?api-version=2018-03-01-preview

### <a name="delete-models"></a>Modellen verwijderen

Gebruik deze aanroep om een lijst met modellen en hun Id's op te halen:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models?api-version=2018-03-01-preview

Afzonderlijke modellen kunnen worden verwijderd met:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models/{id}?api-version=2018-03-01-preview

### <a name="delete-assets"></a>Assets verwijderen

Gebruik deze aanroep om een lijst met assets en hun Id's op te halen:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets?api-version=2018-03-01-preview

Afzonderlijke activa kunnen worden verwijderd met:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets/{id}?api-version=2018-03-01-preview

### <a name="delete-images"></a>Installatie kopieën verwijderen

Gebruik deze aanroep om een lijst met installatie kopieën en hun Id's op te halen:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images?api-version=2018-03-01-preview

Afzonderlijke installatie kopieën kunnen worden verwijderd met:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images/{id}?api-version=2018-03-01-preview

### <a name="delete-services"></a>Services verwijderen

Gebruik deze aanroep om een lijst met Services en de bijbehorende Id's op te halen:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services?api-version=2018-03-01-preview

Afzonderlijke services kunnen worden verwijderd met:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services/{id}?api-version=2018-03-01-preview

## <a name="export-service-data-with-the-rest-api"></a>Service gegevens exporteren met de REST API

Als u gegevens wilt exporteren, kunnen de volgende API-aanroepen worden gemaakt met de term HTTP GET. Deze worden geautoriseerd door een `Authorization: Bearer <arm-token>` header te hebben in de aanvraag, waarbij `<arm-token>` het toegangs token voor AAD is voor het eind punt `https://management.core.windows.net/`  

Zie [azure rest API-documentatie](https://docs.microsoft.com/rest/api/azure/)voor meer informatie over het ophalen van dit token en het aanroepen van Azure-eind punten.   

Vervang in de volgende voor beelden de tekst in {} door de instantie namen die de gekoppelde resource bepalen.

### <a name="export-workspace-information"></a>Werkruimte gegevens exporteren

Deze aanroep gebruiken om een lijst met alle werk ruimten op te halen:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces?api-version=2018-03-01-preview

Informatie over een afzonderlijke werk ruimte kan worden verkregen door:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}?api-version=2018-03-01-preview

### <a name="export-compute-information"></a>Reken gegevens exporteren

Alle reken doelen die aan een werk ruimte zijn gekoppeld, kunnen worden verkregen door:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroup/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes?api-version=2018-03-01-preview

Informatie over één Compute-doel kan worden verkregen door:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroup/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes/{computeName}?api-version=2018-03-01-preview

### <a name="export-run-history-data"></a>Gegevens van de uitvoerings Geschiedenis exporteren
Met deze aanroep kunt u een lijst met alle experimenten en hun gegevens ophalen:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments 

Alle uitvoeringen voor een bepaald experiment kunnen worden verkregen door:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/runs 

Uitvoerings geschiedenis-items kunnen worden verkregen door:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/runs/{runId} 

Alle uitvoerings metrieken voor een experiment kunnen worden verkregen door:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/metrics 

Eén run-waarde kan worden verkregen door:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/metrics/{metricId}
    
### <a name="export-artifacts"></a>Artefacten exporteren

Gebruik deze aanroep om een lijst met artefacten en hun paden op te halen:

    https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/artifacts/origins/ExperimentRun/containers/{runId}
    
### <a name="export-notifications"></a>Meldingen over exporteren

Met deze aanroep kunt u een lijst met opgeslagen taken ophalen:     

    https://{location}.experiments.azureml.net/notification/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/tasks

Meldingen voor één taak kunnen worden verkregen door:

    https://{location}.experiments.azureml.net/notification/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}tasks/{taskId}

### <a name="export-data-stores"></a>Gegevens archieven exporteren

Deze aanroep gebruiken om een lijst met gegevens archieven op te halen:

    https://{location}.experiments.azureml.net/datastore/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/datastores

Afzonderlijke gegevens archieven kunnen worden verkregen door:

    https://{location}.experiments.azureml.net/datastore/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/datastores/{name}

### <a name="export-models"></a>Exportmodellen

Gebruik deze aanroep om een lijst met modellen en hun Id's op te halen:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models?api-version=2018-03-01-preview

Afzonderlijke modellen kunnen worden verkregen door:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models/{id}?api-version=2018-03-01-preview

### <a name="export-assets"></a>Assets exporteren

Gebruik deze aanroep om een lijst met assets en hun Id's op te halen:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets?api-version=2018-03-01-preview

Afzonderlijke activa kunnen worden verkregen door:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets/{id}?api-version=2018-03-01-preview

### <a name="export-images"></a>Installatie kopieën exporteren

Gebruik deze aanroep om een lijst met installatie kopieën en hun Id's op te halen:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images?api-version=2018-03-01-preview

Afzonderlijke installatie kopieën kunnen worden verkregen door:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images/{id}?api-version=2018-03-01-preview

### <a name="export-services"></a>Services exporteren

Gebruik deze aanroep om een lijst met Services en de bijbehorende Id's op te halen:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services?api-version=2018-03-01-preview

Afzonderlijke services kunnen worden verkregen door:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services/{id}?api-version=2018-03-01-preview

### <a name="export-pipeline-experiments"></a>Pijplijn experimenten exporteren

Afzonderlijke experimenten kunnen worden verkregen door:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Experiments/{experimentId}

### <a name="export-pipeline-graphs"></a>Pijplijn grafieken exporteren

Afzonderlijke grafieken kunnen worden verkregen door:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Graphs/{graphId}

### <a name="export-pipeline-modules"></a>Pijplijn modules exporteren

Modules kunnen worden verkregen door:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Modules/{id}

### <a name="export-pipeline-templates"></a>Pijplijn sjablonen exporteren

Sjablonen kunnen worden verkregen door:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Templates/{templateId}

### <a name="export-pipeline-data-sources"></a>Gegevens bronnen van pijp lijn exporteren

Gegevens bronnen kunnen worden verkregen door:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/DataSources/{id}

## <a name="delete-assets-in-the-designer"></a>Assets verwijderen in de ontwerp functie

Verwijder afzonderlijke assets in de ontwerp functie waar u uw experiment hebt gemaakt:

1. Ga naar Designer

    ![Assets verwijderen](media/how-to-export-delete-data.md/delete-experiment.png)

1. Selecteer in de lijst het concept van de afzonderlijke pijp lijn dat u wilt verwijderen.

1. Selecteer aan de onderkant **verwijderen**.

## <a name="unregister-data-in-the-designer"></a>Registratie van gegevens in de ontwerp functie opheffen

In de ontwerp functie waar u uw experiment hebt gemaakt, exporteert u de gegevens die u hebt toegevoegd:

1. Selecteer aan de linkerkant **gegevens sets**.



    ![Gegevens downloaden](media/how-to-export-delete-data.md/unregister-dataset.png)

1. Selecteer de afzonderlijke gegevensset in de lijst en klik op registratie ongedaan maken

