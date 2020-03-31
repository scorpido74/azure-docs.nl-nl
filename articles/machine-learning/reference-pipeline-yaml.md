---
title: Machine Learning-pijplijn YAML
titleSuffix: Azure Machine Learning
description: Meer informatie over het definiëren van een machine learning-pijplijn met behulp van een YAML-bestand. YAML-pijplijndefinities worden gebruikt met de machine learning-extensie voor de Azure CLI.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: sanpil
author: sanpil
ms.date: 11/11/2019
ms.openlocfilehash: a677aaa891e21f4c9eeda02eebcb94e9d79a55ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79368822"
---
# <a name="define-machine-learning-pipelines-in-yaml"></a>Machine learning-pijplijnen definiëren in YAML

Meer informatie over het definiëren van uw machine learning-pijplijnen in [YAML.](https://yaml.org/) Bij het gebruik van de machine learning-extensie voor de Azure CLI verwachten veel van de pijplijngerelateerde opdrachten een YAML-bestand dat de pijplijn definieert.

In de volgende tabel wordt een overzicht weergegeven van wat wel en niet wordt ondersteund bij het definiëren van een pijplijn in YAML:

| Staptype | Ondersteund? |
| ----- | :-----: |
| PythonScriptStep | Ja |
| AdlaStep AdlaStep | Ja |
| AzureBatchStep AzureBatchStep | Ja |
| DatabricksStep | Ja |
| DataTransferStep | Ja |
| AutoMLStep | Nee |
| HyperDriveStep (HyperDriveStep) | Nee |
| ModuleStap | Ja |
| MPIStep | Nee |
| EstimatorStep | Nee |

## <a name="pipeline-definition"></a>Pijplijndefinitie

Een pijplijndefinitie gebruikt de volgende sleutels, die overeenkomen met de klasse [Pijplijnen:](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline.pipeline?view=azure-ml-py)

| YAML-toets | Beschrijving |
| ----- | ----- |
| `name` | De beschrijving van de pijpleiding. |
| `parameters` | Parameter(s) naar de pijplijn. |
| `data_reference` | Hiermee bepaalt u hoe en waar gegevens beschikbaar moeten worden gesteld in een run. |
| `default_compute` | Standaard compute-doel waarbij alle stappen in de pijplijn worden uitgevoerd. |
| `steps` | De stappen die in de pijplijn worden gebruikt. |

## <a name="parameters"></a>Parameters

De `parameters` sectie gebruikt de volgende toetsen, die overeenkomen met de klasse [PipelineParameter:](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelineparameter?view=azure-ml-py)

| YAML-toets | Beschrijving |
| ---- | ---- |
| `type` | Het waardetype van de parameter. Geldige typen `string` `int`zijn `float` `bool`, `datapath`, , of . |
| `default` | De standaardwaarde. |

Elke parameter krijgt de naam. In het volgende YAML-fragment worden `NumIterationsParameter` `DataPathParameter`bijvoorbeeld `NodeCountParameter`drie parameters gedefinieerd met de naam , en :

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        NumIterationsParameter:
            type: int
            default: 40
        DataPathParameter:
            type: datapath
            default:
                datastore: workspaceblobstore
                path_on_datastore: sample2.txt
        NodeCountParameter:
            type: int
            default: 4
```

## <a name="data-reference"></a>Verwijzing naar gegevens

De `data_references` sectie maakt gebruik van de volgende toetsen, die overeenkomen met de [DataReference:](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py)

| YAML-toets | Beschrijving |
| ----- | ----- |
| `datastore` | De datastore om naar te verwijzen. |
| `path_on_datastore` | Het relatieve pad in de achtergrondopslag voor de gegevensverwijzing. |

Elke gegevensverwijzing is opgenomen in een sleutel. In het volgende YAML-fragment wordt bijvoorbeeld een `employee_data`gegevensverwijzing gedefinieerd die is opgeslagen in de sleutel met de naam :

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        employee_data:
            datastore: adftestadla
            path_on_datastore: "adla_sample/sample_input.csv"
```

## <a name="steps"></a>Stappen

Stappen definiëren een computationele omgeving, samen met de bestanden die op de omgeving moeten worden uitgevoerd. Als u het type stap `type` wilt definiëren, gebruikt u de sleutel:

| Staptype | Beschrijving |
| ----- | ----- |
| `AdlaStep` | Hiermee wordt een U-SQL-script uitgevoerd met Azure Data Lake Analytics. Komt overeen met de [adlaStep-klasse.](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.adlastep?view=azure-ml-py) |
| `AzureBatchStep` | Voert taken uit met Azure Batch. Komt overeen met de klasse [AzureBatchStep.](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.azurebatchstep?view=azure-ml-py) |
| `DatabricsStep` | Hiermee voegt u een Databricks-notitieblok, Python-script of JAR toe. Komt overeen met de klasse [DatabricksStep.](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricksstep?view=azure-ml-py) |
| `DataTransferStep` | Gegevens worden overgedragen tussen opslagopties. Komt overeen met de klasse [DataTransferStep.](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py) |
| `PythonScriptStep` | Draait een Python-script uit. Komt overeen met de klasse [PythonScriptStep.](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py) |

### <a name="adla-step"></a>ADLA-stap

| YAML-toets | Beschrijving |
| ----- | ----- |
| `script_name` | De naam van het U-SQL-script (ten opzichte van de). `source_directory` |
| `compute_target` | Het Azure Data Lake-rekendoel dat voor deze stap moet worden gebruikt. |
| `parameters` | [Parameters](#parameters) voor de pijplijn. |
| `inputs` | Ingangen kunnen [InputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py), [DataReference](#data-reference), [PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), [Dataset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py), [DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)of [PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py)zijn . |
| `outputs` | Uitvoer kan [pipelinedata](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) of [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py)zijn. |
| `source_directory` | Directory die het script, samenstellingen, enz. |
| `priority` | De prioriteitswaarde die moet worden gebruikt voor de huidige taak. |
| `params` | Woordenboek van naam-waardeparen. |
| `degree_of_parallelism` | De mate van parallellisme te gebruiken voor deze baan. |
| `runtime_version` | De runtime-versie van de Data Lake Analytics-engine. |
| `allow_reuse` | Hiermee bepaalt u of de stap eerdere resultaten opnieuw moet gebruiken wanneer deze opnieuw worden uitgevoerd met dezelfde instellingen. |

In het volgende voorbeeld vindt u een ADLA-stapdefinitie:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        employee_data:
            datastore: adftestadla
            path_on_datastore: "adla_sample/sample_input.csv"
    default_compute: adlacomp
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "AdlaStep"
            name: "MyAdlaStep"
            script_name: "sample_script.usql"
            source_directory: "D:\\scripts\\Adla"
            inputs:
                employee_data:
                    source: employee_data
            outputs:
                OutputData:
                    destination: Output4
                    datastore: adftestadla
                    bind_mode: mount
```

### <a name="azure-batch-step"></a>Azure Batch-stap

| YAML-toets | Beschrijving |
| ----- | ----- |
| `compute_target` | Het berekendoel van Azure Batch dat voor deze stap moet worden gebruikt. |
| `inputs` | Ingangen kunnen [InputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py), [DataReference](#data-reference), [PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), [Dataset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py), [DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)of [PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py)zijn . |
| `outputs` | Uitvoer kan [pipelinedata](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) of [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py)zijn. |
| `source_directory` | Directory met de modulebinaries, uitvoerbare, samenstellingen, enz. |
| `executable` | Naam van de opdracht/uitvoerbare die wordt uitgevoerd als onderdeel van deze taak. |
| `create_pool` | Booleaanse vlag om aan te geven of u de groep moet maken voordat u de taak uitvoert. |
| `delete_batch_job_after_finish` | Booleaanse vlag om aan te geven of de taak moet worden verwijderd uit het Batch-account nadat deze is voltooid. |
| `delete_batch_pool_after_finish` | Booleaanse vlag om aan te geven of de groep moet worden verwijderd nadat de taak is voltooid. |
| `is_positive_exit_code_failure` | Booleaanse vlag om aan te geven of de taak mislukt als de taak wordt afgesloten met een positieve code. |
| `vm_image_urn` | Als `create_pool` `True`dat wel `VirtualMachineConfiguration`is, en vm gebruikt . |
| `pool_id` | De ID van het zwembad waar de taak wordt uitgevoerd. |
| `allow_reuse` | Hiermee bepaalt u of de stap eerdere resultaten opnieuw moet gebruiken wanneer deze opnieuw worden uitgevoerd met dezelfde instellingen. |

In het volgende voorbeeld vindt u een azure batch-stapdefinitie:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        input:
            datastore: workspaceblobstore
            path_on_datastore: "input.txt"
    default_compute: testbatch
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "AzureBatchStep"
            name: "MyAzureBatchStep"
            pool_id: "MyPoolName"
            create_pool: true
            executable: "azurebatch.cmd"
            source_directory: "D:\\scripts\\AureBatch"
            allow_reuse: false
            inputs:
                input:
                    source: input
            outputs:
                output:
                    destination: output
                    datastore: workspaceblobstore
```

### <a name="databricks-step"></a>Databricks-stap

| YAML-toets | Beschrijving |
| ----- | ----- |
| `compute_target` | Het azure Databricks-rekendoel dat voor deze stap moet worden gebruikt. |
| `inputs` | Ingangen kunnen [InputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py), [DataReference](#data-reference), [PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), [Dataset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py), [DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)of [PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py)zijn . |
| `outputs` | Uitvoer kan [pipelinedata](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) of [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py)zijn. |
| `run_name` | De naam in Databricks voor deze run. |
| `source_directory` | Directory die het script en andere bestanden bevat. |
| `num_workers` | Het statische aantal werknemers voor het cluster Databricks run. |
| `runconfig` | Het pad `.runconfig` naar een bestand. Dit bestand is een YAML-weergave van de klasse [RunConfiguration.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py) Zie [runconfigschema.json](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json)voor meer informatie over de structuur van dit bestand. |
| `allow_reuse` | Hiermee bepaalt u of de stap eerdere resultaten opnieuw moet gebruiken wanneer deze opnieuw worden uitgevoerd met dezelfde instellingen. |

In het volgende voorbeeld vindt u een stap Databricks:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        adls_test_data:
            datastore: adftestadla
            path_on_datastore: "testdata"
        blob_test_data:
            datastore: workspaceblobstore
            path_on_datastore: "dbtest"
    default_compute: mydatabricks
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "DatabricksStep"
            name: "MyDatabrickStep"
            run_name: "DatabricksRun"
            python_script_name: "train-db-local.py"
            source_directory: "D:\\scripts\\Databricks"
            num_workers: 1
            allow_reuse: true
            inputs:
                blob_test_data:
                    source: blob_test_data
            outputs:
                OutputData:
                    destination: Output4
                    datastore: workspaceblobstore
                    bind_mode: mount
```

### <a name="data-transfer-step"></a>Stap voor gegevensoverdracht

| YAML-toets | Beschrijving |
| ----- | ----- |
| `compute_target` | Het rekendoel azure data factory dat voor deze stap moet worden gebruikt. |
| `source_data_reference` | Invoerverbinding die fungeert als de bron van gegevensoverdrachtsbewerkingen. Ondersteunde waarden zijn [InputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py), [DataReference](#data-reference), [PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), [Dataset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py), [DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)of [PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py). |
| `destination_data_reference` | Invoerverbinding die dient als bestemming voor gegevensoverdrachtsbewerkingen. Ondersteunde waarden zijn [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) en [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py). |
| `allow_reuse` | Hiermee bepaalt u of de stap eerdere resultaten opnieuw moet gebruiken wanneer deze opnieuw worden uitgevoerd met dezelfde instellingen. |

In het volgende voorbeeld vindt u een stap voor gegevensoverdracht:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        adls_test_data:
            datastore: adftestadla
            path_on_datastore: "testdata"
        blob_test_data:
            datastore: workspaceblobstore
            path_on_datastore: "testdata"
    default_compute: adftest
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "DataTransferStep"
            name: "MyDataTransferStep"
            adla_compute_name: adftest
            source_data_reference:
                adls_test_data:
                    source: adls_test_data
            destination_data_reference:
                blob_test_data:
                    source: blob_test_data
```

### <a name="python-script-step"></a>Python-scriptstap

| YAML-toets | Beschrijving |
| ----- | ----- |
| `compute_target` | Het rekendoel dat u voor deze stap moet gebruiken. Het rekendoel kan een Azure Machine Learning Compute, Virtual Machine (zoals de Data Science VM) of HDInsight zijn. |
| `inputs` | Ingangen kunnen [InputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py), [DataReference](#data-reference), [PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), [Dataset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py), [DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)of [PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py)zijn . |
| `outputs` | Uitvoer kan [pipelinedata](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) of [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py)zijn. |
| `script_name` | De naam van het Python-script (ten opzichte van `source_directory`). |
| `source_directory` | Directory met het script, de Conda-omgeving, enz. |
| `runconfig` | Het pad `.runconfig` naar een bestand. Dit bestand is een YAML-weergave van de klasse [RunConfiguration.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py) Zie [runconfig.json](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json)voor meer informatie over de structuur van dit bestand. |
| `allow_reuse` | Hiermee bepaalt u of de stap eerdere resultaten opnieuw moet gebruiken wanneer deze opnieuw worden uitgevoerd met dezelfde instellingen. |

In het volgende voorbeeld vindt u een Python-scriptstap:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        DataReference1:
            datastore: workspaceblobstore
            path_on_datastore: testfolder/sample.txt
    default_compute: cpu-cluster
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "PythonScriptStep"
            name: "MyPythonScriptStep"
            script_name: "train.py"
            allow_reuse: True
            source_directory: "D:\\scripts\\PythonScript"
            inputs:
                InputData:
                    source: DataReference1
            outputs:
                OutputData:
                    destination: Output4
                    datastore: workspaceblobstore
                    bind_mode: mount
```

## <a name="schedules"></a>Planningen

Bij het definiëren van de planning voor een pijplijn kan deze worden geactiveerd of terugkerend op basis van een tijdsinterval. De volgende zijn de sleutels die worden gebruikt om een schema te definiëren:

| YAML-toets | Beschrijving |
| ----- | ----- |
| `description` | Een beschrijving van het schema. |
| `recurrence` | Bevat herhalingsinstellingen als het schema wordt herhaald. |
| `pipeline_parameters` | Alle parameters die vereist zijn door de pijplijn. |
| `wait_for_provisioning` | Of te wachten op de inrichting van het schema te voltooien. |
| `wait_timeout` | Het aantal seconden om te wachten voordat de timing uit. |
| `datastore_name` | Het gegevensarchief dat moet worden gecontroleerd op gewijzigde/toegevoegde blobs. |
| `polling_interval` | Hoe lang, in minuten, tussen polling voor gewijzigde / toegevoegde blobs. Standaardwaarde: 5 minuten. Alleen ondersteund voor datastore-schema's. |
| `data_path_parameter_name` | De naam van de parameter pijplijn voor gegevenspaden die moet worden ingesteld met het gewijzigde blobpad. Alleen ondersteund voor datastore-schema's. |
| `continue_on_step_failure` | Of u de uitvoering van andere stappen in de ingediende PipelineRun wilt voortzetten als een stap mislukt. Als dit is `continue_on_step_failure` verstrekt, wordt de instelling van de pijplijn overschreven.
| `path_on_datastore` | Optioneel. Het pad op het gegevensarchief dat moet worden gecontroleerd op gewijzigde/toegevoegde blobs. Het pad bevindt zich onder de container voor het gegevensarchief, zodat het werkelijke pad dat de planning bewaakt container/`path_on_datastore`is. Als deze er geen is, wordt de datastore-container gecontroleerd. Toevoegingen/wijzigingen in een submap `path_on_datastore` van de worden niet gecontroleerd. Alleen ondersteund voor datastore-schema's. |

In het volgende voorbeeld wordt de definitie voor een schema dat door gegevensarchief wordt geactiveerd:

```yaml
Schedule: 
      description: "Test create with datastore" 
      recurrence: ~ 
      pipeline_parameters: {} 
      wait_for_provisioning: True 
      wait_timeout: 3600 
      datastore_name: "workspaceblobstore" 
      polling_interval: 5 
      data_path_parameter_name: "input_data" 
      continue_on_step_failure: None 
      path_on_datastore: "file/path" 
```

Gebruik bij het definiëren van een `recurrence`terugkerend **schema**de volgende toetsen onder :

| YAML-toets | Beschrijving |
| ----- | ----- |
| `frequency` | Hoe vaak het schema terugkeert. Geldige waarden `"Minute"` `"Hour"`zijn `"Day"` `"Week"`, `"Month"`, , of . |
| `interval` | Hoe vaak het schema in brand vliegt. De gehele waarde is het aantal tijdeenheden dat moet wachten tot de planning opnieuw wordt geactiveerd. |
| `start_time` | De begintijd van het schema. De tekenreeksnotatie van `YYYY-MM-DDThh:mm:ss`de waarde is . Als er geen begintijd is opgegeven, wordt de eerste werkbelasting direct uitgevoerd en worden toekomstige workloads uitgevoerd op basis van de planning. Als de begintijd in het verleden is, wordt de eerste werkbelasting uitgevoerd op de eerstberekende runtime. |
| `time_zone` | De tijdzone voor de begintijd. Als er geen tijdzone is voorzien, wordt UTC gebruikt. |
| `hours` | Als `frequency` `"Day"` dat `"Week"`wel het is of is, u een of meer gehele getallen van 0 tot 23, gescheiden door komma's, opgeven als de uren van de dag waarop de pijplijn moet worden uitgevoerd. Alleen `time_of_day` `hours` of `minutes` en kan worden gebruikt. |
| `minutes` | Als `frequency` `"Day"` dat `"Week"`wel het is of is, u een of meer gehele getallen van 0 tot 59, gescheiden door komma's, opgeven als de minuten van het uur waarop de pijplijn moet worden uitgevoerd. Alleen `time_of_day` `hours` of `minutes` en kan worden gebruikt. |
| `time_of_day` | Als `frequency` `"Day"` dat `"Week"`wel het is of, u een tijdstip opgeven waarop het schema moet worden uitgevoerd. De tekenreeksnotatie van `hh:mm`de waarde is . Alleen `time_of_day` `hours` of `minutes` en kan worden gebruikt. |
| `week_days` | Als `frequency` `"Week"`dat het wel is, u een of meer dagen opgeven, gescheiden door komma's, wanneer de planning moet worden uitgevoerd. Geldige waarden `"Monday"` `"Tuesday"`zijn `"Wednesday"` `"Thursday"`, `"Friday"` `"Saturday"`, `"Sunday"`, , en . |

In het volgende voorbeeld vindt u de definitie voor een terugkerend schema:

```yaml
Schedule: 
    description: "Test create with recurrence" 
    recurrence: 
        frequency: Week # Can be "Minute", "Hour", "Day", "Week", or "Month". 
        interval: 1 # how often fires 
        start_time: 2019-06-07T10:50:00 
        time_zone: UTC 
        hours: 
        - 1 
        minutes: 
        - 0 
        time_of_day: null 
        week_days: 
        - Friday 
    pipeline_parameters: 
        'a': 1 
    wait_for_provisioning: True 
    wait_timeout: 3600 
    datastore_name: ~ 
    polling_interval: ~ 
    data_path_parameter_name: ~ 
    continue_on_step_failure: None 
    path_on_datastore: ~ 
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [gebruik van de CLI-extensie voor Azure Machine Learning](reference-azure-machine-learning-cli.md).
