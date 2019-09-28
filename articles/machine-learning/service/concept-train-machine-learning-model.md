---
title: Trainings methoden voor modellen
titleSuffix: Azure Machine Learning
description: Meer informatie over de verschillende methoden die u kunt gebruiken voor het trainen van model met Azure Machine Learning. Schattingen bieden een eenvoudige manier om te werken met populaire frameworks zoals Scikit-learn, tensor flow, Keras, PyTorch en Chainer. Met Machine Learning pijp lijnen kunt u eenvoudig uitvoeringen zonder toezicht plannen, heterogene Compute-omgevingen gebruiken en delen van uw werk stroom hergebruiken. En uitvoerings configuraties bieden een gedetailleerde controle over de reken doelen waarop het trainings proces wordt uitgevoerd.
services: machine-learning
ms.service: machine-learning
author: Blackmist
ms.author: larryfr
ms.subservice: core
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: a755fe1607e581cb0a25eb9bd90c2ba223829a46
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350592"
---
# <a name="train-models-with-azure-machine-learning"></a>Trainen van modellen met Azure Machine Learning

Azure Machine Learning biedt verschillende manieren om uw modellen te trainen, van code First Solutions met behulp van de SDK tot low-code oplossingen, zoals geautomatiseerde machine learning en de visuele interface. Gebruik de volgende lijst om te bepalen welke Trainings methode het meest geschikt is voor u:

