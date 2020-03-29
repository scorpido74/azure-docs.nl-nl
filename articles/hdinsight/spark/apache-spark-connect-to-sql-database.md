---
title: Apache Spark gebruiken om gegevens te lezen en te schrijven naar Azure SQL Database
description: Meer informatie over het instellen van een verbinding tussen HDInsight Spark-cluster en een Azure SQL Database om gegevens te lezen, gegevens te schrijven en te streamen naar een SQL-database
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/05/2020
ms.openlocfilehash: 4e0c1626582297aa7d80cbbd4241b6f81e314f8f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78927466"
---
# <a name="use-hdinsight-spark-cluster-to-read-and-write-data-to-azure-sql-database"></a>HdInsight Spark-cluster gebruiken om gegevens te lezen en te schrijven naar Azure SQL Database

Lees hoe u een Apache Spark-cluster in Azure HDInsight verbindt met een Azure SQL-database en vervolgens gegevens leest, schrijft en streamt naar de SQL-database. De instructies in dit artikel gebruiken een [Jupyter-notitieblok](https://jupyter.org/) om de Scala-codefragmenten uit te voeren. U echter een zelfstandige toepassing maken in Scala of Python en dezelfde taken uitvoeren.

## <a name="prerequisites"></a>Vereisten

* Azure HDInsight Spark-cluster.  Volg de instructies bij [Een Apache Spark-cluster maken in HDInsight](apache-spark-jupyter-spark-sql.md).

* Azure SQL Database. Volg de instructies bij [Een Azure SQL Database maken.](../../sql-database/sql-database-get-started-portal.md) Zorg ervoor dat u een database maakt met het voorbeeld **van adventureworkslt-schema** en gegevens. Zorg er ook voor dat u een firewallregel op serverniveau maakt om het IP-adres van uw client toegang te geven tot de SQL-database op de server. De instructies voor het toevoegen van de firewallregel zijn beschikbaar in hetzelfde artikel. Zodra u uw Azure SQL Database hebt gemaakt, moet u ervoor zorgen dat u de volgende waarden bij de hand houdt. Je hebt ze nodig om verbinding te maken met de database vanuit een Spark-cluster.

    * Servernaam die de Azure SQL Database host.
    * Azure SQL-databasenaam.
    * De gebruikersnaam /wachtwoord van azure SQL Database-beheerder.

* SQL Server Management Studio (SSMS). Volg de instructies bij [SSMS gebruiken om gegevens te verbinden en op te vragen.](../../sql-database/sql-database-connect-query-ssms.md)

## <a name="create-a-jupyter-notebook"></a>Een Jupyter Notebook maken

Begin met het maken van een [Jupyter-notitieblok](https://jupyter.org/) dat is gekoppeld aan het Spark-cluster. U gebruikt dit notitieblok om de codefragmenten uit te voeren die in dit artikel worden gebruikt.

1. Open uw cluster vanuit de [Azure-portal.](https://portal.azure.com/)
1. Selecteer **Jupyter-notitieblok** onder **clusterdashboards** aan de rechterkant.  Als u **clusterdashboards**niet ziet, selecteert u **Overzicht** in het linkermenu. Voer de beheerdersreferenties voor het cluster in als u daarom wordt gevraagd.

    ![Jupyter notebook op Apache Spark](./media/apache-spark-connect-to-sql-database/hdinsight-spark-cluster-dashboard-jupyter-notebook.png "Jupyter-laptop op Spark")

   > [!NOTE]  
   > U ook toegang krijgen tot het Jupyter-notitieblok op het Spark-cluster door de volgende URL in uw browser te openen. Vervang **CLUSTERNAME** door de naam van uw cluster.
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

1. Klik in het Jupyter-notitieblok in de rechterbovenhoek op **Nieuw**en klik vervolgens op **Spark** om een Scala-notitieblok te maken. Jupyter-laptops op hdInsight Spark-cluster bieden ook de **PySpark-kernel** voor Python2-toepassingen en de **PySpark3-kernel** voor Python3-toepassingen. Voor dit artikel maken we een Scala-notebook.

    ![Kernels voor Jupyter-laptop op Spark](./media/apache-spark-connect-to-sql-database/kernel-jupyter-notebook-on-spark.png "Kernels voor Jupyter-laptop op Spark")

    Zie [Use Jupyter notebook kernels with Apache Spark clusters in HDInsight](apache-spark-jupyter-notebook-kernels.md) (Jupyter-notebookkernels gebruiken met Apache Spark-clusters in HDInsight) voor meer informatie over de kernels.

   > [!NOTE]  
   > In dit artikel gebruiken we een Spark -kernel (Scala), omdat het streamen van gegevens van Spark naar SQL-database momenteel alleen wordt ondersteund in Scala en Java. Hoewel lezen van en schrijven in SQL kan worden gedaan met behulp van Python, voor consistentie in dit artikel, gebruiken we Scala voor alle drie de bewerkingen.

1. Hiermee wordt een nieuw notitieblok geopend met een standaardnaam, **Zonder titel.** Klik op de naam van het notitieblok en voer een naam van uw keuze in.

    ![Een naam opgeven voor de notebook](./media/apache-spark-connect-to-sql-database/hdinsight-spark-jupyter-notebook-name.png "Een naam opgeven voor de notebook")

U nu beginnen met het maken van uw toepassing.

## <a name="read-data-from-azure-sql-database"></a>Gegevens uit Azure SQL-database lezen

In deze sectie leest u gegevens uit een tabel (bijvoorbeeld **SalesLT.Address)** die in de AdventureWorks-database bestaat.

1. Plak in een nieuw Jupyter-notitieblok in een codecel het volgende fragment en vervang de tijdelijke aanduidingswaarden door de waarden voor uw Azure SQL Database.

       // Declare the values for your Azure SQL database

       val jdbcUsername = "<SQL DB ADMIN USER>"
       val jdbcPassword = "<SQL DB ADMIN PWD>"
       val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
       val jdbcPort = 1433
       val jdbcDatabase ="<AZURE SQL DB NAME>"

    Druk op **SHIFT+ENTER** om de codecel uit te voeren.  

1. Gebruik het onderstaande fragment om een JDBC-URL te maken die u doorgeven aan de Spark-gegevensframe-API's. De code `Properties` maakt een object om de parameters vast te houden. Plak het fragment in een codecel en druk op **SHIFT + ENTER** om uit te voeren.

       import java.util.Properties

       val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
       val connectionProperties = new Properties()
       connectionProperties.put("user", s"${jdbcUsername}")
       connectionProperties.put("password", s"${jdbcPassword}")         

1. Gebruik het onderstaande fragment om een gegevensframe te maken met de gegevens uit een tabel in uw Azure SQL Database. In dit fragment gebruiken `SalesLT.Address` we een tabel die beschikbaar is als onderdeel van de **AdventureWorksLT-database.** Plak het fragment in een codecel en druk op **SHIFT + ENTER** om uit te voeren.

       val sqlTableDF = spark.read.jdbc(jdbc_url, "SalesLT.Address", connectionProperties)

1. U nu bewerkingen uitvoeren op het gegevensframe, zoals het verkrijgen van het gegevensschema:

       sqlTableDF.printSchema

    U ziet een uitvoer die vergelijkbaar is met de volgende:

    ![schema-uitvoer](./media/apache-spark-connect-to-sql-database/read-from-sql-schema-output.png "schema-uitvoer")

1. U ook bewerkingen uitvoeren zoals de bovenste 10 rijen ophalen.

       sqlTableDF.show(10)

1. Of haal specifieke kolommen op uit de gegevensset.

       sqlTableDF.select("AddressLine1", "City").show(10)

## <a name="write-data-into-azure-sql-database"></a>Gegevens schrijven in Azure SQL-database

In deze sectie gebruiken we een voorbeeld csv-bestand dat beschikbaar is op het cluster om een tabel in Azure SQL Database te maken en te vullen met gegevens. Het voorbeeld CSV-bestand **(HVAC.csv)** is beschikbaar `HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv`op alle HDInsight-clusters op .

1. Plak in een nieuw Jupyter-notitieblok in een codecel het volgende fragment en vervang de tijdelijke aanduidingswaarden door de waarden voor uw Azure SQL Database.

       // Declare the values for your Azure SQL database

       val jdbcUsername = "<SQL DB ADMIN USER>"
       val jdbcPassword = "<SQL DB ADMIN PWD>"
       val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
       val jdbcPort = 1433
       val jdbcDatabase ="<AZURE SQL DB NAME>"

    Druk op **SHIFT+ENTER** om de codecel uit te voeren.  

1. In het volgende fragment wordt een JDBC-URL gebouwd die u doorgeven aan de SPARK-gegevensframe-API's. De code `Properties` maakt een object om de parameters vast te houden. Plak het fragment in een codecel en druk op **SHIFT + ENTER** om uit te voeren.

       import java.util.Properties

       val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
       val connectionProperties = new Properties()
       connectionProperties.put("user", s"${jdbcUsername}")
       connectionProperties.put("password", s"${jdbcPassword}")

1. Gebruik het volgende fragment om het schema van de gegevens in HVAC.csv te extraheren en `readDf`gebruik het schema om de gegevens van de CSV in een dataframe te laden. Plak het fragment in een codecel en druk op **SHIFT + ENTER** om uit te voeren.

       val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
       val readDf = spark.read.format("csv").schema(userSchema).load("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

1. Gebruik `readDf` het gegevensframe om een `temphvactable`tijdelijke tabel te maken. Gebruik vervolgens de tijdelijke tabel om `hvactable_hive`een bijenkorftabel te maken.

       readDf.createOrReplaceTempView("temphvactable")
       spark.sql("create table hvactable_hive as select * from temphvactable")

1. Gebruik ten slotte de korftabel om een tabel in Azure SQL Database te maken. Het volgende `hvactable` fragment maakt in Azure SQL Database.

       spark.table("hvactable_hive").write.jdbc(jdbc_url, "hvactable", connectionProperties)

1. Maak verbinding met de Azure SQL Database met `dbo.hvactable` SSMS en controleer of u daar een auto ziet.

    a. Start SSMS en maak verbinding met de Azure SQL Database door verbindingsgegevens te verstrekken zoals in de onderstaande schermafbeelding wordt weergegeven.

    ![Verbinding maken met SQL-database met SSMS1](./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms.png "Verbinding maken met SQL-database met SSMS1")

    b. Vouw vanuit **Object Explorer**de Azure SQL Database en het tabelknooppunt uit om de **dbo.hvactable** te zien die is gemaakt.

    ![Verbinding maken met SQL-database met SSMS2](./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms-locate-table.png "Verbinding maken met SQL-database met SSMS2")

1. Voer een query uit in SSMS om de kolommen in de tabel te bekijken.

    ```sql
    SELECT * from hvactable
    ```

## <a name="stream-data-into-azure-sql-database"></a>Gegevens streamen naar Azure SQL-database

In deze sectie streamen we `hvactable` gegevens naar de gegevens die u in de vorige sectie al hebt gemaakt in Azure SQL Database.

1. Als eerste stap, zorg ervoor dat `hvactable`er geen records in de . Voer met SSMS de volgende query in de tabel uit.

    ```sql
    TRUNCATE TABLE [dbo].[hvactable]
    ```

1. Maak een nieuwe Jupyter-laptop op het HDInsight Spark-cluster. Plak in een codecel het volgende fragment en druk op **Shift + ENTER:**

       import org.apache.spark.sql._
       import org.apache.spark.sql.types._
       import org.apache.spark.sql.functions._
       import org.apache.spark.sql.streaming._
       import java.sql.{Connection,DriverManager,ResultSet}

1. We streamen gegevens van de `hvactable` **HVAC.csv** naar de . HVAC.csv-bestand is beschikbaar `/HdiSamples/HdiSamples/SensorSampleData/HVAC/`op het cluster op . In het volgende fragment krijgen we eerst het schema van de gegevens die moeten worden gestreamd. Vervolgens maken we een streaming dataframe met behulp van dat schema. Plak het fragment in een codecel en druk op **SHIFT + ENTER** om uit te voeren.

       val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
       val readStreamDf = spark.readStream.schema(userSchema).csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/") 
       readStreamDf.printSchema

1. De uitvoer toont het schema van **HVAC.csv**. Het `hvactable` heeft hetzelfde schema ook. De uitvoer bevat de kolommen in de tabel.

    ![hdinsight Apache Spark schema tabel](./media/apache-spark-connect-to-sql-database/hdinsight-schema-table.png "Schema van tabel")

1. Gebruik ten slotte het volgende fragment om gegevens van de `hvactable` HVAC.csv te lezen en deze naar de in Azure SQL Database te streamen. Plak het fragment in een codecel, vervang de tijdelijke aanduidingswaarden door de waarden voor uw Azure SQL Database en druk vervolgens op **SHIFT + ENTER** om uit te voeren.

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

1. Controleer of de gegevens worden `hvactable` gestreamd naar de volgende query in SQL Server Management Studio (SSMS). Elke keer dat u de query uitvoert, wordt het aantal rijen in de tabel weergegeven.

    ```sql
    SELECT COUNT(*) FROM hvactable
    ```

## <a name="next-steps"></a>Volgende stappen

* [HDInsight Spark-cluster gebruiken om gegevens in Data Lake Storage te analyseren](apache-spark-use-with-data-lake-store.md)
* [Gestructureerde streaminggebeurtenissen verwerken met EventHub](apache-spark-eventhub-structured-streaming.md)
* [Apache Spark Structured Streaming gebruiken met Apache Kafka op HDInsight](../hdinsight-apache-kafka-spark-structured-streaming.md)
