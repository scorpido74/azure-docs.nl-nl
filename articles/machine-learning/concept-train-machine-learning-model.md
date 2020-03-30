---
title: Modeltrainingsmethoden
titleSuffix: Azure Machine Learning
description: Meer informatie over de verschillende methoden die u gebruiken om model te trainen met Azure Machine Learning. Schatters bieden een eenvoudige manier om te werken met populaire frameworks zoals Scikit-learn, TensorFlow, Keras, PyTorch en Chainer. Machine Learning-pijplijnen maken het eenvoudig om onbeheerde uitvoeringen te plannen, heterogene compute-omgevingen te gebruiken en delen van uw workflow opnieuw te gebruiken. En uitvoeren configuraties bieden gedetailleerde controle over de compute targets waarop het trainingsproces wordt uitgevoerd.
services: machine-learning
ms.service: machine-learning
author: Blackmist
ms.author: larryfr
ms.subservice: core
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: 68ad9cc47d68f7bc3ae952f7e458781cdc1c4ab2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79129766"
---
# <a name="train-models-with-azure-machine-learning"></a>Modellen trainen met Azure Machine Learning

Azure Machine Learning biedt verschillende manieren om uw modellen te trainen, van code first-oplossingen met behulp van de SDK tot low-code-oplossingen zoals geautomatiseerde machine learning en de visuele ontwerper. Gebruik de volgende lijst om te bepalen welke trainingsmethode het bij u hoort:

