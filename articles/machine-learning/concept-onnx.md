---
title: 'ONNX: high-perf, cross-platform gevolgtrekking'
titleSuffix: Azure Machine Learning
description: Ontdek hoe het gebruik van de Open Neural Network Exchange (ONNX) kan helpen bij het optimaliseren van de conclusie van uw machine learning-model.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: prasantp
author: prasanthpul
ms.date: 08/15/2019
ms.custom: seodec18
ms.openlocfilehash: 98aebb4733c2aa2a6d0b0217f1f437bcea1992e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270171"
---
# <a name="onnx-and-azure-machine-learning-create-and-accelerate-ml-models"></a>ONNX en Azure Machine Learning: ML-modellen maken en versnellen

Ontdek hoe het gebruik van de [Open Neural Network Exchange](https://onnx.ai) (ONNX) kan helpen bij het optimaliseren van de conclusie van uw machine learning-model. Gevolgtrekking, of modelscore, is de fase waarin het geïmplementeerde model wordt gebruikt voor voorspelling, meestal op productiegegevens. 

Het optimaliseren van machine learning-modellen voor gevolgtrekking (of modelscore) is moeilijk omdat u het model en de gevolgtrekkingsbibliotheek moet afstemmen om optimaal gebruik te maken van de hardwaremogelijkheden. Het probleem wordt extreem moeilijk als je optimale prestaties wilt krijgen op verschillende soorten platforms (cloud / edge, CPU / GPU, enz.), omdat elk een andere mogelijkheden en kenmerken heeft. De complexiteit neemt toe als je modellen hebt van verschillende frameworks die op verschillende platforms moeten worden uitgevoerd. Het is erg tijdrovend om alle verschillende combinaties van frameworks en hardware te optimaliseren. Een oplossing om eenmaal te trainen in uw voorkeursframework en overal in de cloud of rand te draaien is nodig. Dit is waar ONNX om de hoek komt kijken.

Microsoft en een community van partners hebben ONNX gemaakt als een open standaard voor het weergeven van machine learning-modellen. Modellen uit [vele frameworks,](https://onnx.ai/supported-tools) waaronder TensorFlow, PyTorch, SciKit-Learn, Keras, Chainer, MXNet en MATLAB, kunnen worden geëxporteerd of geconverteerd naar het standaard ONNX-formaat. Zodra de modellen in het ONNX-formaat zijn, kunnen ze op verschillende platforms en apparaten worden uitgevoerd.

[ONNX Runtime](https://github.com/Microsoft/onnxruntime) is een krachtige inferentie-engine voor het implementeren van ONNX-modellen in productie. Het is geoptimaliseerd voor zowel cloud als edge en werkt op Linux, Windows en Mac. Geschreven in C++, het heeft ook C, Python en C# API's. ONNX Runtime biedt ondersteuning voor alle ONNX-ML-specificatie en integreert ook met versnellers op verschillende hardware, zoals TensorRT op NVidia GPU's.

De ONNX Runtime wordt gebruikt in grootschalige Microsoft-services zoals Bing, Office en Cognitive Services. Prestatieverbeteringen zijn afhankelijk van een aantal factoren, maar deze Microsoft-services hebben gezien een __gemiddelde 2x prestatiewinst op CPU__. ONNX Runtime wordt ook gebruikt als onderdeel van Windows ML op honderden miljoenen apparaten. U de runtime gebruiken met Azure Machine Learning. Door ONNX Runtime te gebruiken, u profiteren van de uitgebreide optimalisaties, tests en voortdurende verbeteringen van productiekwaliteit.

[![ONNX-stroomdiagram met training, converters en implementatie](./media/concept-onnx/onnx.png)](././media/concept-onnx/onnx.png#lightbox)

## <a name="get-onnx-models"></a>OnNX-modellen

U ONNX-modellen op verschillende manieren verkrijgen:
+ Een nieuw ONNX-model trainen in Azure Machine Learning (zie voorbeelden onderaan dit artikel)
+ Bestaand model converteren van een andere indeling naar ONNX (zie de [zelfstudies)](https://github.com/onnx/tutorials) 
+ Krijg een vooraf getraind ONNX-model van de [ONNX Model Zoo](https://github.com/onnx/models) (zie voorbeelden onderaan dit artikel)
+ Een aangepast ONNX-model genereren vanuit [azure custom vision-service](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/) 

Veel modellen, waaronder beeldclassificatie, objectdetectie en tekstverwerking, kunnen worden weergegeven als ONNX-modellen. Sommige modellen kunnen echter niet met succes worden geconverteerd. Als u in deze situatie terechtkomt, dient u een probleem op in de GitHub van de desbetreffende converter die u hebt gebruikt. U uw bestaande indelingsmodel blijven gebruiken totdat het probleem is verholpen.

## <a name="deploy-onnx-models-in-azure"></a>ONNX-modellen implementeren in Azure

Met Azure Machine Learning u uw ONNX-modellen implementeren, beheren en bewaken. Met behulp van de standaard [implementatieworkflow](concept-model-management-and-deployment.md) en ONNX Runtime u een REST-eindpunt maken dat in de cloud wordt gehost. Zie voorbeeld Jupyter notebooks aan het einde van dit artikel om het uit te proberen voor jezelf. 

### <a name="install-and-use-onnx-runtime-with-python"></a>OnNX Runtime installeren en gebruiken met Python

Python-pakketten voor ONNX Runtime zijn beschikbaar op [PyPi.org](https://pypi.org) [(CPU](https://pypi.org/project/onnxruntime), [GPU).](https://pypi.org/project/onnxruntime-gpu) Lees [de systeemvereisten](https://github.com/Microsoft/onnxruntime#system-requirements) voor de installatie. 

 Als u ONNX Runtime voor Python wilt installeren, gebruikt u een van de volgende opdrachten: 
```python   
pip install onnxruntime       # CPU build
pip install onnxruntime-gpu   # GPU build
```

Als u ONNX Runtime wilt aanroepen in uw Python-script, gebruikt u het als:    
```python
import onnxruntime
session = onnxruntime.InferenceSession("path to model")
```

De documentatie bij het model vertelt u meestal de ingangen en uitgangen voor het gebruik van het model. U ook een visualisatietool zoals [Netron](https://github.com/lutzroeder/Netron) gebruiken om het model te bekijken. Met ONNX Runtime u ook de modelmetagegevens, -ingangen en uitvoer opvragen:    
```python
session.get_modelmeta()
first_input_name = session.get_inputs()[0].name
first_output_name = session.get_outputs()[0].name
```

Als u uw model `run` wilt ferenceren, gebruikt en geeft u deze door in de lijst met uitvoer die u wilt geretourneerd (leeg laten als u ze allemaal wilt) en een kaart met de invoerwaarden. Het resultaat is een lijst van de uitgangen.  
```python
results = session.run(["output1", "output2"], {
                      "input1": indata1, "input2": indata2})
results = session.run([], {"input1": indata1, "input2": indata2})
```

Zie de [onnx runtime-referentiedocumenten voor](https://aka.ms/onnxruntime-python)de volledige Referentie van de Python API .    

## <a name="examples"></a>Voorbeelden

Bekijk [how-to-use-azureml/deployment/onnx](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx) voor bijvoorbeeld notitieblokken die ONNX-modellen maken en implementeren.

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="more-info"></a>Meer informatie

Meer informatie over ONNX of bijdragen aan het project:
+ [Website van het ONNX-project](https://onnx.ai)
+ [ONNX-code op GitHub](https://github.com/onnx/onnx)

Meer informatie over ONNX Runtime of bijdragen aan het project:
+ [ONNX Runtime GitHub Repo](https://github.com/Microsoft/onnxruntime)


