---
title: Apache Spark gebruiken om gegevens te lezen en te schrijven naar Azure SQL database
description: Meer informatie over het instellen van een verbinding tussen een HDInsight Spark-cluster en een Azure SQL database om gegevens te lezen, gegevens te schrijven en gegevens naar een SQL database te streamen
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/21/2019
ms.openlocfilehash: 20c4571ee795c280e6c916e3080279a6d13fecce
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/09/2019
ms.locfileid: "70814212"
---
# <a name="use-hdinsight-spark-cluster-to-read-and-write-data-to-azure-sql-database"></a>HDInsight Spark-cluster gebruiken om gegevens te lezen en te schrijven naar Azure SQL database

Informatie over het verbinden van een Apache Spark cluster in azure HDInsight met een Azure SQL database en vervolgens gegevens in de SQL database te lezen, schrijven en streamen. De instructies in dit artikel gebruiken een [Jupyter notebook](https://jupyter.org/) voor het uitvoeren van de code fragmenten scala. U kunt echter een zelfstandige toepassing maken in scala of python en dezelfde taken uitvoeren.

## <a name="prerequisites"></a>Vereisten

* **Azure HDInsight Spark cluster**.  Volg de instructies in [een Apache Spark-cluster maken in HDInsight](apache-spark-jupyter-spark-sql.md).

* **Azure SQL database**. Volg de instructies in [Create a Azure SQL database](../../sql-database/sql-database-get-started-portal.md). Zorg ervoor dat u een Data Base maakt met het voor beeld- **AdventureWorksLT** schema en de gegevens. Zorg er ook voor dat u een firewall regel op server niveau maakt waarmee het IP-adres van uw client toegang heeft tot de SQL database op de server. De instructies voor het toevoegen van de firewall regel zijn beschikbaar in hetzelfde artikel. Wanneer u uw Azure SQL database hebt gemaakt, moet u ervoor zorgen dat u de volgende waarden bij de hand hebt. U hebt deze nodig om verbinding te maken met de data base vanuit een Spark-cluster.

    * De server naam die als host fungeert voor de Azure-SQL database.
    * Naam van Azure-SQL database.
    * Gebruikers naam/wacht woord voor Azure SQL database-beheerder.

* **SQL Server Management Studio**. Volg de instructies op [SSMS gebruiken om verbinding te maken en gegevens op te vragen](../../sql-database/sql-database-connect-query-ssms.md).

## <a name="create-a-jupyter-notebook"></a>Een Jupyter Notebook maken 

Maak eerst een [Jupyter notebook](https://jupyter.org/) dat is gekoppeld aan het Spark-cluster. U gebruikt dit notitie blok om de code fragmenten uit te voeren die in dit artikel worden gebruikt. 

1. Open uw cluster vanuit het [Azure Portal](https://portal.azure.com/).
1. Selecteer **Jupyter notebook** onder **cluster dashboards** aan de rechter kant.  Als **cluster dashboards**niet worden weer gegeven, selecteert u **overzicht** in het menu links. Voer de beheerdersreferenties voor het cluster in als u daarom wordt gevraagd.

    ![Jupyter notebook in Spark](./media/apache-spark-connect-to-sql-database/hdinsight-spark-cluster-dashboard-jupyter-notebook.png "Jupyter notebook in Spark")
   
   > [!NOTE]  
   > U kunt ook toegang krijgen tot het Jupyter-notebook in het Spark-cluster door de volgende URL in uw browser te openen. Vervang **CLUSTERNAME** door de naam van uw cluster.
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

1. Klik in de rechter bovenhoek van de Jupyter-notebook op **Nieuw**en klik vervolgens op **Spark** om een scala-notebook te maken. Jupyter notebooks in HDInsight Spark-cluster bieden ook de **PySpark** kernel voor Python2-toepassingen en de **PySpark3** -kernel voor Python3-toepassingen. Voor dit artikel maken we een scala-notebook.
   
    ![Kernels voor Jupyter notebook in Spark](./media/apache-spark-connect-to-sql-database/kernel-jupyter-notebook-on-spark.png "Kernels voor Jupyter notebook in Spark")

    Zie [Use Jupyter notebook kernels with Apache Spark clusters in HDInsight](apache-spark-jupyter-notebook-kernels.md) (Jupyter-notebookkernels gebruiken met Apache Spark-clusters in HDInsight) voor meer informatie over de kernels.

   > [!NOTE]  
   > In dit artikel gebruiken we een Spark-kernel (scala), omdat het streamen van gegevens van Spark naar SQL database alleen wordt ondersteund in scala en Java. Hoewel het lezen van en schrijven naar SQL kan worden uitgevoerd met behulp van python, wordt voor consistentie in dit artikel scala gebruikt voor alle drie de bewerkingen.

1. Hiermee opent u een nieuw notitie blok met een standaard naam, zonder **titel**. Klik op de naam van het notitie blok en voer de naam van uw keuze in.

    ![Een naam opgeven voor de notebook](./media/apache-spark-connect-to-sql-database/hdinsight-spark-jupyter-notebook-name.png "Een naam opgeven voor de notebook")

U kunt nu beginnen met het maken van uw toepassing.
    
## <a name="read-data-from-azure-sql-database"></a>Gegevens lezen van Azure SQL database

In deze sectie leest u gegevens uit een tabel (bijvoorbeeld **tabel saleslt. Address**) die zich in de AdventureWorks-Data Base bevindt.

1. Plak in een nieuwe Jupyter-notebook in een code-cel het volgende code fragment en vervang de waarden van de tijdelijke aanduiding door de waarden voor uw Azure-SQL database.

       // Declare the values for your Azure SQL database

       val jdbcUsername = "<SQL DB ADMIN USER>"
       val jdbcPassword = "<SQL DB ADMIN PWD>"
       val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
       val jdbcPort = 1433
       val jdbcDatabase ="<AZURE SQL DB NAME>"

    Druk op **SHIFT+ENTER** om de codecel uit te voeren.  

1. Gebruik het onderstaande fragment om een JDBC-URL te maken die u kunt door geven aan de Spark data frame `Properties` api's maakt een object voor de para meters. Plak het fragment in een code-cel en druk op **SHIFT + ENTER** om uit te voeren.

       import java.util.Properties

       val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
       val connectionProperties = new Properties()
       connectionProperties.put("user", s"${jdbcUsername}")
       connectionProperties.put("password", s"${jdbcPassword}")         

1. Gebruik het onderstaande fragment om een data frame te maken met de gegevens uit een tabel in uw Azure SQL database. In dit code fragment gebruiken we een tabel **tabel saleslt. Address** die beschikbaar is als onderdeel van de **AdventureWorksLT** -data base. Plak het fragment in een code-cel en druk op **SHIFT + ENTER** om uit te voeren.

       val sqlTableDF = spark.read.jdbc(jdbc_url, "SalesLT.Address", connectionProperties)

1. U kunt nu bewerkingen uitvoeren op de data frame, zoals het ophalen van het gegevens schema:

       sqlTableDF.printSchema
   
    De uitvoer ziet er ongeveer als volgt uit:

    ![schema-uitvoer](./media/apache-spark-connect-to-sql-database/read-from-sql-schema-output.png "schema-uitvoer")

1. U kunt ook bewerkingen uitvoeren zoals, de Top 10 rijen ophalen.

       sqlTableDF.show(10)

1. Of haal specifieke kolommen op uit de gegevensset.

       sqlTableDF.select("AddressLine1", "City").show(10)

## <a name="write-data-into-azure-sql-database"></a>Gegevens schrijven naar Azure SQL database

In deze sectie gebruiken we een voor beeld van een CSV-bestand dat op het cluster beschikbaar is om een tabel in Azure SQL database te maken en deze te vullen met gegevens. Het CSV-voorbeeld bestand (**HVAC. CSV**) is beschikbaar op alle HDInsight- `HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv`clusters op.

1. Plak in een nieuwe Jupyter-notebook in een code-cel het volgende code fragment en vervang de waarden van de tijdelijke aanduiding door de waarden voor uw Azure-SQL database.

       // Declare the values for your Azure SQL database

       val jdbcUsername = "<SQL DB ADMIN USER>"
       val jdbcPassword = "<SQL DB ADMIN PWD>"
       val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
       val jdbcPort = 1433
       val jdbcDatabase ="<AZURE SQL DB NAME>"

    Druk op **SHIFT+ENTER** om de codecel uit te voeren.  

1. Het volgende code fragment bouwt een JDBC-URL die u kunt door geven aan de Spark data frame `Properties` api's maakt een object voor de para meters. Plak het fragment in een code-cel en druk op **SHIFT + ENTER** om uit te voeren.

       import java.util.Properties

       val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
       val connectionProperties = new Properties()
       connectionProperties.put("user", s"${jdbcUsername}")
       connectionProperties.put("password", s"${jdbcPassword}")

1. Gebruik het volgende code fragment voor het extra heren van het schema van de gegevens in HVAC. CSV en gebruik het schema om de gegevens uit het CSV- `readDf`bestand in een data frame te laden. Plak het fragment in een code-cel en druk op **SHIFT + ENTER** om uit te voeren.

       val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
       val readDf = spark.read.format("csv").schema(userSchema).load("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

1. Gebruik de `readDf` data frame om een tijdelijke `temphvactable`tabel te maken. Gebruik vervolgens de tijdelijke tabel om een Hive- `hvactable_hive`tabel te maken.

       readDf.createOrReplaceTempView("temphvactable")
       spark.sql("create table hvactable_hive as select * from temphvactable")

1. Ten slotte gebruikt u de Hive-tabel om een tabel in Azure SQL database te maken. Het volgende code fragment `hvactable` wordt gemaakt in Azure SQL database.

       spark.table("hvactable_hive").write.jdbc(jdbc_url, "hvactable", connectionProperties)

1. Maak verbinding met de Azure-SQL database met behulp van SSMS en `dbo.hvactable` Controleer of u er een ziet.

    a. Start SSMS en maak verbinding met de Azure-SQL database door verbindings gegevens op te geven, zoals wordt weer gegeven in de onderstaande scherm afbeelding.

    ![Verbinding maken met SQL database met behulp van SSMS1](./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms.png "Verbinding maken met SQL database met behulp van SSMS1")

    b. Vouw vanuit het Objectverkenner de Azure-SQL database en het tabel knooppunt uit om de **dbo. hvactable** gemaakt weer te geven.

    ![Verbinding maken met SQL database met behulp van SSMS2](./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms-locate-table.png "Verbinding maken met SQL database met behulp van SSMS2")

1. Voer een query uit in SSMS om de kolommen in de tabel weer te geven.

        SELECT * from hvactable

## <a name="stream-data-into-azure-sql-database"></a>Gegevens streamen naar Azure SQL database

In deze sectie worden gegevens gestreamd naar de **hvactable** die u al hebt gemaakt in Azure SQL database in de vorige sectie.

1. Als eerste stap zorgt u ervoor dat de **hvactable**geen records bevat. Gebruik SSMS om de volgende query uit te voeren op de tabel.

       TRUNCATE TABLE [dbo].[hvactable]

1. Maak een nieuw Jupyter-notebook op het HDInsight Spark-cluster. Plak het volgende fragment in een code-cel en druk op **SHIFT + ENTER**:

       import org.apache.spark.sql._
       import org.apache.spark.sql.types._
       import org.apache.spark.sql.functions._
       import org.apache.spark.sql.streaming._
       import java.sql.{Connection,DriverManager,ResultSet}

1. We streamen gegevens uit het **HVAC. CSV** -bestand in de hvactable. Het bestand HVAC. csv is beschikbaar op het cluster `/HdiSamples/HdiSamples/SensorSampleData/HVAC/`op. In het volgende code fragment ontvangen we eerst het schema van de gegevens die moeten worden gestreamd. Vervolgens maken we een streaming-data frame met behulp van dat schema. Plak het fragment in een code-cel en druk op **SHIFT + ENTER** om uit te voeren.

       val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
       val readStreamDf = spark.readStream.schema(userSchema).csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/") 
       readStreamDf.printSchema

1. In de uitvoer ziet u het schema van **HVAC. CSV**. De **hvactable** heeft ook hetzelfde schema. In de uitvoer worden de kolommen in de tabel weer gegeven.

    ![Schema van tabel](./media/apache-spark-connect-to-sql-database/schema-of-table.png "Schema van tabel")

1. Gebruik ten slotte het volgende fragment om gegevens uit het HVAC. CSV-bestand te lezen en te streamen naar de **hvactable** in Azure SQL database. Plak het fragment in een code-cel, vervang de waarden van de tijdelijke aanduiding door de waarden voor uw Azure-SQL database en druk op **SHIFT + ENTER** om uit te voeren.

       val WriteToSQLQuery  = readStreamDf.writeStream.foreach(new ForeachWriter[Row] {
          var connection:java.sql.Connection = _
          var statement:java.sql.Statement = _
          
          val jdbcUsername = "<SQL DB ADMIN USER>"
          val jdbcPassword = "<SQL DB ADMIN PWD>"
          val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
          val jdbcPort = 1433
          val jdbcDatabase ="<AZURE SQL DB NAME>"
          val driver = "com.microsoft.sqlserver.jdbc.SQLServerDriver"
          val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;"
  
         def open(partitionId: Long, version: Long):Boolean = {
           Class.forName(driver)
           connection = DriverManager.getConnection(jdbc_url, jdbcUsername, jdbcPassword)
           statement = connection.createStatement
           true
         }
  
         def process(value: Row): Unit = {
           val Date  = value(0)
           val Time = value(1)
           val TargetTemp = value(2)
           val ActualTemp = value(3)
           val System = value(4)
           val SystemAge = value(5)
           val BuildingID = value(6)  
    
           val valueStr = "'" + Date + "'," + "'" + Time + "'," + "'" + TargetTemp + "'," + "'" + ActualTemp + "'," + "'" + System + "'," + "'" + SystemAge + "'," + "'" + BuildingID + "'"
           statement.execute("INSERT INTO " + "dbo.hvactable" + " VALUES (" + valueStr + ")")   
           }

         def close(errorOrNull: Throwable): Unit = {
            connection.close
          }
         })
        
         var streamingQuery = WriteToSQLQuery.start()

1. Controleer of de gegevens worden gestreamd naar het **hvactable** door de volgende query uit te voeren in SQL Server Management Studio (SSMS). Telkens wanneer u de query uitvoert, wordt het aantal rijen in de tabel verhoogd.

        SELECT COUNT(*) FROM hvactable

## <a name="next-steps"></a>Volgende stappen

* [HDInsight Spark-cluster gebruiken voor het analyseren van gegevens in Data Lake Storage](apache-spark-use-with-data-lake-store.md)
* [Structured streaming-gebeurtenissen verwerken met EventHub](apache-spark-eventhub-structured-streaming.md)
* [Apache Spark Structured streamen gebruiken met Apache Kafka in HDInsight](../hdinsight-apache-kafka-spark-structured-streaming.md)
