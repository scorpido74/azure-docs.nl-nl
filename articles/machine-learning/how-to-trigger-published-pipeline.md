---
title: De uitvoering van een ML-pijp lijn vanuit een logische app activeren
titleSuffix: Azure Machine Learning
description: Meer informatie over hoe u de uitvoering van een ML-pijp lijn kunt activeren met behulp van Azure Logic Apps.
services: machine-learning
author: sanpil
ms.author: sanpil
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 6bb976b8b310fb3eb4d0247a8d745599f688d7b5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77122855"
---
# <a name="trigger-a-run-of-a-machine-learning-pipeline-from-a-logic-app"></a>Een uitvoering van een Machine Learning pijp lijn vanuit een logische app activeren

De uitvoering van uw Azure Machine Learning-pijp lijn activeren wanneer er nieuwe gegevens worden weer gegeven. U kunt bijvoorbeeld de pijp lijn activeren om een nieuw model te trainen wanneer er nieuwe gegevens worden weer gegeven in het Blob Storage-account. Stel de trigger in met [Azure Logic apps](../logic-apps/logic-apps-overview.md).

## <a name="prerequisites"></a>Vereisten

* Een Azure Machine Learning-werkruimte. Zie [een Azure machine learning-werk ruimte maken](how-to-manage-workspace.md)voor meer informatie.

* Het REST-eind punt voor een gepubliceerde Machine Learning-pijp lijn. [Uw pijp lijn maken en publiceren](how-to-create-your-first-pipeline.md). Zoek vervolgens het REST-eind punt van uw PublishedPipeline met behulp van de pijp lijn-ID:
    
     ```
    # You can find the pipeline ID in Azure Machine Learning studio
    
    published_pipeline = PublishedPipeline.get(ws, id="<pipeline-id-here>")
    published_pipeline.endpoint 
    ```
* [Azure Blob-opslag](../storage/blobs/storage-blobs-overview.md) om uw gegevens op te slaan.
* [Een gegevens opslag](how-to-access-data.md) in uw werk ruimte die de details van uw Blob Storage-account bevat.

## <a name="create-a-logic-app"></a>Een logische app maken

Maak nu een exemplaar van de [Azure Logic-app](../logic-apps/logic-apps-overview.md) . Gebruik, indien gewenst, [een integratie service omgeving (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) en [Stel een door de klant beheerde sleutel](../logic-apps/customer-managed-keys-integration-service-environment.md) in voor gebruik door uw logische app.

Als uw logische app is ingericht, gebruikt u deze stappen om een trigger voor uw pijp lijn te configureren:

1. [Maak een door het systeem toegewezen beheerde identiteit](../logic-apps/create-managed-service-identity.md) om de app toegang te geven tot uw Azure machine learning-werkruimte.

1. Navigeer naar de weer gave van de ontwerp functie voor logische apps en selecteer de sjabloon lege logische app. 
    > [!div class="mx-imgBorder"]
    > ![Lege sjabloon](media/how-to-trigger-published-pipeline/blank-template.png)

1. Zoek in de ontwerp functie naar **BLOB**. Selecteer de trigger **Wanneer een BLOB wordt toegevoegd of gewijzigd (alleen eigenschappen)** en voeg deze trigger toe aan uw logische app.
    > [!div class="mx-imgBorder"]
    > ![Trigger toevoegen](media/how-to-trigger-published-pipeline/add-trigger.png)

1. Vul de verbindings gegevens in voor het Blob-opslag account dat u wilt bewaken voor het toevoegen of wijzigen van blobs. Selecteer de container die u wilt bewaken. 
 
    Kies het **interval** en de **frequentie** voor het controleren op updates die voor u werken.  

    > [!NOTE]
    > Met deze trigger wordt de geselecteerde container bewaakt, maar worden de submappen niet bewaakt.

1. Een HTTP-actie toevoegen die wordt uitgevoerd wanneer een nieuwe of gewijzigde BLOB wordt gedetecteerd. Selecteer **+ nieuwe stap**, zoek naar en selecteer de http-actie.

  > [!div class="mx-imgBorder"]
  > ![HTTP-actie zoeken](media/how-to-trigger-published-pipeline/search-http.png)

  Gebruik de volgende instellingen om uw actie te configureren:

  | Instelling | Waarde | 
  |---|---|
  | HTTP-actie | POST |
  | URI |het eind punt naar de gepubliceerde pijp lijn die u als een [vereiste](#prerequisites) hebt gevonden |
  | Verificatiemodus | Beheerde identiteit |

1. Stel uw planning in om de waarde in te stellen van een [DataPath-PipelineParameters](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-datapath-and-pipelineparameter.ipynb) die u mogelijk hebt:

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

    Gebruik de `DataStoreName` die u hebt toegevoegd aan uw werk ruimte als een [vereiste](#prerequisites).
     
    > [!div class="mx-imgBorder"]
    > ![HTTP-instellingen](media/how-to-trigger-published-pipeline/http-settings.png)

1. Selecteer **Opslaan** en uw planning is nu gereed.