---
title: Inleiding tot Azure Data Science Virtual Machine voor Linux en Windows | Microsoft Docs
description: Belangrijke analysescenario's en componenten voor Windows en Linux Data Science Virtual Machines.
keywords: hulpprogramma's voor datatechnologie, virtuele machine voor datatechnologie, hulpprogramma voor datatechnologie, linux-datatechnologie
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.assetid: d4f91270-dbd2-4290-ab2b-b7bfad0b2703
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 02/22/2019
ms.author: vijetaj
ms.openlocfilehash: 2814ad51d2f0f868833cf9c6964b7ea4a8424435
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69574925"
---
# <a name="what-is-azure-data-science-virtual-machine-for-linux-and-windows"></a>Wat is Azure Data Science Virtual Machine voor Linux en Windows?

Data Science Virtual Machine (DSVM) is een aangepaste VM-installatiekopie in de Azure-cloud van Microsoft, die speciaal is gebouwd voor datatechnologie. DSVM bevat veel populaire gegevenswetenschap- en andere hulpprogramma's die vooraf zijn geïnstalleerd en geconfigureerd voor het snel ontwikkelen van intelligente toepassingen voor geavanceerde analyses. 

De configuraties van het hulpprogramma zijn uitvoerig getest door data scientists en ontwikkelaars bij Microsoft en door de bredere data science-community om te controleren of de stabiliteit en algemene gemaakt.

De DSVM is beschikbaar op:
+ Windows Server 2016, Windows Server 2012
+ Ubuntu 16,04 LTS en CentOS 7,4

Alle hulpprogram ma's voor het uitdiepen van **de VM** zijn uitgebreid naar Data Science VM. 


## <a name="what-can-i-do-with-dsvm"></a>Wat kan ik doen met DSVM?
Het doel van de Data Science Virtual Machine (DSVM) is om dataprofessionals van allerlei vaardigheidsniveaus en in allerlei branches te voorzien van een probleemloze, vooraf geconfigureerde en volledig geïntegreerde datatechnologieomgeving. In plaats van zelf een vergelijkbare werkruimte uit te rollen, kunt u een DSVM inrichten. Zo bespaart u dagen of zelfs _weken_ aan installatie-, configuratie- en pakketbeheerprocessen. Nadat uw DSVM is toegewezen, kunt u meteen aan uw datatechnologieproject gaan werken.

De Data Science VM is ontworpen en geconfigureerd om met een breed scala aan gebruiksscenario's te kunnen werken. U kunt uw omgeving omhoog of omlaag schalen naarmate de project vereisten veranderen. U kunt ook uw voorkeurs taal gebruiken voor het Program meren van data Science-taken en andere hulpprogram ma's installeren om het systeem aan te passen aan uw exacte behoeften.

### <a name="preconfigured-analytics-desktop-in-the-cloud"></a>Vooraf geconfigureerd analysebureaublad in de cloud
De Data Science VM biedt een basisconfiguratie voor datatechnologieteams die hun lokale bureaublad willen vervangen door een beheerd bureaublad in de cloud. Deze basisconfiguratie zorgt ervoor dat alle datawetenschappers in een team een consistente installatie hebben, zodat ze experimenten kunnen verifiëren en de samenwerking kunnen bevorderen. De kosten worden ook verlaagd door de overhead van sysadmin te verminderen. Deze last reductie bespaart op tijd die nodig is om de diverse software pakketten te evalueren, te installeren en te onderhouden die nodig zijn om geavanceerde analyses uit te voeren.

### <a name="data-science-training-and-education"></a>Training en onderwijs voor datatechnologie
Enter prise trainers en docenten die data Science branches leren, bieden doorgaans een installatie kopie van een virtuele machine. Ze bieden de installatie kopie om ervoor te zorgen dat hun studenten een consistente installatie hebben en dat de voor beelden werken zoals verwacht. De Data Science VM maakt een omgeving op aanvraag met een consistente configuratie, waardoor zich minder problemen met ondersteuning en incompatibiliteit voordoen. In gevallen waarin deze omgevingen vaak moeten worden gemaakt, met name voor kortere trainingen, levert dit veel voordelen op.

### <a name="on-demand-elastic-capacity-for-large-scale-projects"></a>Flexibele capaciteit op aanvraag voor grootschalige projecten
Datatechnologie-hackathons/-wedstrijden of grootschalige gegevensmodellen en grootschalig dataonderzoek vereisen uitgebreide hardwarecapaciteit, doorgaans voor een korte duur. De Data Science VM kan helpen om de data Science-omgeving snel op aanvraag te repliceren, op geschaalde servers die experimenten toestaan dat IT-bronnen met een hoge voeding worden uitgevoerd.

