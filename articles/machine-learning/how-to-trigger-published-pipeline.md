---
title: De uitvoering van een ML-pijplijn activeren vanuit een Logic-app
titleSuffix: Azure Machine Learning
description: Meer informatie over het activeren van de uitvoering van een ML-pijplijn met Azure Logic Apps.
services: machine-learning
author: sanpil
ms.author: sanpil
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 6bb976b8b310fb3eb4d0247a8d745599f688d7b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77122855"
---
# <a name="trigger-a-run-of-a-machine-learning-pipeline-from-a-logic-app"></a>Een run van een Machine Learning-pijplijn activeren vanuit een Logische App

Activeer de uitvoering van uw Azure Machine Learning Pipeline wanneer er nieuwe gegevens worden weergegeven. U bijvoorbeeld de pijplijn activeren om een nieuw model te trainen wanneer er nieuwe gegevens worden weergegeven in het blob-opslagaccount. Stel de trigger in met [Azure Logic Apps](../logic-apps/logic-apps-overview.md).

## <a name="prerequisites"></a>Vereisten

* Een Azure Machine Learning-werkruimte. Zie [Een Azure Machine Learning-werkruimte maken](how-to-manage-workspace.md)voor meer informatie.

* Het REST-eindpunt voor een gepubliceerde Machine Learning-pijplijn. [Maak en publiceer uw pijplijn.](how-to-create-your-first-pipeline.md) Zoek vervolgens het REST-eindpunt van uw PublishedPipeline met behulp van de pijplijn-id:
    
     ```
    # You can find the pipeline ID in Azure Machine Learning studio
    
    published_pipeline = PublishedPipeline.get(ws, id="<pipeline-id-here>")
    published_pipeline.endpoint 
    ```
* [Azure blob-opslag](../storage/blobs/storage-blobs-overview.md) om uw gegevens op te slaan.
* [Een gegevensarchief](how-to-access-data.md) in uw werkruimte met de details van uw blob-opslagaccount.

## <a name="create-a-logic-app"></a>Een logische app maken

Maak nu een [Azure Logic App-exemplaar.](../logic-apps/logic-apps-overview.md) Gebruik desgewenst [een integratieserviceomgeving (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) en [stelt een door de klant beheerde sleutel in](../logic-apps/customer-managed-keys-integration-service-environment.md) voor gebruik door uw Logic App.

Zodra uw Logic App is ingericht, gebruikt u de volgende stappen om een trigger voor uw pijplijn te configureren:

1. [Maak een door het systeem toegewezen beheerde identiteit](../logic-apps/create-managed-service-identity.md) om de app toegang te geven tot uw Azure Machine Learning Workspace.

1. Navigeer naar de maker-weergave Logic App en selecteer de sjabloon Blank Logic App. 
    > [!div class="mx-imgBorder"]
    > ![Lege sjabloon](media/how-to-trigger-published-pipeline/blank-template.png)

1. Zoek in de ontwerper naar **blob.** Selecteer de **trigger Wanneer een blob wordt toegevoegd of gewijzigd (alleen eigenschappen)** en voeg deze trigger toe aan uw Logische App.
    > [!div class="mx-imgBorder"]
    > ![Trigger toevoegen](media/how-to-trigger-published-pipeline/add-trigger.png)

1. Vul de verbindingsgegevens in voor het Blob-opslagaccount dat u wilt controleren op blobtoevoegingen of wijzigingen. Selecteer de container die u wilt controleren. 
 
    Kies de **interval** en **frequentie** om te peilen voor updates die voor u werken.  

    > [!NOTE]
    > Deze trigger controleert de geselecteerde container, maar controleert geen submappen.

1. Voeg een HTTP-actie toe die wordt uitgevoerd wanneer een nieuwe of gewijzigde blob wordt gedetecteerd. Selecteer **+ Nieuwe stap,** zoek vervolgens naar en selecteer de HTTP-actie.

  > [!div class="mx-imgBorder"]
  > ![Zoeken naar HTTP-actie](media/how-to-trigger-published-pipeline/search-http.png)

  Gebruik de volgende instellingen om uw actie te configureren:

  | Instelling | Waarde | 
  |---|---|
  | HTTP-actie | POST |
  | URI |het eindpunt van de gepubliceerde pijplijn die u als [voorwaarde hebt](#prerequisites) gevonden |
  | Verificatiemodus | Beheerde identiteit |

1. Stel uw planning in om de waarde in te stellen van [gegevenspadpijplijnparameters die](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-datapath-and-pipelineparameter.ipynb) u mogelijk hebt:

    ```json
    "DataPathAssignments": { 
         "input_datapath": { 
                            "DataStoreName": "<datastore-name>", 
                            "RelativePath": "@triggerBody()?['Name']" 
    } 
    }, 
    "ExperimentName": "MyRestPipeline", 
    "ParameterAssignments": { 
    "input_string": "sample_string3" 
    },
    ```

    Gebruik `DataStoreName` de u die u als voorwaarde aan uw werkruimte [hebt](#prerequisites)toegevoegd.
     
    > [!div class="mx-imgBorder"]
    > ![HTTP-instellingen](media/how-to-trigger-published-pipeline/http-settings.png)

1. Selecteer **Opslaan** en uw planning is nu klaar.