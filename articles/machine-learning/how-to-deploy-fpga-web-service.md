---
title: Wat zijn FPGA - hoe te implementeren
titleSuffix: Azure Machine Learning
description: Meer informatie over het implementeren van een webservice met een model dat wordt uitgevoerd op een FPGA met Azure Machine Learning voor ultralage latentie-gevolgtrekking.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: jordane
author: jpe316
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: 8cb6cf49e302122849dc2402bcff008e72e15608
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472355"
---
# <a name="what-are-field-programmable-gate-arrays-fpga-and-how-to-deploy"></a>Wat zijn veldprogrammeerbare gate arrays (FPGA) en hoe te implementeren
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In dit artikel vindt u een inleiding tot veldprogrammeerbare gatearrays (FPGA) en wordt u weergegeven hoe u uw modellen implementeert met Azure Machine Learning in een Azure FPGA.

FPGA's bevatten een matrix van programmeerbare logische blokken en een hiërarchie van herconfigureerbare interconnects. De interconnects maken het mogelijk om deze blokken na de productie op verschillende manieren te configureren. In vergelijking met andere chips bieden FPGA's een combinatie van programmeerbaarheid en prestaties.

## <a name="fpgas-vs-cpu-gpu-and-asic"></a>FPGA's vs. CPU, GPU en ASIC

In het volgende diagram en de volgende tabel ziet u hoe FPGA's zich verhouden tot andere processors.

![Diagram met Azure Machine Learning FPGA-vergelijking](./media/how-to-deploy-fpga-web-service/azure-machine-learning-fpga-comparison.png)

|Processor||Beschrijving|
|---|:-------:|------|
|Toepassingsspecifieke geïntegreerde schakelingen|Asics|Aangepaste circuits, zoals De TensorFlow Processor Units (TPU) van Google, bieden de hoogste efficiëntie. Ze kunnen niet opnieuw worden geconfigureerd als uw behoeften veranderen.|
|Veldprogrammeerbare poortarrays|FPGAs|FPGA's, zoals die beschikbaar zijn op Azure, bieden prestaties in de buurt van ASIC's. Ze zijn ook flexibel en herconfigureerbaar na verloop van tijd, om nieuwe logica te implementeren.|
|Grafische verwerkingseenheden|GPU's|Een populaire keuze voor AI-berekeningen. GPU's bieden parallelle verwerkingsmogelijkheden, waardoor het sneller is bij het renderen van afbeeldingen dan CPU's.|
|Centrale verwerkingseenheden|CPU's|Processors voor algemene doeleinden, waarvan de prestaties niet ideaal zijn voor grafische en videoverwerking.|

FPGA's op Azure zijn gebaseerd op Intel's FPGA-apparaten, die gegevenswetenschappers en ontwikkelaars gebruiken om realtime AI-berekeningen te versnellen. Deze FPGA-architectuur biedt prestaties, flexibiliteit en schaal en is beschikbaar op Azure.

FPGA's maken het mogelijk om lage latentie te bereiken voor real-time inference (of model scoring) verzoeken. Asynchrone aanvragen (batching) zijn niet nodig. Batching kan latentie veroorzaken, omdat er meer gegevens moeten worden verwerkt. Implementaties van neurale verwerkingseenheden vereisen geen batching; daarom kan de latentie vele malen lager zijn, in vergelijking met CPU- en GPU-processors.

### <a name="reconfigurable-power"></a>Herconfigureerbaar vermogen
U FPGA's opnieuw configureren voor verschillende typen machine learning-modellen. Deze flexibiliteit maakt het gemakkelijker om de toepassingen te versnellen op basis van de meest optimale numerieke precisie en het geheugenmodel dat wordt gebruikt. Omdat FPGA's herconfigureerbaar zijn, u op de hoogte blijven van de vereisten van snel veranderende AI-algoritmen.

## <a name="whats-supported-on-azure"></a>Wat wordt ondersteund op Azure
Microsoft Azure is 's werelds grootste cloudinvestering in FPGA's. Met behulp van deze FPGA-enabled hardware architectuur, getrainde neurale netwerken draaien snel en met een lagere latency. Azure kan vooraf getrainde deep neural networks (DNN) via FPGA's parallellopen om uw service uit te schalen. De DN's kunnen worden voorgetraind, als een diepe featurizer voor transfer learning, of verfijnd met bijgewerkte gewichten.

FPGA's op Azure ondersteunt:

+ Scenario's voor beeldclassificatie en -herkenning
+ TensorFlow-implementatie
+ Intel FPGA-hardware

