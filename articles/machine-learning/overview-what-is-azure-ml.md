---
title: Wat is Azure Machine Learning
description: 'Overzicht van Azure Machine Learning: een geïntegreerde, end-to-end oplossing voor gegevens wetenschap voor professionele gegevens wetenschappers om geavanceerde analyse toepassingen te ontwikkelen, te experimenteren en te implementeren op Cloud schaal.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: j-martens
ms.author: jmartens
ms.date: 11/04/2019
ms.openlocfilehash: b8dbbb2810277bef20cb3b9b47a63deeea3e0ff9
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "79241610"
---
# <a name="what-is-azure-machine-learning"></a>Wat is Azure Machine Learning?

In dit artikel vindt u meer informatie over Azure Machine Learning, een cloud omgeving die u kunt gebruiken voor het trainen, implementeren, automatiseren, beheren en volgen van ML-modellen. 

Azure Machine Learning kunnen worden gebruikt voor elk soort machine learning, van klassieke ml tot diep gaande lessen, onder Super visie en zonder toezicht. Of u liever python-of R-code schrijft of opties voor nul code/laag code, zoals de [ontwerp functie](tutorial-designer-automobile-price-train-score.md), u kunt zeer nauw keurige machine learning en diep gaande modellen bouwen, trainen en bijhouden in een Azure machine learning-werkruimte. 

Start training op uw lokale machine en schaal vervolgens naar de Cloud. 

De service is ook compatibel met populaire open-source hulpprogram ma's, zoals PyTorch, tensor flow en scikit-learn.

