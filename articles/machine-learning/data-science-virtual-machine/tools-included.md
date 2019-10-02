---
title: Hulpprogram ma's die zijn opgenomen in de DSVM
titleSuffix: Azure Data Science Virtual Machine tools
description: Een lijst met hulpprogram ma's die zijn opgenomen in de Windows-en Ubuntu DSVM-installatie kopieën
keywords: hulpprogramma's voor datatechnologie, virtuele machine voor datatechnologie, hulpprogramma voor datatechnologie, linux-datatechnologie
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: overview
ms.date: 09/27/2019
ms.openlocfilehash: 79de3406e47b84a6120496acce5bba948fbb1a6d
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803941"
---
# <a name="what-tools-are-included-on-the-azure-data-science-virtual-machine"></a>Welke hulpprogram ma's zijn opgenomen in de Azure Data Science Virtual Machine?

Hieronder vindt u een bijgewerkte lijst met hulpprogram ma's die zijn opgenomen op de Data Science Virtual Machine, samen met koppelingen om te begrijpen hoe elk hulp programma is geconfigureerd.


| **Hulpprogramma**                                                           | **Windows DSVM** | **Linux DSVM** | **Gebruiks notities** |
| :------------------------------------------------------------------ |:-------------------:|:------------------:|:------:|
| [Microsoft R Open](https://mran.microsoft.com/open/) met populaire pakketten die vooraf zijn geïnstalleerd   |<span class='green-check'>&#9989;</span>                     |<span class='green-check'>&#9989;</span>  | [R gebruiken op de DSVM](./dsvm-languages.md#r)           |
| [Microsoft machine learning server (R, python)](https://docs.microsoft.com/machine-learning-server/) Developer Edition bevat: <br />  &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [RevoScaleR/revoscalepy](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-revoscaler) parallel en gedistribueerd Framework met hoge prestaties (R en python)<br />  &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [MicrosoftML](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-the-microsoftml-package), nieuwe geavanceerde machine learning algoritmen van micro soft <br />  &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [R en python uitoefening](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)                                            |<span class='green-check'>&#9989;</span>                       |<span class='green-check'>&#9989;</span> | |
| [Microsoft Office](https://products.office.com/business/office-365-proplus-business-software) ProPlus met gedeelde activering: Excel, Word en Power Point   |<span class='green-check'>&#9989;</span>                       |<span class='red-x'>&#10060;</span>              | |
| [Anaconda Python](https://www.continuum.io/) 2,7 en 3,5 met populaire pakketten die vooraf zijn geïnstalleerd    |<span class='green-check'>&#9989;</span>                       |<span class='green-check'>&#9989;</span>               | |
| [JuliaPro](https://juliacomputing.com/products/juliapro.html) met populaire pakketten voor Julia-taal vooraf geïnstalleerd                         |<span class='green-check'>&#9989;</span>                       |<span class='green-check'>&#9989;</span>               | [Julia gebruiken op de DSVM](./dsvm-languages.md#julia) |
| Relationele databases                                                            | [SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition| [PostgreSQL](https://www.postgresql.org/) (CentOS),<br/>[SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition (Ubuntu) | (./dsvm-data-platforms # SQL-Server-2016-Developer-edition.md) |
| Databasehulpprogramma's                                                       |  SQL Server Management Studio <br/> SQL Server Integration Services<br/> [BCP, Sqlcmd](https://docs.microsoft.com/sql/tools/command-prompt-utility-reference-database-engine)<br />  ODBC/JDBC-Stuur Programma's|  [Squirrel SQL](http://squirrel-sql.sourceforge.net/) (hulp programma voor query's), <br />  BCP, Sqlcmd <br />  ODBC/JDBC-Stuur Programma's| |
| Schaal bare data base Analytics met SQL Server machine learning Services (R, python) |<span class='green-check'>&#9989;</span>    | <span class='red-x'>&#10060;</span>                | |
| [Jupyter notebook server](https://jupyter.org/) met de volgende kernels:                                  |<span class='green-check'>&#9989;</span>     |<span class='green-check'>&#9989;</span> | [Jupyter Notebook-voor beelden](./dsvm-samples-and-walkthroughs.md) | 
|     &nbsp; @ NO__T-1 @ NO__T-2 @ NO__T-3 R |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [R Jupyter-voor beelden](./dsvm-samples-and-walkthroughs.md#r-language) |
|     &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 python |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Python Jupyter-voor beelden](./dsvm-samples-and-walkthroughs.md#python-language) |
|     &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 Julia |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Julia Jupyter-voor beelden](./dsvm-samples-and-walkthroughs.md#julia-language) |
|     &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 PySpark |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [pySpark Jupyter-voor beelden](./dsvm-samples-and-walkthroughs.md#sparkml)
|     &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Sparkmagic](https://github.com/jupyter-incubator/sparkmagic) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span>(Alleen Ubuntu) | |
|     &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 Spark     |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| JupyterHub (notebook server voor meerdere gebruikers)|<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| Jjupyterlab (notebook server voor meerdere gebruikers) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span>(Alleen Ubuntu) | |
| Ontwikkel hulpprogramma's, Ide's en code-editors:| | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Visual Studio 2019 (Community Edition)](https://www.visualstudio.com/community/) met git-invoeg toepassing, Azure HDInsight (Hadoop), Azure Data Lake, SQL Server Data tools, [node. js](https://github.com/Microsoft/nodejstools), [python](https://aka.ms/ptvs)en [R-hulpprogram ma's voor Visual Studio (RTVS)](https://microsoft.github.io/RTVS-docs/) |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | [Visual Studio 2019 op de DSVM](./dsvm-tools-development.md#visual-studio-2019) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Visual Studio code](https://code.visualstudio.com/) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Visual Studio code op de DSVM](./dsvm-tools-development.md#visual-studio-code) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [RStudio Desktop](https://www.rstudio.com/products/rstudio/#Desktop) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [RStudio bureau blad op de DSVM](./dsvm-tools-development.md#rstudio--desktop) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [RStudio-server](https://www.rstudio.com/products/rstudio/#Server) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | [RStudio-server op de DSVM](./dsvm-tools-development.md#rstudio--server)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [PyCharm Community Edition](https://www.jetbrains.com/pycharm/) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | [PyCharm op de DSVM](./dsvm-tools-development.md#pycharm)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Atom](https://atom.io/) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Juno (Julia IDE)](https://junolab.org/)|<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Juno op de DSVM](./dsvm-tools-development.md#juno)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 vim en emacs |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 Git en git bash |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 OpenJDK |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 .NET Framework |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | |
| Power BI Desktop |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   |
| SDK's voor toegang tot Azure en services van de Cortana Intelligence Suite |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Power BI bureau blad op de DSVM](./dsvm-tools-development.md#power-bi-desktop) |
| Hulpprogram ma's voor gegevens verplaatsing en-beheer: | | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 Azure Storage Explorer |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Azure cli](https://docs.microsoft.com/cli/azure) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 Azure PowerShell |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Azcopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy) |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | [Azcopy op de DSVM](./dsvm-tools-ingestion.md#azcopy)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [BLOB-zeker stuur programma](https://github.com/Azure/azure-storage-fuse) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | [blobfuse op de DSVM](./dsvm-tools-ingestion.md#blobfuse)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Adlcopy (Azure data Lake Storage)](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob) |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | [Adlcopy op de DSVM](./dsvm-tools-ingestion.md#adlcopy)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Azure Cosmos DB hulp programma voor gegevens migratie](https://docs.microsoft.com/azure/documentdb/documentdb-import-data) |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | [Cosmos DB op de DSVM](./dsvm-tools-ingestion.md#azure-cosmos-db-data-migration-tool) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [micro soft data management gateway](https://msdn.microsoft.com/library/dn879362.aspx): gegevens verplaatsen tussen on-premises en de Cloud |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | [DSVM gebruik](./dsvm-tools-ingestion.md#microsoft-data-management-gateway) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 Unix/Linux-opdracht regel Programma's |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
| [Apache-analyse](https://drill.apache.org) voor het verkennen van gegevens |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Apache-analyse op de DSVM](./dsvm-tools-explore-and-visualize.md#apache-drill)
| Machine learning-hulpprogram ma's: ||||
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3-integratie met [Azure machine learning](https://azure.microsoft.com/services/machine-learning/) (R, python) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Azure ML SDK](./dsvm-ml-data-science-tools.md#azure-machine-learning-sdk-for-python)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [XGBoost](https://github.com/dmlc/xgboost) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [XGBoost op de DSVM](./dsvm-ml-data-science-tools.md#xgboost)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Vowpal Wabbit op de DSVM](./dsvm-ml-data-science-tools.md#vowpal-wabbit)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Fridge](https://www.cs.waikato.ac.nz/ml/weka/) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Rattle](https://togaware.com/rattle/) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Fridge op de DSVM](./dsvm-ml-data-science-tools.md#weka) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [LightGBM](https://github.com/Microsoft/LightGBM) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span>(Alleen Ubuntu) | [LightGBM op de DSVM](./dsvm-ml-data-science-tools.md#lightgbm)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [CatBoost](https://tech.yandex.com/catboost/) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span>(Alleen Ubuntu) | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [H2O](https://www.h2o.ai/h2o/), [mousserend water](https://www.h2o.ai/sparkling-water/) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span>(Alleen Ubuntu) | [H20 op de DSVM](./dsvm-ml-data-science-tools.md#h2o) |
| Uitgebreide leer hulpmiddelen die op een GPU of CPU werken: |  |  | Klik op de naam van het onderstaande hulp programma voor meer informatie over gebruik |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Microsoft Cognitive Toolkit (CNTK)](./dsvm-deep-learning-ai-frameworks.md#microsoft-cognitive-toolkit-cntk) (Windows 2016) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [tensor flow](./dsvm-deep-learning-ai-frameworks.md#tensorflow) |<span class='green-check'>&#9989;</span>(Windows 2016) |<span class='green-check'>&#9989;</span> | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Horovod](./dsvm-deep-learning-ai-frameworks.md#horovod) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span>Ubuntu | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Caffe en Caffe2](./dsvm-deep-learning-ai-frameworks.md#caffe2) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3- [ketting](./dsvm-deep-learning-ai-frameworks.md#chainer) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Keras](./dsvm-deep-learning-ai-frameworks.md#keras)|<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [PyTorch](./dsvm-deep-learning-ai-frameworks.md#pytorch)|<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [tensor flow](./dsvm-deep-learning-ai-frameworks.md#tensorflow-serving) voor |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [CUDA, cuDNN, nvidia-stuur programma](./dsvm-deep-learning-ai-frameworks.md#cuda-cudnn-nvidia-driver) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