Deze DNN-modellen zijn momenteel beschikbaar:
  - ResNet 50
  - ResNet 152
  - DenseNet-121
  - VGG-16
  - SSD-VGG

FPGA's zijn beschikbaar in deze Azure-regio's:
  - VS - oost
  - Azië - zuidoost
  - Europa -west
  - VS - west 2

> [!IMPORTANT]
> Om de latentie en doorvoer te optimaliseren, moet uw client die gegevens naar het FPGA-model verzendt, zich in een van de bovenstaande regio's bevindt (naar het model waarop u het model hebt geïmplementeerd).

De **PBS-familie van Azure VM's** bevat Intel Arria 10 FPGA's. Het wordt weergegeven als 'Standaard PBS Family vCPU's' wanneer u uw Azure-quotatoewijzing controleert. De PB6 VM heeft zes vCPU's en één FPGA en wordt automatisch door Azure ML ingericht als onderdeel van het implementeren van een model naar een FPGA. Het wordt alleen gebruikt met Azure ML en kan geen willekeurige bitstreams uitvoeren. U bijvoorbeeld de FPGA niet flashen met bitstreams om versleuteling, codering, enz.

### <a name="scenarios-and-applications"></a>Scenario's en toepassingen

Azure FPGA's zijn geïntegreerd met Azure Machine Learning. Microsoft gebruikt FPGA's voor DNN-evaluatie, Bing-zoekresultaten en SDN-versnelling (Software Defined Networking) om de latentie te verminderen en tegelijkertijd CPU's vrij te maken voor andere taken.

