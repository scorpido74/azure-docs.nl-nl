---
title: 'Referentie: CentOS Data Science Virtual Machine'
titleSuffix: Azure Data Science Virtual Machine
description: Details over tools in de CentOS Data Science Virtual Machine
author: gvashishtha
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: gopalv
ms.date: 09/11/2019
ms.topic: reference
ms.openlocfilehash: db49a9f5e0e6675d93cb58d6af9c92fac21e8b74
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77525835"
---
# <a name="reference-centos-linux-data-science-virtual-machine"></a>Referentie: CentOS (Linux) Data Science Virtual Machine

De Linux Data Science Virtual Machine (DSVM) is een op CentOS gebaseerde Azure virtuele machine. De Linux DSVM wordt geleverd met een verzameling vooraf geïnstalleerde tools die u gebruiken voor data-analyse en machine learning. 

De belangrijkste softwarecomponenten in een Linux DSVM zijn:

* Linux CentOS distributie besturingssysteem.
* Microsoft Machine Learning Server.
* Anaconda Python-distributie (versies 3.5 en 2.7), inclusief populaire gegevensanalysebibliotheken.
* JuliaPro, een samengestelde distributie van de Julia taal en populaire wetenschappelijke en data analytics bibliotheken.
* Spark Standalone-exemplaar en hadoop met één knooppunt (HDFS, GAREN).
* JupyterHub, een multiuser Jupyter-notebookserver die R-, Python-, PySpark- en Julia-kernels ondersteunt.
* Azure Storage Explorer.
* Azure CLI, de Azure-opdrachtregelinterface voor het beheer van Azure-resources.
* PostgresSQL-database.
* Machine learning tools:
  * [Microsoft Cognitive Toolkit](https://github.com/Microsoft/CNTK) (CNTK), een deep learning software toolkit van Microsoft Research.
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit), een snel machine learning systeem dat technieken zoals online, hashing, allreduce, reducties, learning2search, actief en interactief leren ondersteunt.
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/), een tool die snelle en nauwkeurige boost tree implementatie biedt.
  * [Rattle](https://togaware.com/rattle/), een tool die het aan de slag met data analytics en machine learning in R eenvoudig maakt. Rattle biedt zowel GUI-gebaseerde data-exploratie en modellering met behulp van automatische R-code generatie.
* Azure SDK in Java, Python, Node.js, Ruby en PHP.
* Bibliotheken in R en Python die kunnen worden gebruikt in Azure Machine Learning en andere Azure-services.
* Ontwikkeltools en -editors (RStudio, PyCharm, IntelliJ, Emacs, gedit, vi).

Data science houdt in dat een reeks taken wordt uitgevoerd:

1. Gegevens zoeken, laden en vooraf verwerken.
1. Bouw en test modellen.
1. Implementeer de modellen voor consumptie in intelligente toepassingen.

Gegevenswetenschappers gebruiken verschillende tools om deze taken uit te voeren. Het kan tijdrovend zijn om de juiste versies van de software te vinden en vervolgens de software te downloaden, compileren en installeren.

De Linux DSVM kan deze last aanzienlijk verlichten. Gebruik de Linux DSVM om uw analyseproject een vliegende start te geven. De Linux DSVM helpt u te werken aan taken in verschillende talen, waaronder R, Python, SQL, Java en C++. Eclipse biedt een gebruiksvriendelijke IDE voor het ontwikkelen en testen van uw code. De Azure SDK, opgenomen in de DSVM, helpt u bij het bouwen van uw toepassingen door gebruik te maken van verschillende services op Linux voor het Microsoft-cloudplatform. Andere talen zijn vooraf geïnstalleerd, waaronder Ruby, Perl, PHP en Node.js.

Voor de DSVM-installatiekopie worden geen softwarekosten in rekening gebracht. U betaalt alleen de azure-hardwaregebruikskosten die worden beoordeeld op basis van de grootte van de virtuele machine die u indient met de DSVM-afbeelding. Zie de Data Science Virtual [Machine for Linux (CentOS) vermelding](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/) in Azure Marketplace voor meer informatie over de rekenkosten.


## <a name="machine-learning-server"></a>Machine Learning-server

R is een van de meest populaire talen voor data-analyse en machine learning. Als u R wilt gebruiken voor uw analyses, heeft de DSVM Machine Learning Server met Microsoft R Open- en Math Kernel Library. Math Kernel Library optimaliseert gangbare wiskundige bewerkingen in analytische algoritmen. R Open is volledig compatibel met CRAN R. Elk van de R-bibliotheken gepubliceerd in CRAN kan worden geïnstalleerd op R Open. 

U Machine Learning Server gebruiken om R-modellen te schalen en te operationaliseren in webservices. U uw R-programma's bewerken in een van de standaardeditors, zoals RStudio, vi of Emacs. De Emacs-editor is vooraf geïnstalleerd op de DSVM. Het Emacs ESS -pakket (Emacs Speaks Statistics) vereenvoudigt het werken met R-bestanden in de Emacs-editor.

Als u de R-console wilt openen, voert u in de shell **R**in. Deze opdracht neemt je mee naar een interactieve omgeving. Om uw R-programma te ontwikkelen, gebruikt u meestal een editor zoals Emacs of vi, en vervolgens de scripts in R. RStudio biedt een volledige grafische IDE voor het ontwikkelen van uw R-programma.

Een R-script dat u gebruiken om de [top 20 R-pakketten](https://www.kdnuggets.com/2015/06/top-20-r-packages.html) te installeren, is opgenomen in de DSVM. U dit script uitvoeren wanneer u zich in de interactieve R-interface bevindt. Zoals eerder vermeld, om die interface te openen, in de shell, voer **R**.  

## <a name="python"></a>Python

Anaconda Python is geïnstalleerd met de Python 3.5 en 2.7 omgevingen. De 2.7 omgeving heet _wortel_ en de 3.5 omgeving heet _py35_. Deze distributie bevat de basis Python samen met ongeveer 300 van de meest populaire wiskunde, engineering, en data analytics pakketten.

De py35-omgeving is de standaardinstelling. Als u de hoofdomgeving (2.7) wilt activeren, gebruikt u deze opdracht:

```bash
source activate root
```

Als u de py35-omgeving opnieuw wilt activeren, gebruikt u de opdracht:

```bash
source activate py35
```

Als u een interactieve Python-sessie wilt aanroepen, voert u in de shell **python**in. 

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

## <a name="jupyter-notebook"></a>Jupyter Notebook

De Anaconda-distributie wordt ook geleverd met een Jupyter Notebook, een omgeving om code en analyse te delen. Toegang tot de Jupyter Notebook via JupyterHub. U meldt zich aan met uw lokale Linux gebruikersnaam en wachtwoord.

De Jupyter Notebook-server is vooraf geconfigureerd met Python 2-, Python 3- en R-kernels. Gebruik het bureaubladpictogram **Jupyter Notebook** om de browser te openen en toegang te krijgen tot de Jupyter Notebook-server. Als u de DSVM via SSH of de X2Go-client opent, heeft u\/ook toegang tot de Jupyter Notebook-server op https: /localhost:8000/.

> [!NOTE]
> Ga door als u certificaatwaarschuwingen krijgt.

U hebt toegang tot de Jupyter-notebookserver vanaf elke host. Voer **https\//\<in: DSVM\>DNS-naam of IP-adres :8000/**.

> [!NOTE]
> Poort 8000 wordt standaard geopend in de firewall wanneer de DSVM is ingericht. 

Microsoft heeft verpakte voorbeeldnotebooks, een in Python en een in R. U de koppeling naar de voorbeelden op de startpagina van Jupyter Notebook bekijken nadat u zich hebt geverifieerd naar het Jupyter-notitieblok met behulp van uw lokale Linux-gebruikersnaam en -wachtwoord. Als u een nieuw notitieblok wilt maken, selecteert u **Nieuw**en selecteert u vervolgens de taalkernel die u wilt gebruiken. Als u de knop **Nieuw** niet ziet, selecteert u het pictogram **Jupyter** linksboven om naar de startpagina van de notitieblokserver te gaan.

## <a name="spark-standalone"></a>Spark Standalone 

Een exemplaar van de Spark Standalone-modus is vooraf geïnstalleerd op de Linux DSVM om u te helpen Spark-toepassingen lokaal te ontwikkelen voordat u ze test en implementeert op grote clusters. 

U PySpark-programma's uitvoeren via de Jupyter-kernel. Wanneer u Jupyter opent, selecteert u de knop **Nieuw** en ziet u een lijst met beschikbare kernels. **Spark - Python** is de PySpark-kernel waarmee je Spark-toepassingen bouwen met behulp van de Python-taal. Je ook een Python IDE zoals PyCharm of Spyder gebruiken om je Spark-programma te bouwen. 

In deze zelfstandige instantie wordt de Spark-stack uitgevoerd in het belclientprogramma. Deze functie maakt het sneller en gemakkelijker om problemen op te lossen in vergelijking met het ontwikkelen op een Spark-cluster.

Jupyter biedt een voorbeeld van PySpark-laptop. Je vindt het in de SparkML-map onder de homedirectory van Jupyter ($HOME/notebooks/SparkML/pySpark). 

Als u programmeert in R voor Spark, u Machine Learning Server, SparkR of sparklyr gebruiken. 

Voordat u in een Spark-context in Machine Learning Server wordt uitgevoerd, moet u een eenmalige installatiestap uitvoeren om een lokale hadoop HDFS- en YARN-instantie met één knooppunt in te schakelen. Standaard zijn Hadoop-services geïnstalleerd, maar uitgeschakeld op de DSVM. Als u Hadoop-services wilt inschakelen, voert u de volgende opdrachten de eerste keer uit als hoofd:

```bash
echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
chmod 0600 ~hadoop/.ssh/authorized_keys
chown hadoop:hadoop ~hadoop/.ssh/id_rsa
chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
systemctl start hadoop-namenode hadoop-datanode hadoop-yarn
```

U de Hadoop-gerelateerde services stoppen wanneer u `systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn`ze niet nodig hebt door te draaien.

De map /dsvm/samples/MRS biedt een voorbeeld dat laat zien hoe machine learning-server kan worden ontwikkeld en getest in een externe Spark-context (de zelfstandige Spark-instantie op de DSVM).

## <a name="ides-and-editors"></a>IME's en redacteuren

U kiezen uit verschillende codeeditors, waaronder vi/VIM, Emacs, gedit, PyCharm, RStudio, Eclipse, LaTeX en IntelliJ. 

* gedit, Eclipse, IntelliJ, R Studio en PyCharm zijn grafische editors. Als u ze wilt gebruiken, moet u zijn aangemeld bij een grafisch bureaublad. U opent ze met behulp van snelkoppelingen in het bureaublad- en toepassingsmenu.

* Vim en Emacs zijn tekstgebaseerde redacteuren. Op Emacs maakt het ESS-add-on pakket het werken met R in de Emacs-editor eenvoudiger. Meer informatie vindt u op de [ESS website.](https://ess.r-project.org/)

* Eclipse is een open-source, uitbreidbare IDE die meerdere talen ondersteunt. Eclipse IDE voor Java Developers is de versie geïnstalleerd op de DSVM. U plug-ins voor verschillende populaire talen installeren om de omgeving uit te breiden. 

  De Azure Toolkit voor Eclipse plug-in is geïnstalleerd met Eclipse op de DSVM. U Azure Toolkit voor Eclipse gebruiken om Azure-toepassingen te maken, te ontwikkelen, te testen en te implementeren met behulp van de ontwikkelomgeving Eclipse die talen zoals Java ondersteunt.

  De Azure SDK voor Java is ook geïnstalleerd met de Azure Toolkit voor Eclipse op de DSVM. De Azure SDK voor Java geeft u toegang tot verschillende Azure-services vanuit een Java-omgeving. 
  
  Zie [Azure-toolkit voor Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse)voor meer informatie.

* LaTeX is geïnstalleerd via het texlive pakket, samen met een Emacs add-on pakket genaamd [AUCTeX](https://www.gnu.org/software/auctex/manual/auctex/auctex.html). Dit pakket vereenvoudigt het schrijven van uw LaTeX-documenten in Emacs. 

## <a name="databases"></a>Databases

De Linux DSVM geeft u toegang tot verschillende database- en command-line tools.

### <a name="postgressql"></a>PostgresSQL

De open-source database PostgresSQL is beschikbaar op de DSVM, met services draaien en initdb voltooid. U moet databases en gebruikers maken. Zie voor meer informatie de [PostgresSQL-documentatie](https://www.postgresql.org/docs/).  

### <a name="squirrel-sql"></a>SQuirreL SQL

SQuirreL SQL is een grafische SQL-client die verbinding kan maken met verschillende databases (waaronder SQL Server, PostgresSQL en MySQL) en SQL-query's kan uitvoeren. U SQuirreL SQL uitvoeren vanuit een grafische desktopsessie (bijvoorbeeld via de X2Go-client) met behulp van een bureaubladpictogram. U de client ook uitvoeren met de volgende opdracht in de shell:

```bash
/usr/local/squirrel-sql-3.7/squirrel-sql.sh /usr/local/squirrel-sql-3.7/squirrel-sql.sh
```

Stel voor het eerste gebruik uw stuurprogramma's en databasealiassen in. De JDBC drivers bevinden zich op /usr/share/java/jdbcdrivers.

Zie [SQuirreL SQL voor](http://squirrel-sql.sourceforge.net/index.php?page=screenshots)meer informatie.

### <a name="command-line-tools-for-accessing-sql-server"></a>Opdrachtregelgereedschappen voor toegang tot SQL Server

Het ODBC-stuurprogrammapakket voor SQL Server wordt ook geleverd met twee command-line-tools:

* **bcp:** het bcp-hulpprogramma kopieert gegevens tussen een instantie van SQL Server en een gegevensbestand in een door de gebruiker opgegeven indeling. U het bcp-hulpprogramma gebruiken om grote aantallen nieuwe rijen in SQL Server-tabellen te importeren of om gegevens uit tabellen naar gegevensbestanden te exporteren. Als u gegevens wilt importeren in een tabel, moet u een indelingsbestand gebruiken dat voor die tabel is gemaakt. U moet ook de structuur van de tabel en de typen gegevens begrijpen die geldig zijn voor de kolommen.

  Zie [Verbinding maken met bcp](https://msdn.microsoft.com/library/hh568446.aspx)voor meer informatie .

* **sqlcmd**: U het sqlcmd-hulpprogramma gebruiken om Transact-SQL-instructies, systeemprocedures en scriptbestanden in te voeren bij de opdrachtprompt. Het sqlcmd-hulpprogramma maakt gebruik van ODBC om Transact-SQL-batches uit te voeren.

  Zie [Verbinding maken met sqlcmd](https://msdn.microsoft.com/library/hh568447.aspx)voor meer informatie.

  > [!NOTE]
  > Er zijn enkele verschillen in deze tool tussen Linux en Windows-platforms. Zie de documentatie voor meer informatie.

### <a name="database-access-libraries"></a>Databasetoegangsbibliotheken

Bibliotheken voor databasetoegang zijn beschikbaar in R en Python:

* In R u het RODBC-pakket of het dplyr-pakket gebruiken om SQL-instructies op de databaseserver op te vragen of uit te voeren.
* In Python biedt de pyodbc-bibliotheek databasetoegang met ODBC als onderliggende laag.

## <a name="azure-tools"></a>Azure-hulpprogramma's

De volgende Azure-hulpprogramma's zijn geïnstalleerd op de DSVM:

* **Azure CLI**: U de opdrachtregelinterface in Azure gebruiken om Azure-resources te maken en te beheren via shell-opdrachten. Als u de Azure-hulpprogramma's wilt openen, voert u **azure help**in. Zie de [documentatiepagina van Azure CLI voor](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)meer informatie .
* **Azure Storage Explorer**: Azure Storage Explorer is een grafisch hulpmiddel dat u gebruiken om door de objecten te bladeren die u hebt opgeslagen in uw Azure-opslagaccount en om gegevens van en naar Azure blobs te uploaden en te downloaden. U hebt toegang tot Storage Explorer via het pictogram voor snelkoppelingen op het bureaublad. U het ook openen vanuit een shell prompt door **StorageExplorer**in te voeren. U moet zijn aangemeld bij een X2Go-client of x11-forwarding hebben ingesteld.
* **Azure-bibliotheken:** de volgende bibliotheken zijn vooraf geïnstalleerd op de DSVM:
  
  * **Python**: De Azure-gerelateerde bibliotheken in Python zijn *azure,* *azureml,* *pydocumentdb*en *pyodbc*. Met de eerste drie bibliotheken hebt u toegang tot Azure-opslagservices, Azure Machine Learning en Azure Cosmos DB (een NoSQL-database op Azure). De vierde bibliotheek, pyodbc (samen met het Microsoft ODBC-stuurprogramma voor SQL Server), biedt toegang tot SQL Server, Azure SQL Database en Azure SQL Data Warehouse van Python met behulp van een ODBC-interface. Voer **de pip-lijst** in om alle vermelde bibliotheken te bekijken. Zorg ervoor dat u deze opdracht uitvoert in zowel de Python 2.7- als 3.5-omgevingen.
  * **R:** De Azure-gerelateerde bibliotheken in R zijn AzureML en RODBC.
  * **Java**: De lijst met Azure Java-bibliotheken is te vinden in de map /dsvm/sdk/AzureSDKJava op de DSVM. De belangrijkste bibliotheken zijn Azure-opslag- en beheer-API's, Azure Cosmos DB- en JDBC-stuurprogramma's voor SQL Server.  

U hebt toegang tot de [Azure-portal](https://portal.azure.com) vanuit de vooraf geïnstalleerde Firefox-browser. In de Azure-portal u Azure-resources maken, beheren en bewaken.

## <a name="azure-machine-learning"></a>Azure Machine Learning

Azure Machine Learning is een volledig beheerde cloudservice die u gebruiken om voorspellende analyseoplossingen te bouwen, implementeren en delen. U bouwt uw experimenten en modellen vanuit de Azure Machine Learning Studio (klassiek). Als u Azure Machine Learning wilt openen vanuit een webbrowser op de DSVM, gaat u naar microsoft [Azure Machine Learning](https://studio.azureml.net).

Nadat u zich hebt aangemeld bij Azure Machine Learning Studio (klassiek), u een experimenteercanvas gebruiken om een logische stroom voor de machine learning-algoritmen op te bouwen. U hebt ook toegang tot een Jupyter-notitieblok dat wordt gehost op Azure Machine Learning. Het notitieblok kan naadloos samenwerken met de experimenten in Azure Machine Learning Studio (klassiek). 

Operationaliseer de machine learning-modellen die u bouwt door ze in een webservice-interface te verpakken. Door machine learning-modellen te operationaliseren, kunnen klanten die in elke taal worden geschreven, voorspellingen van die modellen aanroepen. Zie de machine [learning-documentatie](https://azure.microsoft.com/documentation/services/machine-learning/)voor meer informatie.

U uw modellen ook in R of Python bouwen op de DSVM en ze vervolgens in productie implementeren op Azure Machine Learning. Microsoft heeft bibliotheken geïnstalleerd in R **(AzureML)** en Python **(azureml)** om deze functionaliteit te ondersteunen.

Zie [Tien dingen die u doen op de Virtual Machine Machine Data.](vm-do-ten-things.md)

> [!NOTE]
> De instructies in [Tien dingen die je doen op de Data Science Virtual Machine](vm-do-ten-things.md) zijn geschreven voor de Windows-versie van de DSVM. De informatie over het implementeren van modellen naar Azure Machine Learning is echter ook van toepassing op de Linux DSVM.

## <a name="machine-learning-tools"></a>Machine learning-tools

De DSVM wordt geleverd met een paar machine learning tools en algoritmen die vooraf zijn samengesteld en vooraf geïnstalleerd lokaal. Deze omvatten:

* **Microsoft Cognitive Toolkit**: Een deep learning toolkit.
* **Vowpal Wabbit**: Een snel online leeralgoritme.
* **XGBoost:** een tool die geoptimaliseerde, gepromote boomalgoritmen biedt.
* **Python**: Anaconda Python wordt geleverd met machine learning-algoritmen met bibliotheken zoals Scikit-learn. U andere bibliotheken installeren `pip install` met behulp van de opdracht.
* **R:** Een uitgebreide bibliotheek met machine learning-functies is beschikbaar voor R. Vooraf geïnstalleerde bibliotheken zijn lm, glm, randomForest en rpart. U andere bibliotheken `install.packages(<lib name>)`installeren door.

Microsoft Cognitive Toolkit, Vowpal Wabbit en XGBoost worden in de volgende secties nader besproken.

### <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit

Microsoft Cognitive Toolkit is een open-source deep learning toolkit. Het is een command-line tool (CNTK) en is al in de PATH.

Als u een basisvoorbeeld wilt uitvoeren, voert u de volgende opdrachten in de shell uit:

```bash
cd /home/[USERNAME]/notebooks/CNTK/HelloWorld-LogisticRegression
cntk configFile=lr_bs.cntk makeMode=false command=Train
```

Zie voor meer informatie de [GitHub CNTK repository](https://github.com/Microsoft/CNTK) en de [CNTK wiki.](https://github.com/Microsoft/CNTK/wiki)

### <a name="vowpal-wabbit"></a>Vowpal Wabbit

Vowpal Wabbit is een machine learning systeem dat gebruik maakt van technieken zoals online, hashing, allreduce, reducties, learning2search, actief en interactief leren.

Voer de volgende opdrachten uit om het gereedschap in een basisvoorbeeld uit te voeren:

```bash
cp -r /dsvm/tools/VowpalWabbit/demo vwdemo
cd vwdemo
vw house_dataset
```

De Vowpal Wabbit demo directory bevat andere, grotere demo's. Zie voor meer informatie over Vowpal Wabbit de [GitHub Vowpal Wabbit repository](https://github.com/JohnLangford/vowpal_wabbit) en de [Vowpal Wabbit wiki.](https://github.com/JohnLangford/vowpal_wabbit/wiki)

### <a name="xgboost"></a>XGBoost

De XGBoost-bibliotheek is ontworpen en geoptimaliseerd voor gebooste (boom)algoritmen. Het doel van de XGBoost-bibliotheek is om de berekeningslimieten van machines tot het uiterste te verleggen dat nodig is om grootschalige boomboosting te bieden die schaalbaar, draagbaar en nauwkeurig is.

XGBoost wordt geleverd als een opdrachtregel en als Een R-bibliotheek.

Als u de XGBoost-bibliotheek in R wilt gebruiken, start u een interactieve R-sessie (in de shell, voert **U R**in) en laadt u de bibliotheek.

Hier is een eenvoudig voorbeeld dat u uitvoeren bij de R-prompt:

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

Als u de xgboost-opdrachtregel wilt uitvoeren, voert u de volgende opdrachten uit in de shell:

```bash
cp -r /dsvm/tools/xgboost/demo/binary_classification/ xgboostdemo
cd xgboostdemo
xgboost mushroom.conf
```

Een .model-bestand wordt naar de opgegeven map geschreven. Zie [Binaire classificatie](https://github.com/dmlc/xgboost/tree/master/demo/binary_classification)voor informatie over dit demovoorbeeld op GitHub.

Zie voor meer informatie over XGBoost de [XGBoost-documentatie](https://xgboost.readthedocs.org/en/latest/) en de [XGBoost GitHub-repository.](https://github.com/dmlc/xgboost)

### <a name="rattle"></a>Rattle

Rattle (*R* *A*nalytical *T*ool *T*o *L*verdienen *E*asily) maakt gebruik van GUI-gebaseerde data exploratie en modellering. Rattle:
- Presenteert statistische en visuele samenvattingen van gegevens.
- Hiermee worden gegevens omgezet die gemakkelijk kunnen worden gemodelleerd.
- Bouwt zowel onbewaakte als gecontroleerde modellen uit data.
- Presenteert de prestaties van modellen grafisch.
- Hiermee scoort u nieuwe gegevenssets.
- Genereert R-code.
- Hiermee worden bewerkingen in de gebruikersinterface gerepliceerd die rechtstreeks in R kunnen worden uitgevoerd of als uitgangspunt kunnen worden gebruikt voor meer analyse.

Als u Rattle wilt uitvoeren, moet u zijn aangemeld bij een grafische desktopsessie. Voer in een terminal **R** in om de R-omgeving te openen. Voer bij de R-prompt de volgende opdrachten in:

```R
library(rattle)
rattle()
```

Er wordt een grafische interface geopend met een reeks tabbladen. Gebruik de volgende snelstartstappen in Rattle om een voorbeeldvan weergegevensset te gebruiken en een model te maken. In sommige stappen wordt u gevraagd om een aantal vereiste R-pakketten die nog niet op het systeem staan automatisch te installeren en te laden.

> [!NOTE]
> Als u geen machtigingen hebt om het pakket te installeren in de systeemmap (de standaardinstelling), ziet u mogelijk een prompt in uw R-consolevenster om pakketten in uw persoonlijke bibliotheek te installeren. Als u deze aanwijzingen ziet, voert u **y**in.

1. Selecteer **Uitvoeren**.
1. In een dialoogvenster wordt u gevraagd de voorbeeldgegevensset voor weergegevens te laden. Selecteer **Ja** om het voorbeeld te laden.
1. Selecteer het tabblad **Model.**
1. Selecteer **Uitvoeren** om een beslissingsstructuur te maken.
1. Selecteer **Tekenen** om de beslissingsstructuur weer te geven.
1. Selecteer de optie **Forest** en selecteer **Uitvoeren** om een willekeurig forest te maken.
1. Selecteer het tabblad **Evalueren.**
1. Selecteer de optie **Risico** en selecteer **Uitvoeren** om twee **prestatiepunten voor risico's weer** te geven.
1. Selecteer het tabblad **Logboek** om de gegenereerde R-code voor de voorgaande bewerkingen weer te geven. (Vanwege een bug in de huidige release van **#** Rattle, moet u een teken invoegen voor **Exporteren dit logboek** in de tekst van het logboek.)
1. Selecteer de knop **Exporteren** om het R-scriptbestand met de naam weather_script op te *slaan. R* naar de thuismap.

Je Rattle en R verlaten. Nu u het gegenereerde R-script wijzigen. Of gebruik het script zoals het is, en voer het op elk gewenst moment uit om alles te herhalen wat in de Rattle UI is gedaan. Vooral voor beginners in R is dit een manier om snel analyse en machine learning te doen in een eenvoudige grafische interface, terwijl het automatisch genereren van code in R om te wijzigen of om te leren.

## <a name="next-steps"></a>Volgende stappen

Hebt u meer vragen? Overweeg om een [ondersteuningsticket te maken.](https://azure.microsoft.com/support/create-ticket/)