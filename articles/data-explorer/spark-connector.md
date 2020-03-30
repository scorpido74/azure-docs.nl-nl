---
title: Gebruik de Azure Data Explorer-connector voor Apache Spark om gegevens tussen Azure Data Explorer en Spark-clusters te verplaatsen.
description: In dit onderwerp ziet u hoe u gegevens verplaatst tussen Azure Data Explorer en Apache Spark-clusters.
author: orspod
ms.author: orspodek
ms.reviewer: michazag
ms.service: data-explorer
ms.topic: conceptual
ms.date: 1/14/2020
ms.openlocfilehash: b358287664ac6d6a3b641e1ab63073810ceb4c40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78208585"
---
# <a name="azure-data-explorer-connector-for-apache-spark"></a>Azure Data Explorer-connector voor Apache Spark

[Apache Spark](https://spark.apache.org/) is een uniforme analyse-engine voor grootschalige gegevensverwerking. Azure Data Explorer is een snelle, volledig beheerde data-analyseservice voor realtime analyse van grote hoeveelheden gegevens. 

De Azure Data Explorer-connector voor Spark is een [open source-project](https://github.com/Azure/azure-kusto-spark) dat op elk Spark-cluster kan worden uitgevoerd. Het implementeert gegevensbron en gegevenssink voor het verplaatsen van gegevens in Azure Data Explorer- en Spark-clusters. Met Azure Data Explorer en Apache Spark u snelle en schaalbare toepassingen bouwen die zich richten op datagestuurde scenario's. Bijvoorbeeld machine learning (ML), Extract-Transform-Load (ETL) en Log Analytics. Met de connector wordt Azure Data Explorer een geldig gegevensarchief voor standaard spark-bron- en sinkbewerkingen, zoals schrijven, lezen en schrijvenStream.

U naar Azure Data Explorer schrijven in batch- of streamingmodus. Lezen vanuit Azure Data Explorer ondersteunt kolomsnoeiing en predicaat pushdown, die de gegevens in Azure Data Explorer filtert, waardoor het volume van overgedragen gegevens wordt verminderd.

In dit onderwerp wordt beschreven hoe u de Azure Data Explorer Spark-connector installeert en configureert en gegevens verplaatst tussen Azure Data Explorer en Apache Spark-clusters.

> [!NOTE]
> Hoewel sommige van de onderstaande voorbeelden verwijzen naar een [Azure Databricks](https://docs.azuredatabricks.net/) Spark-cluster, neemt de Azure Data Explorer Spark-connector geen directe afhankelijkheid van Databricks of een andere Spark-distributie.

## <a name="prerequisites"></a>Vereisten

* [Een Azure Data Explorer-cluster en -database maken](/azure/data-explorer/create-cluster-database-portal) 
* Een Spark-cluster maken
* Installeer Azure Data Explorer-connectorbibliotheek:
    * Vooraf gebouwde bibliotheken voor [Spark 2.4, Scala 2.11](https://github.com/Azure/azure-kusto-spark/releases) 
    * [Maven repo](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/spark-kusto-connector)
* [Maven 3.x](https://maven.apache.org/download.cgi) geïnstalleerd

> [!TIP]
> 2.3.x-versies worden ook ondersteund, maar vereisen mogelijk enkele wijzigingen in pom.xml-afhankelijkheden.

## <a name="how-to-build-the-spark-connector"></a>De Spark-connector bouwen

> [!NOTE]
> Deze stap is optioneel. Als u vooraf gebouwde bibliotheken gebruikt, gaat u naar [Spark-clusterinstellingen](#spark-cluster-setup).

### <a name="build-prerequisites"></a>Vereisten samenstellen

1. Installeer de bibliotheken die worden vermeld in [afhankelijkheden,](https://github.com/Azure/azure-kusto-spark#dependencies) waaronder de volgende [Kusto Java SDK-bibliotheken:](/azure/kusto/api/java/kusto-java-client-library)
    * [Kusto Data-client](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-data)
    * [Kusto Ingest-klant](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-ingest)

1. Raadpleeg [deze bron](https://github.com/Azure/azure-kusto-spark) voor het bouwen van de Spark Connector.

1. Voor Scala/Java-toepassingen die Maven-projectdefinities gebruiken, koppelt u uw toepassing aan het volgende artefact (de nieuwste versie kan verschillen):
    
    ```Maven
       <dependency>
         <groupId>com.microsoft.azure</groupId>
         <artifactId>spark-kusto-connector</artifactId>
         <version>1.1.0</version>
       </dependency>
    ```

### <a name="build-commands"></a>Opdrachten maken

Om pot te bouwen en alle tests uit te voeren:

```
mvn clean package
```

Als u een potje wilt bouwen, voert u alle tests uit en installeert u jar naar uw lokale Maven-opslagplaats:

```
mvn clean install
```

Zie [het gebruik van de connector voor](https://github.com/Azure/azure-kusto-spark#usage)meer informatie .

## <a name="spark-cluster-setup"></a>Spark-clusterinstelling

> [!NOTE]
> Het wordt aanbevolen om de nieuwste Azure Data Explorer Spark-connectorrelease te gebruiken bij het uitvoeren van de volgende stappen.

1. Configureer de volgende Spark-clusterinstellingen op basis van azure databricks-cluster met Spark 2.4.4 en Scala 2.11:

    ![Clusterinstellingen voor Databricks](media/spark-connector/databricks-cluster.png)
    
1. Installeer de nieuwste spark-kusto-connector bibliotheek van Maven:
    
    ![](media/spark-connector/db-libraries-view.png) ![Bibliotheken importeren Selecteer Spark-Kusto-Connector](media/spark-connector/db-dependencies.png)

1. Controleer of alle vereiste bibliotheken zijn geïnstalleerd:

    ![Bibliotheken controleren die zijn geïnstalleerd](media/spark-connector/db-libraries-view.png)

1. Controleer voor installatie met een JAR-bestand of er extra afhankelijkheden zijn geïnstalleerd:

    ![Afhankelijkheden toevoegen](media/spark-connector/db-not-maven.png)

## <a name="authentication"></a>Authentication

Azure Data Explorer Spark-connector stelt u in staat om te verifiëren met Azure Active Directory (Azure AD) met een van de volgende methoden:
* Een [Azure AD-toepassing](#azure-ad-application-authentication)
* Een [Azure AD-toegangstoken](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#direct-authentication-with-access-token)
* [Apparaatverificatie](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#device-authentication) (voor niet-productiescenario's)
* Een [Azure Key Vault](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#key-vault) Om toegang te krijgen tot de Key Vault-bron, installeert u het azure-keyvault-pakket en verstrekt u toepassingsreferenties.

### <a name="azure-ad-application-authentication"></a>Azure AD-toepassingsverificatie

Azure AD-toepassingsverificatie is de eenvoudigste en meest voorkomende verificatiemethode en wordt aanbevolen voor de Azure Data Explorer Spark-connector.

|Eigenschappen  |Beschrijving  |
|---------|---------|
|**KUSTO_AAD_CLIENT_ID**     |   Azure AD-toepassings -id (client).      |
|**KUSTO_AAD_AUTHORITY_ID**     |  Azure AD-verificatieautoriteit. Azure AD Directory (tenant) ID.        |
|**KUSTO_AAD_CLIENT_PASSWORD**    |    Azure AD-toepassingssleutel voor de client.     |

### <a name="azure-data-explorer-privileges"></a>Azure Data Explorer-bevoegdheden

De volgende bevoegdheden verlenen voor een Azure Data Explorer-cluster:

* Voor het lezen (gegevensbron) moet de Azure AD-identiteit *viewerrechten* hebben in de doeldatabase of *beheerdersrechten* in de doeltabel.
* Voor het schrijven (gegevenssink) moet de Azure AD-identiteit *innamebevoegdheden* hebben op de doeldatabase. Het moet ook *gebruikersrechten* op de doeldatabase hebben om nieuwe tabellen te maken. Als de doeltabel al bestaat, moet u *beheerdersrechten* configureren in de doeltabel.
 
Zie [op rollen gebaseerde autorisatie voor](/azure/kusto/management/access-control/role-based-authorization)meer informatie over de hoofdrollen van Azure Data Explorer. Zie beheer van [beveiligingsrollen](/azure/kusto/management/security-roles)voor het beheren van beveiligingsrollen.

## <a name="spark-sink-writing-to-azure-data-explorer"></a>Spark sink: schrijven naar Azure Data Explorer

1. Sinkparameters instellen:

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

1. Schrijf Spark DataFrame naar azure Data Explorer-cluster als batch:

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

## <a name="spark-source-reading-from-azure-data-explorer"></a>Spark-bron: lezen vanuit Azure Data Explorer

1. Bij het lezen [van kleine hoeveelheden gegevens](/azure/kusto/concepts/querylimits)definieert u de gegevensquery:

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

1. Optioneel: als **u** de tijdelijke blobopslag (en niet Azure Data Explorer) opgeeft, worden de blobs gemaakt onder de verantwoordelijkheid van de beller. Dit omvat het inrichten van de opslag, het roteren van toegangssleutels en het verwijderen van tijdelijke artefacten. 
    De KustoBlobStorageUtils-module bevat helperfuncties voor het verwijderen van blobs op basis van account- en containercoördinaten en accountreferenties, of een volledige SAS-URL met schrijf-, lees- en lijstmachtigingen. Wanneer de bijbehorende RDD niet meer nodig is, worden bij elke transactie tijdelijke blobartefacten opgeslagen in een afzonderlijke map. Deze map wordt vastgelegd als onderdeel van lees-transactie informatie logboeken gemeld op de Spark Driver knooppunt.

    ```scala
    // Use either container/account-key/account name, or container SaS
    val container = dbutils.secrets.get(scope = "KustoDemos", key = "blobContainer")
    val storageAccountKey = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountKey")
    val storageAccountName = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountName")
    // val storageSas = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageSasUrl")
    ```

    In het bovenstaande voorbeeld is de Key Vault niet toegankelijk via de connectorinterface. een eenvoudigere methode voor het gebruik van de Databricks geheimen wordt gebruikt.

1. Lees uit Azure Data Explorer.

    * Als **u** de tijdelijke blobopslag opgeeft, leest u uit Azure Data Explorer als volgt:

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

    * Als **Azure Data Explorer** de tijdelijke blobopslag biedt, leest u uit Azure Data Explorer als volgt:
    
        ```scala
        val dfFiltered = df2
          .where(df2.col("ColA").startsWith("row-2"))
          .filter("ColB > 12")
          .filter("ColB <= 21")
          .select("ColA")
        
        display(dfFiltered)
        ```

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de [Azure Data Explorer Spark Connector](https://github.com/Azure/azure-kusto-spark/tree/master/docs)
* [Voorbeeldcode voor Java en Python](https://github.com/Azure/azure-kusto-spark/tree/master/samples/src/main)
