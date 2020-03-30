---
title: Ondersteunde dataplatforms
titleSuffix: Azure Data Science Virtual Machine
description: Meer informatie over de ondersteunde gegevensplatforms en -hulpprogramma's voor de Azure Data Science Virtual Machine.
keywords: hulpprogramma's voor datatechnologie, virtuele machine voor datatechnologie, hulpprogramma voor datatechnologie, linux-datatechnologie
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: cd787881957d78f179107e46b2650de4618c7724
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282321"
---
# <a name="data-platforms-supported-on-the-data-science-virtual-machine"></a>Dataplatforms ondersteund op de Data Science Virtual Machine

Met een Data Science Virtual Machine (DSVM) u uw analyses bouwen aan de hand van een breed scala aan dataplatforms. Naast interfaces voor externe dataplatforms biedt de DSVM een lokaal voorbeeld voor snelle ontwikkeling en prototyping.

De volgende tools voor dataplatform worden ondersteund op de DSVM.

## <a name="sql-server-developer-edition"></a>SQL Server Developer Edition

| | |
| ------------- | ------------- |
| Wat is het?   | Een lokaal relationeel database-exemplaar      |
| Ondersteunde DSVM-edities      | Windows 2016: SQL Server 2017, Windows 2019: SQL Server 2019      |
| Typische toepassingen      | Snelle ontwikkeling lokaal met kleinere dataset <br/> In-database R uitvoeren   |
| Koppelingen naar voorbeelden      |    Een kleine steekproef van een gegevensset van New York City wordt in de SQL-database geladen:<br/>  `nyctaxi` <br/> Jupyter-voorbeeld met Microsoft Machine Learning Server en in-database-analyses is te vinden op:<br/> `~notebooks/SQL_R_Services_End_to_End_Tutorial.ipynb`  |
| Gerelateerde tools op de DSVM       | SQL Server Management Studio <br/> ODBC/JDBC-stuurprogramma's<br/> pyodbc, RODBC<br />Apache Drill      |

> [!NOTE]
> SQL Server Developer Edition kan alleen worden gebruikt voor ontwikkelings- en testdoeleinden. U hebt een licentie of een van de SQL Server VM's nodig om deze in productie uit te voeren.


### <a name="setup"></a>Instellen

De databaseserver is al vooraf geconfigureerd en de Windows-services met betrekking tot SQL Server (zoals) `SQL Server (MSSQLSERVER)`worden automatisch uitgevoerd. De enige handmatige stap is het inschakelen van In-database analytics met behulp van Microsoft Machine Learning Server. U analytics inschakelen door de volgende opdracht uit te voeren als een eenmalige actie in SQL Server Management Studio (SSMS). Voer deze opdracht uit nadat u zich hebt aangemeld als machinebeheerder, opent u `master`een nieuwe query in SSMS en controleer of de geselecteerde database:

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 

        (Replace %COMPUTERNAME% with your VM name.)
       
Als u SQL Server Management Studio wilt uitvoeren, u zoeken naar 'SQL Server Management Studio' in de programmalijst of Windows Search gebruiken om deze te zoeken en uit te voeren. Selecteer **Windows-verificatie** wanneer u om referenties wordt ```localhost``` gevraagd en gebruikt de machinenaam of in het veld **SQL Server-naam.**

### <a name="how-to-use-and-run-it"></a>Hoe het te gebruiken en uit te voeren

Standaard wordt de databaseserver met de standaarddatabase-instantie automatisch uitgevoerd. U hulpprogramma's zoals SQL Server Management Studio op de VM gebruiken om lokaal toegang te krijgen tot de SQL Server-database. Lokale beheerdersaccounts hebben beheerderstoegang in de database.

Ook wordt de DSVM geleverd met ODBC- en JDBC-stuurprogramma's om met SQL Server, Azure SQL-databases en Azure SQL Data Warehouse te praten vanuit toepassingen die in meerdere talen zijn geschreven, waaronder Python en Machine Learning Server.

### <a name="how-is-it-configured-and-installed-on-the-dsvm"></a>Hoe is het geconfigureerd en geïnstalleerd op de DSVM? 

 SQL Server is standaard geïnstalleerd. Het is te `C:\Program Files\Microsoft SQL Server`vinden op . De instantie Machine Learning Server in `C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\R_SERVICES`de database is te vinden op . De DSVM heeft ook een aparte standalone Machine `C:\Program Files\Microsoft\R Server\R_SERVER`Learning Server-instantie, die is geïnstalleerd op . Deze twee Machine Learning Server-exemplaren delen geen bibliotheken.


## <a name="apache-spark-2x-standalone"></a>Apache Spark 2.x (Standalone)

