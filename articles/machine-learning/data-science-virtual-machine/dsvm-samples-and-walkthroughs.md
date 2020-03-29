---
title: Voorbeeldprogramma's & ML-walkthroughs
titleSuffix: Azure Data Science Virtual Machine
description: Leer via deze voorbeelden en walkthroughs hoe u veelvoorkomende taken en scenario's verwerken met de Data Science Virtual Machine.
keywords: hulpprogramma's voor datatechnologie, virtuele machine voor datatechnologie, hulpprogramma voor datatechnologie, linux-datatechnologie
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: d064e940997d13e51be4fd38e9f678b778ce31f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74900053"
---
# <a name="samples-on-azure-data-science-virtual-machines"></a>Voorbeelden op virtual machines van Azure Data Science

Azure Data Science Virtual Machines (DSVMs) bevatten een uitgebreide set voorbeeldcode. Deze voorbeelden omvatten Jupyter notebooks en scripts in talen als Python en R.
> [!NOTE]
> Zie de sectie [Access Jupyter](#access-jupyter) voor meer informatie over het uitvoeren van Jupyter-notitieblokken op uw virtuele machines voor data science.

## <a name="prerequisites"></a>Vereisten

Om deze monsters uit te voeren, moet u een Data Science Virtual Machine hebben ingericht. Bekijk de quickstarts voor [Windows](./provision-vm.md) en [Ubuntu.](./dsvm-ubuntu-intro.md)

## <a name="available-samples"></a>Beschikbare voorbeelden
| Categorie Monsters | Beschrijving | Locaties |
| ------------- | ------------- | ------------- |
| R-taal  | Voorbeelden illustreren scenario's zoals hoe u verbinding maken met cloudgegevensstores in Azure en hoe open-source R en Microsoft Machine Learning Server kunnen worden vergeleken. Ze leggen ook uit hoe je modellen operationaliseren op Microsoft Machine Learning Server en SQL Server. <br/> [R-taal](#r-language) | <br/>`~notebooks` <br/> <br/> `~samples/MicrosoftR` <br/> <br/> `~samples/RSqlDemo` <br/> <br/> `~samples/SQLRServices`<br/> <br/>|
| Python-taal  | Voorbeelden leggen scenario's uit zoals hoe je verbinding maken met azure-gebaseerde cloudgegevensstores en hoe je werken met Azure Machine Learning.  <br/> [Python-taal](#python-language) | <br/>`~notebooks` <br/><br/>|
| De taal van Julia  | Geeft een gedetailleerde beschrijving van plotten en deep learning in Julia. Legt ook uit hoe je C en Python van Julia bellen. <br/> [De taal van Julia](#julia-language) |<br/> Windows:<br/> `~notebooks/Julia_notebooks`<br/><br/> Linux:<br/> `~notebooks/julia`<br/><br/> |
| Azure Machine Learning  | Illustreert hoe je machine learning- en deep learning-modellen bouwen met Machine Learning. Implementeer modellen overal. Gebruik geautomatiseerde machine learning en intelligente hyperparameter tuning. Gebruik ook modelmanagement en gedistribueerde training. <br/> [Machine Learning](#azure-machine-learning) | <br/>`~notebooks/AzureML`<br/> <br/>|
| PyTorch-notitieblokken  | Deep-learning monsters die gebruik maken van PyTorch-gebaseerde neurale netwerken. Notebooks variëren van beginners tot geavanceerde scenario's.  <br/> [PyTorch-notitieblokken](#pytorch) | <br/>`~notebooks/Deep_learning_frameworks/pytorch`<br/> <br/>|
| TensorFlow  |  Een verscheidenheid aan neurale netwerkmonsters en -technieken die worden geïmplementeerd met behulp van het TensorFlow-framework. <br/> [TensorFlow (TensorFlow)](#tensorflow) | <br/>`~notebooks/Deep_learning_frameworks/tensorflow`<br/><br/> |
| Microsoft Cognitive Toolkit <br/>   | Deep-learning samples gepubliceerd door het Cognitive Toolkit team van Microsoft.  <br/> [Cognitive Toolkit](#cntk) | <br/> `~notebooks/DeepLearningTools/CNTK/Tutorials`<br/><br/> Linux:<br/> `~notebooks/CNTK`<br/> <br/>|
| Caffe2 | Deep-learning monsters die caffe2-gebaseerde neurale netwerken gebruiken. Verschillende notebooks vertrouwd maken gebruikers met Caffe2 en hoe het effectief te gebruiken. Voorbeelden hiervan zijn het maken van voorbewerking van afbeeldingen en het maken van gegevenssets. Ze omvatten ook regressie en hoe voorgetrainde modellen te gebruiken. <br/> [Caffe2](#caffe2) | <br/>`~notebooks/Deep_learning_frameworks/caffe2`<br/><br/> |
| H2O   | Python-gebaseerde monsters die H2O gebruiken voor echte probleemscenario's. <br/> [H2O](#h2o) | <br/>`~notebooks/h2o`<br/><br/> |
| SparkML-taal  | Voorbeelden die functies van de Apache Spark MLLib toolkit gebruiken via pySpark en MMLSpark: Microsoft Machine Learning voor Apache Spark op Apache Spark 2.x.  <br/> [SparkML-taal](#sparkml) | <br/>`~notebooks/SparkML/pySpark`<br/>`~notebooks/MMLSpark`<br/><br/>  |
| XGBoost | Standaard machine learning-voorbeelden in XGBoost voor scenario's zoals classificatie en regressie. <br/> [XGBoost (XGBoost)](#xgboost) | <br/>Windows:<br/>`\dsvm\samples\xgboost\demo`<br/><br/> |

<br/>

## <a name="access-jupyter"></a>Toegang jupyter 

Als u toegang wilt krijgen tot Jupyter, selecteert u het pictogram **Jupyter** in het bureaublad of het toepassingsmenu. Je hebt ook toegang tot Jupyter op een Linux-editie van een DSVM. Ga naar Ubuntu om op `https://<Full Domain Name or IP Address of the DSVM>:8000` afstand toegang te krijgen vanuit een webbrowser.

Als u uitzonderingen wilt toevoegen en Jupyter-toegang beschikbaar wilt maken via een browser, gebruikt u de volgende richtlijnen:


![Uitzondering op Jupyter inschakelen](./media/ubuntu-jupyter-exception.png)


Meld u aan met hetzelfde wachtwoord dat u gebruikt om u aan te melden bij de Data Science Virtual Machine.
<br/>

**Jupyter thuis**
<br/>![Jupyter thuis](./media/jupyter-home.png)<br/>

## <a name="r-language"></a>R-taal 
<br/>![R-monsters](./media/r-language-samples.png)<br/>

## <a name="python-language"></a>Python-taal
<br/>![Python-voorbeelden](./media/python-language-samples.png)<br/>

## <a name="julia-language"></a>De taal van Julia 
<br/>![Julia monsters](./media/julia-samples.png)<br/>

## <a name="azure-machine-learning"></a>Azure Machine Learning 
<br/>![Azure Machine Learning-voorbeelden](./media/azureml-samples.png)<br/>

## <a name="pytorch"></a>PyTorch
<br/>![PyTorch monsters](./media/pytorch-samples.png)<br/>

## <a name="tensorflow"></a>TensorFlow 
<br/>![TensorFlow-monsters](./media/tensorflow-samples.png)<br/>


## <a name="cntk"></a>CNTK 
<br/>![CNTK-monsters](./media/cntk-samples.png)<br/>


## <a name="caffe2"></a>Caffe2 
<br/>![caffe2 monsters](./media/caffe2-samples.png)<br/>

## <a name="h2o"></a>H2O 
<br/>![H2O-monsters](./media/h2o-samples.png)<br/>

## <a name="sparkml"></a>SparkML (SparkML) 
<br/>![SparkML-monsters](./media/sparkml-samples.png)<br/>

## <a name="xgboost"></a>XGBoost 
<br/>![XGBoost-monsters](./media/xgboost-samples.png)<br/>

