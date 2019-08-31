---
title: Ondersteunde gegevens platforms
titleSuffix: Azure Data Science Virtual Machine
description: Meer informatie over de ondersteunde gegevens platforms en hulpprogram ma's voor Azure Data Science Virtual Machine.
keywords: hulpprogramma's voor datatechnologie, virtuele machine voor datatechnologie, hulpprogramma voor datatechnologie, linux-datatechnologie
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 03/16/2018
ms.openlocfilehash: e0ba013f7739e7ade717136cb5507af630263a18
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70192207"
---
# <a name="data-platforms-supported-on-the-data-science-virtual-machine"></a>Gegevensplatforms die worden ondersteund op de Data Science Virtual Machine

De Data Science Virtual Machine (DSVM) kunt u uw analyses op basis van een breed scala aan gegevensplatforms. Naast de interfaces van externe gegevensplatforms biedt de DSVM een lokaal exemplaar voor snelle ontwikkeling en ontwikkelen van prototypen. 

Hieronder vindt u de programma's voor gegevensplatforms ondersteund op de DSVM. 

## <a name="sql-server-2016-developer-edition"></a>SQL Server 2016 Developer Edition

| | |
| ------------- | ------------- |
| Wat is het?   | Een lokale relationele database-exemplaar      |
| Ondersteunde DSVM-edities      | Windows      |
| Wordt doorgaans gebruikt      | Snelle ontwikkeling lokaal met kleinere gegevensset <br/> In de database R uitvoeren   |
| Koppelingen naar voorbeelden      |    Een klein aantal New York City gegevensset worden geladen in de SQL-database `nyctaxi`. <br/> Jupyter-voorbeeld met Microsoft R en in-database analyses kunt u vinden op:<br/> `~notebooks/SQL_R_Services_End_to_End_Tutorial.ipynb`  |
| Meer hulpprogramma's op de DSVM       | SQL Server Management Studio <br/> ODBC-/ JDBC-stuurprogramma 's<br/> pyodbc, RODBC<br />Apache Drill      |

> [!NOTE]
> De SQL Server 2016 developer edition kan alleen worden gebruikt voor ontwikkelings- en testdoeleinden. U moet een licentie of een van de SQL Server-VM's uit te voeren in de productieomgeving. 


### <a name="setup"></a>Instellen

De database-server al vooraf is geconfigureerd en de Windows-services die betrekking hebben op SQL Server (zoals `SQL Server (MSSQLSERVER)`) zijn ingesteld op automatisch worden uitgevoerd. Het alleen handmatige stap moet worden uitgevoerd, is om in te schakelen In-database analyses met behulp van Microsoft-R. U kunt dit doen door het uitvoeren van de volgende opdracht uit als een eenmalige actie in SQL Server Management Studio (SSMS) nadat u bent aangemeld in als de beheerder van de computer, open een 'nieuwe Query' in SSMS, zorg ervoor dat de geselecteerde database is `master` en voer vervolgens: 

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 

        (Please replace the %COMPUTERNAME% with your VM name)
       
U kunt om uit te voeren in SQL Server Management Studio, zoek naar 'SQL Server Management Studio' op de lijst met programma's of Windows Search gebruiken om te zoeken en uitvoeren. Wanneer u hierom wordt gevraagd om referenties, kies 'Windows Authentication' en naam van de machine gebruiken of ```localhost``` in SQL Server-naam. 

### <a name="how-to-use--run-it"></a>Hoe kan ik gebruik / uitvoeren?  

De database-server met het standaarddatabase-exemplaar wordt standaard automatisch uitgevoerd. U kunt hulpprogramma's zoals SQL Server Management Studio op de virtuele machine gebruiken voor toegang tot de SQL Server-database lokaal. Lokale Administrator-account hebben beheerderstoegang op de database. 

De DSVM wordt geleverd met ODBC-stuurprogramma's en JDBC-stuurprogramma's om te communiceren met de SQL Server, Azure SQL-databases, en Azure SQL Data Warehouse van toepassingen die zijn geschreven in meerdere talen, waaronder van Python, r 

### <a name="how-is-it-configured--installed-on-the-dsvm"></a>Hoe wordt deze geconfigureerd / geïnstalleerd op de DSVM? 

De SQL-Server is geïnstalleerd op de gebruikelijke manier. Kan worden gevonden op `C:\Program Files\Microsoft SQL Server`. De R In database-instantie is gevonden op `C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\R_SERVICES`. De DSVM heeft ook een afzonderlijk zelfstandig R Server-exemplaar geïnstalleerd op `C:\Program Files\Microsoft\R Server\R_SERVER`. Deze twee-R-instanties delen niet de bibliotheken.


## <a name="apache-spark-2x-standalone"></a>Apache Spark-2.x (zelfstandig)

