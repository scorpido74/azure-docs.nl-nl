---
title: Wat zijn FPGA-implementeren?
titleSuffix: Azure Machine Learning
description: Meer informatie over het implementeren van een webservice met een model dat wordt uitgevoerd op een FPGA met Azure Machine Learning voor een extreem lage latentie.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: jordane
author: jpe316
ms.date: 06/03/2020
ms.topic: conceptual
ms.custom: how-to, contperfq4, devx-track-python
ms.openlocfilehash: a5f56beb179f7c72fe66c7423999201f3460b143
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/10/2020
ms.locfileid: "89646299"
---
# <a name="what-are-field-programmable-gate-arrays-fpga-and-how-to-deploy"></a>Wat zijn veld-Programmeer bare poort matrices (FPGA) en implementeren

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Dit artikel bevat een inleiding tot veld-Programmeer bare poort matrices (FPGA) en laat zien hoe u uw modellen implementeert met behulp van [Azure machine learning](overview-what-is-azure-ml.md) naar een Azure-FPGA.

## <a name="what-are-fpgas"></a>Wat zijn Fpga's

FPGA's bevatten een matrix van programmeerbare logische blokken en een hiërarchie van herconfigureerbare interconnects. Met de onderlinge verbindingen kunnen deze blokken na de productie op verschillende manieren worden geconfigureerd. Vergeleken met andere chips, biedt Fpga's een combi natie van programmeer baarheid en prestaties. 

![Diagram van Azure Machine Learning FPGA-vergelijking](./media/how-to-deploy-fpga-web-service/azure-machine-learning-fpga-comparison.png)

|Processor| Afkorting |Beschrijving|
|---|:-------:|------|
|Toepassingsspecifieke geïntegreerde circuits|ASICs|Aangepaste circuits, zoals Google tensor flow processor units (TPU), bieden de hoogste efficiëntie. Ze kunnen niet opnieuw worden geconfigureerd als uw behoeften veranderen.|
|Veld-Programmeer bare poort matrices|FPGAs|Fpga's, zoals die beschikbaar zijn op Azure, bieden de prestaties bijna ASICs. Ze zijn ook flexibel en kunnen na verloop van tijd opnieuw worden geconfigureerd om nieuwe logica te implementeren.|
|Grafische verwerkings eenheden|GPU's|Een populaire keuze voor AI-berekeningen. Gpu's bieden parallelle verwerkings mogelijkheden, waardoor het sneller wordt weer gegeven in de beeld weergave dan Cpu's.|
|Centrale verwerkings eenheden|CPU's|Processors voor algemeen gebruik, de prestaties die niet ideaal zijn voor grafische en video verwerking.|


Fpga's maken het mogelijk om lage latentie te bieden voor aanvragen voor realtime-interferentie (of model scores). Asynchrone aanvragen (batch verwerking) zijn niet nodig. Batch verwerking kan latentie veroorzaken, omdat er meer gegevens moeten worden verwerkt. Implementaties van Neural-verwerkings eenheden vereisen geen batch verwerking; de latentie kan daarom veel keer lager zijn, vergeleken met CPU-en GPU-processors.

U kunt Fpga's opnieuw configureren voor verschillende soorten machine learning modellen. Deze flexibiliteit maakt het eenvoudiger om de toepassingen te versnellen op basis van de meest optimale numerieke precisie en het gebruikte geheugen model dat wordt gebruikt. Omdat Fpga's opnieuw worden geconfigureerd, kunt u op de hoogte blijven van de vereisten van het snel wijzigen van AI-algoritmen.

### <a name="fpga-support-in-azure"></a>Ondersteuning voor FPGA in azure

Microsoft Azure is de grootste Cloud investering wereld wijd in Fpga's. Micro soft maakt gebruik van Fpga's voor DNN-evaluatie, zoek volgorde van Bing en SDN (software defined Networking) om de latentie te reduceren, terwijl Cpu's worden vrijgemaakt voor andere taken.

Fpga's op Azure zijn gebaseerd op de FPGA-apparaten van Intel, die gegevens wetenschappers en ontwikkel aars gebruiken om real-time AI-berekeningen te versnellen. Deze architectuur met FPGA biedt prestaties, flexibiliteit en schaal baarheid en is beschikbaar in Azure.

Azure Fpga's zijn geïntegreerd met Azure Machine Learning. Azure kan vooraf getrainde diepe Neural-netwerken (DNN) parallelliserenen over Fpga's om uw service uit te breiden. De DNNs kan vooraf worden getraind als een diep gaande featurizer voor het leren van de overdracht of nauw keurig zijn afgestemd op bijgewerkte gewichten.

Fpga's op Azure ondersteunt:

+ Scenario's voor classificatie en herkenning van installatie kopieën
+ Tensor flow-implementatie (vereist tensor flow 1. x)
+ Intel FPGA-hardware

Deze DNN modellen zijn momenteel beschikbaar:

  - ResNet 50
  - ResNet 152
  - DenseNet-121
  - VGG-16
  - SSD-VGG

  
Fpga's zijn beschikbaar in deze Azure-regio's:
  - VS - oost
  - Azië - zuidoost
  - Europa -west
  - VS - west 2

Om de latentie en door voer te optimaliseren, moet uw client gegevens verzenden naar het FPGA-model in een van de bovenstaande regio's (het model dat u hebt geïmplementeerd.)

De **PBS-serie van Azure-vm's** bevat Intel Arria 10 fpga's. Het wordt weer gegeven als ' standaard-PBS-serie Vcpu's ' wanneer u uw Azure-quotum toewijzing controleert. De PB6-VM heeft zes Vcpu's en één FPGA en wordt automatisch ingericht door Azure ML als onderdeel van het implementeren van een model op een FPGA. Het wordt alleen gebruikt in combi natie met Azure ML en er kan geen wille keurige bitstreams worden uitgevoerd. U kunt de FPGA bijvoorbeeld niet flashen met bitstreams om versleuteling, code ring, enzovoort uit te voeren.


## <a name="deploy-models-on-fpgas"></a>Modellen implementeren op Fpga's

U kunt een model als een webservice implementeren op Fpga's met [Azure Machine Learning modellen met hardwareversnelling](https://docs.microsoft.com/python/api/azureml-accel-models/azureml.accel?view=azure-ml-py&preserve-view=true). Het gebruik van Fpga's biedt een dering van ultra lage latentie, zelfs met één batch grootte. Defactorion of model Score is de fase waarin het geïmplementeerde model wordt gebruikt voor de voor spelling, meestal op productie gegevens.

Het implementeren van een model voor een FPGA bestaat uit de volgende stappen:

1. Het tensor flow-model definiëren
1. Het model converteren naar ONNX
1. Het model implementeren naar de Cloud of een edge-apparaat
1. Het geïmplementeerde model gebruiken

In dit voor beeld maakt u een tensor flow-grafiek om de invoer installatie kopie voor te verwerken, maakt u een featurizer met ResNet 50 op een FPGA en voert u de functies uit via een classificatie die is getraind voor de ImageNet data set. Vervolgens wordt het model geïmplementeerd naar een AKS-cluster.