+ [Azure machine learning SDK voor python](#python-sdk): De python-SDK biedt verschillende manieren om modellen te trainen, elk met verschillende mogelijkheden.

    | Trainings methode | Description |
    | ----- | ----- |
    | [Configuratie uitvoeren](#run-configuration) | Een **algemene manier om modellen te trainen** is door een trainings script te gebruiken en de configuratie uit te voeren. De uitvoerings configuratie bevat de informatie die nodig is voor het configureren van de trainings omgeving die wordt gebruikt voor het trainen van uw model. U kunt een uitvoerings configuratie, uw trainings script en een berekenings doel (de trainings omgeving) maken en een trainings taak uitvoeren. |
    | [Automatische machine learning](#automated-machine-learning) | Met geautomatiseerde machine learning kunt u **modellen trainen zonder uitgebreide data Wetenschappen of programmeer kennis**. Voor mensen met een gegevens wetenschap en-programmeer achtergrond biedt het een manier om tijd en resources te besparen door het automatiseren van algoritme selectie en afstemming-afstemming. U hoeft zich geen zorgen te maken over het definiëren van een configuratie voor het uitvoeren van automatische machine learning. |
    | [Schattingen](#estimators) | Met Estimator-klassen kunt u **eenvoudig modellen trainen op basis van populaire machine learning Frameworks**. Er zijn Estimator-klassen voor **Scikit-Learn**, **PyTorch**, **tensor flow**en **Chainer**. Er is ook een algemene Estimator die kan worden gebruikt met frameworks die nog geen specifieke Estimator-klasse hebben. U hoeft zich geen zorgen te maken over het definiëren van een uitvoerings configuratie bij het gebruik van schattingen. |
    | [Machine learning-pijp lijn](#machine-learning-pipeline) | Pijp lijnen zijn geen andere Trainings methode, maar een **manier om een werk stroom te definiëren met behulp van modulaire, herbruikbare stappen**die training kunnen bevatten als onderdeel van de werk stroom. Machine learning-pijp lijnen ondersteunen het gebruik van geautomatiseerde machine learning, schattingen en het uitvoeren van een configuratie voor het trainen van modellen. Omdat pijp lijnen niet specifiek zijn gericht op training, zijn de redenen voor het gebruik van een pijp lijn meer gevarieerder dan de andere trainings methoden. Over het algemeen kunt u gebruikmaken van een pijp lijn wanneer:<br>* U wilt **processen zonder toezicht plannen** , zoals langlopende trainings taken of gegevens voorbereiding.<br>* Gebruik **meerdere stappen** die worden gecoördineerd over heterogene reken resources en opslag locaties.<br>* Gebruik de pijp lijn als een **herbruikbare sjabloon** voor specifieke scenario's, zoals retraining of batch scores.<br>* **gegevens bronnen, invoer en uitvoer** voor uw werk stroom bijhouden.<br>* Uw werk stroom wordt **geïmplementeerd door verschillende teams die onafhankelijk werken met specifieke stappen**. Stappen kunnen vervolgens aan elkaar worden gekoppeld in een pijp lijn om de werk stroom te implementeren. |

+ **Visuele interface**: De Azure Machine Learning __Visual Interface__ biedt een eenvoudig ingangs punt in machine learning voor het bouwen van een proef versie van concepten of voor gebruikers met weinig code ring. Het biedt u de mogelijkheid om modellen te trainen met behulp van een slepen-en-neerzetten op het web. U kunt python-code gebruiken als onderdeel van het ontwerp of modellen trainen zonder code te schrijven.

+ **CLI**: De machine learning CLI bevat opdrachten voor algemene taken met Azure Machine Learning en wordt vaak gebruikt voor het **uitvoeren van scripts en het automatiseren van taken**. Als u bijvoorbeeld een trainings script of pijp lijn hebt gemaakt, kunt u de CLI gebruiken om een training te starten volgens een planning of wanneer de gegevens bestanden die worden gebruikt voor de training, worden bijgewerkt. Voor trainings modellen bevat deze opdrachten voor het verzenden van trainings taken. Het kan taken verzenden met behulp van run-configuraties of-pijp lijnen.

Elk van deze trainings methoden kan gebruikmaken van verschillende soorten reken resources voor training. Deze resources worden gezamenlijk aangeduid als [__Compute-doelen__](concept-azure-machine-learning-architecture.md#compute-targets). Een compute-doel kan een lokale machine of een Cloud resource zijn, zoals een Azure Machine Learning compute, Azure HDInsight of een externe virtuele machine.

## <a name="python-sdk"></a>Python-SDK

Met de Azure Machine Learning SDK voor python kunt u machine learning werk stromen maken en uitvoeren met Azure Machine Learning. U kunt met de service communiceren vanuit een interactieve python-sessie, Jupyter-notebooks, Visual Studio code of een andere IDE.

* [Wat is de Azure Machine Learning SDK voor python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [De SDK installeren/bijwerken](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
* [Een ontwikkel omgeving configureren voor Azure Machine Learning](how-to-configure-environment.md)

### <a name="run-configuration"></a>Configuratie van de uitvoering

Een algemene trainings taak met Azure Machine Learning kan worden gedefinieerd met behulp van de [RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py). De uitvoerings configuratie wordt vervolgens gebruikt, samen met uw trainings script (s) voor het trainen van een model op een compute-doel.

U kunt beginnen met het uitvoeren van een configuratie voor uw lokale computer en overschakelen naar een op de cloud gebaseerd reken doel als dat nodig is. Wanneer u het berekenings doel wijzigt, wijzigt u alleen de configuratie van de uitvoering die u gebruikt. Een uitvoering registreert ook informatie over de trainings taak, zoals de invoer, uitvoer en Logboeken.

* [Wat is een uitvoerings configuratie?](concept-azure-machine-learning-architecture.md#run-configurations)
* [Zelfstudie: Train uw eerste ML model @ no__t-0
* [Examples: Jupyter Notebook voor beelden van trainings modellen @ no__t-0
* [Procedure: Reken doelen instellen en gebruiken voor model training @ no__t-0

### <a name="automated-machine-learning"></a>Geautomatiseerde Machine Learning

Definieer de herhalingen, afstemming-instellingen, parametrisatie en andere instellingen. Tijdens de training probeert Azure Machine Learning verschillende algoritmen en para meters parallel. De training stopt zodra de door u opgegeven afsluit criteria zijn gevonden. U hoeft zich geen zorgen te maken over het definiëren van een uitvoerings configuratie bij het gebruik van schattingen.

> [!TIP]
> In naast aan de python-SDK kunt u ook gebruikmaken van automatische MILLILITERs via de [pagina voor het land van de werk ruimte (preview)](https://ml.azure.com).

* [Wat is automatische machine learning?](concept-automated-ml.md)
* [Zelfstudie: Uw eerste classificatie model maken met automatische machine learning @ no__t-0
* [Zelfstudie: Automatische machine learning gebruiken om de taxi tarieven te voors pellen @ no__t-0
* [Examples: Jupyter Notebook-voor beelden voor automatische machine learning @ no__t-0
* [Procedure: Automatische ML experimenten configureren in python @ no__t-0
* [Procedure: Een time-series-prognose model voor tijd reeksen autotrainen @ no__t-0
* [Procedure: Automatische machine learning experimenten maken, verkennen en implementeren met behulp van de landings pagina van Azure Machine Learning werk ruimte (preview) ](how-to-create-portal-experiments.md)

### <a name="estimators"></a>Schattingen

Schattingen maken het eenvoudig om modellen te trainen met behulp van populaire ML-frameworks. Als u **Scikit-Learn**, **PyTorch**, **tensor flow**of **Chainer**gebruikt, kunt u overwegen een Estimator te gebruiken voor de training. Er is ook een algemene Estimator die kan worden gebruikt met frameworks die nog geen specifieke Estimator-klasse hebben. U hoeft zich geen zorgen te maken over het definiëren van een uitvoerings configuratie bij het gebruik van schattingen.

* [Wat zijn schattingen?](concept-azure-machine-learning-architecture.md#estimators)
* [Zelfstudie: Beeld classificatie modellen trainen met MNIST-gegevens en scikit-meer informatie met behulp van Azure Machine Learning @ no__t-0
* [Examples: Jupyter Notebook voor beelden van het gebruik van schattingen @ no__t-0
* [Procedure: Schattingen maken in training @ no__t-0

### <a name="machine-learning-pipeline"></a>Machine learning-pijp lijn

Machine learning-pijp lijnen kunnen de eerder genoemde trainings methoden (configuratie, schattingen en geautomatiseerd machine learning) gebruiken. Pijp lijnen zijn meer informatie over het maken van een werk stroom, zodat ze meer dan alleen de training van modellen omvatten. In een pijp lijn kunt u een model trainen met geautomatiseerde machine learning, schattingen of het uitvoeren van configuraties.

* [Wat zijn ML-pijp lijnen in Azure Machine Learning?](concept-ml-pipelines.md)
* [machine learning-pijp lijnen maken en uitvoeren met Azure Machine Learning SDK](how-to-create-your-first-pipeline.md)
* [Zelfstudie: Azure Machine Learning pijplijnen gebruiken voor batch Score @ no__t-0
* [Examples: Jupyter Notebook-voor beelden voor machine learning pijp lijnen @ no__t-0
* [Examples: Pijp lijn met automatische machine learning @ no__t-0
* [Examples: Pijp lijn met schattingen @ no__t-0

## <a name="visual-interface"></a>Visuele interface

Met de Visual Interface (preview) kunt u modellen trainen met behulp van een slepen-en-neerzetten-interface in uw webbrowser.

+ [Wat is de visuele interface?](ui-concept-visual-interface.md)
+ [Tutorial: Prijs voor auto Mobile voors pellen @ no__t-0
+ [Regression: Prijs voors pellen](how-to-ui-sample-regression-predict-automobile-price-basic.md)
+ [Classification: Krediet risico voors pellen](how-to-ui-sample-classification-predict-credit-risk-basic.md)
+ [Classification: Verloop, verlangen en up-sell voors pellen](how-to-ui-sample-classification-predict-churn.md)

## <a name="cli"></a>CLI

De machine learning CLI is een uitbrei ding voor de Azure CLI. Het biedt platformoverschrijdende CLI-opdrachten voor het werken met Azure Machine Learning. Normaal gesp roken gebruikt u de CLI om taken te automatiseren, zoals het trainen van een machine learning model.

* [De CLI-uitbrei ding voor Azure Machine Learning gebruiken](reference-azure-machine-learning-cli.md)
* [MLOps op Azure](https://github.com/microsoft/MLOps)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [instellen van trainings omgevingen](how-to-set-up-training-targets.md).
