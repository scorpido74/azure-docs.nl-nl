---
title: 'Referentie: Ubuntu Data Science Virtual Machine'
titleSuffix: Azure Data Science Virtual Machine
description: Details over tools in de Ubuntu Data Science Virtual Machine
author: gvashishtha
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: gopalv
ms.date: 09/11/2019
ms.topic: reference
ms.openlocfilehash: 5c184e7f1dc828c3f9ff8d449d29ab3aaa4d1cf6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77525818"
---
# <a name="reference-ubuntu-linux-data-science-virtual-machine"></a>Referentie: Ubuntu (Linux) Data Science Virtual Machine

Zie hieronder voor een lijst met beschikbare tools op uw Ubuntu Data Science Virtual Machine. 

## <a name="deep-learning-libraries"></a>Deep learning bibliotheken

### <a name="cntk"></a>CNTK

De Microsoft Cognitive Toolkit is een open-source deep learning toolkit. Python bindingen zijn beschikbaar in de root en py35 Conda omgevingen. Het heeft ook een command-line tool (CNTK) die al in het pad.

Voorbeeld python-notitieblokken zijn beschikbaar in JupyterHub. Als u een basisvoorbeeld op de opdrachtregel wilt uitvoeren, voert u de volgende opdrachten uit in de shell:

```bash
cd /home/[USERNAME]/notebooks/CNTK/HelloWorld-LogisticRegression
cntk configFile=lr_bs.cntk makeMode=false command=Train
```

