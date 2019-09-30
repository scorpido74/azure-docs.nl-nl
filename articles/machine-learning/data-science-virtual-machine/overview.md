---
title: Wat is Azure Data Science Virtual Machine
description: Belangrijke analysescenario's en componenten voor Windows en Linux Data Science Virtual Machines.
keywords: hulpprogramma's voor datatechnologie, virtuele machine voor datatechnologie, hulpprogramma voor datatechnologie, linux-datatechnologie
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: overview
ms.date: 02/22/2019
ms.openlocfilehash: f40ad01d74df26e5a6d90c348a706e8a3c666f2f
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/30/2019
ms.locfileid: "71675081"
---
# <a name="what-is-the-azure-data-science-virtual-machine-for-linux-and-windows"></a>Wat is de Azure-Data Science Virtual Machine voor Linux en Windows?

De Data Science Virtual Machine (DSVM) is een aangepaste VM-installatie kopie op het Azure-Cloud platform dat speciaal is gebouwd voor het uitvoeren van data Science. DSVM bevat veel populaire gegevenswetenschap- en andere hulpprogramma's die vooraf zijn geïnstalleerd en geconfigureerd voor het snel ontwikkelen van intelligente toepassingen voor geavanceerde analyses. 

De hulpprogram ma's voor het hulp programma worden rigoureuze getest door gegevens wetenschappers en ontwikkel aars van micro soft en door de bredere data Science-community. Deze test zorgt voor een stabiliteit en een algemene levens vatbaarheid.

De DSVM is beschikbaar op:
+ Windows Server 2016, Windows Server 2012
+ Ubuntu 16,04 LTS en CentOS 7,4

> [!NOTE]
> Alle VM-hulpprogram ma's voor uitgebreid leren zijn gevouwen in de Data Science Virtual Machine. 


## <a name="what-can-i-do-with-the-dsvm"></a>Wat kan ik doen met het DSVM?
Het doel van de Data Science Virtual Machine is om gegevens specialisten van alle vaardigheids niveaus en allerlei branches te voorzien van een frictie vrije, vooraf geconfigureerde en volledig geïntegreerde data Science-omgeving. In plaats van zelf een vergelijk bare werk ruimte te implementeren, kunt u een DSVM inrichten. Met deze optie kunt u dagen of zelfs _weken_ besparen op de installatie-, configuratie-en pakket beheer processen. Nadat uw DSVM is toegewezen, kunt u meteen aan uw datatechnologieproject gaan werken.

De DSVM is ontworpen en geconfigureerd voor het werken met een breed scala aan gebruiks scenario's. U kunt uw omgeving omhoog of omlaag schalen naarmate de project vereisten veranderen. U kunt ook uw voorkeurs taal gebruiken voor het Program meren van data Science-taken en andere hulpprogram ma's installeren om het systeem aan uw behoeften aan te passen.

### <a name="preconfigured-analytics-desktop-in-the-cloud"></a>Vooraf geconfigureerd analysebureaublad in de cloud
De DSVM biedt een basislijn configuratie voor data Science-teams die hun lokale Bureau bladen willen vervangen door een beheerd Cloud bureau blad. Deze basisconfiguratie zorgt ervoor dat alle datawetenschappers in een team een consistente installatie hebben, zodat ze experimenten kunnen verifiëren en de samenwerking kunnen bevorderen. De kosten worden ook verlaagd door de overhead van sysadmin te verminderen. Deze last reductie bespaart op de tijd die nodig is om software pakketten voor geavanceerde analyse te evalueren, te installeren en te onderhouden.

### <a name="data-science-training-and-education"></a>Training en onderwijs voor datatechnologie
Enter prise trainers en docenten die data Science branches leren, hebben meestal een installatie kopie van een virtuele machine. De installatie kopie zorgt ervoor dat studenten een consistente installatie hebben en dat de voor beelden werken zoals verwacht. 

De DSVM maakt een omgeving op aanvraag met een consistente installatie die de uitdagingen voor de ondersteuning en incompatibiliteit vereenvoudigt. In gevallen waarin deze omgevingen vaak moeten worden gemaakt, met name voor kortere trainingen, levert dit veel voordelen op.

