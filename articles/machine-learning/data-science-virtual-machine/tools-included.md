---
title: Hulpprogramma's in de DSVM
titleSuffix: Azure Data Science Virtual Machine tools
description: Een lijst met hulpprogram ma's die zijn opgenomen in de Windows-en Ubuntu DSVM-installatie kopieën
keywords: hulpprogramma's voor datatechnologie, virtuele machine voor datatechnologie, hulpprogramma voor datatechnologie, linux-datatechnologie
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: overview
ms.date: 10/10/2019
ms.openlocfilehash: 6c5eecb762aceb6f674e0b8084ed98c6b5500f98
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331716"
---
# <a name="what-tools-are-included-on-the-azure-data-science-virtual-machine"></a>Welke hulpprogram ma's zijn opgenomen in de Azure Data Science Virtual Machine?

Hieronder vindt u een bijgewerkte lijst met hulpprogram ma's die zijn opgenomen op de Data Science Virtual Machine, samen met koppelingen om te begrijpen hoe elk hulp programma is geconfigureerd.


| **Hulpprogramma**                                                           | **Windows DSVM** | **Linux DSVM** | **Gebruiks notities** |
| :------------------------------------------------------------------ |:-------------------:|:------------------:|:------:|
| [Microsoft R Open](https://mran.microsoft.com/open/) met populaire pakketten die vooraf zijn geïnstalleerd   |<span class='green-check'>&#9989;</span></br> (3.4.3)                    |<span class='green-check'>&#9989;</span></br> (3.4.3)  | [R op de DSVM](./dsvm-languages.md#r)           |
| [Microsoft machine learning server (R, python)](https://docs.microsoft.com/machine-learning-server/) Developer Edition bevat: <br />  &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [RevoScaleR/revoscalepy](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-revoscaler) parallel en gedistribueerd Framework met hoge prestaties (R en python)<br />  &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [MicrosoftML](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-the-microsoftml-package), nieuwe geavanceerde machine learning algoritmen van micro soft <br />  &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [R en python uitoefening](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)                                            |<span class='green-check'>&#9989;</span>  </br> (9.3.0)                 |<span class='green-check'>&#9989;</span> | |
| [Microsoft Office](https://products.office.com/business/office-365-proplus-business-software) ProPlus met gedeelde activering: Excel, Word en Power Point   |<span class='green-check'>&#9989;</span>                       |<span class='red-x'>&#10060;</span>              | |
| [Anaconda Python](https://www.continuum.io/) 2,7 en 3,5 met populaire pakketten die vooraf zijn geïnstalleerd    |<span class='green-check'>&#9989;</span>                       |<span class='green-check'>&#9989;</span></br> (4,2)        | |
| [JuliaPro](https://juliacomputing.com/products/juliapro.html) met populaire pakketten voor Julia-taal vooraf geïnstalleerd                         |<span class='green-check'>&#9989;</span> </br> (0.6.4)                   |<span class='green-check'>&#9989;</span></br> (0.6.2)              | [Julia op de DSVM](./dsvm-languages.md#julia) |
| Relationele databases                                                            | [SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition| [PostgreSQL](https://www.postgresql.org/) (CentOS),<br/>[SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition (Ubuntu) | [SQL Server op de DSVM](./dsvm-data-platforms.md#sql-server-2017-developer-edition) |
| Databasehulpprogramma's                                                       |  SQL Server Management Studio <br/> SQL Server Integration Services<br/> [BCP, Sqlcmd](https://docs.microsoft.com/sql/tools/command-prompt-utility-reference-database-engine)<br />  ODBC/JDBC-Stuur Programma's|  [Squirrel SQL](http://squirrel-sql.sourceforge.net/) (hulp programma voor query's), <br />  BCP, Sqlcmd <br />  ODBC/JDBC-Stuur Programma's| |
| Schaal bare data base Analytics met SQL Server machine learning Services (R, python) |<span class='green-check'>&#9989;</span>    | <span class='red-x'>&#10060;</span>                | |
| [Jupyter notebook server](https://jupyter.org/) met de volgende kernels:                                  |<span class='green-check'>&#9989;</span></br> 5\.5.0   |<span class='green-check'>&#9989;</span> | [Jupyter Notebook-voor beelden](./dsvm-samples-and-walkthroughs.md) | 
|     &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 R |<span class='green-check'>&#9989;</span></br> (3.4.3) |<span class='green-check'>&#9989;</span></br> (3.4.3) | [R Jupyter-voor beelden](./dsvm-samples-and-walkthroughs.md#r-language) |
|     &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 python |<span class='green-check'>&#9989;</span></br> 3D |<span class='green-check'>&#9989;</span> | [Python Jupyter-voor beelden](./dsvm-samples-and-walkthroughs.md#python-language) |
|     &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 Julia |<span class='green-check'>&#9989;</span></br> (0.6.4) |<span class='green-check'>&#9989;</span></br> (0.6.2) | [Julia Jupyter-voor beelden](./dsvm-samples-and-walkthroughs.md#julia-language) |
|     &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 PySpark |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [pySpark Jupyter-voor beelden](./dsvm-samples-and-walkthroughs.md#sparkml)
|     &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Sparkmagic](https://github.com/jupyter-incubator/sparkmagic) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (Alleen Ubuntu) | |
|     &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 Spark     |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| JupyterHub (notebook server voor meerdere gebruikers)|<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| Jjupyterlab (notebook server voor meerdere gebruikers) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> | |
| Ontwikkel hulpprogramma's, Ide's en code-editors:| | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Visual Studio 2017 (Community Edition)](https://www.visualstudio.com/community/) met git-invoeg toepassing, Azure HDInsight (Hadoop), Azure Data Lake, SQL Server Data tools, [node. js](https://github.com/Microsoft/nodejstools), [python](https://aka.ms/ptvs)en [R-hulpprogram ma's voor Visual Studio (RTVS)](https://microsoft.github.io/RTVS-docs/) |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | [Visual Studio op de DSVM](./dsvm-tools-development.md#visual-studio-community-2017) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Visual Studio code](https://code.visualstudio.com/) |<span class='green-check'>&#9989;</span></br> (1.31.1) |<span class='green-check'>&#9989;</span></br> (1,31) | [Visual Studio code op de DSVM](./dsvm-tools-development.md#visual-studio-code) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [RStudio Desktop](https://www.rstudio.com/products/rstudio/#Desktop) |<span class='green-check'>&#9989;</span></br> (1.2.50 xx) |<span class='green-check'>&#9989;</span></br> (1.1.456) | [RStudio bureau blad op de DSVM](./dsvm-tools-development.md#rstudio--desktop) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [RStudio-server](https://www.rstudio.com/products/rstudio/#Server) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | [RStudio-server op de DSVM](./dsvm-tools-development.md#rstudio--server)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [PyCharm Community Edition](https://www.jetbrains.com/pycharm/) |<span class='green-check'>&#9989;</span></br> (19.2.3)  |<span class='green-check'>&#9989;</span></br> (2018.2.3) | [PyCharm op de DSVM](./dsvm-tools-development.md#pycharm)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Atom](https://atom.io/) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (1.26.1) | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Juno (Julia IDE)](https://junolab.org/)|<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span> | [Juno op de DSVM](./dsvm-tools-development.md#juno)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [vim](https://www.vim.org) |<span class='green-check'>&#9989;</span></br> (8.1.5) |<span class='green-check'>&#9989;</span></br> (7.4.1689) | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [emacs](https://www.gnu.org/software/emacs) | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span></br> (24.5.1) | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Git](https://git-scm.com/) en git bash |<span class='green-check'>&#9989;</span></br> (2.20.1) |<span class='green-check'>&#9989;</span></br> (0.6.2) | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [openjdk](https://openjdk.java.net) |<span class='green-check'>&#9989;</span></br> (1.8.0_201) |<span class='green-check'>&#9989;</span></br> (1.8.0_222) | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 .NET Framework |<span class='green-check'>&#9989;</span></br> (4.7.2) |<span class='red-x'>&#10060;</span>   | |
| [Power BI Desktop](https://powerbi.microsoft.com/en-us/) |<span class='green-check'>&#9989;</span></br> (2.73.55 xx) |<span class='red-x'>&#10060;</span>   |
| SDK's voor toegang tot Azure en services van de Cortana Intelligence Suite |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Power BI bureau blad op de DSVM](./dsvm-tools-development.md#power-bi-desktop) |
| Hulpprogram ma's voor gegevens verplaatsing en-beheer: | | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Azure Storage Explorer](https://azure.microsoft.com/en-us/features/storage-explorer/) |<span class='green-check'>&#9989;</span></br> (1.10.1) |<span class='green-check'>&#9989;</span></br> (0.7.20160129.1) | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Azure cli](https://docs.microsoft.com/cli/azure) |<span class='green-check'>&#9989;</span></br> (2.0.56) |<span class='green-check'>&#9989;</span></br> (2.0.58) | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Azcopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy) |<span class='green-check'>&#9989;</span></br> (8.1.0) |<span class='red-x'>&#10060;</span>   | [Azcopy op de DSVM](./dsvm-tools-ingestion.md#azcopy)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [BLOB-zeker stuur programma](https://github.com/Azure/azure-storage-fuse) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> 1\.0.2 | [blobfuse op de DSVM](./dsvm-tools-ingestion.md#blobfuse)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Adlcopy (Azure data Lake Storage)](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob) |<span class='green-check'>&#9989;</span></br> (1.0.13) |<span class='red-x'>&#10060;</span>   | [Adlcopy op de DSVM](./dsvm-tools-ingestion.md#adlcopy)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Azure Cosmos DB hulp programma voor gegevens migratie](https://docs.microsoft.com/azure/documentdb/documentdb-import-data) |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | [Cosmos DB op de DSVM](./dsvm-tools-ingestion.md#azure-cosmos-db-data-migration-tool) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 Unix/Linux-opdracht regel Programma's |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
| [Apache-analyse](https://drill.apache.org) voor het verkennen van gegevens |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span></br>(1.14.0) | [Apache-analyse op de DSVM](./dsvm-tools-explore-and-visualize.md#apache-drill)
| Machine learning-hulpprogram ma's: ||||
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3-integratie met [Azure machine learning](https://azure.microsoft.com/services/machine-learning/) (R, python) |<span class='green-check'>&#9989;</span></br> (0.2.7) |<span class='green-check'>&#9989;</span></br> (1.0.45) | [Azure ML SDK](./dsvm-ml-data-science-tools.md#azure-machine-learning-sdk-for-python)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [XGBoost](https://github.com/dmlc/xgboost) |<span class='green-check'>&#9989;</span></br> (0,81) |<span class='green-check'>&#9989;</span></br> (0,80) | [XGBoost op de DSVM](./dsvm-ml-data-science-tools.md#xgboost)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span></br> (8,1) | [Vowpal Wabbit op de DSVM](./dsvm-ml-data-science-tools.md#vowpal-wabbit)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Fridge](https://www.cs.waikato.ac.nz/ml/weka/) |<span class='green-check'>&#9989;</span></br> (3,8) |<span class='green-check'>&#9989;</span></br> (3.8.0) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Rattle](https://togaware.com/rattle/) |<span class='green-check'>&#9989;</span></br> (5.1.0) |<span class='green-check'>&#9989;</span></br> 5\.2.2 | [Fridge op de DSVM](./dsvm-ml-data-science-tools.md#weka) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [LightGBM](https://github.com/Microsoft/LightGBM) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> omschreven | [LightGBM op de DSVM](./dsvm-ml-data-science-tools.md#lightgbm)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [CatBoost](https://tech.yandex.com/catboost/) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (0.10.3) | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [H2O](https://www.h2o.ai/h2o/), [mousserend water](https://www.h2o.ai/sparkling-water/) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (3.20.0.7) | [H20 op de DSVM](./dsvm-ml-data-science-tools.md#h2o) |
| Uitgebreide leer hulpmiddelen die op een GPU of CPU werken: |  |  |  |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Caffe](https://github.com/BVLC/caffe) en [Caffe2](https://github.com/facebookarchive/caffe2) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | [Caffe](./dsvm-deep-learning-ai-frameworks.md#caffe) en [CAFFE2](./dsvm-deep-learning-ai-frameworks.md#caffe2) op de DSVM |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3- [ketting](https://chainer.org) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (5.2.0) | [Chainer op de DSVM](./dsvm-deep-learning-ai-frameworks.md#chainer) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [CUDA, cuDNN, nvidia-stuur programma](https://developer.nvidia.com/cuda-toolkit) |<span class='green-check'>&#9989;</span></br> (10.0.130) |<span class='green-check'>&#9989;</span></br> (10.0.130) | [CUDA, cuDNN, NVIDIA-stuur programma op de DSVM](./dsvm-deep-learning-ai-frameworks.md#cuda-cudnn-nvidia-driver) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Horovod](https://github.com/horovod/horovod) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (0.16.1) | [Horovod op de DSVM](./dsvm-deep-learning-ai-frameworks.md#horovod) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Keras](https://keras.io) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> 2\.2.4 | [Keras op de DSVM](./dsvm-deep-learning-ai-frameworks.md#keras) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/en-us/cognitive-toolkit/) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span></br> 2\.5.1 | [CNTK op de DSVM](./dsvm-deep-learning-ai-frameworks.md#microsoft-cognitive-toolkit-cntk) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [MXNet](https://mxnet.apache.org/) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span></br> (1.3.0) | [MXNet op de DSVM](./dsvm-deep-learning-ai-frameworks.md#mxnet) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [MXNet-model server](https://github.com/awslabs/mxnet-model-server#quick-start) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span></br> 1\.0.1 | [MXNet-model server op de DSVM](./dsvm-deep-learning-ai-frameworks.md#mxnet-model-server) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [NVIDIA-Systeembeheer interface (NVIDIA-SMI)](https://developer.nvidia.com/nvidia-system-management-interface) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span></br>| [NVIDIA-SMI op het DSVM](./dsvm-deep-learning-ai-frameworks.md#nvidia-system-management-interface-nvidia-smi) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [PyTorch](https://pytorch.org) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> 1\.2.0 | [PyTorch op de DSVM](./dsvm-deep-learning-ai-frameworks.md#pytorch) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [tensor flow](https://www.tensorflow.org) |<span class='green-check'>&#9989;</span></br> (1,13) |<span class='green-check'>&#9989;</span></br> (1,13) | [Tensor flow op de DSVM](./dsvm-deep-learning-ai-frameworks.md#tensorflow) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [tensor flow](https://www.tensorflow.org/tfx/guide/serving) voor |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (1.12.0) | [Tensor flow voor de DSVM](./dsvm-deep-learning-ai-frameworks.md#tensorflow-serving) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Theano](https://github.com/Theano/Theano) | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span></br> (1.0.3) | [Theano op de DSVM](./dsvm-deep-learning-ai-frameworks.md#theano)

