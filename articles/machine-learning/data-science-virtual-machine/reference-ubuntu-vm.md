---
title: 'Referentie: Ubuntu-DSVM'
description: Details over hulpprogram ma's die zijn opgenomen in de Ubuntu-Data Science Virtual Machine
author: gvashishtha
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: gopalv
ms.date: 09/11/2019
ms.topic: reference
ms.openlocfilehash: 0c48b8ba28f67ac7f38d6f636728abe7c5a6bd4e
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/23/2019
ms.locfileid: "71204036"
---
# <a name="reference-ubuntu-linux-data-science-virtual-machine"></a>Referentie: Ubuntu (Linux) Data Science Virtual Machine

Hieronder vindt u een lijst met beschik bare hulpprogram ma's op uw Ubuntu-Data Science Virtual Machine. 

## <a name="deep-learning-libraries"></a>Diep gaande leer bibliotheken

### <a name="cntk"></a>CNTK

De Microsoft Cognitive Toolkit is een open-source diep Learning Toolkit. Python-bindingen zijn beschikbaar in de hoofdmap en py35 Conda-omgevingen. Het bevat ook een opdracht regel programma (CNTK) dat al aanwezig is in het pad.

Voorbeeld van Python-laptops zijn beschikbaar in JupyterHub. Als u een basis voorbeeld wilt uitvoeren op de opdracht regel, voert u de volgende opdrachten uit in de shell:

```bash
cd /home/[USERNAME]/notebooks/CNTK/HelloWorld-LogisticRegression
cntk configFile=lr_bs.cntk makeMode=false command=Train
```

