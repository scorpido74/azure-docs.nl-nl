---
title: Gebruik de Azure Data Explorer-connector voor Apache Spark om gegevens te verplaatsen tussen Azure Data Explorer-en Spark-clusters.
description: In dit onderwerp wordt beschreven hoe u gegevens kunt verplaatsen tussen Azure Data Explorer en Apache Spark-clusters.
author: orspod
ms.author: orspodek
ms.reviewer: michazag
ms.service: data-explorer
ms.topic: conceptual
ms.date: 1/14/2020
ms.openlocfilehash: b358287664ac6d6a3b641e1ab63073810ceb4c40
ms.sourcegitcommit: 5192c04feaa3d1bd564efe957f200b7b1a93a381
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2020
ms.locfileid: "78208585"
---
# <a name="azure-data-explorer-connector-for-apache-spark"></a>Azure Data Explorer-connector voor Apache Spark

[Apache Spark](https://spark.apache.org/) is een geïntegreerde analyse-engine voor grootschalige gegevens verwerking. Azure Data Explorer is een snelle, volledig beheerde service voor gegevens analyse voor real-time analyse op grote hoeveel heden gegevens. 

De Azure Data Explorer-connector voor Spark is een [open-source project](https://github.com/Azure/azure-kusto-spark) dat kan worden uitgevoerd op een Spark-cluster. Het implementeert de gegevens bron en gegevens Sink voor het verplaatsen van gegevens tussen Azure Data Explorer-en Spark-clusters. Met Azure Data Explorer en Apache Spark kunt u snelle en schaal bare toepassingen bouwen die gericht zijn op gegevensgestuurde scenario's. Bijvoorbeeld machine learning (ML), uittreksel-Transform-load (ETL) en Log Analytics. Met de connector wordt Azure Data Explorer een geldig gegevens Archief voor standaard Spark-bron-en Sink-bewerkingen, zoals schrijven, lezen en writeStream.

U kunt schrijven naar Azure Data Explorer in de batch-of de streaming-modus. Het lezen van Azure Data Explorer ondersteunt het weghalen van kolommen en predikaten pushdown, waarmee de gegevens in azure Data Explorer worden gefilterd, waardoor het volume van de overgedragen gegevens wordt verkleind.

In dit onderwerp wordt beschreven hoe u de Azure Data Explorer Spark-connector installeert en configureert en gegevens verplaatst tussen Azure-Data Explorer en Apache Spark-clusters.

> [!NOTE]
> Hoewel sommige van de onderstaande voor beelden verwijzen naar een [Azure Databricks](https://docs.azuredatabricks.net/) Spark-cluster, nemen Azure Data Explorer Spark-connector geen rechtstreekse afhankelijkheden op Databricks of andere Spark-distributie.

## <a name="prerequisites"></a>Vereisten

* [Een Azure Data Explorer-cluster en-data base maken](/azure/data-explorer/create-cluster-database-portal) 
* Een Spark-cluster maken
* Azure Data Explorer-connector bibliotheek installeren:
    * Vooraf ontwikkelde bibliotheken voor [Spark 2,4, Scala 2,11](https://github.com/Azure/azure-kusto-spark/releases) 
    * [Maven opslag plaats](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/spark-kusto-connector)
* [Maven 3. x](https://maven.apache.org/download.cgi) geïnstalleerd

> [!TIP]
> 2.3. x-versies worden ook ondersteund, maar kunnen wijzigingen in pom. XML-afhankelijkheden vereisen.

## <a name="how-to-build-the-spark-connector"></a>De Spark-connector maken

> [!NOTE]
> Deze stap is optioneel. Als u vooraf gemaakte bibliotheken gebruikt, gaat u naar [Spark-cluster installatie](#spark-cluster-setup).

### <a name="build-prerequisites"></a>Vereiste onderdelen maken

1. Installeer de bibliotheken die worden vermeld in [afhankelijkheden](https://github.com/Azure/azure-kusto-spark#dependencies) , waaronder de volgende [Kusto Java SDK](/azure/kusto/api/java/kusto-java-client-library) -bibliotheken:
    * [Kusto data-client](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-data)
    * [Kusto opname-client](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-ingest)

1. Raadpleeg [deze bron](https://github.com/Azure/azure-kusto-spark) voor het bouwen van de Spark-connector.

1. Voor scala/Java-toepassingen met Maven-project definities koppelt u uw toepassing aan het volgende artefact (de nieuwste versie kan anders zijn):
    
    ```Maven
       <dependency>
         <groupId>com.microsoft.azure</groupId>
         <artifactId>spark-kusto-connector</artifactId>
         <version>1.1.0</version>
       </dependency>
    ```

### <a name="build-commands"></a>Opdrachten bouwen

Om jar te bouwen en alle tests uit te voeren:

```
mvn clean package
```

Als u jar wilt bouwen, voert u alle tests uit en installeert u jar naar de lokale maven-opslag plaats:

```
mvn clean install
```

Zie voor meer informatie [connector gebruik](https://github.com/Azure/azure-kusto-spark#usage).

## <a name="spark-cluster-setup"></a>Configuratie van Spark-cluster

> [!NOTE]
> Het is raadzaam om de nieuwste versie van de Azure Data Explorer Spark-connector te gebruiken bij het uitvoeren van de volgende stappen.

1. Configureer de volgende Spark-cluster instellingen op basis van Azure Databricks cluster met Spark 2.4.4 en scala 2,11:

    ![Databricks-cluster instellingen](media/spark-connector/databricks-cluster.png)
    
1. Installeer de meest recente Spark-kusto-connector bibliotheek van Maven:
    
    ![bibliotheken importeren](media/spark-connector/db-libraries-view.png) ![Selecteer Spark-Kusto-connector](media/spark-connector/db-dependencies.png)

1. Controleer of alle vereiste bibliotheken zijn geïnstalleerd:

    ![Geïnstalleerde bibliotheken controleren](media/spark-connector/db-libraries-view.png)

1. Controleer voor installatie met behulp van een JAR-bestand of er aanvullende afhankelijkheden zijn geïnstalleerd:

    ![Afhankelijkheden toevoegen](media/spark-connector/db-not-maven.png)

## <a name="authentication"></a>Verificatie

Met Azure Data Explorer Spark-connector kunt u met behulp van een van de volgende methoden verifiëren met Azure Active Directory (Azure AD):
* Een [Azure AD-toepassing](#azure-ad-application-authentication)
* Een [Azure AD-toegangs token](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#direct-authentication-with-access-token)
* [Verificatie van apparaten](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#device-authentication) (voor niet-productie scenario's)
* Een [Azure Key Vault](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#key-vault) om toegang te krijgen tot de Key Vault resource, het Azure-sleutel kluis-pakket installeren en toepassings referenties opgeven.

### <a name="azure-ad-application-authentication"></a>Azure AD-toepassings verificatie

Azure AD-toepassings verificatie is de eenvoudigste en meest voorkomende verificatie methode en wordt aanbevolen voor de Azure Data Explorer Spark-connector.

|Eigenschappen  |Beschrijving  |
|---------|---------|
|**KUSTO_AAD_CLIENT_ID**     |   Id van de Azure AD-toepassing (client).      |
|**KUSTO_AAD_AUTHORITY_ID**     |  Azure AD-verificatie autoriteit. ID van Azure AD-Directory (Tenant).        |
|**KUSTO_AAD_CLIENT_PASSWORD**    |    Azure AD-toepassings sleutel voor de client.     |

### <a name="azure-data-explorer-privileges"></a>Bevoegdheden voor Azure Data Explorer

Verleen de volgende bevoegdheden op een Azure Data Explorer-cluster:

* Voor lees bewerkingen (gegevens bron) moet de Azure AD-identiteit beschikken over *Viewer* -bevoegdheden voor de doel database of over *beheerders* rechten voor de doel tabel.
* Voor schrijven (gegevens-Sink) moet de Azure AD-identiteit over *ingestie* -bevoegdheden beschikken voor de doel database. Het moet ook *gebruikers* rechten hebben voor de doel database om nieuwe tabellen te maken. Als de doel tabel al bestaat, moet u *beheerders* bevoegdheden configureren voor de doel tabel.
 
Zie [autorisatie op basis van rollen](/azure/kusto/management/access-control/role-based-authorization)voor meer informatie over de belangrijkste rollen van Azure Data Explorer. Zie [beveiligings rollen](/azure/kusto/management/security-roles)beheren voor het beheren van beveiligings rollen.

## <a name="spark-sink-writing-to-azure-data-explorer"></a>Spark-Sink: schrijven naar Azure Data Explorer

1. Sink-para meters instellen:

     ```scala
    val KustoSparkTestAppId = dbutils.secrets.get(scope = "KustoDemos", key = "KustoSparkTestAppId")
    val KustoSparkTestAppKey = dbutils.secrets.get(scope = "KustoDemos", key = "KustoSparkTestAppKey")
 
    val appId = KustoSparkTestAppId
    val appKey = KustoSparkTestAppKey
    val authorityId = "72f988bf-86f1-41af-91ab-2d7cd011db47" // Optional - defaults to microsoft.com
    val cluster = "Sparktest.eastus2"
    val database = "TestDb"
    val table = "StringAndIntTable"
    ```

1. Schrijf Spark data frame naar Azure Data Explorer cluster als batch:

    ```scala
    import com.microsoft.kusto.spark.datasink.KustoSinkOptions
    import org.apache.spark.sql.{SaveMode, SparkSession}

    df.write
      .format("com.microsoft.kusto.spark.datasource")
      .option(KustoSinkOptions.KUSTO_CLUSTER, cluster)
      .option(KustoSinkOptions.KUSTO_DATABASE, database)
      .option(KustoSinkOptions.KUSTO_TABLE, "Demo3_spark")
      .option(KustoSinkOptions.KUSTO_AAD_CLIENT_ID, appId)
      .option(KustoSinkOptions.KUSTO_AAD_CLIENT_PASSWORD, appKey)
      .option(KustoSinkOptions.KUSTO_AAD_AUTHORITY_ID, authorityId)
      .option(KustoSinkOptions.KUSTO_TABLE_CREATE_OPTIONS, "CreateIfNotExist")
      .mode(SaveMode.Append)
      .save()  
    ```
    
   Of gebruik de vereenvoudigde syntaxis:
   
    ```scala
         import com.microsoft.kusto.spark.datasink.SparkIngestionProperties
         import com.microsoft.kusto.spark.sql.extension.SparkExtension._
         
         val sparkIngestionProperties = Some(new SparkIngestionProperties()) // Optional, use None if not needed
         df.write.kusto(cluster, database, table, conf, sparkIngestionProperties)
    ```
   
1. Streaminggegevens schrijven:

    ```scala    
    import org.apache.spark.sql.streaming.Trigger
    import java.util.concurrent.TimeUnit
    import java.util.concurrent.TimeUnit
    import org.apache.spark.sql.streaming.Trigger

    // Set up a checkpoint and disable codeGen. 
    spark.conf.set("spark.sql.streaming.checkpointLocation", "/FileStore/temp/checkpoint")
        
    // Write to a Kusto table from a streaming source
    val kustoQ = df
          .writeStream
          .format("com.microsoft.kusto.spark.datasink.KustoSinkProvider")
          .options(conf) 
          .option(KustoSinkOptions.KUSTO_WRITE_ENABLE_ASYNC, "true") // Optional, better for streaming, harder to handle errors
          .trigger(Trigger.ProcessingTime(TimeUnit.SECONDS.toMillis(10))) // Sync this with the ingestionBatching policy of the database
          .start()
    ```

## <a name="spark-source-reading-from-azure-data-explorer"></a>Spark-Bron: lezen van Azure Data Explorer

1. Bij het lezen [van kleine hoeveel heden gegevens](/azure/kusto/concepts/querylimits), definieert u de gegevens query:

    ```scala
    import com.microsoft.kusto.spark.datasource.KustoSourceOptions
    import org.apache.spark.SparkConf
    import org.apache.spark.sql._
    import com.microsoft.azure.kusto.data.ClientRequestProperties

    val query = s"$table | where (ColB % 1000 == 0) | distinct ColA"
    val conf: Map[String, String] = Map(
          KustoSourceOptions.KUSTO_AAD_CLIENT_ID -> appId,
          KustoSourceOptions.KUSTO_AAD_CLIENT_PASSWORD -> appKey
        )

    val df = spark.read.format("com.microsoft.kusto.spark.datasource").
      options(conf).
      option(KustoSourceOptions.KUSTO_QUERY, query).
      option(KustoSourceOptions.KUSTO_DATABASE, database).
      option(KustoSourceOptions.KUSTO_CLUSTER, cluster).
      load()

    // Simplified syntax flavor
    import com.microsoft.kusto.spark.sql.extension.SparkExtension._
    
    val cpr: Option[ClientRequestProperties] = None // Optional
    val df2 = spark.read.kusto(cluster, database, query, conf, cpr)
    display(df2)
    ```

1. Optioneel: als **u** de tijdelijke Blob-opslag opgeeft (en geen Azure Data Explorer), worden de blobs gemaakt onder verantwoordelijkheid van de aanroeper. Dit omvat het inrichten van de opslag, het roteren van toegangs sleutels en het verwijderen van tijdelijke artefacten. 
    De KustoBlobStorageUtils-module bevat hulp functies voor het verwijderen van blobs op basis van de account-en container coördinaten en account referenties, of een volledige SAS-URL met de machtigingen schrijven, lezen en lijst. Wanneer de bijbehorende RDD niet meer nodig is, slaat elke trans actie tijdelijke BLOB-artefacten op in een afzonderlijke map. Deze directory wordt vastgelegd als onderdeel van informatie logboeken met lees transacties die zijn gerapporteerd op het Spark-stuur programma.

    ```scala
    // Use either container/account-key/account name, or container SaS
    val container = dbutils.secrets.get(scope = "KustoDemos", key = "blobContainer")
    val storageAccountKey = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountKey")
    val storageAccountName = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountName")
    // val storageSas = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageSasUrl")
    ```

    In het bovenstaande voor beeld is de Key Vault niet toegankelijk via de connector interface; Er wordt een eenvoudigere methode gebruikt voor het gebruik van de Databricks-geheimen.

1. Lezen uit Azure Data Explorer.

    * Als **u** de tijdelijke Blob-opslag opgeeft, leest u van Azure Data Explorer als volgt:

        ```scala
         val conf3 = Map(
              KustoSourceOptions.KUSTO_AAD_CLIENT_ID -> appId,
              KustoSourceOptions.KUSTO_AAD_CLIENT_PASSWORD -> appKey
              KustoSourceOptions.KUSTO_BLOB_STORAGE_SAS_URL -> storageSas)
        val df2 = spark.read.kusto(cluster, database, "ReallyBigTable", conf3)
        
        val dfFiltered = df2
          .where(df2.col("ColA").startsWith("row-2"))
          .filter("ColB > 12")
          .filter("ColB <= 21")
          .select("ColA")
        
        display(dfFiltered)
        ```

    * Als **azure Data Explorer** de tijdelijke Blob-opslag biedt, lees dan van Azure Data Explorer als volgt:
    
        ```scala
        val dfFiltered = df2
          .where(df2.col("ColA").startsWith("row-2"))
          .filter("ColB > 12")
          .filter("ColB <= 21")
          .select("ColA")
        
        display(dfFiltered)
        ```

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de [Azure Data Explorer Spark-connector](https://github.com/Azure/azure-kusto-spark/tree/master/docs)
* [Voorbeeld code voor Java en python](https://github.com/Azure/azure-kusto-spark/tree/master/samples/src/main)