| | |
| ------------- | ------------- |
| Wat is het?   | Een zelfstandig (één knooppunt in-process)-exemplaar van de populaire Apache Spark-platform, een systeem voor het snel grootschalige gegevensverwerking en machine learning     |
| Ondersteunde DSVM-edities      | Linux <br /> Windows (experimenteel)      |
| Wordt doorgaans gebruikt      | * Snelle ontwikkeling van Spark/PySpark-toepassingen lokaal met kleinere gegevensset en hoger implementeert op grote Spark-clusters, zoals Azure HDInsight<br/> * Test Microsoft R Server Spark-Context <br />* Gebruik SparkML of van Microsoft open-source [MMLSpark](https://github.com/Azure/mmlspark) bibliotheek om ML-toepassingen te bouwen  |
| Koppelingen naar voorbeelden      |    Jupyter-voorbeeld: <br />&nbsp;&nbsp;* ~/notebooks/SparkML/pySpark <br /> &nbsp;&nbsp;* ~/notebooks/MMLSpark <br /> Microsoft R Server (Spark-Context): /dsvm/samples/MRS/MRSSparkContextSample.R |
| Meer hulpprogramma's op de DSVM       | PySpark, Scala<br/>Jupyter (Spark/PySpark-Kernels)<br/>Microsoft R Server, SparkR, Sparklyr <br />Apache Drill      |

### <a name="how-to-use-it"></a>Het gebruik ervan
U kunt Spark uitvoeren door het indienen van Spark-taken op de opdrachtregel met `spark-submit` of `pyspark` opdrachten. U kunt ook een Jupyter-notebook maken door een nieuwe notebook maken met de Spark-kernel. 

U kunt Spark van R met behulp van bibliotheken zoals SparkR, Sparklyr of Microsoft R Server die beschikbaar op de DSVM zijn. Zie de verwijzingen naar de voorbeelden in de voorgaande tabel. 

> [!NOTE]
> Met Microsoft R Server in Spark-context van DSVM wordt alleen ondersteund voor de Ubuntu Linux-DSVM-editie. 



### <a name="setup"></a>Instellen
Voordat u uitvoert in Spark-context in Microsoft R Server op Ubuntu Linux-DSVM-editie, moet u een setup-stap om in te schakelen van een lokale één knooppunt HDFS Hadoop en Yarn-instantie eenmaal te doen. Hadoop-services worden standaard geïnstalleerd maar uitgeschakeld op de DSVM. Als u wilt inschakelen, moet u de volgende opdrachten als root de eerste keer uitvoeren:

    echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
    cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
    chmod 0600 ~hadoop/.ssh/authorized_keys
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
    chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
    systemctl start hadoop-namenode hadoop-datanode hadoop-yarn

U kunt de Hadoop stoppen gerelateerde services wanneer u ze niet door het uitvoeren van handelingen nodig ```systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn``` een voorbeeld laat zien hoe u kunt ontwikkelen en testen van MEVR in externe Spark-context (dit is de Spark-instantie van de zelfstandige versie van de DSVM) is opgegeven en beschikbaar in de `/dsvm/samples/MRS` de map. 


### <a name="how-is-it-configured--installed-on-the-dsvm"></a>Hoe wordt deze geconfigureerd / geïnstalleerd op de DSVM? 
|Platform|Locatie ($SPARK_HOME) installeren|
|:--------|:--------|
|Windows | c:\dsvm\tools\spark-X.X.X-bin-hadoopX.X|
|Linux   | /dsvm/Tools/Spark-X.X.X-bin-hadoopX.X|


Bibliotheken om toegang te krijgen tot gegevens uit Azure BLOB Storage of Azure Data Lake-opslag (ADLS) en met behulp van Microsoft MMLSpark machine learning-bibliotheken zijn vooraf geïnstalleerd in $SPARK_HOME/JAR-bestanden. Deze JAR-bestanden worden automatisch geladen wanneer Spark wordt gestart. Spark maakt standaard gebruik van gegevens op de lokale schijf. Opdat de Spark-instantie op de DSVM voor toegang tot gegevens die zijn opgeslagen in Azure blob of ADLS die u wilt maken/configureren de `core-site.xml` bestand op basis van sjabloon gevonden in $SPARK_HOME/conf/core-site.xml.template (indien er tijdelijke aanduidingen voor Blob en ADLS zijn configuraties) met de juiste referenties voor Azure-blob en Azure Data Lake-opslag. U vindt meer gedetailleerde stappen voor het maken van de referenties van de service ADLS [hier](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-authenticate-using-active-directory). Zodra de referenties voor Azure blob of ADLS worden ingevoerd in het bestand core-site.xml, kunt u verwijzen naar de gegevens die zijn opgeslagen in de bronnen met het URI-voorvoegsel van wasb: / / of adl: / /. 