+ [Azure Machine Learning SDK voor Python](#python-sdk): De Python SDK biedt verschillende manieren om modellen te trainen, elk met verschillende mogelijkheden.

    | Trainingsmethode | Beschrijving |
    | ----- | ----- |
    | [Configuratie uitvoeren](#run-configuration) | Een **generieke manier om modellen te trainen** is het gebruik van een trainingsscript en het uitvoeren van configuratie. De runconfiguratie biedt de informatie die nodig is om de trainingsomgeving te configureren die wordt gebruikt om uw model te trainen. U een runconfiguratie, uw trainingsscript en een rekendoel (de trainingsomgeving) uitvoeren en een trainingstaak uitvoeren. |
    | [Geautomatiseerde machine learning](#automated-machine-learning) | Met geautomatiseerde machine learning u **modellen trainen zonder uitgebreide data science of programmeerkennis.** Voor mensen met een data science en programmeerachtergrond biedt het een manier om tijd en middelen te besparen door algoritmeselectie en hyperparametertuning te automatiseren. U hoeft zich geen zorgen te maken over het definiëren van een runconfiguratie bij het gebruik van geautomatiseerde machine learning. |
    | [Schatters](#estimators) | Schatterklassen **maken het gemakkelijk om modellen te trainen op basis van populaire machine learning-frameworks.** Er zijn schatter klassen voor **Scikit-learn**, **PyTorch**, **TensorFlow**, en **Chainer**. Er is ook een generieke schatter die kan worden gebruikt met frameworks die nog geen speciale schatterklasse hebben. U hoeft zich geen zorgen te maken over het definiëren van een run configuratie bij het gebruik van schatters. |
    | [Machine learning-pijplijn](#machine-learning-pipeline) | Pijplijnen zijn geen andere trainingsmethode, maar een **manier om een werkstroom te definiëren met behulp van modulaire, herbruikbare stappen,** waaronder training als onderdeel van de workflow. Machine learning-pijplijnen ondersteunen het gebruik van geautomatiseerde machine learning, schatters en runconfiguratie om modellen te trainen. Aangezien pijpleidingen niet specifiek op training zijn gericht, zijn de redenen voor het gebruik van een pijpleiding gevarieerder dan de andere trainingsmethoden. Over het algemeen u een pijplijn gebruiken wanneer:<br>* U wilt **onbeheerde processen plannen,** zoals langlopende trainingstaken of gegevensvoorbereiding.<br>* Gebruik **meerdere stappen** die worden gecoördineerd over heterogene compute resources en opslaglocaties.<br>* Gebruik de pijplijn als een **herbruikbare sjabloon** voor specifieke scenario's, zoals omscholing of batchscoring.<br>* **Gegevensbronnen, ingangen en uitvoer voor** uw werkstroom bijhouden en versie.<br>* Uw workflow **wordt geïmplementeerd door verschillende teams die zelfstandig aan specifieke stappen werken.** Stappen kunnen vervolgens worden samengevoegd in een pijplijn om de werkstroom te implementeren. |

+ [Azure Machine Learning SDK voor Python](#r-sdk): De SDK gebruikt het reticulaatpakket om te binden aan De Python SDK van Azure Machine Learning. Hierdoor hebt u toegang tot kernobjecten en -methoden die in de Python SDK zijn geïmplementeerd vanuit elke R-omgeving.

+ **Ontwerper**: Azure Machine Learning designer (preview) biedt een eenvoudig toegangspunt tot machine learning voor het bouwen van proof of concepts, of voor gebruikers met weinig coderingservaring. Hiermee u modellen trainen met behulp van een webgebaseerde gebruikersinterface voor slepen en neerzetten. U Python-code gebruiken als onderdeel van het ontwerp of modellen trainen zonder code te schrijven.

+ **CLI**: De cli voor machine learning biedt opdrachten voor veelvoorkomende taken met Azure Machine Learning en wordt vaak gebruikt voor **het scripten en automatiseren van taken.** Als u bijvoorbeeld een trainingsscript of pijplijn hebt gemaakt, u de CLI gebruiken om een trainingsrun volgens een planning te starten of wanneer de gegevensbestanden die voor de training worden gebruikt, worden bijgewerkt. Voor trainingsmodellen biedt het opdrachten die trainingstaken indienen. Het kan taken indienen met behulp van run configuraties of pijplijnen.

Elk van deze trainingsmethoden kan verschillende typen rekenbronnen gebruiken voor training. Gezamenlijk worden deze resources [__compute targets__](concept-azure-machine-learning-architecture.md#compute-targets)genoemd. Een compute target kan een lokale machine of een cloudbron zijn, zoals een Azure Machine Learning Compute, Azure HDInsight of een externe virtuele machine.

## <a name="python-sdk"></a>Python SDK

Met de Azure Machine Learning SDK voor Python u machine learning-werkstromen bouwen en uitvoeren met Azure Machine Learning. U communiceren met de service vanuit een interactieve Python-sessie, Jupyter-notitieblokken, Visual Studio Code of andere IDE.You can interact with the service from an interactive Python session, Jupyter Notebooks, Visual Studio Code, or other IDE.

* [Wat is de Azure Machine Learning SDK voor Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [De SDK installeren/bijwerken](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
* [Een ontwikkelomgeving configureren voor Azure Machine Learning](how-to-configure-environment.md)

### <a name="run-configuration"></a>Configuratie uitvoeren

Een algemene trainingstaak met Azure Machine Learning kan worden gedefinieerd met de [RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py). De runconfiguratie wordt vervolgens gebruikt, samen met uw trainingsscript(en) om een model te trainen op een compute-doel.

U beginnen met een runconfiguratie voor uw lokale computer en vervolgens naar een configuratie overschakelen voor een op de cloud gebaseerd gegevensdoel als dat nodig is. Wanneer u het rekendoel wijzigt, wijzigt u alleen de runconfiguratie die u gebruikt. Een run registreert ook informatie over de trainingstaak, zoals de ingangen, uitvoer en logboeken.

* [Wat is een run configuratie?](concept-azure-machine-learning-architecture.md#run-configurations)
* [Zelfstudie: Train je eerste ML-model](tutorial-1st-experiment-sdk-train.md)
* [Voorbeelden: Jupyter Notebook voorbeelden van trainingsmodellen](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training)
* [How to: Compute targets instellen en gebruiken voor modeltraining](how-to-set-up-training-targets.md)

### <a name="automated-machine-learning"></a>Geautomatiseerde Machine Learning

Definieer de iteraties, hyperparameter-instellingen, featurization en andere instellingen. Tijdens de training probeert Azure Machine Learning verschillende algoritmen en parameters parallel. De training stopt zodra deze de door u gedefinieerde exitcriteria raakt. U hoeft zich geen zorgen te maken over het definiëren van een run configuratie bij het gebruik van schatters.

> [!TIP]
> Naast de Python SDK u automated ML ook gebruiken via [Azure Machine Learning studio.](https://ml.azure.com)

* [Wat is geautomatiseerde machine learning?](concept-automated-ml.md)
* [Zelfstudie: Maak uw eerste classificatiemodel met geautomatiseerde machine learning](tutorial-first-experiment-automated-ml.md)
* [Zelfstudie: Gebruik geautomatiseerde machine learning om taxitarieven te voorspellen](tutorial-auto-train-models.md)
* [Voorbeelden: Jupyter Notebook voorbeelden voor geautomatiseerde machine learning](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)
* [How to: Geautomatiseerde ML-experimenten configureren in Python](how-to-configure-auto-train.md)
* [How to: Autotrain een tijd-serie voorspelling model](how-to-auto-train-forecast.md)
* [Hoe: geautomatiseerde machine learning-experimenten maken, verkennen en implementeren met Azure Machine Learning-studio](how-to-use-automated-ml-for-ml-models.md)

### <a name="estimators"></a>Schatters

Schatters maken het gemakkelijk om modellen te trainen met behulp van populaire ML-frameworks. Als u **Scikit-learn**, **PyTorch**, **TensorFlow**of **Chainer**gebruikt, moet u overwegen een schatter te gebruiken voor training. Er is ook een generieke schatter die kan worden gebruikt met frameworks die nog geen speciale schatterklasse hebben. U hoeft zich geen zorgen te maken over het definiëren van een run configuratie bij het gebruik van schatters.

* [Wat zijn schatters?](concept-azure-machine-learning-architecture.md#estimators)
* [Zelfstudie: Classificatiemodellen trainen met MNIST-gegevens en leren met Azure Machine Learning](tutorial-train-models-with-aml.md)
* [Voorbeelden: Voorbeelden van Jupyter Notebook van het gebruik van schatters](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning)
* [How to: Schatters maken in training](how-to-train-ml-models.md)

### <a name="machine-learning-pipeline"></a>Machine learning-pijplijn

Machine learning-pijplijnen kunnen gebruik maken van de eerder genoemde trainingsmethoden (run configuratie, schatters en geautomatiseerde machine learning). Pijplijnen zijn meer over het creëren van een workflow, dus ze omvatten meer dan alleen de opleiding van modellen. In een pijplijn u een model trainen met behulp van geautomatiseerde machine learning, schatters of runconfiguraties.

* [Wat zijn ML-pijplijnen in Azure Machine Learning?](concept-ml-pipelines.md)
* [Machine learning-pijplijnen maken en uitvoeren met Azure Machine Learning SDK](how-to-create-your-first-pipeline.md)
* [Zelfstudie: Azure Machine Learning-pijplijnen gebruiken voor batchscores](tutorial-pipeline-batch-scoring-classification.md)
* [Voorbeelden: Voorbeelden van Jupyter Notebook voor machine learning-pijplijnen](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines)
* [Voorbeelden: Pijplijn met geautomatiseerde machine learning](https://aka.ms/pl-automl)
* [Voorbeelden: Pijplijn met schatters](https://aka.ms/pl-estimator)

## <a name="r-sdk"></a>R SDK

Met de R SDK u de R-taal gebruiken met Azure Machine Learning. De SDK gebruikt het reticulaatpakket om te binden aan Azure Machine Learning's Python SDK. Hierdoor hebt u toegang tot kernobjecten en -methoden die in de Python SDK zijn geïmplementeerd vanuit elke R-omgeving.

Raadpleeg voor meer informatie de volgende artikelen:

* [Zelfstudie: Een logistiek regressiemodel maken](tutorial-1st-r-experiment.md)
* [Azure Machine Learning SDK voor R-verwijzing](https://azure.github.io/azureml-sdk-for-r/index.html)

## <a name="azure-machine-learning-designer"></a>Azure Machine Learning-ontwerper

Met de ontwerper u modellen trainen met behulp van een interface voor slepen en neerzetten in uw webbrowser.

+ [Wat is de ontwerper?](concept-designer.md)
+ [Tutorial : Voorspel autoprijs](tutorial-designer-automobile-price-train-score.md)
+ [Regressie: Prijs voorspellen](how-to-designer-sample-regression-automobile-price-basic.md)
+ [Classificatie: Voorspel inkomsten](how-to-designer-sample-classification-predict-income.md)
+ [Classificatie: Voorspel churn, appetency, en up-selling](how-to-designer-sample-classification-churn.md)
+ [Classificatie met aangepast R-script: voorspel vertragingen voor vluchten](how-to-designer-sample-classification-flight-delay.md)
+ [Tekstclassificatie: Wikipedia SP 500-gegevensset](how-to-designer-sample-text-classification.md)

## <a name="cli"></a>CLI

De machine learning CLI is een extensie voor de Azure CLI. Het biedt cross-platform CLI-opdrachten voor het werken met Azure Machine Learning. Normaal gesproken gebruikt u de CLI om taken te automatiseren, zoals het trainen van een machine learning-model.

* [De CLI-extensie voor Azure Machine Learning gebruiken](reference-azure-machine-learning-cli.md)
* [MLOps op Azure](https://github.com/microsoft/MLOps)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [instellen van trainingsomgevingen](how-to-set-up-training-targets.md).