### <a name="on-demand-elastic-capacity-for-large-scale-projects"></a>Flexibele capaciteit op aanvraag voor grootschalige projecten
Data Science hackathons trappen/wedstrijden of grootschalige gegevens modellering en-exploratie vereisen geschaalde hardware-capaciteit, meestal voor korte duur. De DSVM kan helpen de data Science-omgeving snel op aanvraag te repliceren, op scale-out servers die experimenten toestaan dat IT-bronnen met hoge prestaties kunnen worden uitgevoerd.

### <a name="custom-compute-power-for-azure-notebooks"></a>Aangepaste reken kracht voor Azure Notebooks
[Azure notebooks](../../notebooks/azure-notebooks-overview.md) is een gratis gehoste service voor het ontwikkelen, uitvoeren en delen van Jupyter-notebooks in de Cloud zonder installatie. De gratis service laag is beperkt tot 4 GB aan geheugen en 1 GB aan gegevens. 

Als u alle limieten wilt vrijgeven, kunt u een notitieblok project koppelen aan een DSVM of een andere virtuele machine die wordt uitgevoerd op een Jupyter-server. Als u zich aanmeldt bij Azure Notebooks met een account met behulp van Azure Active Directory (zoals een bedrijfs account), worden in notitie blokken automatisch Dsvm weer gegeven in de abonnementen die aan dat account zijn gekoppeld. U kunt [een DSVM aan Azure notebooks koppelen](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier) om de beschik bare reken kracht uit te breiden.

### <a name="short-term-experimentation-and-evaluation"></a>Experimenten en evaluatie op korte termijn
U kunt de DSVM gebruiken om hulp middelen als volgt te evalueren of te leren, met minimale instellings inspanning:

- Microsoft Machine Learning Server
- SQL Server
- Visual Studio-hulpprogramma's
- Jupyter
- Diep gaande leer-en machine learning-werkset
- Nieuwe hulpprogram ma's populair in de Community 

Omdat u de DSVM snel kunt instellen, kunt u deze Toep assen in andere gebruiks scenario's voor de korte termijn. Deze scenario's zijn onder andere het repliceren van gepubliceerde experimenten, het uitvoeren van demo's en het volgen van instructies in online sessies en zelf studies voor vergaderingen.

### <a name="deep-learning-with-gpus"></a>Dieper leren met Gpu's
In de DSVM kunnen uw trainings modellen diepe leer algoritmen gebruiken op hardware die is gebaseerd op Gpu's (graphics processing units). Door gebruik te maken van de VM-schaal mogelijkheden van het Azure-platform, helpt de DSVM u bij het gebruik van op GPU gebaseerde hardware in de Cloud op basis van uw behoeften. U kunt overschakelen naar een op GPU gebaseerde VM wanneer u grote modellen bijwerkt of wanneer u hoge snelheids berekeningen nodig hebt terwijl dezelfde besturingssysteem schijf wordt bewaard. U kunt een van de virtuele machines van de N-serie GPU die met DSVM zijn ingeschakeld, kiezen. Houd er rekening mee dat gratis Azure-accounts voor GPU ingeschakelde Sku's voor virtuele machines niet ondersteunen.

De Windows Server 2016-versie van de DSVM wordt vooraf geïnstalleerd met GPU-Stuur Programma's, frameworks en GPU-versies van diepe leer raamwerken. Op de Linux-editie is dieper leren op Gpu's ingeschakeld op zowel de CentOS-als Ubuntu-Dsvm. 

U kunt ook de Ubuntu-, CentOS-of Windows 2016-versie van de DSVM implementeren op een virtuele machine van Azure die niet is gebaseerd op Gpu's. In dit geval worden alle diepe leer raamwerken terugvallen op de CPU-modus.
 
Meer [informatie over beschik bare diep gaande lessen en AI-Frameworks](dsvm-deep-learning-ai-frameworks.md).

<a name="included"></a>

## <a name="whats-included-on-the-dsvm"></a>Wat is opgenomen in de DSVM?
In de Data Science VM zijn vele populaire hulpprogramma's voor datatechnologie en deep learning vooraf geïnstalleerd en geconfigureerd. Hieronder zijn ook hulpprogramma's waarmee u gemakkelijk kunt werken met verschillende Azure-gegevens en -analyseproducten. Deze producten omvatten Microsoft Machine Learning Server (R, python) voor het bouwen van voorspellende modellen en SQL Server 2017 voor grootschalige exploratie van gegevens sets. De DSVM bevat andere hulpprogram ma's van de open-source community en van micro soft, samen met [voorbeeld code en notitie blokken](dsvm-samples-and-walkthroughs.md). 

