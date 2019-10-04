---
title: Ondersteunde gegevens platforms
titleSuffix: Azure Data Science Virtual Machine
description: Meer informatie over de ondersteunde gegevens platforms en hulpprogram ma's voor Azure Data Science Virtual Machine.
keywords: hulpprogramma's voor datatechnologie, virtuele machine voor datatechnologie, hulpprogramma voor datatechnologie, linux-datatechnologie
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: conceptual
ms.date: 10/3/2019
ms.openlocfilehash: df112889fd7cd8ad1574147072b6e13137945462
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/04/2019
ms.locfileid: "71947542"
---
# <a name="data-platforms-supported-on-the-data-science-virtual-machine"></a>Gegevensplatforms die worden ondersteund op de Data Science Virtual Machine

Met een Data Science Virtual Machine (DSVM) kunt u uw analyses maken op basis van een breed scala aan gegevens platforms. Naast de interfaces van externe gegevensplatforms biedt de DSVM een lokaal exemplaar voor snelle ontwikkeling en ontwikkelen van prototypen.

De volgende hulpprogram ma's voor gegevens platforms worden ondersteund op de DSVM.

## <a name="sql-server-2017-developer-edition"></a>SQL Server 2017 Developer Edition

| | |
| ------------- | ------------- |
| Wat is het?   | Een lokale relationele database-exemplaar      |
| Ondersteunde DSVM-edities      | Windows      |
| Wordt doorgaans gebruikt      | Snelle ontwikkeling lokaal met kleinere gegevensset <br/> In de database R uitvoeren   |
| Koppelingen naar voorbeelden      |    Een klein voor beeld van een nieuwe gegevensset in Utrecht City wordt geladen in de SQL database:<br/>  `nyctaxi` <br/> Jupyter-voor beeld met Microsoft Machine Learning Server en in-data base Analytics vindt u op:<br/> `~notebooks/SQL_R_Services_End_to_End_Tutorial.ipynb`  |
| Gerelateerde hulpprogram ma's op de DSVM       | SQL Server Management Studio <br/> ODBC/JDBC-Stuur Programma's<br/> pyodbc, RODBC<br />Apache Drill      |

> [!NOTE]
> SQL Server 2016 Developer Edition kan alleen worden gebruikt voor ontwikkelings-en test doeleinden. U moet een licentie of een van de SQL Server-VM's uit te voeren in de productieomgeving.


### <a name="setup"></a>Instellen

De database server is al vooraf geconfigureerd en de Windows-services die betrekking hebben op SQL Server (zoals `SQL Server (MSSQLSERVER)`) zijn ingesteld om automatisch te worden uitgevoerd. Voor de enige hand matige stap moet u in-data base Analytics inschakelen met behulp van Microsoft Machine Learning Server. U kunt dit doen door de volgende opdracht uit te voeren als eenmalige actie in SQL Server Management Studio (SSMS). Voer deze opdracht uit nadat u zich hebt aangemeld als machine beheerder, open een nieuwe query in SSMS en zorg ervoor dat de geselecteerde data base `master` is:

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 

        (Replace %COMPUTERNAME% with your VM name.)
       
Als u SQL Server Management Studio wilt uitvoeren, kunt u zoeken op ' SQL Server Management Studio ' in de lijst met Program ma's, of Windows Search gebruiken om het bestand te zoeken en uit te voeren. Wanneer u om referenties wordt gevraagd, selecteert u **Windows-verificatie** en gebruikt u de computer naam of het ```localhost``` in het veld **SQL Server naam** .

### <a name="how-to-use-and-run-it"></a>Hoe gebruiken en uitvoeren

Standaard wordt de database server met het standaard exemplaar van de data base automatisch uitgevoerd. U kunt hulpprogramma's zoals SQL Server Management Studio op de virtuele machine gebruiken voor toegang tot de SQL Server-database lokaal. Lokale beheerders accounts hebben beheerders toegang tot de data base.

Daarnaast wordt de DSVM geleverd met ODBC-en JDBC-Stuur Programma's om te praten met SQL Server, Azure SQL-data bases en Azure SQL Data Warehouse vanuit toepassingen die in meerdere talen zijn geschreven, met inbegrip van python en Machine Learning Server.

### <a name="how-is-it-configured-and-installed-on-the-dsvm"></a>Hoe wordt het geconfigureerd en geïnstalleerd op de DSVM? 

 SQL Server wordt op de normale manier geïnstalleerd. Kan worden gevonden op `C:\Program Files\Microsoft SQL Server`. De Machine Learning Server-instantie in de data base is gevonden op `C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\R_SERVICES`. De DSVM heeft ook een afzonderlijke zelfstandige Machine Learning Server instantie, die wordt geïnstalleerd op `C:\Program Files\Microsoft\R Server\R_SERVER`. Deze twee Machine Learning Server exemplaren delen geen bibliotheken.


## <a name="apache-spark-2x-standalone"></a>Apache Spark-2.x (zelfstandig)

