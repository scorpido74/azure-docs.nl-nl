---
title: Maken van een CentOS Linux Data Science Virtual Machine
titleSuffix: Azure
description: Een Linux-Data Science Virtual Machine in azure maken en configureren voor analyses en machine learning.
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.custom: seodec18
ms.assetid: 3bab0ab9-3ea5-41a6-a62a-8c44fdbae43b
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 03/16/2018
ms.author: vijetaj
ms.openlocfilehash: c0464253c55aa5e51e8e86686405ea6b107c8382
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/27/2019
ms.locfileid: "70047723"
---
# <a name="provision-a-linux-centos-data-science-virtual-machine-in-azure"></a>Een Linux CentOS-Data Science Virtual Machine inrichten in azure

De Linux-Data Science Virtual Machine (DSVM) is een Azure-virtuele machine op basis van CentOS. Linux DSVM wordt geleverd met een verzameling vooraf geïnstalleerde hulpprogram ma's die u kunt gebruiken voor gegevens analyse en machine learning. 

De belangrijkste software onderdelen die deel uitmaken van een Linux-DSVM zijn:

* Linux CentOS-distributie besturingssysteem.
* Microsoft Machine Learning Server.
* Anaconda python-distributie (versies 3,5 en 2,7), met inbegrip van populaire gegevens analyse bibliotheken.
* JuliaPro, een gespreide distributie van de Julia-taal en populaire weten schappelijke en Data Analytics-bibliotheken.
* Het zelfstandige Spark-exemplaar en Hadoop met één knoop punt (HDFS, GARENs).
* JupyterHub, een Jupyter-notebook server voor meerdere gebruikers die R-, Python-, PySpark-en Julia-kernels ondersteunen.
* Azure Storage Explorer.
* Azure CLI, de Azure-opdracht regel interface voor het beheer van Azure-resources.
* PostgresSQL-data base.
* Machine learning-hulpprogram ma's:
  * [Microsoft Cognitive Toolkit](https://github.com/Microsoft/CNTK) (CNTK), een diep gaande software Toolkit van micro soft Research.
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit), een snel machine learning systeem dat technieken zoals online, hashing, allreduce, reductie, learning2search, actief en interactief leren ondersteunt.
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/), een hulp programma dat snelle en nauw keurige implementatie van structuur biedt.
  * [Rattle](https://togaware.com/rattle/), een hulp programma dat aan de slag gaat met gegevens analyse en machine learning in R is eenvoudig. Rattle biedt zowel op GUI gebaseerde gegevens exploratie als-modellering met behulp van automatische R-code generatie.
* Azure SDK in Java, Python, node. js, Ruby en PHP.
* Bibliotheken in R en python voor gebruik in Azure Machine Learning en andere Azure-Services.
* Ontwikkelingsprogram ma's en editors (RStudio, PyCharm, IntelliJ, emacs, gedit, VI).

Wetenschappelijke gegevens omvat het doorlopen van een reeks taken:

1. Gegevens zoeken, laden en vooraf verwerken.
1. Bouwen en testen van modellen.
1. De modellen voor gebruik in intelligente toepassingen te implementeren.

Gegevenswetenschappers verschillende hulpprogramma's gebruiken om deze taken te voltooien. Het kan tijdrovend zijn om de juiste versies van de software te vinden en vervolgens de software te downloaden, te compileren en te installeren.

De Linux-DSVM kunnen deze belasting aanzienlijk versnellen. Gebruik de Linux-DSVM om uw analyse project te starten. De Linux-DSVM helpt u bij het werken aan taken in verschillende talen, waaronder R, Python, SQL, C++Java en. Eclips biedt een eenvoudig te gebruiken IDE voor het ontwikkelen en testen van uw code. De Azure SDK, die is opgenomen in de DSVM, helpt u bij het bouwen van uw toepassingen met behulp van verschillende services op Linux voor het micro soft-Cloud platform. Andere talen zijn vooraf geïnstalleerd, waaronder Ruby, perl, PHP en node. js.

Er zijn geen software kosten voor de DSVM-installatie kopie. U betaalt alleen de gebruiks kosten voor Azure-hardware die worden beoordeeld op basis van de grootte van de virtuele machine die u met de DSVM-installatie kopie hebt ingericht. Voor meer informatie over de reken kosten raadpleegt u de [vermelding voor data Science virtual machine voor Linux (CentOS)](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/) in azure Marketplace.

## <a name="prerequisites"></a>Vereisten

Voordat u een Linux-Data Science Virtual Machine kunt maken, moet u aan de volgende vereisten voldoen:

* **Azure-abonnement**: Zie [een gratis Azure-account maken](https://azure.microsoft.com/free/)voor meer informatie over een Azure-abonnement.
* **Azure-opslagaccount**: Zie [een opslag account maken](../../storage/common/storage-quickstart-create-account.md)voor meer informatie over het ophalen van een Azure-opslag account. Als u geen bestaand Azure Storage-account wilt gebruiken, kunt u een opslag account maken wanneer u de DSVM maakt.

## <a name="other-versions-of-the-data-science-virtual-machine"></a>Andere versies van de Data Science Virtual Machine

De Data Science Virtual Machine is ook beschikbaar in de volgende versies:

* [Ubuntu](dsvm-ubuntu-intro.md): De Ubuntu-afbeelding heeft veel dezelfde hulp middelen als de CentOS-installatie kopie, inclusief diepe leer raamwerken. 
* [Windows](provision-vm.md)

## <a name="create-a-linux-data-science-virtual-machine"></a>Een Linux-Data Science Virtual Machine maken

Een exemplaar van de Linux-DSVM maken:

1. Ga naar de vermelding van de virtuele machine in de [Azure Portal](https://portal.azure.com/#create/microsoft-ads.linux-data-science-vmlinuxdsvm).
1. Selecteer **maken** om de wizard te openen.

   ![De wizard waarmee een Data Science Virtual Machine wordt geconfigureerd](./media/linux-dsvm-intro/configure-linux-data-science-virtual-machine.png)
1. Typ of selecteer de volgende informatie voor elke stap van de wizard:
   
   **1** **basis beginselen**:

      * **Naam**: De naam van de data science-server die u maakt.
      * **Gebruikers naam**: de aanmeldings-id van het eerste account.
      * **Wachtwoord**: het wachtwoord van het eerste account. (U kunt een openbare SSH-sleutel gebruiken in plaats van een wachtwoord.)
      * **Abonnement**: Als u meer dan één abonnement hebt, selecteert u het abonnement waarop de machine wordt gemaakt en gefactureerd. U moet gemachtigd zijn om resources te maken voor het abonnement.
      * **Resourcegroep**: U kunt een nieuwe resource groep maken of een bestaande groep gebruiken.
      * **Locatie**: Selecteer een Data Center dat moet worden gebruikt voor de DSVM. In de meeste gevallen selecteert u het Data Center dat het meren deel van uw gegevens bevat of zich het dichtst bij uw fysieke locatie bevindt (voor de snelste netwerk toegang).
   
   **2** **grootte**: Selecteer een server type dat voldoet aan uw functionele vereiste en kosten beperkingen. Selecteer **Alles weergeven** om te zien van meer opties van VM-grootten.
   
   
   **3** **instellingen**:
      * **Schijf type**: Als u liever een SSD-schijf (Solid-State Drive) kiest, selecteert u **Premium**. Selecteer anders **standaard**.
      * **Opslagaccount**: U kunt een nieuw Azure-opslag account maken in uw abonnement of een bestaand Azure-account gebruiken op dezelfde locatie die u hebt geselecteerd in de stap **basis beginselen** van de wizard.
      * **Andere para meters**: In de meeste gevallen gebruikt u de standaard waarden om andere para meters te configureren. Als u niet-standaard waarden wilt zien, houdt u de muis aanwijzer boven de informatieve koppeling voor de para meter.
   
   **4** **samen vatting**: Controleer of de gegevens die u hebt ingevoerd juist zijn.
   
   **5** **kopen**: Selecteer **kopen**om het inrichten te starten. Er wordt een koppeling naar de voor waarden van de trans actie gegeven. Er zijn geen extra kosten verbonden aan de DSVM buiten de compute voor de grootte van deserver die u selecteert.

Het inrichten duurt 10-20 minuten. De status van de inrichting wordt weer gegeven in de Azure Portal.

## <a name="how-to-access-the-linux-data-science-virtual-machine"></a>Toegang tot de Linux Data Science Virtual Machine

Nadat de DSVM is gemaakt, kunt u zich aanmelden met behulp van SSH. Gebruik de account referenties die u hebt gemaakt in de sectie **basis beginselen** van de wizard voor de tekst shell-interface. In Windows kunt u een SSH-client hulpprogramma downloaden, [](https://www.putty.org)zoals putty. Als u liever een grafisch bureau blad (X Window-Systeem) hebt, kunt u X11 door sturen gebruiken op PuTTy of de X2Go-client installeren.

> [!NOTE]
> De client X2Go beter dan X11 doorsturen tests uitgevoerd. Het is raadzaam om met behulp van de client X2Go voor een grafische interface voor het bureaublad.

## <a name="install-and-configure-the-x2go-client"></a>De X2Go-client installeren en configureren

De Linux-DSVM wordt ingericht met X2Go server en is klaar om client verbindingen te accepteren. Voer de volgende procedure uit op de client om verbinding te maken met het Linux DSVM graphical Desktop:

1. Download en installeer de client X2Go voor uw clientplatform van [X2Go](https://wiki.x2go.org/doku.php/doc:installation:x2goclient).
1. Voer de X2Go-client uit. Selecteer **nieuwe sessie**. Er wordt een configuratie venster met meerdere tabbladen geopend. Voer de volgende configuratieparameters:
   * **Tabblad sessie**:
     * **Host**: Voer de hostnaam of het IP-adres in van uw Linux-DSVM.
     * **Aanmelding**: Voer de gebruikers naam in op de Linux-DSVM.
     * **SSH-poort**: Behoud de standaard waarde **22**.
     * **Sessie type**: Wijzig de waarde in **xfce**. De Linux-DSVM ondersteunt momenteel alleen het XFCE-bureau blad.
   * Tabblad **Media** : U kunt geluids ondersteuning en client afdrukken uitschakelen als u deze niet nodig hebt.
   * **Gedeelde mappen**: Als u wilt dat directory's van uw client computers zijn gekoppeld aan de Linux-DSVM, voegt u de client computer directory's toe die u wilt delen met de DSVM.

Nadat u zich hebt aangemeld bij de DSVM met behulp van de SSH-client of het grafische XFCE-bureau blad via de X2Go-client, kunt u beginnen met het gebruik van de hulpprogram ma's die zijn geïnstalleerd en geconfigureerd op de DSVM. Op XFCE kunt u snelkoppelingen en bureaublad pictogrammen voor toepassingen weer geven voor veel van de hulpprogram ma's.

## <a name="tools-installed-on-the-linux-data-science-virtual-machine"></a>Hulpprogramma's zijn geïnstalleerd op de Linux Data Science Virtual Machine

### <a name="machine-learning-server"></a>Machine Learning Server

R is een van de meest populaire talen voor data-analyse en machine learning. Als u R voor uw analyse wilt gebruiken, is de DSVM Machine Learning Server met micro soft R open en de reken kundige kernel-bibliotheek. De reken kundige kernel-bibliotheek optimaliseert veelvoorkomende wiskundige bewerkingen in analytische algoritmen. R open is volledig compatibel met KRANen R. Een van de R-bibliotheken die in KRANs zijn gepubliceerd, kunnen worden geïnstalleerd op R open. 

U kunt Machine Learning Server gebruiken om R-modellen te schalen en operationeel maken in webservices. U kunt uw R-programma's in een van de standaard-editors, zoals RStudio, vi of Emacs bewerken. De Emacs-editor is vooraf geïnstalleerd op de DSVM. Het emacs ESS (Emacsse Statistics)-pakket vereenvoudigt het werken met R-bestanden in de Emacs-editor.

Als u de R-console wilt openen, voert u in de shell **r**in. Met deze opdracht gaat u naar een interactieve omgeving. Voor het ontwikkelen van uw R-programma gebruikt u doorgaans een editor zoals Emacs of VI, en voert u de scripts uit in R. RStudio biedt een volledige grafische IDE voor het ontwikkelen van uw R-programma.

Een R-script dat u kunt gebruiken voor het installeren van de [Top 20 R pakketten](https://www.kdnuggets.com/2015/06/top-20-r-packages.html) is opgenomen in de DSVM. U kunt dit script uitvoeren wanneer u zich in de R-interactieve interface bevindt. Zoals eerder vermeld, voert u in de shell **R**in om die interface te openen.  

### <a name="python"></a>Python

Anaconda python wordt geïnstalleerd met de python 3,5-en 2,7-omgevingen. De 2,7-omgeving heet _root_ en de 3,5-omgeving heet _py35_. Deze verdeling bevat de basis Python samen met ongeveer 300 van de meest populaire math, engineering en data analytics-pakketten.

De omgeving py35 is de standaardinstelling. Gebruik deze opdracht om de basis omgeving (2,7) te activeren:

```bash
source activate root
```

Gebruik deze opdracht om de py35-omgeving opnieuw te activeren:

```bash
source activate py35
```

Als u een een python-interactieve sessie wilt aanroepen, voert u **python**in de shell in. 

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

### <a name="jupyter-notebook"></a>Jupyter Notebook

De Anaconda-distributie wordt ook geleverd met een Jupyter Notebook, een omgeving om code en analyse te delen. Open de Jupyter Notebook via JupyterHub. U meldt zich aan met uw lokale Linux-gebruikers naam en-wacht woord.

De Jupyter Notebook-server is vooraf geconfigureerd met python 2-, python 3-en R-kernels. Gebruik het pictogram **Jupyter notebook** bureau blad om de browser te openen en toegang te krijgen tot de Jupyter notebook-server. Als u toegang hebt tot de DSVM via SSH of de X2Go-client, kunt u ook toegang krijgen tot de\/Jupyter notebook-server op https:/localhost: 8000/.

> [!NOTE]
> Doorgaan als u certificaatwaarschuwingen.

U kunt de Jupyter-notebook-server openen vanaf elke host. Voer **https in\/:/\>DSVM DNS-naam of IP-adres: 8000/.\<**

> [!NOTE]
> Poort 8000 wordt standaard geopend in de firewall wanneer de DSVM is ingericht. 

Micro soft heeft voorbeeld notitieblokken, een in Python en één in R, verpakt. U kunt de koppeling naar de voor beelden op de start pagina van Jupyter Notebook bekijken nadat u zich bij de Jupyter Notebook hebt geverifieerd met behulp van uw lokale Linux-gebruikers naam en-wacht woord. Als u een nieuw notitie blok wilt maken, selecteert u **Nieuw**en selecteert u vervolgens de taal-kernel die u wilt gebruiken. Als de knop **Nieuw** niet wordt weer gegeven, selecteert u het **Jupyter** pictogram in de linkerbovenhoek om naar de start pagina van de notebook server te gaan.

### <a name="spark-standalone"></a>Niet-zelfstandige Spark 

Een exemplaar van de zelfstandige modus van Spark wordt vooraf geïnstalleerd op de Linux-DSVM om u te helpen bij het lokaal ontwikkelen van Spark-toepassingen voordat u ze test en implementeert op grote clusters. 

U kunt de PySpark-programma's uitvoeren via de Jupyter-kernel. Wanneer u Jupyter opent, selecteert u de knop **Nieuw** en ziet u een lijst met beschik bare kernels. **Spark-python** is de PySpark-kernel waarmee u Spark-toepassingen kunt bouwen met behulp van de python-taal. U kunt ook een python IDE zoals PyCharm of Spyder gebruiken om uw Spark-programma te bouwen. 

In deze zelfstandige instantie wordt de Spark-stack uitgevoerd in het aanroepende client programma. Met deze functie kunt u sneller en eenvoudiger problemen oplossen in vergelijking met het ontwikkelen van een Spark-cluster.

Jupyter biedt een voor beeld van een PySpark-notebook. U vindt deze in de SparkML in de hoofdmap van Jupyter ($HOME/notebooks/SparkML/pySpark). 

Als u programmeert in R voor Spark, kunt u Machine Learning Server, Spark of sparklyr gebruiken. 

Voordat u uitvoert in een Spark-context in Machine Learning Server, moet u een eenmalige installatie stap uitvoeren om een lokaal Hadoop-en garen-exemplaar met één knoop punt in te scha kelen. Hadoop-services worden standaard geïnstalleerd maar uitgeschakeld op de DSVM. Als u Hadoop-Services wilt inschakelen, voert u de volgende opdrachten uit als hoofd de eerste keer:

```bash
echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
chmod 0600 ~hadoop/.ssh/authorized_keys
chown hadoop:hadoop ~hadoop/.ssh/id_rsa
chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
systemctl start hadoop-namenode hadoop-datanode hadoop-yarn
```

U kunt de Hadoop-gerelateerde services stoppen wanneer u deze niet nodig hebt door `systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn`uit te voeren.

De/dsvm/samples/MRS Directory bevat een voor beeld van het ontwikkelen en testen van Machine Learning Server in een externe Spark-context (het zelfstandige Spark-exemplaar op de DSVM).

### <a name="ides-and-editors"></a>IDE's en editors

U kunt kiezen uit verschillende code editors, waaronder VI/VIM, emacs, gedit, PyCharm, RStudio, eclips, LaTeX en IntelliJ. 

* gedit, eclips, IntelliJ, R Studio en PyCharm zijn grafische editors. Als u deze wilt gebruiken, moet u zijn aangemeld bij een grafisch bureau blad. U opent ze met de snelkoppelingen voor het menu bureau blad en toepassingen.

* Vim en emacs zijn op tekst gebaseerde editors. Op emacs maakt het ESS add-on-pakket in de Emacs-editor gemakkelijker werken met R. Meer informatie vindt u op de [ESS-website](https://ess.r-project.org/).

* Eclips is een open-source, uitbreid bare IDE die ondersteuning biedt voor meerdere talen. Eclips IDE voor Java-Ontwikkel aars is de versie die is geïnstalleerd op de DSVM. U kunt invoeg toepassingen voor verschillende populaire talen installeren om de omgeving uit te breiden. 

  De Azure-toolkit voor Eclipse-invoeg toepassing wordt geïnstalleerd met een eclips op het DSVM. U kunt Azure-toolkit voor Eclipse gebruiken om Azure-toepassingen te maken, te ontwikkelen, te testen en te implementeren met behulp van de eclips-ontwikkel omgeving die talen als Java ondersteunt.

  De Azure SDK voor Java wordt ook geïnstalleerd met de Azure-toolkit voor Eclipse op de DSVM. De Azure SDK voor Java geeft u toegang tot verschillende Azure-Services vanuit een Java-omgeving. 
  
  Zie [Azure-Toolkit voor eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse)voor meer informatie.

* LaTeX wordt geïnstalleerd via het texlive-pakket, samen met een Emacs-invoeg toepassings pakket met de naam [AUCTeX](https://www.gnu.org/software/auctex/manual/auctex/auctex.html). Met dit pakket wordt het ontwerpen van uw LaTeX-documenten in Emacs vereenvoudigd. 

### <a name="databases"></a>Databases

De Linux-DSVM biedt u toegang tot verschillende data base-en opdracht regel Programma's.

#### <a name="postgressql"></a>PostgresSQL

De open-source data base PostgresSQL is beschikbaar op de DSVM, met de services die worden uitgevoerd en initdb zijn voltooid. U moet data bases en gebruikers maken. Zie de [PostgresSQL-documentatie](https://www.postgresql.org/docs/)voor meer informatie.  

#### <a name="squirrel-sql"></a>SQuirreL SQL

SQuirreL SQL is een grafische SQL-client die verbinding kan maken met verschillende data bases (inclusief SQL Server, PostgresSQL en MySQL) en SQL-query's kan uitvoeren. U kunt SQuirreL SQL uitvoeren vanuit een grafische bureaublad sessie (bijvoorbeeld via de X2Go-client) met behulp van een bureaublad pictogram. U kunt ook de-client uitvoeren met behulp van de volgende opdracht in de shell:

```bash
/usr/local/squirrel-sql-3.7/squirrel-sql.sh /usr/local/squirrel-sql-3.7/squirrel-sql.sh
```

Voordat u het eerste gebruik, stuurprogramma's en database-aliassen hebt ingesteld. De JDBC-Stuur Programma's bevinden zich op/usr/share/java/jdbcdrivers.

Zie [Squirrel SQL](http://squirrel-sql.sourceforge.net/index.php?page=screenshots)voor meer informatie.

#### <a name="command-line-tools-for-accessing-sql-server"></a>Opdracht regel Programma's voor toegang tot SQL Server

Het pakket ODBC-stuurprogramma voor SQL Server wordt geleverd met twee opdrachtregelprogramma's:

* **bcp**: Het hulp programma BCP kopieert gegevens tussen een exemplaar van SQL Server en een gegevens bestand in een door de gebruiker opgegeven indeling. U kunt het hulp programma BCP gebruiken voor het importeren van grote aantallen nieuwe rijen in SQL Server tabellen of het exporteren van gegevens uit tabellen naar gegevens bestanden. Als u gegevens wilt importeren in een tabel, moet u een indelings bestand gebruiken dat voor die tabel is gemaakt. Of u moet inzicht hebben in de structuur van de tabel en de typen gegevens die geldig zijn voor de kolommen.

  Zie [verbinding maken met BCP](https://msdn.microsoft.com/library/hh568446.aspx)voor meer informatie.

* **sqlcmd**: U kunt het Sqlcmd-hulp programma gebruiken om Transact-SQL-instructies, systeem procedures en script bestanden op te geven bij de opdracht prompt. Het Sqlcmd-hulp programma gebruikt ODBC om Transact-SQL-batches uit te voeren.

  Zie [verbinding maken met Sqlcmd](https://msdn.microsoft.com/library/hh568447.aspx)voor meer informatie.

  > [!NOTE]
  > Er zijn enkele verschillen in dit hulp programma tussen Linux-en Windows-platforms. Zie de documentatie voor meer informatie.

#### <a name="database-access-libraries"></a>Database toegang bibliotheken

Bibliotheken voor database toegang zijn beschikbaar in R en python:

* In R kunt u het RODBC-pakket of het dplyr-pakket gebruiken om SQL-instructies op de database server op te vragen of uit te voeren.
* In python biedt de pyodbc-bibliotheek database toegang met ODBC als de onderliggende laag.

### <a name="azure-tools"></a>Azure-hulpprogramma 's

De volgende Azure-hulpprogram ma's zijn geïnstalleerd op de DSVM:

* **Azure CLI**: U kunt de opdracht regel interface in azure gebruiken om Azure-resources te maken en te beheren via shell-opdrachten. Als u de Azure-hulpprogram ma's wilt openen, voert u **Azure Help**in. Zie voor meer informatie de [documentatiepagina van Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).
* **Azure Storage Explorer**: Azure Storage Explorer is een grafisch hulp programma dat u kunt gebruiken om te bladeren door de objecten die u in uw Azure-opslag account hebt opgeslagen, en om gegevens van en naar Azure-blobs te uploaden en te downloaden. U kunt Storage Explorer openen via het pictogram van de snelkoppeling op het bureaublad. U kunt deze ook openen vanuit een shell-prompt door **StorageExplorer**in te voeren. U moet zijn aangemeld bij een X2Go-client of X11-door sturing hebben ingesteld.
* **Azure-bibliotheken**: De volgende bibliotheken zijn vooraf geïnstalleerd op de DSVM:
  
  * **Python**: De Azure-gerelateerde bibliotheken in python zijn *Azure*, *azureml*, *pydocumentdb*en *pyodbc*. Met de eerste drie bibliotheken, kunt u toegang tot Azure storage-services, Azure Machine Learning en Azure Cosmos DB (een NoSQL-database in Azure). De vierde servicebibliotheek pyodbc (samen met het Microsoft ODBC-stuurprogramma voor SQL Server), kunt u toegang tot SQL Server, Azure SQL Database en Azure SQL Data Warehouse met Python met behulp van een ODBC-interface. Voer **pip lijst** om te zien van de vermelde bibliotheken. Zorg dat u deze opdracht wordt uitgevoerd in de Python 2.7 en 3,5 omgevingen.
  * **R**: De Azure-gerelateerde bibliotheken in R zijn AzureML en RODBC.
  * **Java**: De lijst met Azure Java-bibliotheken vindt u in de map/dsvm/sdk/AzureSDKJava op de DSVM. De sleutel-bibliotheken zijn Azure-opslag en beheer van API's, Azure Cosmos DB en JDBC-stuurprogramma's voor SQL Server.  

U kunt de [Azure Portal](https://portal.azure.com) openen vanuit de vooraf geïnstalleerde Firefox-browser. In de Azure Portal kunt u Azure-resources maken, beheren en bewaken.

### <a name="azure-machine-learning"></a>Azure Machine Learning

Azure Machine Learning is een volledig beheerde Cloud service die u kunt gebruiken om predictive analytics oplossingen te bouwen, te implementeren en te delen. U bouwen uw experimenten en modellen in Azure Machine Learning Studio. Als u toegang wilt krijgen tot Azure Machine Learning vanuit een webbrowser op de DSVM, gaat u naar [Microsoft Azure machine learning](https://studio.azureml.net).

Nadat u zich hebt aangemeld bij Azure Machine Learning Studio, kunt u een Experimenteer teken gebruiken om een logische stroom te maken voor de machine learning-algoritmen. U hebt ook toegang tot een Jupyter Notebook die wordt gehost op Azure Machine Learning. Het notitie blok kan naadloos samen werken met de experimenten in Machine Learning Studio. 

Operationeel maken de machine learning modellen die u bouwt door ze in te pakken in een webservice-interface. Met inzet machine learning-modellen kunnen clients die in elke taal zijn geschreven, voor spellingen van deze modellen aanroepen. Zie voor meer informatie de [documentatie voor Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/).

U kunt ook uw modellen in R of python bouwen op de DSVM en deze vervolgens implementeren in productie op Azure Machine Learning. Micro soft heeft bibliotheken in R (**azureml**) en python (**azureml**) geïnstalleerd ter ondersteuning van deze functionaliteit.

Zie [tien dingen die u kunt doen op de data Science virtual machine](vm-do-ten-things.md)voor informatie over het implementeren van modellen in R en Python naar Azure machine learning.

> [!NOTE]
> De instructies in [tien dingen die u op het data Science virtual machine kunt doen](vm-do-ten-things.md) , zijn geschreven voor de Windows-versie van de DSVM. De informatie over het implementeren van modellen naar Azure Machine Learning is echter ook van toepassing op de Linux-DSVM.

### <a name="machine-learning-tools"></a>Hulpprogramma's voor machine learning

De DSVM wordt geleverd met een paar machine learning-hulpprogram ma's en-algoritmen die lokaal worden gecompileerd en vooraf zijn geïnstalleerd. Deze omvatten:

* **Microsoft Cognitive Toolkit**: Een diep gaande leer Toolkit.
* **Vowpal Wabbit**: Een Fast Online Learning-algoritme.
* **XGBoost**: Een hulp programma dat geoptimaliseerde, gestimuleerde structuur algoritmen biedt.
* **Python**: Anaconda python wordt geleverd met machine learning algoritmen met bibliotheken zoals Scikit. U kunt andere bibliotheken installeren met behulp van de `pip install` opdracht.
* **R**: Er is een uitgebreide bibliotheek met machine learning functies beschikbaar voor R. vooraf geïnstalleerde bibliotheken zijn LM, GLM, randomForest en rpart. U kunt andere bibliotheken installeren door uit `install.packages(<lib name>)`te voeren.

Microsoft Cognitive Toolkit, Vowpal Wabbit en XGBoost worden gedetailleerd besproken in de volgende secties.

#### <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit

Microsoft Cognitive Toolkit is een open-source diep Learning Toolkit. Het is een opdracht regel programma (CNTK) en bevindt zich al in het pad.

Als u een basis voorbeeld wilt uitvoeren, voert u de volgende opdrachten uit in de shell:

```bash
cd /home/[USERNAME]/notebooks/CNTK/HelloWorld-LogisticRegression
cntk configFile=lr_bs.cntk makeMode=false command=Train
```

Zie de [GITHUB CNTK-opslag plaats](https://github.com/Microsoft/CNTK) en de CNTK- [wiki](https://github.com/Microsoft/CNTK/wiki)voor meer informatie.

#### <a name="vowpal-wabbit"></a>Vowpal Wabbit

Vowpal Wabbit is een machine learning systeem dat gebruikmaakt van technieken zoals online, hashing, allreduce, reducties, learning2search, actief en interactief leren.

Voer de volgende opdrachten uit om het hulp programma uit te voeren op basis van een eenvoudig voor beeld:

```bash
cp -r /dsvm/tools/VowpalWabbit/demo vwdemo
cd vwdemo
vw house_dataset
```

De Vowpal Wabbit-demo Directory bevat andere, grotere demo's. Zie [github Vowpal Wabbit repository](https://github.com/JohnLangford/vowpal_wabbit) en de [Vowpal Wabbit wiki](https://github.com/JohnLangford/vowpal_wabbit/wiki)voor meer informatie over Vowpal Wabbit.

#### <a name="xgboost"></a>XGBoost

De XGBoost-bibliotheek is ontworpen en geoptimaliseerd voor geboostte algoritmen (structuur). De doel stelling van de XGBoost-bibliotheek is het pushen van de reken limieten van machines tot de Extremes die nodig zijn om grootschalige, schaal bare structuren te bieden.

XGBoost wordt gegeven als opdracht regel en als R-bibliotheek.

Als u de XGBoost-bibliotheek in R wilt gebruiken, start u een interactieve R-sessie (in de shell, voert u **R**) in en laadt u de tape wisselaar.

Hier volgt een eenvoudig voor beeld dat u kunt uitvoeren op de R-prompt:

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

Voer de volgende opdrachten uit in de shell om de XGBoost-opdracht regel uit te voeren:

```bash
cp -r /dsvm/tools/xgboost/demo/binary_classification/ xgboostdemo
cd xgboostdemo
xgboost mushroom.conf
```

Een. model-bestand wordt geschreven naar de opgegeven map. Zie [binaire classificatie](https://github.com/dmlc/xgboost/tree/master/demo/binary_classification)voor informatie over dit voor beeld van een demo op github.

Zie de [XGBoost-documentatie](https://xgboost.readthedocs.org/en/latest/) en de [XGBoost github-opslag plaats](https://github.com/dmlc/xgboost)voor meer informatie over XGBoost.

#### <a name="rattle"></a>Rammelaar

Rattle (*R* *A*analytische *t*Ool *t*o *L*verdienen *E*asily) maakt gebruik van op GUI gebaseerde gegevens exploratie en model lering. Rattle:
- Geeft statistische en visuele samen vattingen van gegevens weer.
- Transformeert gegevens die gemakkelijk kunnen worden gemodelleerd.
- Bouwt beide modellen zonder super visie en Super visie van gegevens.
- Geeft de prestaties van modellen grafisch weer.
- Hiermee worden nieuwe gegevens sets gescoord.
- Hiermee wordt de R-code gegenereerd.
- Hiermee repliceert u bewerkingen in de gebruikers interface die rechtstreeks kunnen worden uitgevoerd in R of worden gebruikt als uitgangs punt voor meer analyse.

Als u Rattle wilt uitvoeren, moet u zijn aangemeld bij een grafische bureaublad sessie. Voer in een Terminal **r** in om de R-omgeving te openen. Voer de volgende opdrachten bij de R-prompt:

```R
library(rattle)
rattle()
```

Er wordt een grafische interface met een set tabbladen geopend. Gebruik de volgende Snelstartgids-stappen in Rattle om een voor beeld weer te gegeven en een model te bouwen. In sommige stappen wordt u gevraagd om automatisch enkele vereiste R-pakketten te installeren en te laden die nog niet op het systeem zijn geïnstalleerd.

> [!NOTE]
> Als u geen machtigingen hebt om het pakket te installeren in de systeemmap (de standaard instelling), ziet u mogelijk een prompt op uw R-console venster om pakketten te installeren in uw persoonlijke bibliotheek. Als u deze prompts ziet, voert u **y**in.

1. Selecteer **Uitvoeren**.
1. Er wordt een dialoog venster weer gegeven waarin u wordt gevraagd om de voor beeld-gegevensset te laden. Selecteer **Ja** om het voor beeld te laden.
1. Selecteer het tabblad **model** .
1. Selecteer **uitvoeren** om een beslissings structuur te maken.
1. Selecteer **tekenen** om de beslissings structuur weer te geven.
1. Selecteer de optie **forest** en selecteer vervolgens **uitvoeren** om een wille keurig forest te bouwen.
1. Selecteer het tabblad **evalueren** .
1. Selecteer de optie **risico** en selecteer vervolgens **uitvoeren** om twee **risico's (cumulatief)** prestatie grafieken weer te geven.
1. Selecteer het tabblad **logboek** om de gegenereerde R-code voor de voor gaande bewerkingen weer te geven. (Vanwege een fout in de huidige release van Rattle moet u een **#** teken invoegen vóór het exporteren van **Dit logboek** in de tekst van het logboek.)
1. Selecteer de knop **exporteren** om het R-script bestand met de naam weather_script op te slaan *. R* naar de basismap.

U kunt Rattle en R afsluiten. Nu kunt u het gegenereerde R-script wijzigen. Of gebruik het script zoals het is en voer deze op elk gewenst moment uit om alles te herhalen dat in de Rattle-gebruikers interface is uitgevoerd. Met name voor beginners in R is dit een manier om snel analyses en machine learning uit te voeren in een eenvoudige grafische interface, terwijl automatisch code wordt gegenereerd in R om te wijzigen of te leren.

## <a name="next-steps"></a>Volgende stappen

Hier ziet u hoe u kunt doorgaan met uw learning en verkennen:

* In het overzicht van de [gegevens wetenschap op het data Science virtual machine voor Linux](linux-dsvm-walkthrough.md) ziet u hoe u verschillende algemene data Science-taken kunt uitvoeren met de Linux-DSVM die hier is ingericht. 
* Bekijk de verschillende hulpprogram ma's voor gegevens wetenschap op het DSVM door de hulpprogram ma's die in dit artikel worden beschreven, uit te proberen. U kunt ook uitvoeren `dsvm-more-info` in de shell in de virtuele machine voor een basis kennis Making en voor verwijzingen naar meer informatie over de hulpprogram ma's die op de DSVM zijn geïnstalleerd.  
* Informatie over het bouwen van analytische oplossingen voor end-to-end systematisch met behulp van de [Team Data Science Process](https://aka.ms/tdsp).
* Ga naar de [Azure AI Gallery](https://gallery.azure.ai/) voor machine learning en data analytics-voorbeelden die gebruikmaken van de Azure AI-services.