Zie de sectie CNTK van [github](https://github.com/Microsoft/CNTK) en de [CNTK-wiki](https://github.com/Microsoft/CNTK/wiki)voor meer informatie.

### <a name="caffe"></a>Caffe

Caffe is een deep learning-structuur van de visie van Berkeley en Learning Center. Het is beschikbaar in/opt/Caffe. U kunt voor beelden vinden in/opt/Caffe/examples.

### <a name="caffe2"></a>Caffe2

Caffe2 is een deep learning-frameworks uit Facebook die is gebouwd op Caffe. Het is beschikbaar in Python 2,7 in de hoofd omgeving Conda. Als u deze wilt activeren, voert u de volgende opdracht uit vanuit de shell:

```bash
source /anaconda/bin/activate root
```

Sommige laptops voorbeeld zijn beschikbaar in JupyterHub.

### <a name="h2o"></a>H2O

H2O is een snelle, in het geheugen, gedistribueerde machine learning en predictive analytics-platform. Een Python-pakket is geïnstalleerd in de basis- en py35 Anaconda-omgevingen. Een R-pakket is ook geïnstalleerd. 

Als u H2O vanaf de opdracht regel wilt openen `java -jar /dsvm/tools/h2o/current/h2o.jar`, voert u uit. Er zijn verschillende [opdracht regel opties](http://docs.h2o.ai/h2o/latest-stable/h2o-docs/starting-h2o.html#from-the-command-line) die u mogelijk wilt configureren. U kunt toegang krijgen tot de flow-webgebruikersinterface http://localhost:54321 door te bladeren naar om aan de slag te gaan. Voorbeeldnotitieblokken zijn ook beschikbaar in JupyterHub.

### <a name="keras"></a>Keras

Keras is een Neural-netwerk-API op hoog niveau in python. Het kan worden uitgevoerd op tensor flow, Microsoft Cognitive Toolkit of Theano. Deze is beschikbaar in de hoofd-en py35 python-omgeving.

### <a name="mxnet"></a>MXNet

MXNet is een deep learning-structuur die is ontworpen voor zowel efficiëntie en flexibiliteit. R en Python-bindingen op de DSVM heeft. Voorbeeldnotitieblokken zijn opgenomen in JupyterHub en voorbeeldcode is beschikbaar in /dsvm/samples/mxnet.

### <a name="nvidia-digits"></a>NVIDIA CIJFERS

Het trainings systeem van de NVIDIA-Learning GPU, ook wel cijfers genoemd, is een systeem om veelvoorkomende, uitgebreide leer taken te vereenvoudigen. Deze taken omvatten het beheren van gegevens, het ontwerpen en trainen van Neural netwerken op GPU-systemen en het bewaken van prestaties in realtime met geavanceerde visualisatie.

CIJFERS zijn beschikbaar als een service met de naam *cijfers*. Start de service en blader naar http://localhost:5000 aan de slag.

CIJFERS is ook geïnstalleerd als een Python-module in de omgeving Conda-hoofdmap.

### <a name="tensorflow"></a>TensorFlow

TensorFlow is deep learning-bibliotheek van Google. Het is een open-source software bibliotheek voor numerieke berekening met behulp van gegevens stroom grafieken. TensorFlow is beschikbaar in de py35 Python-omgeving, en sommige voorbeeldnotitieblokken zijn opgenomen in JupyterHub.

### <a name="theano"></a>Theano

Theano is een Python-bibliotheek voor efficiënte numerieke berekeningen. Deze is beschikbaar in de hoofd-en py35 python-omgeving. 

### <a name="torch"></a>Torch

Torch is een wetenschappelijke computing structuur met brede ondersteuning voor machine learning-algoritmen. Het is beschikbaar in/dsvm/tools/Torch en **de interactieve sessie** en LuaRocks package manager zijn beschikbaar op de opdracht regel. Voorbeelden zijn beschikbaar in /dsvm/samples/torch.

PyTorch is ook beschikbaar in de hoofdmap Anaconda-omgeving. Voorbeelden zijn in /dsvm/samples/pytorch.

## <a name="microsoft-machine-learning-server"></a>Microsoft Machine Learning Server

R is een van de meest populaire talen voor data-analyse en machine learning. Als u R voor uw analyse wilt gebruiken, is de VM Microsoft Machine Learning Server met micro soft R open en de reken kundige kernel-bibliotheek. De wiskundige kernelmodus optimaliseert wiskundige bewerkingen die gebruikelijk zijn in analytische algoritmen. Micro soft R open is 100 procent compatibel met de KRANs R en een van de R-bibliotheken die in KRANs zijn gepubliceerd, kan worden geïnstalleerd in micro soft R open. 

Machine Learning Server biedt u schaal-en uitoefening van R-modellen in webservices. U kunt uw R-programma's in een van de standaard-editors, zoals RStudio, vi of Emacs bewerken. Als u liever met behulp van de editor Emacs, is deze vooraf worden geïnstalleerd. Het emacs ESS (Emacsse Statistics)-pakket vereenvoudigt het werken met R-bestanden in de Emacs-editor.

Als u de R-console wilt openen, voert u **R** in de shell in. Met deze opdracht gaat u naar een interactieve omgeving. Voor het ontwikkelen van uw R-programma gebruikt u doorgaans een editor zoals Emacs of VI en voert u de scripts uit in R. Met RStudio beschikt u over een volledige grafische IDE om uw R-programma te ontwikkelen.

Er is ook een R-script waarmee u de [beste 20 R-pakketten](https://www.kdnuggets.com/2015/06/top-20-r-packages.html) kunt installeren als u wilt. U kunt dit script uitvoeren nadat u zich in de R-interactieve interface bevindt. Zoals eerder vermeld, kunt u die interface openen door **R** in de shell in te voeren.  

## <a name="python"></a>Python

Anaconda Python is geïnstalleerd met Python 2.7 en 3,5-omgevingen. De 2,7 omgeving heet _hoofdmap_, en de 3,5 omgeving heet _py35_. Deze verdeling bevat de basis Python samen met ongeveer 300 van de meest populaire math, engineering en data analytics-pakketten.

De omgeving py35 is de standaardinstelling. Gebruik deze opdracht om de basis omgeving (2,7) te activeren:

```bash
source activate root
```

Gebruik deze opdracht om de py35-omgeving opnieuw te activeren:

```bash
source activate py35
```

Als u een een python-interactieve sessie wilt aanroepen, voert u **python** in de shell in. 

Aanvullende python-bibliotheken installeren met behulp van Conda of PIP. Voor PIP moet u eerst de juiste omgeving activeren als u niet de standaard waarde wilt:

```bash
source activate root
pip install <package>
```

Of geef het volledige pad naar het pip:

```bash
/anaconda/bin/pip install <package>
```

Voor Conda moet u altijd de omgevings naam (py35 of root) opgeven:

```bash
conda install <package> -n py35
```

Als u een grafische interface gebruikt of X11 door sturen hebt ingesteld, kunt u **pycharm** invoeren om de PYCHARM python IDE te openen. U kunt de standaard-teksteditors. Daarnaast kunt u Spyder gebruiken, een python IDE die is gebundeld met Anaconda python-distributies. Spyder moet een grafische bureaublad of X11 doorsturen. Het grafische bureau blad heeft een snelkoppeling naar Spyder.

## <a name="jupyter-notebook"></a>Jupyter Notebook

De distributie Anaconda wordt geleverd met een Jupyter-notebook, een omgeving voor het delen van code en -analyse. De Jupyter-notebook toegankelijk is via JupyterHub. U meldt zich aan met uw lokale Linux-gebruikers naam en-wacht woord.

De Jupyter-notebook-server is vooraf geconfigureerd met Python 2, 3 van Python en R-kernels. Gebruik het pictogram **Jupyter notebook** bureau blad om de browser te openen en toegang te krijgen tot de notebook server. Als u gebruikmaakt van de virtuele machine via SSH of de X2Go-client, kunt u ook toegang krijgen tot de [https://localhost:8000/](https://localhost:8000/)Jupyter-notebook server op.

> [!NOTE]
> Doorgaan als u certificaatwaarschuwingen.

U kunt de Jupyter-notebook-server openen vanaf elke host. Voer **de\<DNS-naam of het IP\>-adres van de https://-vm in: 8000/** .

> [!NOTE]
> Poort 8000 wordt standaard in de firewall geopend wanneer de virtuele machine is ingericht. 

We hebben voorbeeld notitieblokken, een in Python en één in R, verpakt. U kunt de koppeling naar de voor beelden op de start pagina van het notitie blok zien nadat u zich bij de Jupyter-notebook hebt geverifieerd met behulp van uw lokale Linux-gebruikers naam en-wacht woord. U kunt een nieuw notitie blok maken door **Nieuw**te selecteren en vervolgens de juiste taal-kernel te selecteren. Als de knop **Nieuw** niet wordt weer gegeven, selecteert u het **Jupyter** pictogram in de linkerbovenhoek om naar de start pagina van de notebook server te gaan.

## <a name="apache-spark-standalone"></a>Zelfstandige Apache Spark

Een zelfstandig exemplaar van Apache Spark is vooraf geïnstalleerd op de Linux-DSVM om u te helpen bij het lokaal ontwikkelen van Spark-toepassingen voordat u ze test en implementeert op grote clusters. 

U kunt de PySpark-programma's uitvoeren via de Jupyter-kernel. Wanneer u Jupyter opent, selecteert u de knop **Nieuw** en ziet u een lijst met beschik bare kernels. **Spark-python** is de PySpark-kernel waarmee u Spark-toepassingen kunt bouwen met behulp van de python-taal. U kunt ook een python IDE zoals PyCharm of Spyder gebruiken om uw Spark-programma te bouwen. 

In deze zelfstandige instantie wordt de Spark-stack uitgevoerd binnen het aanroepende client programma. Met deze functie kunt u sneller en eenvoudiger problemen oplossen, vergeleken met het ontwikkelen van een Spark-cluster.

Jupyter biedt een voor beeld van een PySpark-notebook. U vindt deze in de SparkML in de hoofdmap van Jupyter ($HOME/notebooks/SparkML/pySpark). 

Als u programmeert in R voor Spark, kunt u Microsoft Machine Learning Server, Spark of sparklyr gebruiken. 

Voordat u uitvoert in een Spark-context in Microsoft Machine Learning Server, moet u een eenmalige installatie stap uitvoeren om een lokaal Hadoop-en garen-exemplaar met één knoop punt in te scha kelen. Hadoop-services worden standaard geïnstalleerd maar uitgeschakeld op de DSVM. Als u deze wilt inschakelen, moet u de volgende opdrachten uitvoeren als hoofd de eerste keer:

```bash
echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
chmod 0600 ~hadoop/.ssh/authorized_keys
chown hadoop:hadoop ~hadoop/.ssh/id_rsa
chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
systemctl start hadoop-namenode hadoop-datanode hadoop-yarn
```

U kunt de Hadoop-gerelateerde services stoppen wanneer u deze niet nodig hebt door ```systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn```uit te voeren.

De/dsvm/samples/MRS Directory bevat een voor beeld van het ontwikkelen en testen van Microsoft Machine Learning Server in een externe Spark-context (het zelfstandige Spark-exemplaar op de DSVM).

## <a name="ides-and-editors"></a>IDE's en editors

U kunt kiezen uit verschillende code-editors, waaronder VI/vim, emacs, PyCharm, RStudio en IntelliJ. 

PyCharm, RStudio en IntelliJ zijn grafische editors. Als u deze wilt gebruiken, moet u zijn aangemeld bij een grafisch bureau blad. U opent ze met de snelkoppelingen voor het menu bureau blad en toepassingen.

Vim en emacs zijn op tekst gebaseerde editors. Op emacs maakt het ESS add-on-pakket met R eenvoudiger in de Emacs-editor. Meer informatie vindt u op de [ESS-website](https://ess.r-project.org/).

LaTex wordt geïnstalleerd via het texlive-pakket, samen met een Emacs-invoeg toepassings pakket met de naam [AUCTeX](https://www.gnu.org/software/auctex/manual/auctex/auctex.html). Met dit pakket wordt het ontwerpen van uw LaTex-documenten in Emacs vereenvoudigd.  

## <a name="databases"></a>Databases

### <a name="graphical-sql-client"></a>Grafische SQL-client

SQuirrel SQL, een grafische SQL-Client, kan verbinding maken met verschillende data bases (zoals Microsoft SQL Server en MySQL) en SQL-query's uitvoeren. U kunt SQuirrel SQL uitvoeren vanuit een grafische bureaublad sessie (bijvoorbeeld via de X2Go-client) met behulp van een bureaublad pictogram. U kunt ook de-client uitvoeren met behulp van de volgende opdracht in de shell:

```bash
/usr/local/squirrel-sql-3.7/squirrel-sql.sh
```

Voordat u het eerste gebruik, stuurprogramma's en database-aliassen hebt ingesteld. De JDBC-Stuur Programma's bevinden zich op/usr/share/java/jdbcdrivers.

Zie voor meer informatie, [SQuirrel SQL](http://squirrel-sql.sourceforge.net/index.php?page=screenshots).

### <a name="command-line-tools-for-accessing-microsoft-sql-server"></a>Opdrachtregelprogramma's voor toegang tot Microsoft SQL Server

Het pakket ODBC-stuurprogramma voor SQL Server wordt geleverd met twee opdrachtregelprogramma's:

- **bcp**: Het hulp programma BCP kopieert gegevens tussen een exemplaar van Microsoft SQL Server en een gegevens bestand in een door de gebruiker opgegeven indeling. U kunt het hulp programma BCP gebruiken om grote aantallen nieuwe rijen te importeren in SQL Server tabellen, of om gegevens uit tabellen te exporteren naar gegevens bestanden. Als u gegevens wilt importeren in een tabel, moet u een indelings bestand gebruiken dat voor die tabel is gemaakt. Of u moet inzicht hebben in de structuur van de tabel en de typen gegevens die geldig zijn voor de kolommen.

  Zie voor meer informatie, [verbinding te maken met bcp](https://msdn.microsoft.com/library/hh568446.aspx).

- **sqlcmd**: U kunt Transact-SQL-instructies opgeven met behulp van het hulp programma Sqlcmd. U kunt ook systeem procedures en script bestanden invoeren bij de opdracht prompt. Dit hulp programma gebruikt ODBC om Transact-SQL-batches uit te voeren.

  Zie voor meer informatie, [verbinding te maken met sqlcmd](https://msdn.microsoft.com/library/hh568447.aspx).

  > [!NOTE]
  > Er zijn enkele verschillen in dit hulp programma tussen Linux-en Windows-platforms. Zie de documentatie voor meer informatie.

### <a name="database-access-libraries"></a>Database toegang bibliotheken

Bibliotheken zijn beschikbaar in R en python voor database toegang:

* In R kunt u het RODBC-pakket of het dplyr-pakket gebruiken om SQL-instructies op de database server op te vragen of uit te voeren.
* In python biedt de pyodbc-bibliotheek database toegang met ODBC als de onderliggende laag.  

## <a name="azure-tools"></a>Azure-hulpprogramma 's

De volgende Azure-hulpprogramma's zijn geïnstalleerd op de virtuele machine:

* **Azure CLI**: U kunt de opdracht regel interface in azure gebruiken om Azure-resources te maken en te beheren via shell-opdrachten. Als u de Azure-hulpprogram ma's wilt openen, voert u **Azure Help**in. Zie voor meer informatie de [documentatiepagina van Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).
* **Azure Storage Explorer**: Azure Storage Explorer is een grafisch hulp programma dat u kunt gebruiken om te bladeren door de objecten die u in uw Azure-opslag account hebt opgeslagen, en om gegevens van en naar Azure-blobs te uploaden en te downloaden. U kunt Storage Explorer openen via het pictogram van de snelkoppeling op het bureaublad. U kunt deze ook openen vanuit een shell-prompt door **StorageExplorer**in te voeren. U moet zijn aangemeld bij een X2Go-client of X11 door sturen hebben ingesteld.
* **Azure-bibliotheken**: Hier volgen enkele van de vooraf geïnstalleerde bibliotheken.
  
  * **Python**: De Azure-gerelateerde bibliotheken in python zijn *Azure*, *azureml*, *pydocumentdb*en *pyodbc*. Met de eerste drie bibliotheken, kunt u toegang tot Azure storage-services, Azure Machine Learning en Azure Cosmos DB (een NoSQL-database in Azure). De vierde servicebibliotheek pyodbc (samen met het Microsoft ODBC-stuurprogramma voor SQL Server), kunt u toegang tot SQL Server, Azure SQL Database en Azure SQL Data Warehouse met Python met behulp van een ODBC-interface. Voer **pip lijst** om te zien van de vermelde bibliotheken. Zorg dat u deze opdracht wordt uitgevoerd in de Python 2.7 en 3,5 omgevingen.
  * **R**: De Azure-gerelateerde bibliotheken in R zijn AzureML en RODBC.
  * **Java**: De lijst met Azure Java-bibliotheken vindt u in de map/dsvm/sdk/AzureSDKJava op de virtuele machine. De sleutel-bibliotheken zijn Azure-opslag en beheer van API's, Azure Cosmos DB en JDBC-stuurprogramma's voor SQL Server.  

U hebt toegang tot de [Azure-portal](https://portal.azure.com) van de vooraf geïnstalleerde Firefox-browser. In de Azure-portal, kunt u maken, beheren en controleren van Azure-resources.

## <a name="azure-machine-learning"></a>Azure Machine Learning

Azure Machine Learning is een volledig beheerde cloudservice waarmee u kunt bouwen, implementeren en delen van predictive analytics-oplossingen. U bouwen uw experimenten en modellen in Azure Machine Learning Studio. U kunt deze openen vanuit een webbrowser op de Data Science Virtual Machine door [Microsoft Azure machine learning](https://studio.azureml.net)te bezoeken.

Nadat u zich hebt aangemeld bij Azure Machine Learning Studio, kunt u een Experimenteer teken gebruiken om een logische stroom te maken voor de machine learning-algoritmen. U hebt ook toegang tot een Jupyter-notebook dat wordt gehost op Azure Machine Learning en probleemloos kan samen werken met de experimenten in Machine Learning Studio. 

Operationeel maken van de machine learning-modellen die u hebt gemaakt door ze in een webservice-interface. Met inzet machine learning-modellen kunnen clients die in elke taal zijn geschreven, voor spellingen van deze modellen aanroepen. Zie voor meer informatie de [documentatie voor Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/).

U kunt ook uw modellen bouwen in R of python op de virtuele machine en deze vervolgens implementeren in productie op Azure Machine Learning. Hebben we bibliotheken geïnstalleerd in R (**AzureML**) en Python (**azureml**) om deze functionaliteit te schakelen.

Zie [tien dingen die u kunt doen op de data Science virtual machine](vm-do-ten-things.md)voor meer informatie over het implementeren van modellen in R en Python in azure machine learning.

> [!NOTE]
> Deze instructies zijn geschreven voor de Windows-versie van de Data Science Virtual Machine. Maar de verstrekte informatie bevat over het implementeren van modellen op Azure Machine Learning is van toepassing op de Linux-VM.

## <a name="machine-learning-tools"></a>Hulpprogramma's voor machine learning

De VM wordt geleverd met machine learning-hulpprogram ma's en-algoritmen die vooraf zijn gecompileerd en lokaal zijn geïnstalleerd. Deze omvatten:

* **Vowpal Wabbit**: Een Fast Online Learning-algoritme.
* **xgboost**: Een hulp programma dat geoptimaliseerde, gestimuleerde structuur algoritmen biedt.
* **Rattle**: Een R-gebaseerd grafisch hulp programma voor het snel verkennen en model leren van gegevens.
* **Python**: Anaconda python wordt geleverd met machine learning algoritmen met bibliotheken zoals Scikit. U kunt andere bibliotheken installeren met behulp van de `pip install` opdracht.
* **LightGBM**: Een snelle, gedistribueerde, hoogwaardige kleur overgang voor het verbeteren van het Framework op basis van de beslissings structuur algoritmen.
* **R**: Er is een uitgebreide bibliotheek met machine learning functies beschikbaar voor R. vooraf geïnstalleerde bibliotheken zijn LM, GLM, randomForest en rpart. U kunt andere bibliotheken installeren door deze opdracht uit te voeren:
  
        install.packages(<lib name>)

Hier is aanvullende informatie over de eerste drie machine learning-hulpprogramma's in de lijst.

### <a name="vowpal-wabbit"></a>Vowpal Wabbit

Vowpal Wabbit is een machine learning-systeem dat gebruikmaakt van technieken zoals online, hash, allreduce, kortingen, learning2search, actief, en interactieve training.

Als u het hulp programma wilt uitvoeren op een eenvoudig voor beeld, gebruikt u de volgende opdrachten:

```bash
cp -r /dsvm/tools/VowpalWabbit/demo vwdemo
cd vwdemo
vw house_dataset
```

Er zijn andere, groter demo's in die map. Zie [deze sectie van github](https://github.com/JohnLangford/vowpal_wabbit) en de [Vowpal Wabbit wiki](https://github.com/JohnLangford/vowpal_wabbit/wiki)voor meer informatie over Vowpal Wabbit.

### <a name="xgboost"></a>xgboost

De xgboost-bibliotheek is ontworpen en geoptimaliseerd voor geboostte algoritmen (structuur). Het doel van deze bibliotheek is om de grenzen van de berekening van machines tot het uiterste dat nodig is voor grootschalige structuur versterking die schaalbare, draagbare en nauwkeurige.

Deze wordt weer gegeven als een opdracht regel en een R-bibliotheek. Als u deze bibliotheek in R wilt gebruiken, kunt u een interactieve R-sessie starten (door **R** in de shell in te voeren) en de bibliotheek te laden.

Hier volgt een eenvoudig voor beeld dat u kunt uitvoeren in een R-prompt:

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

Als u de xgboost-opdracht regel wilt uitvoeren, kunt u de volgende opdrachten uitvoeren in de shell:

```bash
cp -r /dsvm/tools/xgboost/demo/binary_classification/ xgboostdemo
cd xgboostdemo
xgboost mushroom.conf
```

Een. model-bestand wordt geschreven naar de opgegeven map. Meer informatie over dit voor beeld van deze demo vindt u [op github](https://github.com/dmlc/xgboost/tree/master/demo/binary_classification).

Zie de [xgboost-documentatie pagina](https://xgboost.readthedocs.org/en/latest/) en de bijbehorende [github-opslag plaats](https://github.com/dmlc/xgboost)voor meer informatie over xgboost.

### <a name="rattle"></a>Rammelaar

Rattle (de **R** **A**analytische **T**lpmiddel **T**o **L**verdienen **E** asily) maakt gebruik van GUI-gebaseerde gegevens verkennen en modelleren. Deze geeft statistische en visuele samenvattingen van gegevens, transformaties die gemakkelijk kan worden gemodelleerd, zonder supervisie en onder supervisie modellen uit de gegevens is gebaseerd, geeft de prestaties van modellen grafische vorm en scores nieuwe gegevens worden ingesteld. Er wordt ook gegenereerd R-code voor het repliceren van de bewerkingen in de gebruikersinterface die kunnen worden rechtstreeks in R uitvoeren of als uitgangspunt gebruikt voor verdere analyse.

Als u wilt uitvoeren Rammelaar, moet u zich in een grafische aanmelden-bureaubladsessie. Voer op de Terminal **r** in om de R-omgeving te openen. Voer de volgende opdrachten bij de R-prompt:

```R
library(rattle)
rattle()
```

Nu wordt een grafische interface geopend met een reeks tabbladen. Gebruik de volgende Snelstartgids-stappen in Rattle om een voor beeld weer te gegeven en een model te bouwen. In sommige stappen wordt u gevraagd om automatisch enkele vereiste R-pakketten te installeren en te laden die nog niet op het systeem zijn geïnstalleerd.

> [!NOTE]
> Als u geen toegang hebt om het pakket te installeren in de systeemmap (de standaard instelling), ziet u mogelijk een prompt op uw R-console venster om pakketten te installeren in uw persoonlijke bibliotheek. Antwoord **y** als u deze stappen ziet.

1. Selecteer **Uitvoeren**.
1. Er wordt een dialoog venster weer gegeven waarin u wordt gevraagd of u de voor beeld-weer gegevens set wilt gebruiken. Selecteer **Ja** om het voor beeld te laden.
1. Selecteer het tabblad **model** .
1. Selecteer **uitvoeren** om een beslissings structuur te maken.
1. Selecteer **tekenen** om de beslissings structuur weer te geven.
1. Selecteer de optie **forest** en selecteer **uitvoeren** om een wille keurig forest te bouwen.
1. Selecteer het tabblad **evalueren** .
1. Selecteer de optie **risico** en selecteer **uitvoeren** om twee **risico's (cumulatief)** prestatie grafieken weer te geven.
1. Selecteer het tabblad **logboek** om de gegenereerde R-code voor de voor gaande bewerkingen weer te geven.
   (Vanwege een fout in de huidige release van Rattle moet u een **#** teken invoegen vóór het exporteren van **Dit logboek** in de tekst van het logboek.)
1. Selecteer de knop **exporteren** om het R-script bestand met de naam weather_script op te slaan *. R* naar de basismap.

U kunt Rattle en R afsluiten. Nu kunt u het gegenereerde R-script wijzigen. Of gebruik het script zoals het is en voer het op elk gewenst moment uit om alles te herhalen dat is uitgevoerd in de Rattle-gebruikers interface. Met name voor beginners in R is dit een manier om snel analyses en machine learning uit te voeren in een eenvoudige grafische interface, terwijl automatisch code wordt gegenereerd in R om te wijzigen of te leren.

## <a name="next-steps"></a>Volgende stappen

Hebt u nog meer vragen? Overweeg een [ondersteunings ticket](https://azure.microsoft.com/support/create-ticket/)te maken.