---
title: MLflow bijhouden van ML-experimenten
titleSuffix: Azure Machine Learning
description: Stel MLflow in met Azure Machine Learning voor het vastleggen van metrische gegevens en artefacten uit ML-modellen die zijn gemaakt in Databricks-clusters, uw lokale omgeving of VM-omgeving.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.topic: conceptual
ms.date: 01/27/2020
ms.custom: seodec18
ms.openlocfilehash: a1263ecacc2af0559c726fb12c799d0e6d2f1014
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76543338"
---
# <a name="track-models-metrics-with-mlflow-and-azure-machine-learning-preview"></a>Gegevens over modellen bijhouden met MLflow en Azure Machine Learning (preview-versie)

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In dit artikel wordt beschreven hoe u de tracking-URI en logboek registratie-API van MLflow kunt inschakelen, die gezamenlijk bekend staat als [MLflow-tracking](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api), om verbinding te maken met uw MLflow experimenten en Azure machine learning. Als u dit doet, kunt u gegevens en artefacten in uw [Azure machine learning-werk ruimte](https://docs.microsoft.com/azure/machine-learning/concept-azure-machine-learning-architecture#workspaces)bijhouden en registreren. Als u MLflow tracking al gebruikt voor uw experimenten, biedt de werk ruimte een gecentraliseerde, veilige en schaal bare locatie voor het opslaan van metrische gegevens en modellen voor trainingen.

<!--
+ Deploy your MLflow experiments as an Azure Machine Learning web service. By deploying as a web service, you can apply the Azure Machine Learning monitoring and data drift detection functionalities to your production models. 
-->

[MLflow](https://www.mlflow.org) is een open-source bibliotheek voor het beheren van de levens cyclus van uw machine learning experimenten. MLFlow tracking is een onderdeel van MLflow waarmee u de metrische gegevens en model artefacten van uw training kunt vastleggen en bijhouden, ongeacht de omgeving van uw experiment, lokaal op uw computer, op een extern Compute-doel, een virtuele machine of een Azure Databricks cluster. 

In het volgende diagram ziet u hoe u met MLflow tracking de metrische gegevens van een experiment kunt bijhouden en model artefacten kunt opslaan in uw Azure Machine Learning-werk ruimte.

![mlflow met Azure machine learning diagram](./media/how-to-use-mlflow/mlflow-diagram-track.png)

> [!TIP]
> De informatie in dit document is voornamelijk bedoeld voor gegevens wetenschappers en ontwikkel aars die het model trainings proces willen bewaken. Zie [Azure machine learning bewaken](monitor-azure-machine-learning.md)als u een beheerder bent die geïnteresseerd is in het bewaken van het resource gebruik en gebeurtenissen van Azure machine learning, zoals quota's, voltooide trainings uitvoeringen of voltooide implementaties van modellen.

## <a name="compare-mlflow-and-azure-machine-learning-clients"></a>MLflow-en Azure Machine Learning-clients vergelijken

 De onderstaande tabel bevat een overzicht van de verschillende clients die Azure Machine Learning kunnen gebruiken en hun respectieve functie mogelijkheden.

 MLflow tracking biedt metrische logboek registratie en opslag functies voor artefacten die alleen beschikbaar zijn via de [python-SDK van Azure machine learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).


| | MLflow&nbsp;bijhouden <!--& Deployment--> | Azure Machine Learning python-SDK |  Azure Machine Learning CLI | Azure Machine Learning Studio|
|---|---|---|---|---|
| Werk ruimte beheren |   | ✓ | ✓ | ✓ |
| Gegevens archieven gebruiken  |   | ✓ | ✓ | |
| Metrische logboek gegevens      | ✓ | ✓ |   | |
| Artefacten uploaden | ✓ | ✓ |   | |
| Metrische gegevens bekijken     | ✓ | ✓ | ✓ | ✓ |
| Compute beheren   |   | ✓ | ✓ | ✓ |

<!--| Deploy models    | ✓ | ✓ | ✓ | ✓ |
|Monitor model performance||✓|  |   |
| Detect data drift |   | ✓ |   | ✓ |
-->
## <a name="prerequisites"></a>Vereisten

* [Installeer MLflow.](https://mlflow.org/docs/latest/quickstart.html)
* [Installeer de Azure machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) op uw lokale computer de SDK biedt de connectiviteit voor MLflow om toegang te krijgen tot uw werk ruimte.
* [Maak een Azure machine learning-werkruimte](how-to-manage-workspace.md).

## <a name="track-local-runs"></a>Lokale uitvoeringen bijhouden

MLflow tracking met Azure Machine Learning kunt u de vastgelegde metrische gegevens en artefacten van uw lokale uitvoeringen opslaan in uw Azure Machine Learning-werk ruimte.

Installeer het `azureml-mlflow`-pakket voor het gebruik van MLflow tracking met Azure Machine Learning van uw experimenten die lokaal worden uitgevoerd in een Jupyter Notebook of code-editor.

```shell
pip install azureml-mlflow
```

>[!NOTE]
>De contrib-naam ruimte wordt regel matig gewijzigd, omdat we de service kunnen verbeteren. Als zodanig moeten alle in deze naam ruimte worden beschouwd als een preview en niet volledig worden ondersteund door micro soft.

Importeer de `mlflow`-en [`Workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py) klassen om de tracerings-URI van de MLflow te openen en uw werk ruimte te configureren.

In de volgende code wijst de `get_mlflow_tracking_uri()` methode een uniek tracking-URI-adres toe aan de werk ruimte, `ws`en `set_tracking_uri()` wijst de tracerings-URI van de MLflow naar dat adres.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

>[!NOTE]
>De tracerings-URI is Maxi maal een uur of minder geldig. Als u het script opnieuw opstart na enige inactieve tijd, gebruikt u de get_mlflow_tracking_uri-API om een nieuwe URI op te halen.

Stel de naam van het MLflow-experiment in met `set_experiment()` en start uw training met `start_run()`. Gebruik vervolgens `log_metric()` om de API voor MLflow-logboek registratie te activeren en de metrische gegevens van uw trainings uitvoering te registreren.

```Python
experiment_name = 'experiment_with_mlflow'
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    mlflow.log_metric('alpha', 0.03)
```

## <a name="track-remote-runs"></a>Externe uitvoeringen bijhouden

MLflow tracking met Azure Machine Learning kunt u de vastgelegde metrische gegevens en artefacten opslaan vanaf uw externe uitvoeringen in uw Azure Machine Learning-werk ruimte.

Met extern uitvoeren kunt u uw modellen trainen op krachtige reken mogelijkheden, zoals virtuele machines met GPU of Machine Learning Compute clusters. Zie [Compute-doelen voor model training instellen](how-to-set-up-training-targets.md) voor meer informatie over verschillende reken opties.

Configureer uw reken-en trainings uitvoerings omgeving met de klasse [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) . Neem `mlflow` en `azure-contrib-run` PIP-pakketten op in de sectie [`CondaDependencies`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) van de omgeving. Maak vervolgens [`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) met uw externe Compute als het reken doel.

```Python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core import ScriptRunConfig

exp = Experiment(workspace = 'my_workspace',
                 name='my_experiment')

mlflow_env = Environment(name='mlflow-env')

cd = CondaDependencies.create(pip_packages=['mlflow', 'azureml-contrib-run'])

mlflow_env.python.conda_dependencies = cd

src = ScriptRunConfig(source_directory='./my_script_location', script='my_training_script.py')

src.run_config.target = 'my-remote-compute-compute'
src.run_config.environment = mlflow_env
```

Importeer in uw trainings script `mlflow` voor het gebruik van de MLflow-logboek registratie-Api's en begin met het vastleggen van de metrische gegevens voor de uitvoering.

```Python
import mlflow

with mlflow.start_run():
    mlflow.log_metric('example', 1.23)
```

Met deze Compute-en training-uitvoerings configuratie gebruikt u de methode `Experiment.submit('train.py')` om een uitvoering te verzenden. Met deze methode wordt de tracerings-URI van de MLflow automatisch ingesteld en wordt de logboek registratie van MLflow naar uw werk ruimte omgeleid.

```Python
run = exp.submit(src)
```

## <a name="track-azure-databricks-runs"></a>Azure Databricks uitvoeringen bijhouden

MLflow tracking met Azure Machine Learning kunt u de vastgelegde metrische gegevens en artefacten opslaan vanuit uw Azure Databricks worden uitgevoerd in uw Azure Machine Learning-werk ruimte.

Als u uw Mlflow experimenten met Azure Databricks wilt uitvoeren, moet u eerst een [Azure Databricks-werk ruimte en-cluster](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)maken. Zorg er in het cluster voor dat u de *azureml-mlflow-* bibliotheek van PyPi installeert om ervoor te zorgen dat uw cluster toegang heeft tot de benodigde functies en klassen.

Importeer uw experiment notebook, koppel deze aan uw Azure Databricks-cluster en voer uw experiment uit. 

### <a name="install-libraries"></a>Bibliotheken installeren

Als u bibliotheken in uw cluster wilt installeren, gaat u naar het tabblad **tape wisselaars** en klikt u op **nieuw installeren**

 ![mlflow met Azure machine learning diagram](./media/how-to-use-mlflow/azure-databricks-cluster-libraries.png)

Typ in het veld **pakket** de tekst azureml-mlflow en klik vervolgens op installeren. Herhaal deze stap als dat nodig is om andere extra pakketten te installeren op uw cluster voor uw experiment.

 ![mlflow met Azure machine learning diagram](./media/how-to-use-mlflow/install-libraries.png)

### <a name="set-up-your-notebook-and-workspace"></a>Uw notitie blok en werk ruimte instellen

Nadat het cluster is ingesteld, importeert u uw experiment notebook, opent u het en koppelt u het cluster eraan.

De volgende code moet in uw proef notitieblok staan. Met deze code worden de details van uw Azure-abonnement opgehaald om uw werk ruimte te instantiëren. In deze code wordt ervan uitgegaan dat u een bestaande resource groep en Azure Machine Learning werk ruimte hebt, anders kunt u [ze maken](how-to-manage-workspace.md). 

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

#### <a name="connect-your-azure-databricks-and-azure-machine-learning-workspaces"></a>Uw Azure Databricks-en Azure Machine Learning-werk ruimten verbinden

Op de [Azure Portal](https://ms.portal.azure.com)kunt u uw Azure DATABRICKS (ADB)-werk ruimte koppelen aan een nieuwe of bestaande Azure machine learning-werk ruimte. Hiertoe gaat u naar uw ADB-werk ruimte en selecteert u de knop **koppeling Azure machine learning werk ruimte** aan de rechter kant. Als u uw werk ruimten koppelt, kunt u de gegevens van uw experiment volgen in de werk ruimte Azure Machine Learning. 

### <a name="link-mlflow-tracking-to-your-workspace"></a>MLflow tracking koppelen aan uw werk ruimte

Nadat u uw werk ruimte hebt gemaakt, stelt u de tracerings-URI voor MLflow in. Door dit te doen, koppelt u de MLflow-tracking aan Azure Machine Learning-werk ruimte. Na het koppelen worden alle experimenten gelandd in de beheerde Azure Machine Learning tracking-service.

#### <a name="directly-set-mlflow-tracking-in-your-notebook"></a>MLflow bijhouden rechtstreeks instellen in uw notitie blok

```python
uri = ws.get_mlflow_tracking_uri()
mlflow.set_tracking_uri(uri)
```

Importeer in uw trainings script mlflow voor het gebruik van de MLflow-logboek registratie-Api's en start de registratie van uw uitvoerings metrieken. In het volgende voor beeld wordt de epoche-verlies metriek vastgelegd. 

```python
import mlflow 
mlflow.log_metric('epoch_loss', loss.item()) 
```

#### <a name="automate-setting-mlflow-tracking"></a>Automatisch instellen van MLflow bijhouden

In plaats van de tracerings-URI hand matig in te stellen tijdens elke volgende experiment notebook-sessie in uw clusters, doet u dit automatisch met behulp van deze [Azure machine learning tracking cluster init script](https://github.com/Azure/MachineLearningNotebooks/blob/3ce779063b000e0670bdd1acc6bc3a4ee707ec13/how-to-use-azureml/azure-databricks/linking/README.md).

Wanneer u correct is geconfigureerd, kunt u uw MLflow-tracking gegevens weer geven in de Azure Machine Learning REST API en alle clients, en in Azure Databricks via de MLflow-gebruikers interface of met behulp van de MLflow-client.

## <a name="view-metrics-and-artifacts-in-your-workspace"></a>Metrische gegevens en artefacten in uw werk ruimte weer geven

De metrische gegevens en artefacten van MLflow-logboek registratie worden bewaard in uw werk ruimte. Als u ze op een wille keurig tijdstip wilt weer geven, gaat u naar uw werk ruimte en zoekt u het experiment op naam in uw werk ruimte in [Azure machine learning Studio](https://ml.azure.com).  Of voer de onderstaande code uit. 

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

 ## <a name="example-notebooks"></a>Voorbeeld-laptops

De [MLflow met Azure ml-notebooks](https://aka.ms/azureml-mlflow-examples) demonstreren en uitvouwen op concepten die in dit artikel worden gepresenteerd.

## <a name="next-steps"></a>Volgende stappen
* [Uw modellen beheren](concept-model-management-and-deployment.md).
* Bewaak uw productie modellen voor [gegevens drift](how-to-monitor-data-drift.md).