### <a name="custom-compute-power-for-azure-notebooks"></a>Aangepaste reken kracht voor Azure Notebooks
[Azure notebooks](../../notebooks/azure-notebooks-overview.md) is een gratis gehoste service voor het ontwikkelen, uitvoeren en delen van Jupyter-notebooks in de Cloud zonder installatie. De gratis servicelaag is echter beperkt tot 4 GB geheugen en 1 GB aan gegevens. Als u alle limieten wilt vrijgeven, kunt u een notitieblok project koppelen aan een Data Science VM of een andere virtuele machine waarop Jupyter server wordt uitgevoerd. Als u zich aanmeldt bij Azure Notebooks met een account met behulp van Azure Active Directory (zoals een bedrijfs account), worden in notitie blokken automatisch virtuele machines met gegevens wetenschap weer gegeven in de abonnementen die aan dat account zijn gekoppeld. U kunt [een Data Science VM toevoegen aan Azure notebooks](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier) om de beschik bare reken kracht uit te breiden.

### <a name="short-term-experimentation-and-evaluation"></a>Experimenten en evaluatie op korte termijn
De Data Science VM kan worden gebruikt om hulpprogramma's als Microsoft ML Server, SQL Server, Visual Studio-hulpprogramma's, Jupyter, deep learning-/ML-toolkits en nieuwe hulpprogramma's die populair zijn in de community met minimale installatie-inspanningen te evalueren en ermee te gaan werken. Aangezien de Data Science VM snel kan worden ingesteld, kan deze worden toegepast in andere gebruiks scenario's voor de korte termijn. Deze scenario's zijn onder andere het repliceren van gepubliceerde experimenten, het uitvoeren van demo's, de volgende scenario's in online sessies en zelf studies voor vergaderingen.

### <a name="deep-learning"></a>Deep learning
De Data Science VM kan worden gebruikt voor trainingsmodellen die gebruik maken van deep-learningalgoritmen op GPU-hardware (Graphics Processing Unit). DVSM maakt gebruik van de VM-schalingsmogelijkheden van Azure Cloud zodat u naar behoefte GPU-hardware in de cloud kunt gebruiken. Wanneer u grote modellen traint of snelle berekeningen nodig hebt, kunt u met behoud van dezelfde besturingssysteemschijf overschakelen naar een GPU-VM.  De Windows Server 2016-editie van DSVM wordt geleverd met vooraf geïnstalleerde GPU-stuurprogramma's, frameworks en GPU-versies van frameworks voor deep learning. In de Linux-editie is deep learning met behulp van de GPU ingeschakeld op zowel de CentOS- als de Ubuntu-DSVM. U kunt de Ubuntu-, CentOS-of Windows 2016-versie van Data Science VM implementeren op een virtuele machine op basis van een niet-GPU. In dit geval worden alle diepe leer raamwerken terugvallen op de CPU-modus. Meer informatie over [beschik bare diep gaande lessen en AI-Frameworks](dsvm-deep-learning-ai-frameworks.md).
 
Meer informatie over [beschik bare diep gaande lessen en AI-Frameworks](dsvm-deep-learning-ai-frameworks.md).

<a name="included"></a>

## <a name="whats-included-on-dsvm"></a>Wat is opgenomen in DSVM?
In de Data Science VM zijn vele populaire hulpprogramma's voor datatechnologie en deep learning vooraf geïnstalleerd en geconfigureerd. Hiertoe behoren ook hulpprogramma's die het werken vergemakkelijken met verschillende Azure-gegevens- en analyseproducten, zoals Microsoft ML Server (R, Python) voor het bouwen van voorspellende modellen of SQL Server 2017 voor het verkennen van grootschalige gegevenssets. De Data Science VM bevat een host van andere hulpprogram ma's van de open-source community en van micro soft, evenals [voorbeeld code en notebooks](dsvm-samples-and-walkthroughs.md). 

Hulpprogram ma's en platformen:
+ [Ondersteunde programmeer talen](dsvm-languages.md)

+ [Ondersteunde gegevens platforms](dsvm-data-platforms.md)

+ [Ontwikkelingsprogram ma's en Ide's](dsvm-tools-development.md)

+ [Dieper leren en AI-Frameworks](dsvm-deep-learning-ai-frameworks.md).

