---
title: Wat is Azure Machine Learning
description: Overzicht van Azure Machine Learning - Een geïntegreerde, end-to-end data science-oplossing voor professionele gegevenswetenschappers om geavanceerde analysetoepassingen op cloudschaal te ontwikkelen, te experimenteren en te implementeren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: j-martens
ms.author: jmartens
ms.date: 11/04/2019
ms.openlocfilehash: b8dbbb2810277bef20cb3b9b47a63deeea3e0ff9
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241610"
---
# <a name="what-is-azure-machine-learning"></a>Wat is Azure Machine Learning?

In dit artikel leert u over Azure Machine Learning, een cloudomgeving die u gebruiken om ML-modellen te trainen, implementeren, automatiseren, beheren en volgen. 

Azure Machine Learning kan worden gebruikt voor elke vorm van machine learning, van klassieke ml tot deep learning, onder toezicht en zonder toezicht leren. Of u nu liever Python- of R-code of zero-code/low-code-opties zoals de [ontwerper](tutorial-designer-automobile-price-train-score.md)schrijft, u zeer nauwkeurige machine learning- en deep learning-modellen bouwen, trainen en bijhouden in een Azure Machine Learning Workspace. 

Begin met trainen op uw lokale machine en schaal vervolgens naar de cloud. 

De dienst werkt ook samen met populaire open-source tools, zoals PyTorch, TensorFlow en scikit-learn.

