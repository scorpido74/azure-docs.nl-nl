---
title: Exporteren of verwijderen van gegevens in de werkruimte
titleSuffix: Azure Machine Learning
description: Meer informatie over hoe u uw werk ruimte exporteert of verwijdert met de Azure Machine Learning Studio, CLI, SDK en geverifieerde REST Api's.
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79218289"
---
# <a name="export-or-delete-your-machine-learning-service-workspace-data"></a>Exporteren of uw gegevens in de werkruimte voor Machine Learning-service verwijderen

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

U kunt in Azure Machine Learning, exporteren of verwijderen van uw gegevens in de werkruimte met de geverifieerde REST-API. In dit artikel leest u hoe.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="control-your-workspace-data"></a>Uw gegevens in de werkruimte beheren

In-product gegevens die zijn opgeslagen door Azure Machine Learning, kunnen worden geëxporteerd en verwijderd via Azure Machine Learning Studio, CLI, SDK en geverifieerde REST-Api's. Telemetriegegevens zijn toegankelijk via de Privacy van de Azure-portal. 

In Azure Machine Learning bestaan persoonlijke gegevens uit gebruikers gegevens in uitvoerings geschiedenis documenten en telemetrie-records van sommige gebruikers interacties met de service.

## <a name="delete-workspace-data-with-the-rest-api"></a>Verwijderen van gegevens in de werkruimte met de REST-API

Als u wilt verwijderen van gegevens, kunnen de volgende API-aanroepen worden gemaakt met de bewerking HTTP DELETE. Deze worden geautoriseerd door een `Authorization: Bearer <arm-token>`-header te hebben in de aanvraag, waarbij `<arm-token>` het AAD-toegangs token voor het `https://management.core.windows.net/`-eind punt is.  

Zie voor meer informatie over het ophalen van dit token en het aanroepen van Azure-eind punten [rest gebruiken voor het beheren van ml resources](how-to-manage-rest.md) en [Azure rest API documentatie](https://docs.microsoft.com/rest/api/azure/).  

Vervang in de volgende voor beelden de tekst in {} door de instantie namen die de gekoppelde resource bepalen.

### <a name="delete-an-entire-workspace"></a>Een volledige-werkruimte verwijderen

Gebruik deze aanroep om te verwijderen van een hele werkruimte.  
> [!WARNING]
> Alle gegevens worden verwijderd en de werkruimte wordt niet langer worden gebruikt.

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}?api-version=2019-11-01

### <a name="delete-models"></a>Verwijderen van modellen

Gebruik deze aanroep voor een lijst van modellen en hun-id's:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models?api-version=2019-11-01

Afzonderlijke modellen kunnen worden verwijderd met:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models/{id}?api-version=2019-11-01

### <a name="delete-assets"></a>Assets verwijderen

Gebruik deze aanroep voor een lijst van assets en hun-id's:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets?api-version=2019-11-01

Afzonderlijke elementen kunnen worden verwijderd met:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets/{id}?api-version=2019-11-01

### <a name="delete-images"></a>Installatiekopieën verwijderen

Gebruik deze aanroep voor een lijst van afbeeldingen en hun-id's:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images?api-version=2019-11-01

Afzonderlijke afbeeldingen kunnen worden verwijderd met:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images/{id}?api-version=2019-11-01

### <a name="delete-services"></a>Services verwijderen

Gebruik deze aanroep voor een lijst met services en de id's:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services?api-version=2019-11-01

Afzonderlijke services kunnen worden verwijderd met:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services/{id}?api-version=2019-11-01

## <a name="export-service-data-with-the-rest-api"></a>Exporteren van de servicegegevens met de REST-API

Als u wilt exporteren, kunnen de volgende API-aanroepen worden gemaakt met de HTTP GET-bewerking. Deze worden geautoriseerd door een `Authorization: Bearer <arm-token>` header te hebben in de aanvraag, waarbij `<arm-token>` het toegangs token voor AAD is voor het eind punt `https://management.core.windows.net/`  

Zie voor meer informatie over het ophalen van dit token en het aanroepen van Azure-eind punten [rest gebruiken voor het beheren van ml resources](how-to-manage-rest.md) en [Azure rest API-documentatie](https://docs.microsoft.com/rest/api/azure/).   

Vervang in de volgende voor beelden de tekst in {} door de instantie namen die de gekoppelde resource bepalen.

### <a name="export-workspace-information"></a>Werkruimtegegevens exporteren

Gebruik deze aanroep voor een lijst van alle werkruimten:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces?api-version=2019-11-01

Informatie over een afzonderlijke werkruimte kan worden verkregen door:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}?api-version=2019-11-01

### <a name="export-compute-information"></a>Compute-gegevens exporteren

Alle compute-doelen die is gekoppeld aan een werkruimte kunnen worden verkregen door:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroup/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes?api-version=2019-11-01

Informatie over een enkele compute-doel kan worden verkregen door:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroup/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes/{computeName}?api-version=2019-11-01

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

### <a name="export-models"></a>Exportmodellen

Gebruik deze aanroep voor een lijst van modellen en hun-id's:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models?api-version=2019-11-01

Afzonderlijke modellen kunnen worden verkregen door:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models/{id}?api-version=2019-11-01

### <a name="export-assets"></a>Exporteren van assets

Gebruik deze aanroep voor een lijst van assets en hun-id's:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets?api-version=2019-11-01

Afzonderlijke elementen kunnen worden verkregen door:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets/{id}?api-version=2019-11-01

### <a name="export-images"></a>Exporteren van afbeeldingen

Gebruik deze aanroep voor een lijst van afbeeldingen en hun-id's:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images?api-version=2019-11-01

Afzonderlijke afbeeldingen kunnen worden verkregen door:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images/{id}?api-version=2019-11-01

### <a name="export-services"></a>Exporteren van services

Gebruik deze aanroep voor een lijst met services en de id's:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services?api-version=2019-11-01

Afzonderlijke services kunnen worden verkregen door:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services/{id}?api-version=2019-11-01

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

## <a name="delete-assets-in-the-designer"></a>Assets verwijderen in de ontwerp functie

Verwijder afzonderlijke assets in de ontwerp functie waar u uw experiment hebt gemaakt:

1. Ga naar Designer

    ![Assets verwijderen](./media/how-to-export-delete-data/delete-experiment.png)

1. Selecteer in de lijst het concept van de afzonderlijke pijp lijn dat u wilt verwijderen.

1. Selecteer **Verwijderen**.

### <a name="delete-datasets-in-the-designer"></a>Gegevens sets verwijderen in de ontwerp functie

Als u gegevens sets in de ontwerp functie wilt verwijderen, gebruikt u de Azure Portal of Storage Explorer om naar verbonden opslag accounts te gaan en de gegevens sets daar te verwijderen. Bij het opheffen van de registratie van gegevens sets in de ontwerp functie wordt alleen het referentie punt in de opslag verwijderd.

## <a name="export-data-in-the-designer"></a>Gegevens exporteren in de ontwerp functie

In de ontwerp functie waar u uw experiment hebt gemaakt, exporteert u de gegevens die u hebt toegevoegd:

1. Selecteer aan de linkerkant **gegevens sets**.

1. Selecteer in de lijst de gegevensset die u wilt exporteren

    ![Gegevens downloaden](./media/how-to-export-delete-data/unregister-dataset.png)
