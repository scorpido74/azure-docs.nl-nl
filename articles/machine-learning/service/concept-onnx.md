---
title: Hoge prestaties, cross-interferentie van platform met ONNX
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de open Neural Network Exchange (ONNX) kan u helpen de deinterferentie van uw machine learning model te optimaliseren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: prasantp
author: prasanthpul
ms.date: 08/15/2019
ms.custom: seodec18
ms.openlocfilehash: dc4a5984f42e87aa42c6873bb1ee63d66744e633
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053586"
---
# <a name="onnx-and-azure-machine-learning-create-and-accelerate-ml-models"></a>ONNX en Azure Machine Learning: ML-modellen maken en versnellen

Meer informatie over het gebruik van de [Open Neural Network Exchange](https://onnx.ai) (ONNX) kan u helpen de deinterferentie van uw machine learning model te optimaliseren. Defactorion of model Score is de fase waarin het geïmplementeerde model wordt gebruikt voor de voor spelling, meestal op productie gegevens. 

Het optimaliseren van machine learning modellen voor deverzekering (of model scores) is lastig omdat u het model en de tape wisselaar voor het afnemen van problemen moet afstemmen om optimaal van de hardware-mogelijkheden te zorgen. Het probleem wordt zeer hard als u optimale prestaties wilt krijgen op verschillende platformen (Cloud/Edge, CPU/GPU, enzovoort), omdat elk van beide verschillende mogelijkheden en kenmerken heeft. De complexiteit neemt toe als u modellen hebt van verschillende frameworks die op verschillende platforms moeten worden uitgevoerd. Het is zeer tijdrovend om alle verschillende combi Naties van frameworks en hardware te optimaliseren. Een oplossing die eenmaal kan worden getraind in uw voorkeurs raamwerk en overal in de Cloud of Edge moet worden uitgevoerd. Hier wordt ONNX geleverd.

Micro soft en een community van partners hebben ONNX als Open Standard gemaakt voor het vertegenwoordigen van machine learning modellen. Modellen van [talloze Frameworks](https://onnx.ai/supported-tools) , waaronder tensor flow, PyTorch, SciKit-learn, Keras, Chainer, MXNET en MATLAB, kunnen worden geëxporteerd of geconverteerd naar de standaard-ONNX-indeling. Zodra de modellen de ONNX-indeling hebben, kunnen ze op verschillende platforms en apparaten worden uitgevoerd.

[ONNX runtime](https://github.com/Microsoft/onnxruntime) is een krachtige engine voor het afnemen van prestaties voor het implementeren van ONNX-modellen voor productie. Het is geoptimaliseerd voor zowel Cloud als Edge en werkt op Linux, Windows en Mac. In C++wordt geschreven, heeft het ook C, python en C# api's. ONNX runtime biedt ondersteuning voor alle ONNX-ML-specificatie en kan ook worden geïntegreerd met accelerators op verschillende hardware, zoals TensorRT op NVidia-Gpu's.

De ONNX-runtime wordt gebruikt bij het hoog schalen van micro soft-Services, zoals Bing, Office en Cognitive Services. Prestatie verbeteringen zijn afhankelijk van een aantal factoren, maar deze micro soft-Services hebben een __gemiddelde 2x-prestatie verbetering gezien op de CPU__. ONNX runtime wordt ook gebruikt als onderdeel van Windows ML op honderden miljoenen apparaten. U kunt de runtime gebruiken met Azure Machine Learning. Met behulp van ONNX runtime kunt u profiteren van de uitgebreide optimalisaties, testen en voortdurende verbeteringen van productie kwaliteit.

[![ONNX-stroom diagram met trainingen, conversie Programma's en implementatie](media/concept-onnx/onnx.png)](./media/concept-onnx/onnx.png#lightbox)

## <a name="get-onnx-models"></a>ONNX-modellen ophalen

U kunt ONNX modellen op verschillende manieren verkrijgen:
+ Train een nieuw ONNX-model in Azure Machine Learning (Zie de voor beelden onder aan dit artikel)
+ Bestaand model converteren van een andere indeling naar ONNX (Raadpleeg de [zelf studies](https://github.com/onnx/tutorials)) 
+ Een vooraf getraind ONNX-model ophalen uit het [ONNX-model Zoo](https://github.com/onnx/models) (Zie voor beelden onder aan dit artikel)
+ Een aangepast ONNX-model genereren vanuit de [Azure Custom Vision-service](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/) 

Veel modellen, waaronder afbeeldings classificatie, object detectie en tekst verwerking, kunnen worden weer gegeven als ONNX modellen. Sommige modellen kunnen echter niet worden geconverteerd. Als u deze situatie uitvoert, dient u een probleem op te lossen in het GitHub van het respectieve conversie programma dat u hebt gebruikt. U kunt door gaan met het gebruik van uw bestaande indelings model totdat het probleem is opgelost.

## <a name="deploy-onnx-models-in-azure"></a>ONNX-modellen implementeren in azure

Met Azure Machine Learning kunt u uw ONNX-modellen implementeren, beheren en bewaken. Met de Standard- [implementatie werk stroom](concept-model-management-and-deployment.md) en ONNX runtime kunt u een rest-eind punt maken dat wordt gehost in de Cloud. Zie voor beelden van Jupyter-notebooks aan het einde van dit artikel om het zelf te proberen. 

### <a name="install-and-use-onnx-runtime-with-python"></a>ONNX-runtime installeren en gebruiken met python

Python-pakketten voor ONNX runtime zijn beschikbaar op [PyPi.org](https://pypi.org) ([CPU](https://pypi.org/project/onnxruntime), [GPU](https://pypi.org/project/onnxruntime-gpu)). Lees de [systeem vereisten](https://github.com/Microsoft/onnxruntime#system-requirements) voor de installatie. 

 Als u ONNX runtime voor python wilt installeren, gebruikt u een van de volgende opdrachten: 
```python   
pip install onnxruntime       # CPU build
pip install onnxruntime-gpu   # GPU build
```

Gebruik voor het aanroepen van ONNX runtime in uw python-script:    
```python
import onnxruntime
session = onnxruntime.InferenceSession("path to model")
```

De documentatie die het model vergezelt, geeft meestal de invoer en uitvoer voor het gebruik van het model aan. U kunt ook een visualisatie programma, zoals [Netron](https://github.com/lutzroeder/Netron) , gebruiken om het model weer te geven. Met ONNX runtime kunt u ook een query uitvoeren op de meta gegevens, invoer en uitvoer van het model:    
```python
session.get_modelmeta()
first_input_name = session.get_inputs()[0].name
first_output_name = session.get_outputs()[0].name
```

Als u uw model wilt delegeren, gebruikt u `run` en geeft u een overzicht van de uitvoer die u wilt retour neren (laat dit leeg als u wilt dat alle) en een kaart van de invoer waarden. Het resultaat is een lijst met de uitvoer.  
```python
results = session.run(["output1", "output2"], {
                      "input1": indata1, "input2": indata2})
results = session.run([], {"input1": indata1, "input2": indata2})
```

Zie [ONNX runtime Reference docs](https://aka.ms/onnxruntime-python)(Engelstalig) voor de volledige Python API-referentie.    

## <a name="examples"></a>Voorbeelden

Zie [How-to-use-azureml/Deployment/onnx](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx) voor voorbeeld notitieblokken voor het maken en implementeren van onnx-modellen.

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="more-info"></a>Meer informatie

Meer informatie over ONNX of bijdragen aan het project:
+ [ONNX-project website](https://onnx.ai)
+ [ONNX-code op GitHub](https://github.com/onnx/onnx)

Meer informatie over ONNX runtime of bijdragen aan het project:
+ [ONNX runtime GitHub opslag plaats](https://github.com/Microsoft/onnxruntime)