| | |
| ------------- | ------------- |
| Wat is het?   | Een zelfstandige (single node in-process) instantie van de populaire Apache Spark platform; een systeem voor snelle, grootschalige gegevensverwerking en machinelearning     |
| Ondersteunde DSVM-edities      | Linux     |
| Typische toepassingen      | * Snelle ontwikkeling van Spark/PySpark-toepassingen lokaal met een kleinere gegevensset en latere implementatie op grote Spark-clusters zoals Azure HDInsight<br/> * Test Microsoft Machine Learning Server Spark-context <br />* Gebruik SparkML of Microsoft's open-source [MMLSpark-bibliotheek](https://github.com/Azure/mmlspark) om ML-toepassingen te bouwen |
| Koppelingen naar voorbeelden      |    Jupyter monster: <br />&nbsp;&nbsp;* ~/notebooks/SparkML/pySpark <br /> &nbsp;&nbsp;* ~/notebooks/MMLSpark <br /> Microsoft Machine Learning Server (Spark-context): /dsvm/samples/MRS/MRSSparkContextSample.R |
| Gerelateerde tools op de DSVM       | PySpark, Scala<br/>Jupyter (Spark/PySpark Kernels)<br/>Microsoft Machine Learning Server, SparkR, Sparklyr <br />Apache Drill      |

### <a name="how-to-use-it"></a>Het gebruik ervan
U Spark-taken op de `spark-submit` opdrachtregel indienen door de opdracht of `pyspark` opdracht uit te voeren. U ook een Jupyter-notitieblok maken door een nieuw notitieblok te maken met de Spark-kernel.

U Spark from R gebruiken met bibliotheken zoals SparkR, Sparklyr en Microsoft Machine Learning Server, die beschikbaar zijn op de DSVM. Zie verwijzingen naar voorbeelden in de vorige tabel.

### <a name="setup"></a>Instellen
Voordat u in een Spark-context in Microsoft Machine Learning Server op Ubuntu Linux DSVM-editie wordt uitgevoerd, moet u een eenmalige installatiestap voltooien om een lokaal knooppunt Hadoop HDFS en gareninstantie in te schakelen. Standaard zijn Hadoop-services geïnstalleerd, maar uitgeschakeld op de DSVM. Voer de volgende opdrachten de eerste keer uit als hoofd:

    echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
    cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
    chmod 0600 ~hadoop/.ssh/authorized_keys
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
    chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
    systemctl start hadoop-namenode hadoop-datanode hadoop-yarn

U de Hadoop-gerelateerde services stoppen wanneer ```systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn```u ze niet meer nodig hebt door te draaien.

Een voorbeeld dat laat zien hoe MRS kan worden ontwikkeld en getest in een externe Spark-context (de zelfstandige Spark-instantie op de DSVM) wordt verstrekt en beschikbaar in de `/dsvm/samples/MRS` map.


### <a name="how-is-it-configured-and-installed-on-the-dsvm"></a>Hoe is het geconfigureerd en geïnstalleerd op de DSVM? 
|Platform|Installatielocatie ($SPARK_HOME)|
|:--------|:--------|
|Linux   | /dsvm/tools/spark-X.X.X-bin-hadoopX.X|


Bibliotheken voor toegang tot gegevens uit Azure Blob-opslag of Azure Data Lake Storage, met behulp van de Microsoft MMLSpark-machine learning-bibliotheken, zijn vooraf geïnstalleerd in $SPARK_HOME/potten. Deze JARs worden automatisch geladen wanneer Spark wordt opgestart. Spark gebruikt standaard gegevens op de lokale schijf. 

Voor de Spark-instantie op de DSVM voor toegang tot gegevens die zijn `core-site.xml` opgeslagen in Blob-opslag of Azure Data Lake Storage, moet u het bestand maken en configureren op basis van de sjabloon in $SPARK_HOME/conf/core-site.xml.template. U moet ook over de juiste referenties beschikken om toegang te krijgen tot Blob-opslag en Azure Data Lake Storage. (Houd er rekening mee dat de sjabloonbestanden tijdelijke aanduidingen gebruiken voor Blob-opslag- en Azure Data Lake-opslagconfiguraties.)

Zie [Verificatie met Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-authenticate-using-active-directory)voor meer gedetailleerde informatie over het maken van azure Data Lake Storage-servicereferenties. Nadat de referenties voor Blob-opslag of Azure Data Lake Storage zijn ingevoerd in het bestand core-site.xml, u de gegevens die in die bronnen zijn opgeslagen, verwijzen via het URI-voorvoegsel van wasb:// of adl://.