> [!VIDEO https://channel9.msdn.com/Events/Connect/Microsoft-Connect--2018/D240/player]

> [!Tip]
> **Gratis proef versie.**  Als u nog geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer vandaag nog de [gratis of betaalde versie van Azure machine learning](https://aka.ms/AMLFree) . U ontvangt tegoed wat u kunt uitgeven aan Azure-services. Als uw tegoed op is, kunt u het account behouden en de [gratis Azure-services](https://azure.microsoft.com/free/) gebruiken. Er wordt nooit geld van uw creditcard afgeschreven, tenzij u uw instellingen zelf wijzigt en expliciet aangeeft dat u wilt betalen.


## <a name="what-is-machine-learning"></a>Wat is machine learning?

Machine learning is een techniek van gegevenswetenschap die computers in staat stelt om op basis van bestaande gegevens, toekomstig gedrag, resultaten en trends te voorspellen. Met behulp van machine learning kunnen computers leren zonder dat ze expliciet worden geprogrammeerd.

Prognoses of voorspellingen op basis van machine learning kunnen apps en apparaten slimmer maken. Wanneer u bijvoorbeeld online winkelt, helpt machine learning op basis van wat u eerder hebt gekocht, bij het aanraden van andere producten waarvoor u mogelijk belangstelling hebt. Of wanneer uw creditcard wordt gebruikt, vergelijkt machine learning de transactie met een database van transacties om fraude te detecteren. En wanneer uw robotstofzuiger een kamer stofzuigt, helpt machine learning bij het besluiten wanneer de taak klaar is.

## <a name="machine-learning-tools-to-fit-each-task"></a>Machine learning-hulpprogram ma's om aan elke taak te voldoen 

Azure Machine Learning biedt alle hulpprogram ma's voor ontwikkel aars en gegevens wetenschappers die hun machine learning werk stromen nodig hebben, waaronder:
+ De [Azure machine learning Designer](tutorial-designer-automobile-price-train-score.md) (preview): modules slepen en neerzetten om uw experimenten te bouwen en vervolgens pijp lijnen te implementeren.

+ Jupyter-notebooks: gebruik onze [voor beelden van notitie blokken](https://aka.ms/aml-notebooks) of maak uw eigen notitie blokken om te profiteren van onze <a href="https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">SDK voor python</a> -voor beelden voor uw machine learning. 

+ R-scripts of-notebooks waarin u de <a href="https://azure.github.io/azureml-sdk-for-r/reference/index.html" target="_blank">SDK voor r</a> gebruikt voor het schrijven van uw eigen code of het gebruik van de R-modules in de ontwerp functie.

+ [Visual Studio Code-extensie](tutorial-setup-vscode-extension.md)

+ [Machine learning-CLI](reference-azure-machine-learning-cli.md)

+ Open source frameworks zoals PyTorch, tensor flow en scikit-Learn en nog veel meer

U kunt zelfs [MLflow gebruiken om metrische gegevens bij te houden en modellen](how-to-use-mlflow.md) of Kubeflow te implementeren om [end-to-end werk stroom pijplijnen te bouwen](https://www.kubeflow.org/docs/azure/).

## <a name="build-ml-models-in-python-or-r"></a>ML-modellen bouwen in Python of R

Start training op uw lokale computer met behulp van de Azure Machine Learning <a href="https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">PYTHON SDK</a> of <a href="https://azure.github.io/azureml-sdk-for-r/reference/index.html" target="_blank">R SDK</a>. Vervolgens kunt u uitschalen naar de Cloud. 

Dankzij de vele beschikbare [rekendoelen](how-to-set-up-training-targets.md) zoals Azure Machine Learning Compute en [Azure Databricks](/azure/azure-databricks/what-is-azure-databricks), en [geavanceerde afstemmingsservices van hyperparameters](how-to-tune-hyperparameters.md), kunt u sneller betere modellen bouwen, met de kracht van de cloud.

U kunt [model training en-aanpassing ook automatiseren](tutorial-auto-train-models.md) met de SDK.

## <a name="build-ml-models-with-no-code-tools"></a>Bouw ML-modellen met hulpprogram ma's zonder code

Voor code-free of low-code training en implementatie kunt u het volgende proberen:

+ **Azure Machine Learning Designer (preview-versie)**

  Gebruik de ontwerp functie voor het voorbereiden van gegevens, het trainen, testen, implementeren, beheren en bijhouden van machine learning modellen zonder code te schrijven. Er is geen programmering vereist. u kunt gegevens sets en modules visueel verbinden om uw model samen te stellen. Probeer de [ontwerp zelf studie](tutorial-designer-automobile-price-train-score.md).

  Meer informatie vindt u in [het artikel overzicht van Azure machine learning Designer](concept-designer.md). 

  ![Voor beeld van Azure Machine Learning Designer](./media/overview-what-is-azure-ml/designer-drag-and-drop.gif)

+ **Automatische machine learning gebruikers interface**

  Meer informatie over het maken van [automatische ml-experimenten](tutorial-first-experiment-automated-ml.md) in de gebruiks vriendelijke interface. 

  [![Navigatie deel venster van Azure Machine Learning Studio](./media/overview-what-is-azure-ml/azure-machine-learning-automated-ml-ui.jpg)](./media/overview-what-is-azure-ml/azure-machine-learning-automated-ml-ui.jpg)

## <a name="mlops-deploy--lifecycle-management"></a>MLOps: & levenscyclus beheer implementeren
Wanneer u het juiste model hebt, kunt u het eenvoudig gebruiken in een webservice, op een IoT-apparaat of in Power BI. Zie het artikel [Hoe implementeren en waar](how-to-deploy-and-where.md) voor meer informatie.

Vervolgens kunt u uw geïmplementeerde modellen beheren met behulp van de [Azure machine learning SDK voor python](https://aka.ms/aml-sdk), [Azure machine learning studio](https://ml.azure.com)of de [machine learning cli](reference-azure-machine-learning-cli.md).

Deze modellen kunnen worden gebruikt en voor spellingen in [realtime](how-to-consume-web-service.md) of [asynchroon](how-to-use-parallel-run-step.md) in grote hoeveel heden gegevens worden geretourneerd.

En met geavanceerde [machine learning pijp lijnen](concept-ml-pipelines.md)kunt u samen werken aan elke stap van het voorbereiden van gegevens, het model leren en evalueren van een cursus, via implementatie. Met pijp lijnen kunt u het volgende doen:

* Het end-to-end-machine learning proces automatiseren in de Cloud
* Onderdelen opnieuw gebruiken en alleen stappen opnieuw uitvoeren als dat nodig is
* In elke stap verschillende reken bronnen gebruiken
* Batch Score taken uitvoeren

Als u scripts wilt gebruiken om uw machine learning werk stroom te automatiseren, biedt de [machine learning cli](reference-azure-machine-learning-cli.md) opdracht regel Programma's voor het uitvoeren van algemene taken, zoals het verzenden van een training of het implementeren van een model.

Zie [volgende stappen](#next-steps)om aan de slag te gaan met Azure machine learning.

## <a name="integration-with-other-services"></a>Integratie met andere services

Azure Machine Learning werkt met andere services op het Azure-platform en kan ook worden geïntegreerd met open source-hulpprogram ma's zoals Git en MLFlow.

+ Reken doelen zoals __Azure Kubernetes service__, __Azure container instances__, __Azure Databricks__, __Azure data Lake Analytics__en __Azure HDInsight__. Zie [Wat zijn reken doelen?](concept-compute-target.md)voor meer informatie over Compute-doelen.
+ __Azure Event grid__. Zie [Azure machine learning-gebeurtenissen gebruiken](concept-event-grid-integration.md)voor meer informatie.
+ __Azure monitor__. Zie [Monitoring Azure machine learning](monitor-azure-machine-learning.md)voor meer informatie.
+ Gegevens archieven zoals __Azure Storage accounts__, __Azure data Lake Storage__, __Azure SQL database__, __Azure database for PostgreSQL__en __Azure open gegevens sets__. Zie voor meer informatie [toegang tot gegevens in azure Storage-services](how-to-access-data.md) en gegevens [sets maken met Azure open data sets](how-to-create-register-datasets.md#create-datasets-with-azure-open-datasets).
+ __Virtuele netwerken van Azure__. Zie voor meer informatie [veilig experimenteren en dezicht in een virtueel netwerk](how-to-enable-virtual-network.md).
+ __Azure-pijp lijnen__. Zie [machine learning modellen trainen en implementeren](/azure/devops/pipelines/targets/azure-machine-learning)voor meer informatie.
+ __Git-opslagplaats logboeken__. Zie [Git-integratie](concept-train-model-git-integration.md)voor meer informatie.
+ __MLFlow__. Zie voor meer informatie [MLflow voor het bijhouden van metrische gegevens en het implementeren van modellen](how-to-use-mlflow.md) 
+ __Kubeflow__. Zie [end-to-end werk stroom pijplijnen bouwen](https://www.kubeflow.org/docs/azure/)voor meer informatie.

### <a name="secure-communications"></a>Beveiligde communicatie

Uw Azure Storage-account, compute-doelen en andere resources kunnen veilig in een virtueel netwerk worden gebruikt om modellen te trainen en de interferentie af te zetten. Zie voor meer informatie [veilig experimenteren en dezicht in een virtueel netwerk](how-to-enable-virtual-network.md).

## <a name="basic--enterprise-editions"></a><a name="sku"></a>Basis & Enter prise-edities

Azure Machine Learning biedt twee edities die zijn afgestemd op uw machine learning behoeften:
+ Basic (algemeen beschikbaar)
+ Enter prise (preview-versie)

Deze edities bepalen welke machine learning-hulpprogram ma's beschikbaar zijn voor ontwikkel aars en gegevens wetenschappers uit hun werk ruimte.   

Met eenvoudige werk ruimten kunt u Azure Machine Learning blijven gebruiken en betaalt u alleen voor de Azure-resources die worden verbruikt tijdens het machine learning proces. Enter prise Edition-werk ruimten worden alleen in rekening gebracht voor hun Azure-verbruik tijdens de preview-versie. Meer informatie over de beschik baarheid vindt u in de Azure Machine Learning [Edition overview & pagina met prijzen](https://azure.microsoft.com/pricing/details/machine-learning/). 

U wijst de editie toe wanneer u een werk ruimte maakt. Bovendien zijn bestaande werk ruimten geconverteerd naar de Basic-editie. De Basic-editie omvat alle functies die al algemeen beschikbaar waren vanaf oktober 2019. Alle experimenten in deze werk ruimten die zijn gemaakt met behulp van ENTER prise Edition-functies, blijven beschikbaar voor u in alleen-lezen totdat u een upgrade naar Enter prise uitvoert. Meer informatie over het [bijwerken van een Basic-werk ruimte naar Enter prise Edition](how-to-manage-workspace.md#upgrade). 

Klanten zijn verantwoordelijk voor de kosten die zijn gemaakt voor reken-en andere Azure-resources gedurende deze periode.

## <a name="next-steps"></a>Volgende stappen

- Maak uw eerste experiment met uw voorkeurs methode:
  + [Python-notebooks gebruiken voor het trainen & implementeren van ML-modellen](tutorial-1st-experiment-sdk-setup.md)
  + [R-korting gebruiken om & Deploy-modellen te trainen](tutorial-1st-r-experiment.md) 
  + [Automatische machine learning gebruiken om & Deploy-modellen te trainen](tutorial-first-experiment-automated-ml.md) 
  + [Gebruik de functie slepen & neerzetten van Designer om & implementeren te trainen](tutorial-designer-automobile-price-train-score.md) 
  + [De machine learning CLI gebruiken om een model te trainen en te implementeren](tutorial-train-deploy-model-cli.md)

- Leer meer over [Machine Learning-pijplijnen](concept-ml-pipelines.md) om uw Machine Learning-scenario’s te bouwen, te optimaliseren en te beheren.

- Lees het artikel uitgebreide [Azure machine learning architecturen en concepten](concept-azure-machine-learning-architecture.md) .