| | |
| ------------- | ------------- |
| Wat is het?   | Een zelfstandig exemplaar (één knoop punt in het proces) van het populaire Apache Spark platform; een systeem voor snelle, grootschalige gegevens verwerking en machine learning     |
| Ondersteunde DSVM-edities      | Linux <br /> Windows (experimenteel)      |
| Wordt doorgaans gebruikt      | * Snelle ontwikkeling van Spark/PySpark-toepassingen lokaal met een kleinere gegevensset en een latere implementatie op grote Spark-clusters, zoals Azure HDInsight<br/> * Test Microsoft Machine Learning Server Spark-context <br />* Gebruik SparkML of de open-source [MMLSpark](https://github.com/Azure/mmlspark) -bibliotheek van micro soft om ml-toepassingen te bouwen |
| Koppelingen naar voorbeelden      |    Jupyter-voorbeeld: <br />&nbsp;&nbsp;* ~/notebooks/SparkML/pySpark <br /> &nbsp;&nbsp;* ~/notebooks/MMLSpark <br /> Microsoft Machine Learning Server (Spark-context):/dsvm/samples/MRS/MRSSparkContextSample.R |
| Gerelateerde hulpprogram ma's op de DSVM       | PySpark, Scala<br/>Jupyter (Spark/PySpark-Kernels)<br/>Microsoft Machine Learning Server, Spark, Sparklyr <br />Apache Drill      |

### <a name="how-to-use-it"></a>Het gebruik ervan
U kunt Spark-taken verzenden op de opdracht regel door de opdracht `spark-submit` of `pyspark` uit te voeren. U kunt ook een Jupyter-notebook maken door een nieuwe notebook maken met de Spark-kernel.

U kunt Spark van R gebruiken met behulp van bibliotheken als Spark, Sparklyr en Microsoft Machine Learning Server, die beschikbaar zijn in de DSVM. Zie de verwijzingen naar de voorbeelden in de voorgaande tabel.

> [!NOTE]
> Het uitvoeren van Microsoft Machine Learning Server in de Spark-context van DSVM wordt alleen ondersteund op de Ubuntu Linux DSVM-editie.



### <a name="setup"></a>Instellen
Voordat u in een Spark-context in Microsoft Machine Learning Server op Ubuntu Linux DSVM-editie uitvoert, moet u een installatie stap van één keer volt ooien om een lokaal Hadoop-en garen-exemplaar van één knoop punt in te scha kelen. Hadoop-services worden standaard geïnstalleerd maar uitgeschakeld op de DSVM. Als u deze wilt inschakelen, voert u de volgende opdrachten uit als hoofd de eerste keer:

    echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
    cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
    chmod 0600 ~hadoop/.ssh/authorized_keys
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
    chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
    systemctl start hadoop-namenode hadoop-datanode hadoop-yarn

U kunt de Hadoop-gerelateerde services stoppen wanneer u deze niet meer nodig hebt door ```systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn``` uit te voeren.

Een voor beeld van het ontwikkelen en testen van MEVR in een externe Spark-context (dat is de zelfstandige Spark-instantie op de DSVM) is beschikbaar in de map `/dsvm/samples/MRS`.


### <a name="how-is-it-configured-and-installed-on-the-dsvm"></a>Hoe wordt het geconfigureerd en geïnstalleerd op de DSVM? 
|Platform|Locatie ($SPARK_HOME) installeren|
|:--------|:--------|
|Windows | c:\dsvm\tools\spark-X.X.X-bin-hadoopX.X|
|Linux   | /dsvm/Tools/Spark-X.X.X-bin-hadoopX.X|


Bibliotheken om toegang te krijgen tot gegevens van Azure Blob-opslag of Azure Data Lake Storage, met behulp van de micro soft MMLSpark machine learning-bibliotheken, zijn vooraf geïnstalleerd in $SPARK _HOME/potten. Deze JAR-bestanden worden automatisch geladen wanneer Spark wordt gestart. Standaard gebruikt Spark gegevens op de lokale schijf. 

Voor de Spark-instantie in de DSVM om toegang te krijgen tot gegevens die zijn opgeslagen in Blob Storage of Azure Data Lake Storage, moet u het bestand `core-site.xml` maken en configureren op basis van de sjabloon die is gevonden in $SPARK _HOME/conf/bestand core-site. XML. Temp late. U moet ook de juiste referenties hebben voor toegang tot de Blob-opslag en de Azure Data Lake Storage. (Houd er rekening mee dat de sjabloon bestanden tijdelijke aanduidingen gebruiken voor Blob Storage en Azure Data Lake Storage configuraties.)

Zie [verificatie met Azure data Lake Storage gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-authenticate-using-active-directory)voor meer gedetailleerde informatie over het maken van Azure data Lake Storage-service referenties. Nadat de referenties voor Blob Storage of Azure Data Lake Storage zijn ingevoerd in het bestand bestand core-site. XML, kunt u verwijzen naar de gegevens die in deze bronnen zijn opgeslagen via het URI-voor voegsel wasb://of adl://.