De volgende scenario's maken gebruik van FPGA's:
+ [Geautomatiseerd optisch inspectiesysteem](https://blogs.microsoft.com/ai/build-2018-project-brainwave/)

+ [Landbedekkingstoewijzing](https://blogs.technet.microsoft.com/machinelearning/2018/05/29/how-to-use-fpgas-for-deep-learning-inference-to-perform-land-cover-mapping-on-terabytes-of-aerial-images/)

## <a name="example-deploy-models-on-fpgas"></a>Voorbeeld: Modellen implementeren op FPGA's

U een model als webservice implementeren op FPGA's met Azure Machine Learning Hardware Accelerated Models. Het gebruik van FPGA's biedt ultralage latentie-gevolgtrekking, zelfs met een enkele batchgrootte. Gevolgtrekking, of modelscore, is de fase waarin het geïmplementeerde model wordt gebruikt voor voorspelling, meestal op productiegegevens.

### <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement.  Als u nog geen account hebt, maakt u een gratis account voordat u begint. Probeer vandaag nog de [gratis of betaalde versie van Azure Machine Learning.](https://aka.ms/AMLFree)

- FPGA-quotum. Gebruik de Azure CLI om te controleren of u quota hebt:

    ```azurecli-interactive
    az vm list-usage --location "eastus" -o table --query "[?localName=='Standard PBS Family vCPUs']"
    ```

    > [!TIP]
    > De andere mogelijke ``southeastasia``locaties ``westeurope``zijn ``westus2``, en .

    De opdracht retourneert tekst die vergelijkbaar is met de volgende tekst:

    ```text
    CurrentValue    Limit    LocalName
    --------------  -------  -------------------------
    0               6        Standard PBS Family vCPUs
    ```

    Zorg ervoor dat u ten minste 6 vCPU's onder __CurrentValue hebt.__

    Als u geen quotum hebt, dient [https://aka.ms/accelerateAI](https://aka.ms/accelerateAI)u een aanvraag in bij .

- Een Azure Machine Learning-werkruimte en de Azure Machine Learning SDK voor Python geïnstalleerd. Zie [Een werkruimte maken](how-to-manage-workspace.md)voor meer informatie.
 
- De Python SDK voor hardware-versnelde modellen:

    ```bash
    pip install --upgrade azureml-accel-models
    ```


## <a name="1-create-and-containerize-models"></a>1. Modellen maken en containeriseren

In dit document wordt beschreven hoe u een TensorFlow-grafiek maakt om de invoerafbeelding vooraf te verwerken, deze een featurizer te maken met ResNet 50 op een FPGA en de functies vervolgens uit te voeren via een classificatie die is getraind op de ImageNet-gegevensset.

Volg de instructies om:

* Het TensorFlow-model definiëren
* Het model converteren
* Het model implementeren
* Het geïmplementeerde model consumeren
* Geïmplementeerde services verwijderen

Gebruik de [Azure Machine Learning SDK voor Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) om een servicedefinitie te maken. Een servicedefinitie is een bestand dat een pijplijn van grafieken (invoer, featurizer en classificatie) beschrijft op basis van TensorFlow. Met de opdracht implementatie worden de definitie en grafieken automatisch gecomprimeerd in een ZIP-bestand en wordt de ZIP-opslag naar Azure Blob geüpload. De DNN is al ingezet om te draaien op de FPGA.

### <a name="load-azure-machine-learning-workspace"></a>Azure Machine Learning-werkruimte laden

Laad uw Azure Machine Learning-werkruimte.

```python
import os
import tensorflow as tf

from azureml.core import Workspace

ws = Workspace.from_config()
print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep='\n')
```

### <a name="preprocess-image"></a>Afbeelding vooraf verwerken

De invoer naar de webservice is een JPEG-afbeelding.  De eerste stap is om de JPEG-afbeelding te decoderen en vooraf te verwerken.  De JPEG-afbeeldingen worden behandeld als tekenreeksen en het resultaat zijn tensoren die de input zijn voor het ResNet 50-model.

```python
# Input images as a two-dimensional tensor containing an arbitrary number of images represented a strings
import azureml.accel.models.utils as utils
tf.reset_default_graph()

in_images = tf.placeholder(tf.string)
image_tensors = utils.preprocess_array(in_images)
print(image_tensors.shape)
```

### <a name="load-featurizer"></a>Load featurizer

Initialiseer het model en download een TensorFlow-controlepunt van de gekwantificeerde versie van ResNet50 om te worden gebruikt als featurizer.  U QuantizedResnet50 in het onderstaande codefragment vervangen door andere diepe neurale netwerken te importeren:

- QuantizedResnet152
- KwantificeerdeVgg16
- Densenet121

```python
from azureml.accel.models import QuantizedResnet50
save_path = os.path.expanduser('~/models')
model_graph = QuantizedResnet50(save_path, is_frozen=True)
feature_tensor = model_graph.import_graph_def(image_tensors)
print(model_graph.version)
print(feature_tensor.name)
print(feature_tensor.shape)
```

### <a name="add-classifier"></a>Classificatie toevoegen

Deze classificatie is getraind op de ImageNet-gegevensset.  Voorbeelden voor het leren en trainen van uw aangepaste gewichten zijn beschikbaar in de set [voorbeeldnotitieblokken.](https://aka.ms/aml-notebooks)

```python
classifier_output = model_graph.get_default_classifier(feature_tensor)
print(classifier_output)
```

### <a name="save-the-model"></a>Het model opslaan

Nu de preprocessor, ResNet 50 featurizer, en de classificatie zijn geladen, sla de grafiek en de bijbehorende variabelen als een model.

```python
model_name = "resnet50"
model_save_path = os.path.join(save_path, model_name)
print("Saving model in {}".format(model_save_path))

with tf.Session() as sess:
    model_graph.restore_weights(sess)
    tf.saved_model.simple_save(sess, model_save_path,
                               inputs={'images': in_images},
                               outputs={'output_alias': classifier_output})
```

### <a name="save-input-and-output-tensors"></a>Invoer- en uitvoertensoren opslaan
De invoer- en uitvoertensoren die zijn gemaakt tijdens de voorbewerkings- en classificatiestappen zijn nodig voor modelconversie en gevolgtrekking.

```python
input_tensors = in_images.name
output_tensors = classifier_output.name

print(input_tensors)
print(output_tensors)
```

> [!IMPORTANT]
> Sla de invoer- en uitvoertensoren op omdat u ze nodig hebt voor modelconversie- en gevolgtrekkingsaanvragen.

De beschikbare modellen en de bijbehorende standaard classificatie tensoren zijn hieronder, dat is wat je zou gebruiken voor gevolgtrekking als u de standaard classificatie gebruikt.

+ Resnet50, QuantizedResnet50
  ```python
  output_tensors = "classifier_1/resnet_v1_50/predictions/Softmax:0"
  ```
+ Resnet152, QuantizedResnet152
  ```python
  output_tensors = "classifier/resnet_v1_152/predictions/Softmax:0"
  ```
+ Densenet121, KwantificeerdDensenet121
  ```python
  output_tensors = "classifier/densenet121/predictions/Softmax:0"
  ```
+ Vgg16, KwantificeerdeVgg16
  ```python
  output_tensors = "classifier/vgg_16/fc8/squeezed:0"
  ```
+ SsdVgg, KwantificeerdeSsdVgg
  ```python
  output_tensors = ['ssd_300_vgg/block4_box/Reshape_1:0', 'ssd_300_vgg/block7_box/Reshape_1:0', 'ssd_300_vgg/block8_box/Reshape_1:0', 'ssd_300_vgg/block9_box/Reshape_1:0', 'ssd_300_vgg/block10_box/Reshape_1:0', 'ssd_300_vgg/block11_box/Reshape_1:0', 'ssd_300_vgg/block4_box/Reshape:0', 'ssd_300_vgg/block7_box/Reshape:0', 'ssd_300_vgg/block8_box/Reshape:0', 'ssd_300_vgg/block9_box/Reshape:0', 'ssd_300_vgg/block10_box/Reshape:0', 'ssd_300_vgg/block11_box/Reshape:0']
  ```

### <a name="register-model"></a>Model registreren

[Registreer](concept-model-management-and-deployment.md) het model met behulp van de SDK met het ZIP-bestand in Azure Blob-opslag. Door tags en andere metadata over het model toe te voegen, u uw getrainde modellen bijhouden.

```python
from azureml.core.model import Model

registered_model = Model.register(workspace=ws,
                                  model_path=model_save_path,
                                  model_name=model_name)

print("Successfully registered: ", registered_model.name,
      registered_model.description, registered_model.version, sep='\t')
```

Als u al een model hebt geregistreerd en het wilt laden, u het ophalen.

```python
from azureml.core.model import Model
model_name = "resnet50"
# By default, the latest version is retrieved. You can specify the version, i.e. version=1
registered_model = Model(ws, name="resnet50")
print(registered_model.name, registered_model.description,
      registered_model.version, sep='\t')
```

### <a name="convert-model"></a>Model converteren

Converteer de tensorflow-grafiek naar de Open Neural Network Exchange-indeling[(ONNX).](https://onnx.ai/)  U moet de namen van de invoer- en uitvoertensoren opgeven en deze namen worden door uw klant gebruikt wanneer u de webservice gebruikt.

```python
from azureml.accel import AccelOnnxConverter

convert_request = AccelOnnxConverter.convert_tf_model(
    ws, registered_model, input_tensors, output_tensors)

# If it fails, you can run wait_for_completion again with show_output=True.
convert_request.wait_for_completion(show_output=False)

# If the above call succeeded, get the converted model
converted_model = convert_request.result
print("\nSuccessfully converted: ", converted_model.name, converted_model.url, converted_model.version,
      converted_model.id, converted_model.created_time, '\n')
```

### <a name="create-docker-image"></a>Een Docker-installatiekopie maken

Het geconverteerde model en alle afhankelijkheden worden toegevoegd aan een Docker-afbeelding.  Deze Docker-afbeelding kan vervolgens worden geïmplementeerd en geinstantieerd.  Ondersteunde implementatiedoelen omvatten AKS in de cloud of een edge-apparaat zoals [Azure Data Box Edge.](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview)  U ook tags en beschrijvingen toevoegen voor uw geregistreerde Docker-afbeelding.

```python
from azureml.core.image import Image
from azureml.accel import AccelContainerImage

image_config = AccelContainerImage.image_configuration()
# Image name must be lowercase
image_name = "{}-image".format(model_name)

image = Image.create(name=image_name,
                     models=[converted_model],
                     image_config=image_config,
                     workspace=ws)
image.wait_for_creation(show_output=False)
```

Lijst van de afbeeldingen op tag en krijg de gedetailleerde logboeken voor eventuele debugging.

```python
for i in Image.list(workspace=ws):
    print('{}(v.{} [{}]) stored at {} with build log {}'.format(
        i.name, i.version, i.creation_state, i.image_location, i.image_build_log_uri))
```

## <a name="2-deploy-to-cloud-or-edge"></a>2. Implementeren in de cloud of rand

### <a name="deploy-to-the-cloud"></a>Implementeren in de cloud

Als u uw model wilt implementeren als een grootschalige productiewebservice, gebruikt u Azure Kubernetes Service (AKS). U een nieuwe maken met de Azure Machine Learning SDK, CLI of [Azure Machine Learning-studio.](https://ml.azure.com)

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Specify the Standard_PB6s Azure VM and location. Values for location may be "eastus", "southeastasia", "westeurope", or "westus2". If no value is specified, the default is "eastus".
prov_config = AksCompute.provisioning_configuration(vm_size = "Standard_PB6s",
                                                    agent_count = 1,
                                                    location = "eastus")

aks_name = 'my-aks-cluster'
# Create the cluster
aks_target = ComputeTarget.create(workspace=ws,
                                  name=aks_name,
                                  provisioning_configuration=prov_config)
```

De AKS-implementatie kan ongeveer 15 minuten duren.  Controleer of de implementatie is geslaagd.

```python
aks_target.wait_for_completion(show_output=True)
print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```

Implementeer de container in het AKS-cluster.
```python
from azureml.core.webservice import Webservice, AksWebservice

# For this deployment, set the web service configuration without enabling auto-scaling or authentication for testing
aks_config = AksWebservice.deploy_configuration(autoscale_enabled=False,
                                                num_replicas=1,
                                                auth_enabled=False)

aks_service_name = 'my-aks-service'

aks_service = Webservice.deploy_from_image(workspace=ws,
                                           name=aks_service_name,
                                           image=image,
                                           deployment_config=aks_config,
                                           deployment_target=aks_target)
aks_service.wait_for_deployment(show_output=True)
```

#### <a name="test-the-cloud-service"></a>De cloudservice testen
De Docker-afbeelding ondersteunt gRPC en de TensorFlow Serving -API.  Gebruik de voorbeeldclient om de Docker-afbeelding aan te roepen om voorspellingen van het model te krijgen.  Voorbeeldclientcode is beschikbaar:
- [Python](https://github.com/Azure/aml-real-time-ai/blob/master/pythonlib/amlrealtimeai/client.py)
- [C #](https://github.com/Azure/aml-real-time-ai/blob/master/sample-clients/csharp)

Als u TensorFlow Serving wilt gebruiken, u [een voorbeeldclient downloaden.](https://www.tensorflow.org/serving/setup)

```python
# Using the grpc client in Azure ML Accelerated Models SDK package
from azureml.accel import PredictionClient

address = aks_service.scoring_uri
ssl_enabled = address.startswith("https")
address = address[address.find('/')+2:].strip('/')
port = 443 if ssl_enabled else 80

# Initialize Azure ML Accelerated Models client
client = PredictionClient(address=address,
                          port=port,
                          use_ssl=ssl_enabled,
                          service_name=aks_service.name)
```

Aangezien deze classificatie is getraind [ImageNet](http://www.image-net.org/) op de ImageNet-gegevensset, u de klassen toewijzen aan door de mens leesbare labels.

```python
import requests
classes_entries = requests.get(
    "https://raw.githubusercontent.com/Lasagne/Recipes/master/examples/resnet50/imagenet_classes.txt").text.splitlines()

# Score image with input and output tensor names
results = client.score_file(path="./snowleopardgaze.jpg",
                            input_name=input_tensors,
                            outputs=output_tensors)

# map results [class_id] => [confidence]
results = enumerate(results)
# sort results by confidence
sorted_results = sorted(results, key=lambda x: x[1], reverse=True)
# print top 5 results
for top in sorted_results[:5]:
    print(classes_entries[top[0]], 'confidence:', top[1])
```

### <a name="clean-up-the-service"></a>De service opschonen
Verwijder uw webservice, afbeelding en model (moet in deze volgorde worden uitgevoerd omdat er afhankelijkheden zijn).

```python
aks_service.delete()
aks_target.delete()
image.delete()
registered_model.delete()
converted_model.delete()
```

### <a name="deploy-to-a-local-edge-server"></a>Implementeren op een server met lokale rand

Alle [Azure Data Box Edge-apparaten](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview
) bevatten een FPGA voor het uitvoeren van het model.  Er kan slechts één model tegelijk op de FPGA worden uitgevoerd.  Als u een ander model wilt uitvoeren, implementeert u gewoon een nieuwe container. Instructies en voorbeeldcode zijn te vinden in [dit Azure-voorbeeld.](https://github.com/Azure-Samples/aml-hardware-accelerated-models)

## <a name="secure-fpga-web-services"></a>Beveiligde FPGA-webservices

Zie het document [Beveiligde webservices](how-to-secure-web-service.md) om uw FPGA-webservices te beveiligen.

## <a name="next-steps"></a>Volgende stappen

Bekijk deze notitieblokken, video's en blogs:

+ Verschillende [voorbeeldnotitieblokken](https://aka.ms/aml-accel-models-notebooks)

+ [Hyperscale hardware: ML op schaal bovenop Azure + FPGA: Build 2018 (video)](https://channel9.msdn.com/events/Build/2018/BRK3202)

+ [In de op Microsoft FPGA gebaseerde configureerbare cloud (video)](https://channel9.msdn.com/Events/Build/2017/B8063)

+ [Project Brainwave voor real-time AI: project startpagina](https://www.microsoft.com/research/project/project-brainwave/)
