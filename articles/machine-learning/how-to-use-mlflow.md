---
title: MLflow bijhouden van ML-experimenten
titleSuffix: Azure Machine Learning
description: Stel MLflow met Azure Machine Learning in om metrische gegevens & artefacten te registreren en modellen te implementeren vanuit Databricks, uw lokale omgeving of VM-omgeving.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.topic: conceptual
ms.date: 09/23/2019
ms.custom: seodec18
ms.openlocfilehash: 6203d78ee8d9c0a9837d03859856c5d0265422ac
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75772326"
---
# <a name="track-metrics-and-deploy-models-with-mlflow-and-azure-machine-learning-preview"></a>Houd metrische gegevens bij en implementeer modellen met MLflow en Azure Machine Learning (preview-versie)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In dit artikel wordt beschreven hoe u de tracerings-URI en logboek registratie-API van MLflow kunt [inschakelen, met](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api)Azure machine learning. Zo kunt u:

+ Meet en log-gegevens en artefacten in uw [Azure machine learning-werk ruimte](https://docs.microsoft.com/azure/machine-learning/concept-azure-machine-learning-architecture#workspaces)bijhouden. Als u MLflow tracking al gebruikt voor uw experimenten, biedt de werk ruimte een gecentraliseerde, veilige en schaal bare locatie voor het opslaan van metrische gegevens en modellen voor trainingen.

+ Implementeer uw MLflow-experimenten als een Azure Machine Learning-webservice. Door te implementeren als een webservice, kunt u de Azure Machine Learning bewaking en de functionaliteit voor de detectie van gegevens drift Toep assen op uw productie modellen. 

[MLflow](https://www.mlflow.org) is een open-source bibliotheek voor het beheren van de levens cyclus van uw machine learning experimenten. MLFlow tracking is een onderdeel van MLflow waarmee u de metrische gegevens en model artefacten van uw training kunt vastleggen en bijhouden, ongeacht de omgeving van uw experiment, op een extern Compute-doel, op een virtuele machine, lokaal op uw computer of op een Azure Databricks cluster.

In het volgende diagram ziet u hoe u met MLflow tracking de metrische gegevens van een experiment kunt bijhouden en model artefacten kunt opslaan in uw Azure Machine Learning-werk ruimte.

![mlflow met Azure machine learning diagram](./media/how-to-use-mlflow/mlflow-diagram-track.png)

> [!TIP]
> De informatie in dit document is voornamelijk bedoeld voor gegevens wetenschappers en ontwikkel aars die het model trainings proces willen bewaken. Als u een beheerder bent die geïnteresseerd is in het bewaken van het resource gebruik en gebeurtenissen van Azure machine learning, zoals quota's, voltooide trainings uitvoeringen of voltooide model implementaties, raadpleegt u [bewaking Azure machine learning](monitor-azure-machine-learning.md).

## <a name="compare-mlflow-and-azure-machine-learning-clients"></a>MLflow-en Azure Machine Learning-clients vergelijken

 De onderstaande tabel bevat een overzicht van de verschillende clients die Azure Machine Learning kunnen gebruiken en hun respectieve functie mogelijkheden.

 MLflow tracking biedt metrische logboek registratie en opslag functies voor artefacten die alleen beschikbaar zijn via de [python-SDK van Azure machine learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).


| | MLflow tracking &-implementatie | Azure Machine Learning python-SDK |  Azure Machine Learning CLI | Azure Machine Learning Studio|
|---|---|---|---|---|
| Werk ruimte beheren |   | ✓ | ✓ | ✓ |
| Gegevens archieven gebruiken  |   | ✓ | ✓ | |
| Metrische logboek gegevens      | ✓ | ✓ |   | |
| Artefacten uploaden | ✓ | ✓ |   | |
| Metrische gegevens bekijken     | ✓ | ✓ | ✓ | ✓ |
| Compute beheren   |   | ✓ | ✓ | ✓ |
| Modellen implementeren    | ✓ | ✓ | ✓ | ✓ |
|Model prestaties bewaken||✓|  |   |
| Gegevensafwijkingen detecteren |   | ✓ |   | ✓ |

## <a name="prerequisites"></a>Vereisten

* [Installeer MLflow.](https://mlflow.org/docs/latest/quickstart.html)
* [Installeer de Azure machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) op uw lokale computer de SDK biedt de connectiviteit voor MLflow om toegang te krijgen tot uw werk ruimte.
* [Maak een Azure machine learning-werkruimte](how-to-manage-workspace.md).

## <a name="track-local-runs"></a>Lokale uitvoeringen bijhouden

MLflow tracking met Azure Machine Learning kunt u de vastgelegde metrische gegevens en artefacten van uw lokale uitvoeringen opslaan in uw Azure Machine Learning-werk ruimte.

Installeer het `azureml-contrib-run`-pakket voor het gebruik van MLflow tracking met Azure Machine Learning van uw experimenten die lokaal worden uitgevoerd in een Jupyter Notebook of code-editor.

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

MLflow tracking met Azure Machine Learning kunt u de vastgelegde metrische gegevens en artefacten opslaan vanuit uw Databricks-uitvoeringen in uw Azure Machine Learning-werk ruimte.

Als u uw Mlflow experimenten met Azure Databricks wilt uitvoeren, moet u eerst een [Azure Databricks-werk ruimte en-cluster](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) maken

Zorg er in het cluster voor dat u de *azureml-mlflow-* bibliotheek van PyPi installeert om ervoor te zorgen dat uw cluster toegang heeft tot de benodigde functies en klassen.
Importeer hier uw proef notitieblok, koppel uw cluster hieraan en voer het experiment uit. 

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

## <a name="deploy-mlflow-models-as-a-web-service"></a>MLflow-modellen implementeren als een webservice

Als u uw MLflow experimenten als een Azure Machine Learning-webservice implementeert, kunt u gebruikmaken van Azure Machine Learning de mogelijkheden voor model beheer en detectie van gegevens drift en deze Toep assen op uw productie modellen.

In het volgende diagram ziet u dat met de API voor MLflow implementeren uw bestaande MLflow-modellen kunnen worden geïmplementeerd als een Azure Machine Learning-webservice, ondanks hun Frameworks--PyTorch, tensor flow, scikit-leren, ONNX, enzovoort, en uw productie modellen beheren in uw werk ruimte.

![mlflow met Azure machine learning diagram](./media/how-to-use-mlflow/mlflow-diagram-deploy.png)

### <a name="log-your-model"></a>Uw model registreren

Voordat u kunt implementeren, moet u ervoor zorgen dat uw model wordt opgeslagen, zodat u ernaar kunt verwijzen en de bijbehorende pad-locatie voor de implementatie. In uw trainings script moet code vergelijkbaar zijn met de volgende methode [mlflow. sklearn. log_model ()](https://www.mlflow.org/docs/latest/python_api/mlflow.sklearn.html) waarmee uw model wordt opgeslagen in de opgegeven uitvoer Directory. 

```python
# change sklearn to pytorch, tensorflow, etc. based on your experiment's framework 
import mlflow.sklearn

# Save the model to the outputs directory for capture
mlflow.sklearn.log_model(regression_model, model_save_path)
```
>[!NOTE]
> Neem de para meter `conda_env` op voor het door geven van een woordenboek weergave van de afhankelijkheden en de omgeving waarin dit model moet worden uitgevoerd.

### <a name="retrieve-model-from-previous-run"></a>Model ophalen uit vorige uitvoering

Als u de uitvoering wilt ophalen, moet u de run-ID en het pad in de uitvoerings geschiedenis van waar het model is opgeslagen, hebben. 

```python
# gets the list of runs for your experiment as an array
experiment_name = 'experiment-with-mlflow'
exp = ws.experiments[experiment_name]
runs = list(exp.get_runs())

# get the run ID and the path in run history
runid = runs[0].id
model_save_path = 'model'
```

### <a name="create-docker-image"></a>Een Docker-installatiekopie maken

De functie `mlflow.azureml.build_image()` bouwt een docker-installatie kopie van het opgeslagen model op een op een framework bewuste manier. Er wordt automatisch een bedrijfsspecifieke wrapper-code voor het decoderen van het Framework gemaakt en er worden pakket afhankelijkheden voor u opgegeven. Geef het pad naar het model, de werk ruimte, de run-ID en andere para meters op.

Met de volgende code wordt een docker-installatie kopie gemaakt met behulp van de uitvoering van *:/< run. id >/model* als het model_uri-pad voor een experiment met Scikit-leren.

```python
import mlflow.azureml

azure_image, azure_model = mlflow.azureml.build_image(model_uri='runs:/{}/{}'.format(runid, model_save_path),
                                                      workspace=ws,
                                                      model_name='sklearn-model',
                                                      image_name='sklearn-image',
                                                      synchronous=True)
```
Het maken van de docker-installatie kopie kan enkele minuten duren. 

### <a name="deploy-the-docker-image"></a>De docker-installatie kopie implementeren 

Nadat de installatie kopie is gemaakt, gebruikt u de Azure Machine Learning SDK om de installatie kopie te implementeren als een webservice.

Geef eerst de implementatie configuratie op. Azure container instance (ACI) is een geschikte keuze voor een snelle ontwikkel-en test implementatie, terwijl Azure Kubernetes service (AKS) geschikt is voor schaal bare productie-implementaties.

#### <a name="deploy-to-aci"></a>Implementeren naar ACI

Stel uw implementatie configuratie in met de methode [deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) . U kunt ook Tags en beschrijvingen toevoegen om uw web-service bij te houden.

```python
from azureml.core.webservice import AciWebservice, Webservice

# Configure 
aci_config = AciWebservice.deploy_configuration(cpu_cores=1, 
                                                memory_gb=1, 
                                                tags={'method' : 'sklearn'}, 
                                                description='Diabetes model',
                                                location='eastus2')
```

Implementeer vervolgens de installatie kopie met behulp van de methode Azure Machine Learning SDK [deploy_from_image ()](/python/api/azureml-core/azureml.core.webservice.webservice(class)?view=azure-ml-py#deploy-from-image-workspace--name--image--deployment-config-none--deployment-target-none--overwrite-false-) . 

```python
webservice = Webservice.deploy_from_image( image=azure_image, 
                                           workspace=ws, 
                                           name='diabetes-model-1', 
                                           deployment_config=aci_config)

webservice.wait_for_deployment(show_output=True)
```
#### <a name="deploy-to-aks"></a>Implementeren naar AKS

Als u wilt implementeren naar AKS, maakt u eerst een AKS-cluster en gaat u naar de docker-installatie kopie die u wilt implementeren. Voor dit voor beeld gaat u naar de eerder gemaakte installatie kopie van de ACI-implementatie.

Gebruik de klasse [Image](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.image.image?view=azure-ml-py) om de installatie kopie van de vorige ACI-implementatie te verkrijgen. 

```python
from azureml.core.image import Image

# Get the image by name, you can change this based on the image you want to deploy
myimage = Image(workspace=ws, name='sklearn-image') 
```

Maak een AKS-cluster met behulp van de methode [ComputeTarget. Create ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.computetarget?view=azure-ml-py#create-workspace--name--provisioning-configuration-) . Het kan 20-25 minuten duren om een nieuw cluster te maken.

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
Stel uw implementatie configuratie in met de methode [deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) . U kunt ook Tags en beschrijvingen toevoegen om uw web-service bij te houden.

```python
from azureml.core.webservice import Webservice, AksWebservice
from azureml.core.image import ContainerImage

# Set the web service configuration (using default here with app insights)
aks_config = AksWebservice.deploy_configuration(enable_app_insights=True)

# Unique service name
service_name ='aks-service'
```

Implementeer vervolgens de installatie kopie met behulp van de methode Azure Machine Learning SDK [deploy_from_image ()](/python/api/azureml-core/azureml.core.webservice.webservice(class)?view=azure-ml-py#deploy-from-image-workspace--name--image--deployment-config-none--deployment-target-none--overwrite-false-) . 

```python
# Webservice creation using single command
aks_service = Webservice.deploy_from_image( workspace=ws, 
                                            name=service_name,
                                            deployment_config = aks_config
                                            image = myimage,
                                            deployment_target = aks_target)

aks_service.wait_for_deployment(show_output=True)
```

De implementatie van de service kan enkele minuten duren.

## <a name="clean-up-resources"></a>Resources opschonen

Als u niet van plan bent om de vastgelegde metrische gegevens en artefacten in uw werk ruimte te gebruiken, is de mogelijkheid om ze afzonderlijk te verwijderen, momenteel niet beschikbaar. Verwijder in plaats daarvan de resource groep die het opslag account en de werk ruimte bevat, zodat u geen kosten in rekening brengt:

1. Selecteer **Resourcegroepen** links in Azure Portal.

   ![Verwijderen in Azure Portal](./media/how-to-use-mlflow/delete-resources.png)

1. Selecteer de resourcegroep die u eerder hebt gemaakt uit de lijst.

1. Selecteer **Resourcegroep verwijderen**.

1. Voer de naam van de resourcegroup in. Selecteer vervolgens **Verwijderen**.


## <a name="example-notebooks"></a>Voorbeeld-laptops

De [MLflow met Azure ml-notebooks](https://aka.ms/azureml-mlflow-examples) demonstreren en uitvouwen op concepten die in dit artikel worden gepresenteerd.

## <a name="next-steps"></a>Volgende stappen
* [Uw modellen beheren](concept-model-management-and-deployment.md).
* Bewaak uw productie modellen voor [gegevens drift](how-to-monitor-data-drift.md).
