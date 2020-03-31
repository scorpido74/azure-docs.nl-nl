---
title: Gebeurtenisraster Machine Learning-gebeurtenisschema azure-gebeurtenisraster
description: Beschrijft de eigenschappen die worden geleverd voor Machine Learning Workspace-gebeurtenissen met Azure Event Grid
services: event-grid
author: jenns
ms.service: event-grid
ms.topic: reference
ms.date: 10/18/2019
ms.author: jenns
ms.openlocfilehash: 4051598a9abd787f6707e67a8c4dab12fc6d626a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79202141"
---
# <a name="azure-event-grid-event-schema-for-azure-machine-learning"></a>Azure Event Grid-gebeurtenisschema voor Azure Machine Learning

In dit artikel vindt u de eigenschappen en het schema voor gebeurtenissen in de werkruimte voor machine learning. Zie Azure Event Grid-gebeurtenisschema voor een inleiding tot gebeurtenisschema ['Azure Event Grid'.](event-schema.md)

Zie [AzureML-gebeurtenisbron](event-sources.md#azure-machine-learning)voor een lijst met voorbeeldscripts en -zelfstudies.

## <a name="available-event-types"></a>Beschikbare gebeurtenistypen

Azure Machine Learning zendt de volgende gebeurtenistypen uit:

| Gebeurtenistype | Beschrijving |
| ---------- | ----------- |
| Microsoft.MachineLearningServices.ModelRegistered | Verhoogd wanneer een nieuwe model- of modelversie met succes is geregistreerd. |
| Microsoft.MachineLearningServices.ModelDeployed | Verhoogd wanneer model(s) zijn geïmplementeerd op een eindpunt. |
| Microsoft.MachineLearningServices.RunCompleted | Verhoogd wanneer een run is voltooid. |
| Microsoft.MachineLearningServices.DatasetDriftdetected | Verhoogd wanneer een driftmonitor van de gegevensset drift detecteert. |
| Microsoft.MachineLearningServices.RunStatusChanged | Verhoogd wanneer een run status verandert in 'mislukt'. |

## <a name="the-contents-of-an-event-response"></a>De inhoud van een gebeurtenisantwoord

Wanneer een gebeurtenis wordt geactiveerd, verzendt de gebeurtenisrasterservice gegevens over die gebeurtenis naar een endpoint van een abonnement.

Deze sectie bevat een voorbeeld van hoe die gegevens er voor elke gebeurtenis uit zouden zien.

### <a name="microsoftmachinelearningservicesmodelregistered-event"></a>Gebeurtenis Microsoft.MachineLearningServices.ModelRegistered

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "models/sklearn_regression_model:20",
  "eventType": "Microsoft.MachineLearningServices.ModelRegistered",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "ModelName": "sklearn_regression_model",
    "ModelVersion": 20,
    "ModelTags": {
        "area": "diabetes",
        "type": "regression"
    },
    "ModelProperties": {
        "type": "test"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftmachinelearningservicesmodeldeployed-event"></a>Microsoft.MachineLearningServices.ModelDeployed-gebeurtenis

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "endpoints/my-sklearn-service",
  "eventType": "Microsoft.MachineLearningServices.ModelDeployed",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "ServiceName": "my-sklearn-service",
    "ServiceComputeType": "ACI",
    "ModelIds": "sklearn_regression_model:1,sklearn_regression_model:2",
    "ServiceTags": {
        "area": "diabetes",
        "type": "regression"
    },
    "ServiceProperties": {
        "type": "test"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftmachinelearningservicesruncompleted-event"></a>Microsoft.MachineLearningServices.RunCompleted- gebeurtenis

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "experiments/0fa9dfaa-cba3-4fa7-b590-23e48548f5c1/runs/AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5",
  "eventType": "Microsoft.MachineLearningServices.RunCompleted",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "ExperimentId": "0fa9dfaa-cba3-4fa7-b590-23e48548f5c1",
    "ExperimentName": "automl-local-regression",
    "RunId": "AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5",
    "RunType": null,
    "RunTags": {},
    "RunProperties": {
        "runTemplate": "automl_child",
        "pipeline_id": "5adc0a4fe02504a586f09a4fcbb241f9a4012062",
        "pipeline_spec": "{\"objects\": [{\"class_name\": \"StandardScaler\", \"module\": \"sklearn.preprocessing\", \"param_args\": [], \"param_kwargs\": {\"with_mean\": true, \"with_std\": false}, \"prepared_kwargs\": {}, \"spec_class\": \"preproc\"}, {\"class_name\": \"LassoLars\", \"module\": \"sklearn.linear_model\", \"param_args\": [], \"param_kwargs\": {\"alpha\": 0.001, \"normalize\": true}, \"prepared_kwargs\": {}, \"spec_class\": \"sklearn\"}], \"pipeline_id\": \"5adc0a4fe02504a586f09a4fcbb241f9a4012062\"}",
        "training_percent": "100",
        "predicted_cost": "0.062226144097381045",
        "iteration": "5",
        "run_template": "automl_child",
        "run_preprocessor": "StandardScalerWrapper",
        "run_algorithm": "LassoLars",
        "conda_env_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/conda_env_v_1_0_0.yml",
        "model_name": "AutoMLad912b2d65",
        "scoring_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/scoring_file_v_1_0_0.py",
        "model_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/model.pkl"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftmachinelearningservicesdatasetdriftdetected-event"></a>Gebeurtenis Microsoft.MachineLearningServices.DatasetDriftDegedetecteerd

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "datadrifts/{}/runs/{}",
  "eventType": "Microsoft.MachineLearningServices.DatasetDriftDetected",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "DataDriftId": "01d29aa4-e6a4-470a-9ef3-66660d21f8ef",
    "DataDriftName": "myDriftMonitor",
    "RunId": "01d29aa4-e6a4-470a-9ef3-66660d21f8ef_1571590300380",
    "BaseDatasetId": "3c56d136-0f64-4657-a0e8-5162089a88a3",
    "TargetDatasetId": "d7e74d2e-c972-4266-b5fb-6c9c182d2a74",
    "DriftCoefficient": 0.83503490684792081,
    "StartTime": "2019-07-04T00:00:00+00:00",
    "EndTime": "2019-07-05T00:00:00+00:00"
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftmachinelearningservicesrunstatuschanged-event"></a>Gebeurtenis Microsoft.MachineLearningServices.RunStatusChanged

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "experiments/0fa9dfaa-cba3-4fa7-b590-23e48548f5c1/runs/AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5",
  "eventType": "Microsoft.MachineLearningServices.RunCompleted",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "ExperimentId": "0fa9dfaa-cba3-4fa7-b590-23e48548f5c1",
    "ExperimentName": "automl-local-regression",
    "RunId": "AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5",
    "RunType": null,
    "RunTags": {},
    "RunProperties": {
        "runTemplate": "automl_child",
        "pipeline_id": "5adc0a4fe02504a586f09a4fcbb241f9a4012062",
        "pipeline_spec": "{\"objects\": [{\"class_name\": \"StandardScaler\", \"module\": \"sklearn.preprocessing\", \"param_args\": [], \"param_kwargs\": {\"with_mean\": true, \"with_std\": false}, \"prepared_kwargs\": {}, \"spec_class\": \"preproc\"}, {\"class_name\": \"LassoLars\", \"module\": \"sklearn.linear_model\", \"param_args\": [], \"param_kwargs\": {\"alpha\": 0.001, \"normalize\": true}, \"prepared_kwargs\": {}, \"spec_class\": \"sklearn\"}], \"pipeline_id\": \"5adc0a4fe02504a586f09a4fcbb241f9a4012062\"}",
        "training_percent": "100",
        "predicted_cost": "0.062226144097381045",
        "iteration": "5",
        "run_template": "automl_child",
        "run_preprocessor": "StandardScalerWrapper",
        "run_algorithm": "LassoLars",
        "conda_env_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/conda_env_v_1_0_0.yml",
        "model_name": "AutoMLad912b2d65",
        "scoring_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/scoring_file_v_1_0_0.py",
        "model_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/model.pkl"
    },
   "RunStatus": "failed"
   },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```




## <a name="event-properties"></a>Gebeurtenis-eigenschappen

Een gebeurtenis heeft de volgende gegevens op het hoogste niveau:

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| onderwerp | tekenreeks | Volledig resourcepad naar de gebeurtenisbron. Dit veld is niet schrijfbaar. Event Grid biedt deze waarde. |
| Onderwerp | tekenreeks | Het door de uitgever gedefinieerde pad naar het gebeurtenisonderwerp. |
| eventType | tekenreeks | Een van de geregistreerde gebeurtenistypen voor deze gebeurtenisbron. |
| eventTime | tekenreeks | De tijd dat de gebeurtenis wordt gegenereerd op basis van de UTC-tijd van de provider. |
| id | tekenreeks | Unieke id voor de gebeurtenis. |
| data | object | Gebeurtenisgegevens voor blobopslag. |
| dataVersion | tekenreeks | De schemaversie van het gegevensobject. De uitgever definieert de schemaversie. |
| metadataVersion | tekenreeks | De schemaversie van de metagegevens van de gebeurtenis. Event Grid definieert het schema voor de eigenschappen op het hoogste niveau. Event Grid biedt deze waarde. |

Het gegevensobject heeft de volgende eigenschappen voor elk gebeurtenistype:

### <a name="microsoftmachinelearningservicesmodelregistered"></a>Microsoft.MachineLearningServices.ModelRegistered

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| ModelName | tekenreeks | De naam van het model dat is geregistreerd. |
| ModelVersie | tekenreeks | De versie van het model dat is geregistreerd. |
| Modeltags | object | De tags van het model dat is geregistreerd. |
| ModelEigenschappen | object | De eigenschappen van het model dat is geregistreerd. |

### <a name="microsoftmachinelearningservicesmodeldeployed"></a>Microsoft.MachineLearningServices.ModelDeployed

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| ServiceName | tekenreeks | De naam van de geïmplementeerde service. |
| ServiceComputeType | tekenreeks | Het rekentype (bijvoorbeeld ACI, AKS) van de geïmplementeerde service. |
  | Modelids | tekenreeks | Een komma gescheiden lijst van model-id's. De id's van de modellen die in de service worden geïmplementeerd. |
| ServiceTags | object | De tags van de geïmplementeerde service. |
| ServiceEigenschappen | object | De eigenschappen van de geïmplementeerde service. |

### <a name="microsoftmachinelearningservicesruncompleted"></a>Microsoft.MachineLearningServices.RunCompleted

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| ExperimentId (ExperimentId) | tekenreeks | De ID van het experiment waartoe de run behoort. |
| ExperimentName | tekenreeks | De naam van het experiment waartoe de run behoort. |
| RunId (RunId) | tekenreeks | De ID van de Run die is voltooid. |
| RunType (RunType) | tekenreeks | Het runtype van de voltooide run. |
| RunTags | object | De tags van de voltooide run. |
| RunEigenschappen | object | De eigenschappen van de voltooide run. |

### <a name="microsoftmachinelearningservicesdatasetdriftdetected"></a>Microsoft.MachineLearningServices.DatasetDriftdetected

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| DataDriftId | tekenreeks | De id van de gegevensdriftmonitor die de gebeurtenis heeft geactiveerd. |
| DataDriftName | tekenreeks | De naam van de monitor gegevensdrift die de gebeurtenis heeft geactiveerd. |
| RunId (RunId) | tekenreeks | De ID van de run die gegevensdrift detecteerde. |
| BaseDatasetId (BaseDatasetId) | tekenreeks | De ID van de basisgegevensset die wordt gebruikt om drift te detecteren. |
| Doeldatasetid | tekenreeks | De ID van de doelgegevensset die wordt gebruikt om drift te detecteren. |
| Driftcoëfficiënt | double | Het coëfficiëntresultaat dat de gebeurtenis heeft geactiveerd. |
| StartTime | datum/tijd | De begintijd van de doelsetuurreeks die resulteerde in driftdetectie.  |
| EndTime | datum/tijd | De eindtijd van de doelsetuurreeks die resulteerde in driftdetectie. |

### <a name="microsoftmachinelearningservicesrunstatuschanged"></a>Microsoft.MachineLearningServices.RunStatusChanged

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| ExperimentId (ExperimentId) | tekenreeks | De ID van het experiment waartoe de run behoort. |
| ExperimentName | tekenreeks | De naam van het experiment waartoe de run behoort. |
| RunId (RunId) | tekenreeks | De ID van de Run die is voltooid. |
| RunType (RunType) | tekenreeks | Het runtype van de voltooide run. |
| RunTags | object | De tags van de voltooide run. |
| RunEigenschappen | object | De eigenschappen van de voltooide run. |
| RunStatus | tekenreeks | De status van de Run. |

## <a name="next-steps"></a>Volgende stappen

* Zie [Wat is gebeurtenisraster voor](overview.md) een inleiding tot Azure Event Grid?
* Zie Abonnement op gebeurtenisrasterabonnement voor meer informatie over het maken van een Azure Event [Grid-abonnement](subscription-creation-schema.md)
* Zie [Azure Machine Learning-gebeurtenissen gebruiken](/azure/machine-learning/service/concept-event-grid-integration) voor een inleiding tot het gebruik van Azure Event Grid met Azure Machine Learning
* Zie [Gebeurtenisgestuurde machine learning-werkstromen maken](/azure/machine-learning/service/how-to-use-event-grid) voor een voorbeeld van het gebruik van Azure Event Grid met Azure Machine Learning