### <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement. Als u er geen hebt, moet u een [betalen per gebruik-](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go) account maken (gratis Azure-accounts komen niet in aanmerking voor FPGA-quotum).
- [Azure-CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- FPGA quota. Gebruik de Azure CLI om te controleren of u een quotum hebt:

    ```azurecli-interactive
    az vm list-usage --location "eastus" -o table --query "[?localName=='Standard PBS Family vCPUs']"
    ```


    De opdracht retourneert tekst die er ongeveer als volgt uitziet:

    ```text
    CurrentValue    Limit    LocalName
    --------------  -------  -------------------------
    0               6        Standard PBS Family vCPUs
    ```

    Zorg ervoor dat er ten minste 6 Vcpu's zijn onder __CurrentValue__.

    Als u geen quota hebt, verzendt u een aanvraag op [https://aka.ms/accelerateAI](https://aka.ms/accelerateAI) .

- Er is een Azure Machine Learning-werk ruimte en de Azure Machine Learning SDK voor python geïnstalleerd. Zie [een werk ruimte maken](how-to-manage-workspace.md)voor meer informatie.
 
- De python-SDK voor modellen die door hardware zijn versneld:

    ```bash
    pip install --upgrade azureml-accel-models[cpu]
    ```
### <a name="1-define-the-tensorflow-model"></a>1. het tensor flow-model definiëren

Gebruik de [Azure machine learning SDK voor python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true) om een service definitie te maken. Een service definitie is een bestand met een beschrijving van een pijp lijn van grafieken (invoer, featurizer en classificatie) op basis van tensor flow. De implementatie opdracht comprimeert de definitie en grafieken automatisch in een ZIP-bestand en uploadt de ZIP naar Azure Blob-opslag. De DNN is al geïmplementeerd om te worden uitgevoerd op de FPGA.

1. Azure Machine Learning werk ruimte laden

   ```python
   import os
   import tensorflow as tf
   
   from azureml.core import Workspace
  
   ws = Workspace.from_config()
   print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep='\n')
   ```

1. Afbeelding voorverwerken. De invoer voor de webservice is een JPEG-afbeelding.  De eerste stap is het decoderen van de JPEG-afbeelding en de voor verwerking ervan.  De JPEG-afbeeldingen worden behandeld als teken reeksen en het resultaat zijn tien tallen die de invoer zijn van het ResNet 50-model.

   ```python
   # Input images as a two-dimensional tensor containing an arbitrary number of images represented a strings
   import azureml.accel.models.utils as utils
   tf.reset_default_graph()
   
   in_images = tf.placeholder(tf.string)
   image_tensors = utils.preprocess_array(in_images)
   print(image_tensors.shape)
   ```

1. Featurizer laden. Initialiseer het model en down load een tensor flow-controle punt van de gevormde versie van ResNet50 die moet worden gebruikt als een featurizer.  U kunt ' QuantizedResnet50 ' in het onderstaande code fragment vervangen door andere diepe Neural-netwerken te importeren:

   - QuantizedResnet152
   - QuantizedVgg16
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

1. Voeg een classificatie toe. Deze classificatie is getraind op de ImageNet-gegevensset.  Voor beelden voor de overdracht van learning en training uw aangepaste gewichten zijn beschikbaar in de set voor [beeld-notebooks](https://github.com/Azure/MachineLearningNotebooks).

   ```python
   classifier_output = model_graph.get_default_classifier(feature_tensor)
   print(classifier_output)
   ```

1. Sla het model op. Nu de preprocessor, ResNet 50 featurizer en de classificatie zijn geladen, slaat u de grafiek en de bijbehorende variabelen op als een model.

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

1. Invoer-en uitvoer intensiteiten opslaan. De bewerkings-en uitvoer intensiteiten die zijn gemaakt tijdens de stappen voor voor verwerking en classificatie, zijn vereist voor model conversie en deinterferentie.

   ```python
   input_tensors = in_images.name
   output_tensors = classifier_output.name

   print(input_tensors)
   print(output_tensors)
   ```

   > [!IMPORTANT]
   > Sla de invoer-en uitvoer intensiteiten op, omdat u ze nodig hebt voor model conversie en aanvragen voor inschakeling.

   De beschik bare modellen en de bijbehorende standaard waarden voor de classifier-uitvoer zijn lager, wat u zou gebruiken voor het afmaken van de standaard classificatie.

   + Resnet50, QuantizedResnet50
     ```python
     output_tensors = "classifier_1/resnet_v1_50/predictions/Softmax:0"
     ```
   + Resnet152, QuantizedResnet152
     ```python
     output_tensors = "classifier/resnet_v1_152/predictions/Softmax:0"
     ```
   + Densenet121, QuantizedDensenet121
     ```python
     output_tensors = "classifier/densenet121/predictions/Softmax:0"
     ```
   + Vgg16, QuantizedVgg16
     ```python
     output_tensors = "classifier/vgg_16/fc8/squeezed:0"
     ```
   + SsdVgg, QuantizedSsdVgg
     ```python
     output_tensors = ['ssd_300_vgg/block4_box/Reshape_1:0', 'ssd_300_vgg/block7_box/Reshape_1:0', 'ssd_300_vgg/block8_box/Reshape_1:0', 'ssd_300_vgg/block9_box/Reshape_1:0', 'ssd_300_vgg/block10_box/Reshape_1:0', 'ssd_300_vgg/block11_box/Reshape_1:0', 'ssd_300_vgg/block4_box/Reshape:0', 'ssd_300_vgg/block7_box/Reshape:0', 'ssd_300_vgg/block8_box/Reshape:0', 'ssd_300_vgg/block9_box/Reshape:0', 'ssd_300_vgg/block10_box/Reshape:0', 'ssd_300_vgg/block11_box/Reshape:0']
     ```

### <a name="2-convert-the-model"></a>2. het model converteren

Voordat u het model op Fpga's implementeert, moet u het converteren naar de ONNX-indeling.

1. [Registreer](concept-model-management-and-deployment.md) het model met behulp van de SDK met het zip-bestand in Azure Blob Storage. Door labels en andere meta gegevens over het model toe te voegen, kunt u uw getrainde modellen volgen.

   ```python
   from azureml.core.model import Model

   registered_model = Model.register(workspace=ws,
                                     model_path=model_save_path,
                                     model_name=model_name)

   print("Successfully registered: ", registered_model.name,
         registered_model.description, registered_model.version, sep='\t')
   ```

   Als u al een model hebt geregistreerd en wilt laden, kunt u het ophalen.

   ```python
   from azureml.core.model import Model
   model_name = "resnet50"
   # By default, the latest version is retrieved. You can specify the version, i.e. version=1
   registered_model = Model(ws, name="resnet50")
   print(registered_model.name, registered_model.description,
         registered_model.version, sep='\t')
   ```

1. Converteer de tensor flow-grafiek naar de open Neural Network Exchange-indeling ([ONNX](https://onnx.ai/)).  U moet de namen van de invoer-en uitvoer-tien tallen opgeven en deze namen worden door de client gebruikt wanneer u de webservice gebruikt.

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

### <a name="3-containerize-and-deploy-the-model"></a>3. container plaatsen en implementeer het model

Maak een docker-installatie kopie van het geconverteerde model en alle afhankelijkheden.  Deze docker-installatie kopie kan vervolgens worden geïmplementeerd en geïnstantieerd.  Ondersteunde implementatie doelen zijn AKS in de Cloud of een edge-apparaat, zoals [Azure data Box Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview).  U kunt ook Tags en beschrijvingen toevoegen voor uw geregistreerde docker-installatie kopie.

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

   Vermeld de installatie kopieën op label en ontvang de gedetailleerde logboeken voor elke fout opsporing.

   ```python
   for i in Image.list(workspace=ws):
       print('{}(v.{} [{}]) stored at {} with build log {}'.format(
           i.name, i.version, i.creation_state, i.image_location, i.image_build_log_uri))
   ```

#### <a name="deploy-to-aks-cluster"></a>Implementeren naar AKS-cluster

1. Gebruik Azure Kubernetes service (AKS) om uw model te implementeren als een grootschalige productie-webservice. U kunt een nieuw abonnement maken met behulp van de Azure Machine Learning SDK, CLI of [Azure machine learning Studio](https://ml.azure.com).

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

    De implementatie van de AKS kan ongeveer 15 minuten duren.  Controleer of de implementatie is geslaagd.

    ```python
    aks_target.wait_for_completion(show_output=True)
    print(aks_target.provisioning_state)
    print(aks_target.provisioning_errors)
    ```

1. Implementeer de container in het AKS-cluster.

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

#### <a name="deploy-to-a-local-edge-server"></a>Implementeren op een lokale Edge-Server

Alle [Azure data Box edge-apparaten](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview
) bevatten een FPGA voor het uitvoeren van het model.  Er kan slechts één model tegelijk op de FPGA worden uitgevoerd.  Als u een ander model wilt uitvoeren, implementeert u gewoon een nieuwe container. Instructies en voorbeeld code vindt u in [Dit Azure](https://github.com/Azure-Samples/aml-hardware-accelerated-models)-voor beeld.

### <a name="4-consume-the-deployed-model"></a>4. het geïmplementeerde model gebruiken

De docker-installatie kopie ondersteunt gRPC en de tensor flow voor ' voors pellen '.  Gebruik de voor beeld-client om een docker-installatie kopie aan te roepen om voor spellingen van het model te verkrijgen.  Voor beeld-client code is beschikbaar:
- [Python](https://github.com/Azure/aml-real-time-ai/blob/master/pythonlib/amlrealtimeai/client.py)
- [C#](https://github.com/Azure/aml-real-time-ai/blob/master/sample-clients/csharp)

Als u tensor flow wilt gebruiken, kunt u [een voorbeeld-client downloaden](https://www.tensorflow.org/serving/setup).

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

Omdat deze classificatie is getraind op de [ImageNet](http://www.image-net.org/) -gegevensset, wijst u de klassen toe aan mensen met lees bare labels.

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

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder uw webservice, installatie kopie en model (dit moet in deze volg orde worden uitgevoerd, omdat er afhankelijkheden zijn).

```python
aks_service.delete()
aks_target.delete()
image.delete()
registered_model.delete()
converted_model.delete()
```

## <a name="next-steps"></a>Volgende stappen

Bekijk deze notitie blokken, Video's en blogs:

+ Verschillende voor [beelden van notitie blokken](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/accelerated-models)
+ Als u uw FPGA-webservices wilt beveiligen, raadpleegt u het document [beveiligde webservices](how-to-secure-web-service.md) .
+ [Grootschalige-hardware: ML op schaal boven op Azure + FPGA: Build 2018 (video)](https://channel9.msdn.com/events/Build/2018/BRK3202)
+ [In de op micro soft FPGA Configureer bare Cloud (video)](https://channel9.msdn.com/Events/Build/2017/B8063)
+ [Project brainwave voor real-time AI: start pagina van project](https://www.microsoft.com/research/project/project-brainwave/)
+ [Systeem voor geautomatiseerde optische inspectie](https://blogs.microsoft.com/ai/build-2018-project-brainwave/)
+ [Landings cover toewijzing](https://blogs.technet.microsoft.com/machinelearning/2018/05/29/how-to-use-fpgas-for-deep-learning-inference-to-perform-land-cover-mapping-on-terabytes-of-aerial-images/)