Hier volgt een lijst met hulpprogram ma's en platformen:
+ [Ondersteunde programmeer talen](dsvm-languages.md)

+ [Ondersteunde gegevens platforms](dsvm-data-platforms.md)

+ [Ontwikkelingsprogram ma's en Ide's](dsvm-tools-development.md)

+ [Diep gaande lessen en AI-Frameworks](dsvm-deep-learning-ai-frameworks.md)

+ [Machine learning-en data Science-hulpprogram ma's](dsvm-ml-data-science-tools.md)

+ [Hulp middelen voor gegevens opname](dsvm-tools-ingestion.md)

+ [Hulp middelen voor het verkennen en visualisatie van gegevens](dsvm-tools-explore-and-visualize.md)

In de volgende tabel vindt u een specificatie en vergelijking van de belangrijkste componenten die zijn opgenomen in de Windows- en Linux-editie van de Data Science Virtual Machine.

| **Hulpprogramma**                                                           | **Windows-editie** | **Linux-editie** |
| :------------------------------------------------------------------ |:-------------------:|:------------------:|
| [Microsoft R Open](https://mran.microsoft.com/open/) met populaire pakketten die vooraf zijn geïnstalleerd   |J                      | J             |
| [Microsoft machine learning server (R, python)](https://docs.microsoft.com/machine-learning-server/) Developer Edition bevat: <br />  &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [RevoScaleR/revoscalepy](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-revoscaler) parallel en gedistribueerd Framework met hoge prestaties (R en python)<br />  &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [MicrosoftML](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-the-microsoftml-package), nieuwe geavanceerde machine learning algoritmen van micro soft <br />  &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [R en python uitoefening](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)                                            |J                      | J |
| [Microsoft Office](https://products.office.com/business/office-365-proplus-business-software) ProPlus met gedeelde activering: Excel, Word en Power Point   |J                      |N              |
| [Anaconda Python](https://www.continuum.io/) 2,7 en 3,5 met populaire pakketten die vooraf zijn geïnstalleerd    |J                      |J              |
| [JuliaPro](https://juliacomputing.com/products/juliapro.html) met populaire pakketten voor Julia-taal vooraf geïnstalleerd                         |J                      |J              |
| Relationele databases                                                            | [SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition| [PostgreSQL](https://www.postgresql.org/) (CentOS),<br/>[SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition (Ubuntu) |
| Databasehulpprogramma's                                                       |  SQL Server Management Studio <br/> SQL Server Integration Services<br/> [BCP, Sqlcmd](https://docs.microsoft.com/sql/tools/command-prompt-utility-reference-database-engine)<br />  ODBC/JDBC-Stuur Programma's|  [Squirrel SQL](http://squirrel-sql.sourceforge.net/) (hulp programma voor query's), <br />  BCP, Sqlcmd <br />  ODBC/JDBC-Stuur Programma's|
| Schaal bare data base Analytics met SQL Server machine learning Services (R, python) | J     |N              |
| [Jupyter notebook server](https://jupyter.org/) met de volgende kernels:                                  | J     | J |
|     &nbsp; @ NO__T-1 @ NO__T-2 @ NO__T-3 R | J | J |
|     &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 python | J | J |
|     &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 Julia | J | J |
|     &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 PySpark | J | J |
|     &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Sparkmagic](https://github.com/jupyter-incubator/sparkmagic) | N | Y (alleen Ubuntu) |
|     &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 Spark     | N | J |
| JupyterHub (notebook server voor meerdere gebruikers)| N | J |
| Jjupyterlab (notebook server voor meerdere gebruikers) | N | Y (alleen Ubuntu) |
| Ontwikkel hulpprogramma's, Ide's en code-editors:| | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Visual Studio 2019 (Community Edition)](https://www.visualstudio.com/community/) met git-invoeg toepassing, Azure HDInsight (Hadoop), Azure Data Lake, SQL Server Data tools, [node. js](https://github.com/Microsoft/nodejstools), [python](https://aka.ms/ptvs)en [R-hulpprogram ma's voor Visual Studio (RTVS)](https://microsoft.github.io/RTVS-docs/) | J | N |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Visual Studio code](https://code.visualstudio.com/) | J | J |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [RStudio Desktop](https://www.rstudio.com/products/rstudio/#Desktop) | J | J |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [RStudio-server](https://www.rstudio.com/products/rstudio/#Server) | N | J |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [PyCharm Community Edition](https://www.jetbrains.com/pycharm/) | N | J |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Atom](https://atom.io/) | N | J |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Juno (Julia IDE)](https://junolab.org/)| J | J |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 vim en emacs | J | J |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 Git en git bash | J | J |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 OpenJDK | J | J |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 .NET Framework | J | N |
| Power BI Desktop | J | N |
| SDK's voor toegang tot Azure en services van de Cortana Intelligence Suite | J | J |
| Hulpprogram ma's voor gegevens verplaatsing en-beheer: | | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 Azure Storage Explorer | J | J |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Azure cli](https://docs.microsoft.com/cli/azure) | J | J |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 Azure PowerShell | J | N |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Azcopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy) | J | N |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [BLOB-zeker stuur programma](https://github.com/Azure/azure-storage-fuse) | N | J |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Adlcopy (Azure data Lake Storage)](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob) | J | N |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Azure Cosmos DB hulp programma voor gegevens migratie](https://docs.microsoft.com/azure/documentdb/documentdb-import-data) | J | N |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [micro soft data management gateway](https://msdn.microsoft.com/library/dn879362.aspx): gegevens verplaatsen tussen on-premises en de Cloud | J | N |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 Unix/Linux-opdracht regel Programma's | J | J |
| [Apache-analyse](https://drill.apache.org) voor het verkennen van gegevens | J | J |
| Machine learning-hulpprogram ma's: |||
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3-integratie met [Azure machine learning](https://azure.microsoft.com/services/machine-learning/) (R, python) | J | J |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [XGBoost](https://github.com/dmlc/xgboost) | J | J |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) | J | J |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Fridge](https://www.cs.waikato.ac.nz/ml/weka/) | J | J |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Rattle](https://togaware.com/rattle/) | J | J |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [LightGBM](https://github.com/Microsoft/LightGBM) | N | Y (alleen Ubuntu) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [CatBoost](https://tech.yandex.com/catboost/) | N | Y (alleen Ubuntu) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [H2O](https://www.h2o.ai/h2o/), [mousserend water](https://www.h2o.ai/sparkling-water/) | N | Y (alleen Ubuntu) |
| Uitgebreide leer hulpmiddelen die op een GPU of CPU werken: |  |  |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/cognitive-toolkit/) (Windows 2016) | J | J |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [tensor flow](https://www.tensorflow.org/) | Y (Windows 2016) | J |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Horovod](https://github.com/uber/horovod) | N | Y (Ubuntu) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [MXNet](https://mxnet.io/) | Y (Windows 2016) | J|
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Caffe en Caffe2](https://github.com/caffe2/caffe2) | N | J |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3- [ketting](https://chainer.org/) | N | J |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Torch](http://torch.ch/) | N | J |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Theano](https://github.com/Theano/Theano) | N | J |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Keras](https://keras.io/)| N | J |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [PyTorch](https://pytorch.org/)| N | J |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [NVIDIA cijfers](https://github.com/NVIDIA/DIGITS) | N | J |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [MXNet-model server](https://github.com/awslabs/mxnet-model-server) | N | J |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [tensor flow](https://www.tensorflow.org/serving/) voor | N | J |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [TensorRT](https://developer.nvidia.com/tensorrt) | N | J |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [CUDA, cuDNN, nvidia-stuur programma](https://developer.nvidia.com/cuda-toolkit) | J | J |

## <a name="next-steps"></a>Volgende stappen

Meer informatie vindt u in deze artikelen:

+ Windows:
  + [Een Windows-DSVM instellen](provision-vm.md)
  + [Tien dingen die u kunt doen op een Windows-DSVM](vm-do-ten-things.md)

+ Linux:
  + [Een Linux-DSVM instellen (Ubuntu)](dsvm-ubuntu-intro.md)
  + [Een Linux-DSVM instellen (CentOS)](linux-dsvm-intro.md)
  + [Data wetenschappen op een Linux-DSVM](linux-dsvm-walkthrough.md)