> [!VIDEO https://channel9.msdn.com/Events/Connect/Microsoft-Connect--2018/D240/player]

> [!Tip]
> **Gratis proefperiode!**  Als u nog geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer vandaag nog de [gratis of betaalde versie van Azure Machine Learning.](https://aka.ms/AMLFree) U ontvangt tegoed wat u kunt uitgeven aan Azure-services. Als uw tegoed op is, kunt u het account behouden en de [gratis Azure-services](https://azure.microsoft.com/free/) gebruiken. Er wordt nooit geld van uw creditcard afgeschreven, tenzij u uw instellingen zelf wijzigt en expliciet aangeeft dat u wilt betalen.


## <a name="what-is-machine-learning"></a>Wat is machine learning?

Machine learning is een techniek van gegevenswetenschap die computers in staat stelt om op basis van bestaande gegevens, toekomstig gedrag, resultaten en trends te voorspellen. Met behulp van machine learning kunnen computers leren zonder dat ze expliciet worden geprogrammeerd.

Prognoses of voorspellingen op basis van machine learning kunnen apps en apparaten slimmer maken. Wanneer u bijvoorbeeld online winkelt, helpt machine learning op basis van wat u eerder hebt gekocht, bij het aanraden van andere producten waarvoor u mogelijk belangstelling hebt. Of wanneer uw creditcard wordt gebruikt, vergelijkt machine learning de transactie met een database van transacties om fraude te detecteren. En wanneer uw robotstofzuiger een kamer stofzuigt, helpt machine learning bij het besluiten wanneer de taak klaar is.

## <a name="machine-learning-tools-to-fit-each-task"></a>Machine learning-tools die bij elke taak passen 

Azure Machine Learning biedt alle tools die ontwikkelaars en gegevenswetenschappers nodig hebben voor hun machine learning-workflows, waaronder:
+ De [Azure Machine Learning-ontwerper](tutorial-designer-automobile-price-train-score.md) (voorbeeld): drag-n-drop-modules om uw experimenten te bouwen en vervolgens pijplijnen te implementeren.

+ Jupyter-notitieblokken: gebruik onze [voorbeeldnotitieblokken](https://aka.ms/aml-notebooks) of maak uw eigen notitieblokken om onze <a href="https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">SDK voor Python-voorbeelden te</a> gebruiken voor uw machine learning. 

+ R-scripts of -notitieblokken waarin u de <a href="https://azure.github.io/azureml-sdk-for-r/reference/index.html" target="_blank">SDK voor R</a> gebruikt om uw eigen code te schrijven of de R-modules in de ontwerper te gebruiken.

+ [Visual Studio Code-extensie](tutorial-setup-vscode-extension.md)

+ [Machine learning-CLI](reference-azure-machine-learning-cli.md)

+ Open-source frameworks zoals PyTorch, TensorFlow en scikit-learn en nog veel meer

U [MLflow](how-to-use-mlflow.md) zelfs gebruiken om statistieken bij te houden en modellen of Kubeflow te implementeren om [end-to-end workflowpijplijnen te bouwen.](https://www.kubeflow.org/docs/azure/)

## <a name="build-ml-models-in-python-or-r"></a>ML-modellen bouwen in Python of R

Start met trainen op uw lokale machine met de Azure Machine Learning <a href="https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">Python SDK</a> of <a href="https://azure.github.io/azureml-sdk-for-r/reference/index.html" target="_blank">R SDK.</a> Vervolgens u opschalen naar de cloud. 

Dankzij de vele beschikbare [rekendoelen](how-to-set-up-training-targets.md) zoals Azure Machine Learning Compute en [Azure Databricks](/azure/azure-databricks/what-is-azure-databricks), en [geavanceerde afstemmingsservices van hyperparameters](how-to-tune-hyperparameters.md), kunt u sneller betere modellen bouwen, met de kracht van de cloud.

U ook [modeltraining en tuning automatiseren](tutorial-auto-train-models.md) met de SDK.

## <a name="build-ml-models-with-no-code-tools"></a>ML-modellen bouwen met hulpprogramma's zonder code

Probeer het als volgt te trainen en implementeren voor codevrije of low-code training en implementatie:

+ **Azure Machine Learning-ontwerper (voorbeeld)**

  Gebruik de ontwerper om gegevens voor te bereiden, te trainen, te testen, implementeren, beheren en bijhouden van machine learning-modellen zonder code te schrijven. Er is geen programmering vereist, u verbindt gegevenssets en modules visueel om uw model te construeren. Probeer de [designer tutorial](tutorial-designer-automobile-price-train-score.md).

  Meer informatie vindt u in [het overzichtsartikel van Azure Machine Learning designer](concept-designer.md). 

  ![Voorbeeld van Azure Machine Learning-ontwerper](./media/overview-what-is-azure-ml/designer-drag-and-drop.gif)

+ **Automatische machine learning-gebruikersinterface**

  Leer hoe u [geautomatiseerde ML-experimenten maakt](tutorial-first-experiment-automated-ml.md) in de gebruiksvriendelijke interface. 

  [![Navigatiedeelvenster azure Machine Learning-studio](./media/overview-what-is-azure-ml/azure-machine-learning-automated-ml-ui.jpg)](./media/overview-what-is-azure-ml/azure-machine-learning-automated-ml-ui.jpg)

## <a name="mlops-deploy--lifecycle-management"></a>MLOps: implementeer & lifecycle management
Wanneer u het juiste model hebt, u het eenvoudig gebruiken in een webservice, op een IoT-apparaat of bij Power BI. Zie het artikel [Hoe implementeren en waar](how-to-deploy-and-where.md) voor meer informatie.

Vervolgens u uw geïmplementeerde modellen beheren met behulp van de [Azure Machine Learning SDK voor Python,](https://aka.ms/aml-sdk)Azure Machine [Learning-studio](https://ml.azure.com)of de [CLI voor machine learning.](reference-azure-machine-learning-cli.md)

Deze modellen kunnen worden verbruikt en retourvoorspellingen in [real-time](how-to-consume-web-service.md) of [asynchroon](how-to-use-parallel-run-step.md) op grote hoeveelheden gegevens.

En met geavanceerde [machine learning-pijplijnen](concept-ml-pipelines.md)u samenwerken bij elke stap, van gegevensvoorbereiding, modeltraining en -evaluatie, tot implementatie. Met pijplijnen u:

* Automatiseer het end-to-end machine learning-proces in de cloud
* Onderdelen opnieuw gebruiken en alleen stappen opnieuw uitvoeren wanneer dat nodig is
* Verschillende rekenbronnen gebruiken in elke stap
* Batchscoringtaken uitvoeren

Als u scripts wilt gebruiken om uw machine learning-workflow te automatiseren, biedt de [machine learning CLI](reference-azure-machine-learning-cli.md) opdrachtregelgereedschappen die veelvoorkomende taken uitvoeren, zoals het indienen van een trainingsrun of het implementeren van een model.

Zie [Volgende stappen](#next-steps)om aan de slag te gaan met Azure Machine Learning.

## <a name="integration-with-other-services"></a>Integratie met andere diensten

Azure Machine Learning werkt met andere services op het Azure-platform en integreert ook met open source-tools zoals Git en MLFlow.

+ Compute-doelen zoals __Azure Kubernetes Service,__ __Azure Container Instances,__ __Azure Databricks,__ __Azure Data Lake Analytics__en __Azure HDInsight__. Zie [Wat zijn rekendoelen?](concept-compute-target.md)
+ __Azure-gebeurtenisraster__. Zie [Azure Machine Learning-gebeurtenissen consumeren](concept-event-grid-integration.md)voor meer informatie .
+ __Azure-monitor__. Zie [Azure Machine Learning controleren](monitor-azure-machine-learning.md)voor meer informatie.
+ Gegevensopslag zoals __Azure Storage-accounts,__ __Azure Data Lake Storage,__ __Azure SQL Database,__ __Azure Database voor PostgreSQL__en __Azure Open Datasets__. Zie [Access-gegevens in Azure-opslagservices](how-to-access-data.md) en [Gegevenssets maken met Azure Open Datasets](how-to-create-register-datasets.md#create-datasets-with-azure-open-datasets)voor meer informatie.
+ __Azure Virtual Networks__. Zie [Veilig experimenteren en gevolgtrekking in een virtueel netwerk](how-to-enable-virtual-network.md)voor meer informatie.
+ __Azure-pijplijnen__. Zie [Machine Learning-modellen trainen en implementeren](/azure/devops/pipelines/targets/azure-machine-learning)voor meer informatie.
+ __Git repository logs__. Zie [Git integration](concept-train-model-git-integration.md)voor meer informatie.
+ __MLFlow__. Zie [MLflow voor](how-to-use-mlflow.md) meer informatie om statistieken bij te houden en modellen te implementeren 
+ __Kubeflow__. Zie [end-to-end workflowpipelines maken voor](https://www.kubeflow.org/docs/azure/)meer informatie.

### <a name="secure-communications"></a>Veilige communicatie

Uw Azure Storage-account, rekendoelen en andere resources kunnen veilig worden gebruikt in een virtueel netwerk om modellen te trainen en gevolgtrekkingen uit te voeren. Zie [Veilig experimenteren en gevolgtrekking in een virtueel netwerk](how-to-enable-virtual-network.md)voor meer informatie.

## <a name="basic--enterprise-editions"></a><a name="sku"></a>Basic & Enterprise-edities

Azure Machine Learning biedt twee edities die zijn afgestemd op uw machine learning-behoeften:
+ Basic (algemeen beschikbaar)
+ Enterprise (voorbeeld)

Deze edities bepalen welke machine learning-tools beschikbaar zijn voor ontwikkelaars en gegevenswetenschappers vanuit hun werkruimte.   

Met basiswerkruimten u Azure Machine Learning blijven gebruiken en alleen betalen voor de Azure-resources die tijdens het machine learning-proces worden verbruikt. Werkruimten voor Enterprise Edition worden alleen in rekening gebracht voor hun Azure-verbruik terwijl de editie in een voorbeeld is. Meer informatie over wat er beschikbaar is in het overzicht van de Azure Machine [Learning-editie & prijspagina.](https://azure.microsoft.com/pricing/details/machine-learning/) 

U wijst de editie toe wanneer u een werkruimte maakt. En reeds bestaande werkruimten zijn voor u geconverteerd naar de Basic-editie. Basic edition bevat alle functies die al algemeen beschikbaar waren vanaf oktober 2019. Alle experimenten in de werkruimten die zijn gebouwd met enterprise-editiefuncties, blijven alleen voor u beschikbaar totdat u een upgrade uitvoert naar Enterprise. Meer informatie over het [upgraden van een Basiswerkruimte naar Enterprise-editie](how-to-manage-workspace.md#upgrade). 

Klanten zijn verantwoordelijk voor de kosten die gedurende deze periode zijn gemaakt voor compute en andere Azure-resources.

## <a name="next-steps"></a>Volgende stappen

- Maak uw eerste experiment met de methode van uw voorkeur:
  + [Python-notitieblokken gebruiken om ML-modellen te trainen & implementeren](tutorial-1st-experiment-sdk-setup.md)
  + [R Markdown gebruiken om ML-modellen & implementeren te trainen](tutorial-1st-r-experiment.md) 
  + [Gebruik geautomatiseerde machine learning om ML-modellen & implementeren te trainen](tutorial-first-experiment-automated-ml.md) 
  + [Gebruik de sleep-&-dropmogelijkheden van de ontwerper om & implementeren te trainen](tutorial-designer-automobile-price-train-score.md) 
  + [Gebruik de machine learning CLI om een model te trainen en te implementeren](tutorial-train-deploy-model-cli.md)

- Leer meer over [Machine Learning-pijplijnen](concept-ml-pipelines.md) om uw Machine Learning-scenario’s te bouwen, te optimaliseren en te beheren.

- Lees het uitgebreide [Azure Machine Learning-architectuur en conceptenartikel.](concept-azure-machine-learning-architecture.md)