Zie voor meer informatie het CNTK-gedeelte van [GitHub](https://github.com/Microsoft/CNTK) en de [CNTK-wiki.](https://github.com/Microsoft/CNTK/wiki)

### <a name="caffe"></a>Caffe

Caffe is een deep learning framework van het Berkeley Vision and Learning Center. Het is beschikbaar in /opt/caffe. U voorbeelden vinden in /opt/caffe/voorbeelden.

### <a name="caffe2"></a>Caffe2

Caffe2 is een deep learning framework van Facebook dat is gebouwd op Caffe. Het is beschikbaar in Python 2.7 in de Conda root omgeving. Voer de volgende opdracht uit vanuit de shell om deze te activeren:

```bash
source /anaconda/bin/activate root
```

Sommige voorbeeldnotitieblokken zijn beschikbaar in JupyterHub.

### <a name="h2o"></a>H2O

H2O is een snel, in-memory, gedistribueerd machine learning en predictive analytics platform. Een Python-pakket is geïnstalleerd in zowel de root- als de py35 Anaconda-omgevingen. Er is ook een R-pakket geïnstalleerd. 

Als u H2O wilt openen `java -jar /dsvm/tools/h2o/current/h2o.jar`vanaf de opdrachtregel, voert u . Er zijn verschillende [opdrachtregelopties](http://docs.h2o.ai/h2o/latest-stable/h2o-docs/starting-h2o.html#from-the-command-line) die u mogelijk wilt configureren. U hebt toegang tot de gebruikersinterface van het Flow-web door te bladeren om aan de slag te http://localhost:54321 gaan. Voorbeeldnotitieblokken zijn ook beschikbaar in JupyterHub.

### <a name="keras"></a>Keras

Keras is een high-level neurale netwerk API in Python. Het kan draaien op de top van TensorFlow, Microsoft Cognitive Toolkit, of Theano. Het is beschikbaar in de root- en py35 Python-omgevingen.

### <a name="mxnet"></a>MXNet

MXNet is een deep learning framework ontworpen voor zowel efficiëntie als flexibiliteit. Het heeft R en Python bindingen opgenomen op de DSVM. Voorbeeldnotitieblokken zijn opgenomen in JupyterHub en voorbeeldcode is beschikbaar in /dsvm/samples/mxnet.

### <a name="nvidia-digits"></a>NVIDIA CIJFERS

Het NVIDIA Deep Learning GPU Training System, bekend als DIGITS, is een systeem om veelvoorkomende deep learning-taken te vereenvoudigen. Deze taken omvatten het beheren van gegevens, het ontwerpen en trainen van neurale netwerken op GPU-systemen en het in realtime monitoren van prestaties met geavanceerde visualisatie.

CIJFERS is beschikbaar als een dienst genaamd *cijfers*. Start de service http://localhost:5000 en blader om aan de slag te gaan.

DIGITS is ook geïnstalleerd als een Python-module in de Conda-root-omgeving.

### <a name="tensorflow"></a>TensorFlow

TensorFlow is de deep learning-bibliotheek van Google. Het is een open-source softwarebibliotheek voor numerieke berekeningen met behulp van dataflowgrafieken. TensorFlow is beschikbaar in de py35 Python-omgeving en sommige voorbeeldnotebooks zijn opgenomen in JupyterHub.

### <a name="theano"></a>Theano

Theano is een Python-bibliotheek voor efficiënte numerieke berekeningen. Het is beschikbaar in de root- en py35 Python-omgevingen. 

### <a name="torch"></a>Torch

Torch is een wetenschappelijk computerkader met brede ondersteuning voor machine learning-algoritmen. Het is beschikbaar in /dsvm/tools/torch, en de **interactieve** sessie en LuaRocks package manager zijn beschikbaar op de command line. Voorbeelden zijn beschikbaar in /dsvm/samples/torch.

PyTorch is ook beschikbaar in de wortel Anaconda omgeving. Voorbeelden zijn in /dsvm/samples/pytorch.

## <a name="microsoft-machine-learning-server"></a>Microsoft Machine Learning Server

R is een van de meest populaire talen voor data-analyse en machine learning. Als u R wilt gebruiken voor uw analyses, heeft de VM Microsoft Machine Learning Server met Microsoft R Open- en Math Kernel Library. Math Kernel Library optimaliseert wiskundige bewerkingen die veel voorkomen in analytische algoritmen. Microsoft R Open is 100 procent compatibel met CRAN R en alle R-bibliotheken die in CRAN zijn gepubliceerd, kunnen worden geïnstalleerd op Microsoft R Open. 

Machine Learning Server biedt u schaalvergroting en operationalisering van R-modellen in webservices. U uw R-programma's bewerken in een van de standaardeditors, zoals RStudio, vi of Emacs. Als u liever de Emacs-editor gebruikt, is deze vooraf geïnstalleerd. Het Emacs ESS -pakket (Emacs Speaks Statistics) vereenvoudigt het werken met R-bestanden binnen de Emacs-editor.

Als u de R-console wilt openen, voert u **R** in de shell in. Deze opdracht neemt je mee naar een interactieve omgeving. Om uw R-programma te ontwikkelen, gebruikt u meestal een editor zoals Emacs of vi en voert u de scripts uit binnen R. Met RStudio heb je een volledige grafische IDE om je R-programma te ontwikkelen.

Er is ook een R-script voor u om de [Top 20 R-pakketten](https://www.kdnuggets.com/2015/06/top-20-r-packages.html) te installeren als u dat wilt. U dit script uitvoeren nadat u zich in de interactieve R-interface bevindt. Zoals eerder vermeld, u die interface openen door **R** in de shell in te voeren.  

## <a name="python"></a>Python

Anaconda Python is geïnstalleerd met Python 2.7 en 3.5 omgevingen. De 2.7 omgeving heet _wortel_, en de 3.5 omgeving heet _py35_. Deze distributie bevat de basis Python samen met ongeveer 300 van de meest populaire wiskunde, engineering, en data analytics pakketten.

De py35-omgeving is de standaardinstelling. Als u de hoofdomgeving (2.7) wilt activeren, gebruikt u deze opdracht:

```bash
source activate root
```

Als u de py35-omgeving opnieuw wilt activeren, gebruikt u de opdracht:

```bash
source activate py35
```

Als u een interactieve Python-sessie wilt aanroepen, voert u **python** in de shell in. 

Installeer extra Python-bibliotheken met Behulp van Conda of pip. Activeer voor pip eerst de juiste omgeving als u de standaardinstelling niet wilt:

```bash
source activate root
pip install <package>
```

Of geef het volledige pad naar pip op:

```bash
/anaconda/bin/pip install <package>
```

Voor Conda moet u altijd de omgevingsnaam opgeven (py35 of root):

```bash
conda install <package> -n py35
```

Als u zich op een grafische interface bevindt of X11 doorsturen hebt ingesteld, u **pycharm** invoeren om de PyCharm Python IDE te openen. U de standaardteksteditors gebruiken. Daarnaast kun je Spyder gebruiken, een Python IDE die is gebundeld met Anaconda Python-distributies. Spyder heeft een grafische desktop of X11 forwarding nodig. De grafische desktop heeft een snelkoppeling naar Spyder.

## <a name="jupyter-notebook"></a>Jupyter-notebook

De Anaconda-distributie wordt ook geleverd met een Jupyter-notebook, een omgeving om code en analyse te delen. De Jupyter notebook is toegankelijk via JupyterHub. U meldt zich aan met uw lokale Linux gebruikersnaam en wachtwoord.

De Jupyter-notebookserver is vooraf geconfigureerd met Python 2-, Python 3- en R-kernels. Gebruik het bureaubladpictogram **Jupyter Notebook** om de browser te openen en toegang te krijgen tot de notebookserver. Als u via SSH of de X2Go-client op de VM zit, heeft [https://localhost:8000/](https://localhost:8000/)u ook toegang tot de Jupyter-notebookserver op .

> [!NOTE]
> Ga door als u certificaatwaarschuwingen krijgt.

U hebt toegang tot de Jupyter-notebookserver vanaf elke host. Voer **\<https:// VM DNS-naam of\>IP-adres in :8000/ .**

> [!NOTE]
> Poort 8000 wordt standaard geopend in de firewall wanneer de VM is ingericht. 

We hebben verpakte voorbeeldnotebooks - een in Python en een in R. U de koppeling naar de voorbeelden op de startpagina van het notitieblok bekijken nadat u zich hebt geverifieerd naar het Jupyter-notitieblok met behulp van uw lokale Linux-gebruikersnaam en -wachtwoord. U een nieuw notitieblok maken door **Nieuw**te selecteren en vervolgens de juiste taalkernel te selecteren. Als u de knop **Nieuw** niet ziet, selecteert u het pictogram **Jupyter** linksboven om naar de startpagina van de notitieblokserver te gaan.

## <a name="apache-spark-standalone"></a>Apache Spark standalone

Een standalone exemplaar van Apache Spark is vooraf geïnstalleerd op de Linux DSVM om u te helpen Spark-toepassingen lokaal te ontwikkelen voordat u ze test en implementeert op grote clusters. 

U PySpark-programma's uitvoeren via de Jupyter-kernel. Wanneer u Jupyter opent, selecteert u de knop **Nieuw** en ziet u een lijst met beschikbare kernels. **Spark - Python** is de PySpark-kernel waarmee je Spark-toepassingen bouwen met behulp van de Python-taal. Je ook een Python IDE zoals PyCharm of Spyder gebruiken om je Spark-programma te bouwen. 

In deze zelfstandige instantie wordt de Spark-stack uitgevoerd binnen het belclientprogramma. Deze functie maakt het sneller en gemakkelijker om problemen op te lossen in vergelijking met het ontwikkelen op een Spark-cluster.

Jupyter biedt een voorbeeld van PySpark-laptop. Je vindt het in de SparkML-map onder de homedirectory van Jupyter ($HOME/notebooks/SparkML/pySpark). 

Als u programmeert in R voor Spark, u Microsoft Machine Learning Server, SparkR of sparklyr gebruiken. 

Voordat u in een Spark-context in Microsoft Machine Learning Server wordt uitgevoerd, moet u een eenmalige installatiestap uitvoeren om een lokale hadoop HDFS- en gareninstantie met één knooppunt in te schakelen. Standaard zijn Hadoop-services geïnstalleerd, maar uitgeschakeld op de DSVM. Als u deze wilt inschakelen, moet u de volgende opdrachten de eerste keer als hoofd uitvoeren:

```bash
echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
chmod 0600 ~hadoop/.ssh/authorized_keys
chown hadoop:hadoop ~hadoop/.ssh/id_rsa
chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
systemctl start hadoop-namenode hadoop-datanode hadoop-yarn
```

U de Hadoop-gerelateerde services stoppen wanneer u ```systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn```ze niet nodig hebt door te draaien.

De map /dsvm/samples/MRS biedt een voorbeeld dat laat zien hoe u Microsoft Machine Learning Server ontwikkelen en testen in een externe Spark-context (de zelfstandige Spark-instantie op de DSVM).

## <a name="ides-and-editors"></a>IME's en redacteuren

Je hebt de keuze uit verschillende codeeditors, waaronder vi/Vim, Emacs, PyCharm, RStudio en IntelliJ. 

PyCharm, RStudio en IntelliJ zijn grafische editors. Om ze te gebruiken, moet u zijn aangemeld bij een grafisch bureaublad. U opent ze met behulp van snelkoppelingen in het bureaublad- en toepassingsmenu.

Vim en Emacs zijn tekstgebaseerde redacteuren. Op Emacs maakt het ESS-add-on pakket het werken met R eenvoudiger binnen de Emacs-editor. Meer informatie vindt u op de [ESS website.](https://ess.r-project.org/)

LaTex is geïnstalleerd via het texlive pakket, samen met een Emacs add-on pakket genaamd [AUCTeX](https://www.gnu.org/software/auctex/manual/auctex/auctex.html). Dit pakket vereenvoudigt het schrijven van uw LaTex-documenten binnen Emacs.  

## <a name="databases"></a>Databases

### <a name="graphical-sql-client"></a>Grafische SQL-client

SQuirrel SQL, een grafische SQL-client, kan verbinding maken met verschillende databases (zoals Microsoft SQL Server en MySQL) en SQL-query's uitvoeren. U SQuirrel SQL uitvoeren vanaf een grafische desktopsessie (bijvoorbeeld via de X2Go-client) met behulp van een bureaubladpictogram. U de client ook uitvoeren met de volgende opdracht in de shell:

```bash
/usr/local/squirrel-sql-3.7/squirrel-sql.sh
```

Stel voor het eerste gebruik uw stuurprogramma's en databasealiassen in. De JDBC drivers bevinden zich op /usr/share/java/jdbcdrivers.

Zie [SQuirrel SQL voor](http://squirrel-sql.sourceforge.net/index.php?page=screenshots)meer informatie.

### <a name="command-line-tools-for-accessing-microsoft-sql-server"></a>Opdrachtregelhulpprogramma's voor toegang tot Microsoft SQL Server

Het ODBC-stuurprogrammapakket voor SQL Server wordt ook geleverd met twee command-line-tools:

- **bcp:** Het bcp-hulpprogramma kopieert gegevens tussen een instantie van Microsoft SQL Server en een gegevensbestand in een door de gebruiker opgegeven indeling. U het bcp-hulpprogramma gebruiken om grote aantallen nieuwe rijen in SQL Server-tabellen te importeren of om gegevens uit tabellen naar gegevensbestanden te exporteren. Als u gegevens wilt importeren in een tabel, moet u een indelingsbestand gebruiken dat voor die tabel is gemaakt. U moet ook de structuur van de tabel en de typen gegevens begrijpen die geldig zijn voor de kolommen.

  Zie [Verbinding maken met bcp](https://msdn.microsoft.com/library/hh568446.aspx)voor meer informatie.

- **sqlcmd**: U Transact-SQL-instructies invoeren met behulp van de sqlcmd-tool. U ook systeemprocedures en scriptbestanden invoeren bij de opdrachtprompt. Deze tool maakt gebruik van ODBC om Transact-SQL-batches uit te voeren.

  Zie [Verbinding maken met sqlcmd](https://msdn.microsoft.com/library/hh568447.aspx)voor meer informatie.

  > [!NOTE]
  > Er zijn enkele verschillen in deze tool tussen Linux en Windows-platforms. Zie de documentatie voor meer informatie.

### <a name="database-access-libraries"></a>Databasetoegangsbibliotheken

Bibliotheken zijn beschikbaar in R en Python voor databasetoegang:

* In R u het RODBC-pakket of het dplyr-pakket gebruiken om SQL-instructies op de databaseserver op te vragen of uit te voeren.
* In Python biedt de pyodbc-bibliotheek databasetoegang met ODBC als onderliggende laag.  

## <a name="azure-tools"></a>Azure-hulpprogramma's

De volgende Azure-hulpprogramma's zijn geïnstalleerd op de VM:

* **Azure CLI**: U de opdrachtregelinterface in Azure gebruiken om Azure-resources te maken en te beheren via shell-opdrachten. Als u de Azure-hulpprogramma's wilt openen, voert u **azure help**in. Zie de [documentatiepagina van Azure CLI voor](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)meer informatie .
* **Azure Storage Explorer**: Azure Storage Explorer is een grafisch hulpmiddel dat u gebruiken om door de objecten te bladeren die u hebt opgeslagen in uw Azure-opslagaccount en om gegevens van en naar Azure blobs te uploaden en te downloaden. U hebt toegang tot Storage Explorer via het pictogram voor snelkoppelingen op het bureaublad. U het ook openen vanuit een shell prompt door **StorageExplorer**in te voeren. U moet zijn aangemeld vanaf een X2Go-client of x11-forwarding hebben ingesteld.
* **Azure-bibliotheken**: Hieronder volgen enkele van de vooraf geïnstalleerde bibliotheken.
  
  * **Python**: De Azure-gerelateerde bibliotheken in Python zijn *azure,* *azureml,* *pydocumentdb*en *pyodbc*. Met de eerste drie bibliotheken hebt u toegang tot Azure-opslagservices, Azure Machine Learning en Azure Cosmos DB (een NoSQL-database op Azure). De vierde bibliotheek, pyodbc (samen met het Microsoft ODBC-stuurprogramma voor SQL Server), biedt toegang tot SQL Server, Azure SQL Database en Azure SQL Data Warehouse van Python met behulp van een ODBC-interface. Voer **de pip-lijst** in om alle vermelde bibliotheken te bekijken. Zorg ervoor dat u deze opdracht uitvoert in zowel de Python 2.7- als 3.5-omgevingen.
  * **R:** De Azure-gerelateerde bibliotheken in R zijn AzureML en RODBC.
  * **Java**: De lijst met Azure Java-bibliotheken is te vinden in de map /dsvm/sdk/AzureSDKJava op de VM. De belangrijkste bibliotheken zijn Azure-opslag- en beheer-API's, Azure Cosmos DB- en JDBC-stuurprogramma's voor SQL Server.  

U hebt toegang tot de [Azure-portal](https://portal.azure.com) vanuit de vooraf geïnstalleerde Firefox-browser. Op de Azure-portal u Azure-resources maken, beheren en bewaken.

## <a name="azure-machine-learning"></a>Azure Machine Learning

Azure Machine Learning is een volledig beheerde cloudservice waarmee u voorspellende analyseoplossingen bouwen, implementeren en delen. U uw experimenten en modellen bouwen vanuit Azure Machine Learning Studio (klassiek). U het openen vanuit een webbrowser op de Data Science Virtual Machine door [microsoft Azure Machine Learning](https://studio.azureml.net)te bezoeken.

Nadat u zich hebt aangemeld bij Azure Machine Learning Studio (klassiek), u een experimenteercanvas gebruiken om een logische stroom voor de machine learning-algoritmen op te bouwen. U hebt ook toegang tot een Jupyter-notitieblok dat wordt gehost op Azure Machine Learning en naadloos kan samenwerken met de experimenten in Azure Machine Learning Studio (klassiek). 

Operationaliseer de machine learning-modellen die u hebt gebouwd door ze in een webservice-interface te verpakken. Door machine learning-modellen te operationaliseren, kunnen klanten die in elke taal worden geschreven, voorspellingen van die modellen aanroepen. Zie de machine [learning-documentatie](https://azure.microsoft.com/documentation/services/machine-learning/)voor meer informatie.

U uw modellen ook in R of Python op de VM bouwen en ze vervolgens in productie implementeren op Azure Machine Learning. We hebben bibliotheken geïnstalleerd in R **(AzureML)** en Python **(azureml)** om deze functionaliteit in te schakelen.

Zie [Tien dingen die u doen op de Virtual Machine-machine](vm-do-ten-things.md)data science voor informatie over het implementeren van modellen in R en Python in Azure Machine Learning.

> [!NOTE]
> Deze instructies zijn geschreven voor de Windows-versie van de Data Science Virtual Machine. Maar de informatie die daar wordt verstrekt over het implementeren van modellen voor Azure Machine Learning is van toepassing op de Linux VM.

## <a name="machine-learning-tools"></a>Machine learning-tools

De VM wordt geleverd met machine learning-tools en algoritmen die vooraf zijn gecompileerd en lokaal zijn geïnstalleerd. Deze omvatten:

* **Vowpal Wabbit**: Een snel online leeralgoritme.
* **xgboost**: Een tool die geoptimaliseerde, gepromote boomalgoritmen biedt.
* **Rattle:** Een R-gebaseerde grafische tool voor eenvoudige data-exploratie en modellering.
* **Python**: Anaconda Python wordt geleverd met machine learning-algoritmen met bibliotheken zoals Scikit-learn. U andere bibliotheken installeren `pip install` met behulp van de opdracht.
* **LightGBM**: Een snel, gedistribueerd, high-performance gradiënt stimuleren kader op basis van decision tree algoritmen.
* **R:** Een uitgebreide bibliotheek met machine learning-functies is beschikbaar voor R. Vooraf geïnstalleerde bibliotheken zijn lm, glm, randomForest en rpart. U andere bibliotheken installeren door deze opdracht uit te voeren:
  
        install.packages(<lib name>)

Hier is wat aanvullende informatie over de eerste drie machine learning-tools in de lijst.

### <a name="vowpal-wabbit"></a>Vowpal Wabbit

Vowpal Wabbit is een machine learning systeem dat gebruik maakt van technieken zoals online, hashing, allreduce, reducties, learning2search, actief en interactief leren.

Als u het gereedschap in een basisvoorbeeld wilt uitvoeren, gebruikt u de volgende opdrachten:

```bash
cp -r /dsvm/tools/VowpalWabbit/demo vwdemo
cd vwdemo
vw house_dataset
```

Er zijn andere, grotere demo's in die map. Zie voor meer informatie over Vowpal Wabbit [dit gedeelte van GitHub](https://github.com/JohnLangford/vowpal_wabbit) en de [Vowpal Wabbit wiki.](https://github.com/JohnLangford/vowpal_wabbit/wiki)

### <a name="xgboost"></a>xgboost

De xgboost-bibliotheek is ontworpen en geoptimaliseerd voor gebooste (boom)algoritmen. Het doel van deze bibliotheek is om de berekeningslimieten van machines tot het uiterste te duwen dat nodig is om grootschalige boomboosting te bieden die schaalbaar, draagbaar en nauwkeurig is.

Het is voorzien als een command line en een R-bibliotheek. Als u deze bibliotheek in R wilt gebruiken, u een interactieve R-sessie starten (door **R** in de shell in te voeren) en de bibliotheek laden.

Hier is een eenvoudig voorbeeld dat u uitvoeren in een R-prompt:

```R
library(xgboost)

data(agaricus.train, package='xgboost')
data(agaricus.test, package='xgboost')
train <- agaricus.train
test <- agaricus.test
bst <- xgboost(data = train$data, label = train$label, max.depth = 2,
                eta = 1, nthread = 2, nround = 2, objective = "binary:logistic")
pred <- predict(bst, test$data)
```

Als u de xgboost-opdrachtregel wilt uitvoeren, zijn hier de opdrachten die in de shell moeten worden uitgevoerd:

```bash
cp -r /dsvm/tools/xgboost/demo/binary_classification/ xgboostdemo
cd xgboostdemo
xgboost mushroom.conf
```

Een .model-bestand wordt naar de opgegeven map geschreven. U vindt informatie over dit demovoorbeeld [op GitHub.](https://github.com/dmlc/xgboost/tree/master/demo/binary_classification)

Zie de [documentatiepagina van xgboost](https://xgboost.readthedocs.org/en/latest/) en de [GitHub-repository](https://github.com/dmlc/xgboost)voor meer informatie over xgboost.

### <a name="rattle"></a>Rattle

Rattle (de **R** **A**nalytical **T**ool **T**o **L**verdienen **E**asily) maakt gebruik van GUI-gebaseerde data exploratie en modellering. Het presenteert statistische en visuele samenvattingen van gegevens, transformeert gegevens die gemakkelijk kunnen worden gemodelleerd, bouwt zowel onbewaakte als gecontroleerde modellen uit de gegevens, presenteert de prestaties van modellen grafisch en scoort nieuwe gegevenssets. Het genereert ook R-code, het repliceren van de bewerkingen in de gebruikersinterface die rechtstreeks kunnen worden uitgevoerd in R of gebruikt als uitgangspunt voor verdere analyse.

Als u Rattle wilt uitvoeren, moet u zich in een grafische aanmeldingssessie op het bureaublad bevinden. Voer op de terminal **R** in om de R-omgeving te openen. Voer bij de R-prompt de volgende opdrachten in:

```R
library(rattle)
rattle()
```

Nu wordt een grafische interface geopend met een reeks tabbladen. Gebruik de volgende snelstartstappen in Rattle om een voorbeeldvan weergegevensset te gebruiken en een model te maken. In sommige stappen wordt u gevraagd om een aantal vereiste R-pakketten die nog niet op het systeem staan automatisch te installeren en te laden.

> [!NOTE]
> Als u geen toegang hebt om het pakket te installeren in de systeemmap (de standaardinstelling), ziet u mogelijk een prompt in uw R-consolevenster om pakketten te installeren in uw persoonlijke bibliotheek. Antwoord **y** als je deze aanwijzingen te zien.

1. Selecteer **Uitvoeren**.
1. Er verschijnt een dialoogvenster met de vraag of u de voorbeeldgegevensset wilt gebruiken. Selecteer **Ja** om het voorbeeld te laden.
1. Selecteer het tabblad **Model.**
1. Selecteer **Uitvoeren** om een beslissingsstructuur te maken.
1. Selecteer **Tekenen** om de beslissingsstructuur weer te geven.
1. Selecteer de optie **Forest** en selecteer **Uitvoeren** om een willekeurig forest te maken.
1. Selecteer het tabblad **Evalueren.**
1. Selecteer de optie **Risico** en selecteer **Uitvoeren** om twee **prestatiepunten voor risico's weer** te geven.
1. Selecteer het tabblad **Logboek** om de gegenereerde R-code voor de voorgaande bewerkingen weer te geven.
   (Vanwege een bug in de huidige release van **#** Rattle, moet u een teken invoegen voor **Exporteren dit logboek** in de tekst van het logboek.)
1. Selecteer de knop **Exporteren** om het R-scriptbestand met de naam weather_script op te *slaan. R* naar de thuismap.

Je Rattle en R verlaten. Nu u het gegenereerde R-script wijzigen. Of gebruik het script zoals het is, en voer het op elk gewenst moment uit om alles te herhalen wat werd gedaan binnen de Rattle UI. Vooral voor beginners in R is dit een manier om snel analyse en machine learning te doen in een eenvoudige grafische interface, terwijl het automatisch genereren van code in R om te wijzigen of te leren.

## <a name="next-steps"></a>Volgende stappen

Hebt u meer vragen? Overweeg om een [ondersteuningsticket te maken.](https://azure.microsoft.com/support/create-ticket/)