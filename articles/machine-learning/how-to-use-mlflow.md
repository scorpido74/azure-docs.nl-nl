---
title: MLflow Tracking voor ML-experimenten
titleSuffix: Azure Machine Learning
description: MLflow instellen met Azure Machine Learning om statistieken en artefacten uit ML-modellen te registreren die zijn gemaakt in Databricks-clusters, uw lokale omgeving of VM-omgeving.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.topic: conceptual
ms.date: 02/03/2020
ms.custom: seodec18
ms.openlocfilehash: dce7db9fc508c70d79be62a7e97b3bf52a316b22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76983695"
---
# <a name="track-models-metrics-with-mlflow-and-azure-machine-learning-preview"></a>Modelstatistieken bijhouden met MLflow en Azure Machine Learning (voorbeeld)

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In dit artikel wordt uitgelegd hoe u de tracking URI- en logging-API van MLflow, gezamenlijk bekend als [MLflow Tracking,](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api)inschakelen om uw MLflow-experimenten en Azure Machine Learning met elkaar te verbinden. Hiermee u experimentstatistieken en -artefacten bijhouden en registreren in uw [Azure Machine Learning-werkruimte.](https://docs.microsoft.com/azure/machine-learning/concept-azure-machine-learning-architecture#workspaces) Als u Al MLflow Tracking gebruikt voor uw experimenten, biedt de werkruimte een gecentraliseerde, veilige en schaalbare locatie om trainingsstatistieken en -modellen op te slaan.

<!--
+ Deploy your MLflow experiments as an Azure Machine Learning web service. By deploying as a web service, you can apply the Azure Machine Learning monitoring and data drift detection functionalities to your production models. 
-->

[MLflow](https://www.mlflow.org) is een open-source bibliotheek voor het beheren van de levenscyclus van uw machine learning experimenten. MLFlow Tracking is een onderdeel van MLflow dat uw trainingsrunstatistieken en modelartefacten registreert en bijhoudt, ongeacht de omgeving van uw experiment - lokaal op uw computer, op een extern rekendoel, een virtuele machine of een Azure Databricks-cluster. 

In het volgende diagram wordt geïllustreerd dat u met MLflow Tracking de runmetrics van een experiment bijhoudt en modelartefacten opslaat in uw Azure Machine Learning-werkruimte.

![mlflow met azure machine learning-diagram](./media/how-to-use-mlflow/mlflow-diagram-track.png)

> [!TIP]
> De informatie in dit document is voornamelijk bedoeld voor gegevenswetenschappers en ontwikkelaars die het modeltrainingsproces willen volgen. Zie Azure [Machine Learning](monitor-azure-machine-learning.md)controleren als u een beheerder bent die het gebruik van resources en gebeurtenissen van Azure Machine Learning wil controleren, zoals quota, voltooide trainingsuitvoeringen of voltooide modelimplementaties.

## <a name="compare-mlflow-and-azure-machine-learning-clients"></a>MLflow- en Azure Machine Learning-clients vergelijken

 In de onderstaande tabel worden de verschillende clients samengevat die Azure Machine Learning kunnen gebruiken en hun respectieve functiemogelijkheden.

 MLflow Tracking biedt metrische logging- en artefactopslagfunctionaliteiten die alleen anders beschikbaar zijn via de [Azure Machine Learning Python SDK.](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)


| | MLflow-tracking&nbsp; <!--& Deployment--> | Azure Machine Learning Python SDK |  Azure Machine Learning CLI | Azure Machine Learning Studio|
|---|---|---|---|---|
| Werkruimte beheren |   | ✓ | ✓ | ✓ |
| Gegevensarchieven gebruiken  |   | ✓ | ✓ | |
| Logboekstatistieken      | ✓ | ✓ |   | |
| Artefacten uploaden | ✓ | ✓ |   | |
| Metrische gegevens bekijken     | ✓ | ✓ | ✓ | ✓ |
| Compute beheren   |   | ✓ | ✓ | ✓ |

<!--| Deploy models    | ✓ | ✓ | ✓ | ✓ |
|Monitor model performance||✓|  |   |
| Detect data drift |   | ✓ |   | ✓ |
-->
## <a name="prerequisites"></a>Vereisten

* [Installeer MLflow.](https://mlflow.org/docs/latest/quickstart.html)
* [Installeer de Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) op uw lokale computer De SDK biedt de connectiviteit voor MLflow om toegang te krijgen tot uw werkruimte.
* [Maak een Azure Machine Learning Workspace](how-to-manage-workspace.md).

## <a name="track-local-runs"></a>Lokale runs bijhouden

Met MLflow Tracking met Azure Machine Learning u de geregistreerde statistieken en artefacten uit uw lokale werkruimte opslaan in uw Azure Machine Learning-werkruimte.

Installeer `azureml-mlflow` het pakket om MLflow Tracking met Azure Machine Learning te gebruiken voor uw experimenten die lokaal worden uitgevoerd in een Jupyter-notitieblok of codeeditor.

```shell
pip install azureml-mlflow
```

>[!NOTE]
>De azureml.contrib-naamruimte verandert regelmatig, omdat we werken aan het verbeteren van de service. Als zodanig moet alles in deze naamruimte worden beschouwd als een voorbeeld en niet volledig worden ondersteund door Microsoft.

Importeer `mlflow` de [`Workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py) klassen en klassen om toegang te krijgen tot de trackingURI van MLflow en configureer uw werkruimte.

In de volgende `get_mlflow_tracking_uri()` code wijst de methode een uniek `ws`uri-adres voor bijhouden toe aan de werkruimte en `set_tracking_uri()` wijst de MLflow-tracking-URI naar dat adres.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

>[!NOTE]
>De tracking URI is geldig tot een uur of minder. Als u uw script opnieuw start na enige idle tijd, gebruikt u de get_mlflow_tracking_uri-API om een nieuwe URI te krijgen.

Stel de naam van `set_experiment()` het MLflow-experiment in en start uw trainingsrun met `start_run()`. Gebruik `log_metric()` vervolgens om de MLflow-logboekregistratie-API te activeren en te beginnen met het registreren van uw trainingsrunstatistieken.

```Python
experiment_name = 'experiment_with_mlflow'
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    mlflow.log_metric('alpha', 0.03)
```

## <a name="track-remote-runs"></a>Externe oplaten bijhouden

Met MLflow Tracking met Azure Machine Learning u de geregistreerde statistieken en artefacten van uw afstandsbediening opslaan in uw Azure Machine Learning-werkruimte.

Met externe uitvoeringen u uw modellen trainen op krachtigere computes, zoals virtuele machines met GPU's of Machine Learning Compute-clusters. Zie [Rekendoelen instellen voor modeltraining](how-to-set-up-training-targets.md) voor meer informatie over verschillende rekenopties.

Configureer uw compute- en [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) trainingsrunomgeving met de klasse. Neem `mlflow` `azureml-mlflow` en pip pakketten [`CondaDependencies`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) in de sectie van de omgeving. Bouw [`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) vervolgens met uw externe gegevensals rekendoel.

```Python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core import ScriptRunConfig

exp = Experiment(workspace = 'my_workspace',
                 name='my_experiment')

mlflow_env = Environment(name='mlflow-env')

cd = CondaDependencies.create(pip_packages=['mlflow', 'azureml-mlflow'])

mlflow_env.python.conda_dependencies = cd

src = ScriptRunConfig(source_directory='./my_script_location', script='my_training_script.py')

src.run_config.target = 'my-remote-compute-compute'
src.run_config.environment = mlflow_env
```

Importeer `mlflow` in je trainingsscript om de MLflow-logboekregistratie-API's te gebruiken en begin met het registreren van je run-statistieken.

```Python
import mlflow

with mlflow.start_run():
    mlflow.log_metric('example', 1.23)
```

Gebruik met deze configuratie van `Experiment.submit('train.py')` de reken- en trainingsuitvoering de methode om een run in te dienen. Met deze methode wordt automatisch de MLflow-tracking URI ingesteld en wordt de logboekregistratie van MLflow naar uw werkruimte doorgebladert.

```Python
run = exp.submit(src)
```

## <a name="track-azure-databricks-runs"></a>Azure Databricks-uitvoeringen bijhouden

Met MLflow Tracking met Azure Machine Learning u de geregistreerde statistieken en artefacten opslaan van uw Azure Databricks-werkplekken in uw Azure Machine Learning-werkruimte.

Als u uw Mlflow-experimenten met Azure Databricks wilt uitvoeren, moet u eerst een [Azure Databricks-werkruimte en -cluster maken.](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) Installeer in uw cluster de *azureml-mlflow-bibliotheek* van PyPi om ervoor te zorgen dat uw cluster toegang heeft tot de benodigde functies en klassen.

Importeer vanaf hier uw experimentnotitieblok, bevestig het aan uw Azure Databricks-cluster en voer uw experiment uit. 

### <a name="install-libraries"></a>Bibliotheken installeren

Als u bibliotheken op uw cluster wilt installeren, navigeert u naar het tabblad **Bibliotheken** en klikt u op **Nieuw installeren**

 ![mlflow met azure machine learning-diagram](./media/how-to-use-mlflow/azure-databricks-cluster-libraries.png)

Typ azureml-mlflow in het veld **Pakket** en klik op installeren. Herhaal deze stap als dat nodig is om andere extra pakketten voor uw cluster voor uw experiment te installeren.

 ![mlflow met azure machine learning-diagram](./media/how-to-use-mlflow/install-libraries.png)

### <a name="set-up-your-notebook-and-workspace"></a>Uw notitieblok en werkruimte instellen

Zodra uw cluster is ingesteld, importeert u het experimentnotitieblok, opent u het en koppelt u het cluster eraan.

De volgende code moet in uw experimentnotitieblok staan. Met deze code worden de details van uw Azure-abonnement opdekweg gegeven om uw werkruimte te instantiëren. Deze code gaat ervan uit dat u een bestaande brongroep en Azure Machine Learning-werkruimte hebt, anders u [deze maken.](how-to-manage-workspace.md) 

```python
import mlflow
import mlflow.azureml
import azureml.mlflow
import azureml.core

from azureml.core import Workspace
from azureml.mlflow import get_portal_url

subscription_id = 'subscription_id'

# Azure Machine Learning resource group NOT the managed resource group
resource_group = 'resource_group_name' 

#Azure Machine Learning workspace name, NOT Azure Databricks workspace
workspace_name = 'workspace_name'  

# Instantiate Azure Machine Learning workspace
ws = Workspace.get(name=workspace_name,
                   subscription_id=subscription_id,
                   resource_group=resource_group)
```

#### <a name="connect-your-azure-databricks-and-azure-machine-learning-workspaces"></a>Uw Azure Databricks- en Azure Machine Learning-werkruimten verbinden

In de [Azure-portal](https://ms.portal.azure.com)u uw Azure Databricks -werkruimte (ADB) koppelen aan een nieuwe of bestaande Azure Machine Learning-werkruimte. Ga hiervoor naar uw ADB-werkruimte en selecteer de knop **Azure Machine Learning-werkruimte koppelen** rechtsonder. Als u uw werkruimten koppelt, u uw experimentgegevens bijhouden in de Azure Machine Learning-werkruimte. 

### <a name="link-mlflow-tracking-to-your-workspace"></a>MLflow-tracking koppelen aan uw werkruimte

Nadat u uw werkruimte hebt geanimeerd, stelt u de MLflow-trackingURI in. Hiermee koppelt u de MLflow-tracking aan Azure Machine Learning-werkruimte. Na het koppelen worden al uw experimenten in de beheerde Azure Machine Learning-trackingservice terecht.

#### <a name="directly-set-mlflow-tracking-in-your-notebook"></a>MLflow Tracking direct instellen in uw notitieblok

```python
uri = ws.get_mlflow_tracking_uri()
mlflow.set_tracking_uri(uri)
```

Importeer in je trainingsscript mlflow om de MLflow-log-API's te gebruiken en begin met het registreren van je runmetrics. In het volgende voorbeeld wordt de statistiek tijdvakverlies bijlogboeken opgeslagen. 

```python
import mlflow 
mlflow.log_metric('epoch_loss', loss.item()) 
```

#### <a name="automate-setting-mlflow-tracking"></a>MLflow Tracking automatiseren

In plaats van de tracking URI handmatig in te stellen in elke volgende experimentnotitiebloksessie op uw clusters, doet u dit automatisch met behulp van dit [Azure Machine Learning Tracking Cluster Init-script.](https://github.com/Azure/MachineLearningNotebooks/blob/3ce779063b000e0670bdd1acc6bc3a4ee707ec13/how-to-use-azureml/azure-databricks/linking/README.md)

Wanneer u correct bent geconfigureerd, u uw MLflow-trackinggegevens zien in de Azure Machine Learning REST API en alle clients, en in Azure Databricks via de MLflow-gebruikersinterface of met behulp van de MLflow-client.

## <a name="view-metrics-and-artifacts-in-your-workspace"></a>Statistieken en artefacten in uw werkruimte weergeven

De statistieken en artefacten van MLflow-logboekregistratie worden bewaard in uw werkruimte. Als u ze altijd wilt bekijken, navigeert u naar uw werkruimte en zoekt u het experiment op naam in uw werkruimte in [Azure Machine Learning-studio.](https://ml.azure.com)  Of voer de onderstaande code uit. 

```python
run.get_metrics()
ws.get_details()
```

<!-- ## Deploy MLflow models as a web service

Deploying your MLflow experiments as an Azure Machine Learning web service allows you to leverage the Azure Machine Learning model management and data drift detection capabilities and apply them to your production models.

The following diagram demonstrates that with the MLflow deploy API you can deploy your existing MLflow models as an Azure Machine Learning web service, despite their frameworks--PyTorch, Tensorflow, scikit-learn, ONNX, etc., and manage your production models in your workspace.

![mlflow with azure machine learning diagram](./media/how-to-use-mlflow/mlflow-diagram-deploy.png)

### Log your model

Before you can deploy, be sure that your model is saved so you can reference it and its path location for deployment. In your training script, there should be code similar to the following [mlflow.sklearn.log_model()](https://www.mlflow.org/docs/latest/python_api/mlflow.sklearn.html) method, that saves your model to the specified outputs directory. 

```python
# change sklearn to pytorch, tensorflow, etc. based on your experiment's framework 
import mlflow.sklearn

# Save the model to the outputs directory for capture
mlflow.sklearn.log_model(regression_model, model_save_path)
```
>[!NOTE]
> Include the `conda_env` parameter to pass a dictionary representation of the dependencies and environment this model should be run in.

### Retrieve model from previous run

To retrieve the run, you need the run ID and the path in run history of where the model was saved. 

```python
# gets the list of runs for your experiment as an array
experiment_name = 'experiment-with-mlflow'
exp = ws.experiments[experiment_name]
runs = list(exp.get_runs())

# get the run ID and the path in run history
runid = runs[0].id
model_save_path = 'model'
```

### Create Docker image

The `mlflow.azureml.build_image()` function builds a Docker image from the saved model in a framework-aware manner. It automatically creates the framework-specific inferencing wrapper code and specifies package dependencies for you. Specify the model path, your workspace, run ID and other parameters.

The following code builds a docker image using *runs:/<run.id>/model* as the model_uri path for a Scikit-learn experiment.

```python
import mlflow.azureml

azure_image, azure_model = mlflow.azureml.build_image(model_uri='runs:/{}/{}'.format(runid, model_save_path),
                                                      workspace=ws,
                                                      model_name='sklearn-model',
                                                      image_name='sklearn-image',
                                                      synchronous=True)
```
The creation of the Docker image can take several minutes. 

### Deploy the Docker image 

After the image is created, use the Azure Machine Learning SDK to deploy the image as a web service.

First, specify the deployment configuration. Azure Container Instance (ACI) is a suitable choice for a quick dev-test deployment, while Azure Kubernetes Service (AKS) is suitable for scalable production deployments.

#### Deploy to ACI

Set up your deployment configuration with the [deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) method. You can also add tags and descriptions to help keep track of your web service.

```python
from azureml.core.webservice import AciWebservice, Webservice

# Configure 
aci_config = AciWebservice.deploy_configuration(cpu_cores=1, 
                                                memory_gb=1, 
                                                tags={'method' : 'sklearn'}, 
                                                description='Diabetes model',
                                                location='eastus2')
```

Then, deploy the image by using the Azure Machine Learning SDK [deploy_from_image()](/python/api/azureml-core/azureml.core.webservice.webservice(class)?view=azure-ml-py#deploy-from-image-workspace--name--image--deployment-config-none--deployment-target-none--overwrite-false-) method. 

```python
webservice = Webservice.deploy_from_image( image=azure_image, 
                                           workspace=ws, 
                                           name='diabetes-model-1', 
                                           deployment_config=aci_config)

webservice.wait_for_deployment(show_output=True)
```
#### Deploy to AKS

To deploy to AKS, first create an AKS cluster and bring over the Docker image you want to deploy. For this example, bring over the previously created image from the ACI deployment.

To get the image from the previous ACI deployment use the [Image](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.image.image?view=azure-ml-py) class. 

```python
from azureml.core.image import Image

# Get the image by name, you can change this based on the image you want to deploy
myimage = Image(workspace=ws, name='sklearn-image') 
```

Create an AKS cluster using the [ComputeTarget.create()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.computetarget?view=azure-ml-py#create-workspace--name--provisioning-configuration-) method. It may take 20-25 minutes to create a new cluster.

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (can also provide parameters to customize)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'aks-mlflow' 

# Create the cluster
aks_target = ComputeTarget.create(workspace=ws, 
                                  name=aks_name, 
                                  provisioning_configuration=prov_config)

aks_target.wait_for_completion(show_output = True)

print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```
Set up your deployment configuration with the [deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) method. You can also add tags and descriptions to help keep track of your web service.

```python
from azureml.core.webservice import Webservice, AksWebservice
from azureml.core.image import ContainerImage

# Set the web service configuration (using default here with app insights)
aks_config = AksWebservice.deploy_configuration(enable_app_insights=True)

# Unique service name
service_name ='aks-service'
```

Then, deploy the image by using the Azure Machine Learning SDK [deploy_from_image()](/python/api/azureml-core/azureml.core.webservice.webservice(class)?view=azure-ml-py#deploy-from-image-workspace--name--image--deployment-config-none--deployment-target-none--overwrite-false-) method. 

```python
# Webservice creation using single command
aks_service = Webservice.deploy_from_image( workspace=ws, 
                                            name=service_name,
                                            deployment_config = aks_config
                                            image = myimage,
                                            deployment_target = aks_target)

aks_service.wait_for_deployment(show_output=True)
```

The service deployment can take several minutes.

## Clean up resources

If you don't plan to use the logged metrics and artifacts in your workspace, the ability to delete them individually is currently unavailable. Instead, delete the resource group that contains the storage account and workspace, so you don't incur any charges:

1. In the Azure portal, select **Resource groups** on the far left.

   ![Delete in the Azure portal](./media/how-to-use-mlflow/delete-resources.png)

1. From the list, select the resource group you created.

1. Select **Delete resource group**.

1. Enter the resource group name. Then select **Delete**.

 -->

 ## <a name="example-notebooks"></a>Voorbeeldnotitieblokken

De [MLflow met Azure ML-notitieblokken](https://aka.ms/azureml-mlflow-examples) tonen concepten die in dit artikel worden gepresenteerd en uit.

## <a name="next-steps"></a>Volgende stappen
* [Beheer uw modellen.](concept-model-management-and-deployment.md)
* Controleer uw productiemodellen op [gegevensdrift.](how-to-monitor-data-drift.md)