+ [Machine learning-en data Science-hulpprogram ma's](dsvm-ml-data-science-tools.md)

+ [Hulp middelen voor gegevens opname](dsvm-tools-ingestion.md)

+ [Hulp middelen voor het verkennen en visualisatie van gegevens](dsvm-tools-explore-and-visualize.md)

In de volgende tabel vindt u een specificatie en vergelijking van de belangrijkste componenten die zijn opgenomen in de Windows- en Linux-editie van de Data Science Virtual Machine.

| **Hulpprogramma**                                                           | **Windows-editie** | **Linux-editie** |
| :------------------------------------------------------------------ |:-------------------:|:------------------:|
| [Microsoft R Open](https://mran.microsoft.com/open/) met populaire pakketten die vooraf zijn geïnstalleerd   |J                      | J             |
| [Microsoft ML Server (R, Python)](https://docs.microsoft.com/machine-learning-server/) Developer-editie bevat, <br />  &nbsp;&nbsp;&nbsp;&nbsp;* [RevoScaleR/revoscalepy](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-revoscaler) parallel en gedistribueerd high-performance framework (R & Python)<br />  &nbsp;&nbsp;&nbsp;&nbsp;* [MicrosoftML](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-the-microsoftml-package) - nieuwe geavanceerde algoritmen van Microsoft <br />  &nbsp;&nbsp;&nbsp;&nbsp;* [R- en Python-operationalisering](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)                                            |J                      | J |
| [Microsoft Office](https://products.office.com/business/office-365-proplus-business-software) Pro-Plus met gedeelde activering: Excel, Word en Power Point   |J                      |N              |
| [Anaconda Python](https://www.continuum.io/) 2.7, 3.5 met populaire pakketten die vooraf zijn geïnstalleerd    |J                      |J              |
| [JuliaPro](https://juliacomputing.com/products/juliapro.html) met populaire pakketten voor Julia-taal vooraf geïnstalleerd                         |J                      |J              |
| Relationele databases                                                            | [SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition| [PostgreSQL](https://www.postgresql.org/) (CentOS),<br/>[SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition (Ubuntu) |
| Databasehulpprogramma's                                                       | * SQL Server Management Studio <br/>* SQL Server Integration Services<br/>* [bcp, sqlcmd](https://docs.microsoft.com/sql/tools/command-prompt-utility-reference-database-engine)<br /> *ODBC-/JDBC-stuurprogramma's| * [SQuirreL SQL](http://squirrel-sql.sourceforge.net/) (querytool), <br /> * bcp, sqlcmd <br /> *ODBC-/JDBC-stuurprogramma's|
| Schaalbare in-database analyses met SQL Server ML-services (R, Python) | J     |N              |
| **[Jupyter-Notebook Server](https://jupyter.org/) met de volgende kernels**                                  | J     | J |
|     &nbsp;&nbsp;&nbsp;&nbsp;* R | J | J |
|     &nbsp;&nbsp;&nbsp;&nbsp;* Python | J | J |
|     &nbsp;&nbsp;&nbsp;&nbsp;* Julia | J | J |
|     &nbsp;&nbsp;&nbsp;&nbsp;* PySpark | J | J |
|     &nbsp;&nbsp;&nbsp;&nbsp;* [Sparkmagic](https://github.com/jupyter-incubator/sparkmagic) | N | Y (alleen Ubuntu) |
|     &nbsp;&nbsp;&nbsp;&nbsp;* SparkR     | N | J |
| JupyterHub (notebookserver voor meerdere gebruikers)| N | J |
| JupyterLab (notebookserver voor meerdere gebruikers) | N | Y (alleen Ubuntu) |
| **Ontwikkel hulpprogramma's, Ide's en code-editors**| | |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Visual studio 2019 (Community Edition)](https://www.visualstudio.com/community/) met git-invoeg toepassing, Azure HDInsight (Hadoop), Data Lake, SQL Server Data tools, [node. js](https://github.com/Microsoft/nodejstools), [python](https://aka.ms/ptvs)en [R tools for Visual Studio (RTVS)](https://microsoft.github.io/RTVS-docs/) | J | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Visual Studio Code](https://code.visualstudio.com/) | J | J |
| &nbsp;&nbsp;&nbsp;&nbsp;* [RStudio Desktop](https://www.rstudio.com/products/rstudio/#Desktop) | J | J |
| &nbsp;&nbsp;&nbsp;&nbsp;* [RStudio Server](https://www.rstudio.com/products/rstudio/#Server) | N | J |
| &nbsp;&nbsp;&nbsp;&nbsp;* [PyCharm Community Edition](https://www.jetbrains.com/pycharm/) | N | J |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Atom](https://atom.io/) | N | J |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Juno (Julia IDE)](https://junolab.org/)| J | J |
| &nbsp;&nbsp;&nbsp;&nbsp;* Vim en Emacs | J | J |
| &nbsp;&nbsp;&nbsp;&nbsp;* Git en GitBash | J | J |
| &nbsp;&nbsp;&nbsp;&nbsp;* OpenJDK | J | J |
| &nbsp;&nbsp;&nbsp;&nbsp;* .NET Framework | J | N |
| Power BI Desktop | J | N |
| SDK's voor toegang tot Azure en services van de Cortana Intelligence Suite | J | J |
| **Hulpprogramma's voor gegevensverplaatsing en -beheer** | | |
| &nbsp;&nbsp;&nbsp;&nbsp;* Azure Opslagverkenner | J | J |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Azure CLI](https://docs.microsoft.com/cli/azure) | J | J |
| &nbsp;&nbsp;&nbsp;&nbsp;* Azure Powershell | J | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Azcopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy) | J | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Blob FUSE-stuurprogramma](https://github.com/Azure/azure-storage-fuse) | N | J |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Adlcopy (Azure Data Lake Storage)](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob) | J | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [DocDB-hulpprogramma voor gegevensmigratie](https://docs.microsoft.com/azure/documentdb/documentdb-import-data) | J | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Microsoft Data Management Gateway](https://msdn.microsoft.com/library/dn879362.aspx): Gegevens verplaatsen tussen on-premises en de cloud | J | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* Unix-/Linux-opdrachtregelprogramma’s | J | J |
| [Apache Drill](https://drill.apache.org) voor dataonderzoek | J | J |
| **Hulpprogramma's voor Machine Learning** |||
| &nbsp;&nbsp;&nbsp;&nbsp;* Integratie met [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) (R, Python) | J | J |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Xgboost](https://github.com/dmlc/xgboost) | J | J |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) | J | J |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Weka](https://www.cs.waikato.ac.nz/ml/weka/) | J | J |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Rattle](https://togaware.com/rattle/) | J | J |
| &nbsp;&nbsp;&nbsp;&nbsp;* [LightGBM](https://github.com/Microsoft/LightGBM) | N | Y (alleen Ubuntu) |
| &nbsp;&nbsp;&nbsp;&nbsp;* [CatBoost](https://tech.yandex.com/catboost/) | N | Y (alleen Ubuntu) |
| &nbsp;&nbsp;&nbsp;&nbsp;* [H2O](https://www.h2o.ai/h2o/), [Sparkling Water](https://www.h2o.ai/sparkling-water/) | N | Y (alleen Ubuntu) |
| **Hulpprogramma's voor Deep Learning** <br>Alle hulpprogramma's werken op een GPU of CPU |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/cognitive-toolkit/) (Windows 2016) | J | J |
| &nbsp;&nbsp;&nbsp;&nbsp;* [TensorFlow](https://www.tensorflow.org/) | Y (Windows 2016) | J |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Horovod](https://github.com/uber/horovod) | N | Y (Ubuntu) |
| &nbsp;&nbsp;&nbsp;&nbsp;* [MXNet](https://mxnet.io/) | Y (Windows 2016) | J|
| &nbsp;&nbsp;&nbsp;&nbsp;* [Caffe & Caffe2](https://github.com/caffe2/caffe2) | N | J |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Chainer](https://chainer.org/) | N | J |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Torch](http://torch.ch/) | N | J |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Theano](https://github.com/Theano/Theano) | N | J |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Keras](https://keras.io/)| N | J |
| &nbsp;&nbsp;&nbsp;&nbsp;* [PyTorch](https://pytorch.org/)| N | J |
| &nbsp;&nbsp;&nbsp;&nbsp;* [NVidia Digits](https://github.com/NVIDIA/DIGITS) | N | J |
| &nbsp;&nbsp;&nbsp;&nbsp;* [MXNet Model Server](https://github.com/awslabs/mxnet-model-server) | N | J |
| &nbsp;&nbsp;&nbsp;&nbsp;* [TensorFlow Serving](https://www.tensorflow.org/serving/) | N | J |
| &nbsp;&nbsp;&nbsp;&nbsp;* [TensorRT](https://developer.nvidia.com/tensorrt) | N | J |
| &nbsp;&nbsp;&nbsp;&nbsp;* [CUDA-, cuDNN-, NVIDIA-stuurprogramma](https://developer.nvidia.com/cuda-toolkit) | J | J |

## <a name="next-steps"></a>Volgende stappen

Meer informatie vindt u in deze artikelen:

+ Windows:
  + [Een Windows-DSVM instellen](provision-vm.md)
  + [Tien dingen die u kunt doen op een Windows-DSVM](vm-do-ten-things.md)

+ Linux:
  + [Een Linux-DSVM instellen (Ubuntu)](dsvm-ubuntu-intro.md)
  + [Een Linux-DSVM instellen (CentOS)](linux-dsvm-intro.md)
  + [Data wetenschappen op een Linux-DSVM](linux-dsvm-walkthrough.md)
