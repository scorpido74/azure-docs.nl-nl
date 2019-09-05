---
title: Gebruik de Azure Data Explorer-connector voor Apache Spark om gegevens te verplaatsen tussen Azure Data Explorer-en Spark-clusters.
description: In dit onderwerp wordt beschreven hoe u gegevens kunt verplaatsen tussen Azure Data Explorer en Apache Spark-clusters.
author: orspod
ms.author: orspodek
ms.reviewer: michazag
ms.service: data-explorer
ms.topic: conceptual
ms.date: 4/29/2019
ms.openlocfilehash: 6a95cbad161906bd12a608880ac694d6bdf1ed27
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/05/2019
ms.locfileid: "70383056"
---
# <a name="azure-data-explorer-connector-for-apache-spark-preview"></a>Azure Data Explorer connector voor Apache Spark (preview-versie)

[Apache Spark](https://spark.apache.org/) is een geïntegreerde analyse-engine voor grootschalige gegevens verwerking. Azure Data Explorer is een snelle, volledig beheerde service voor gegevens analyse voor real-time analyse op grote hoeveel heden gegevens. 

Azure Data Explorer connector voor Spark implementeert gegevens bron en gegevens Sink voor het verplaatsen van gegevens tussen Azure Data Explorer en Spark-clusters om beide mogelijkheden te gebruiken. Met Azure Data Explorer en Apache Spark kunt u snelle en schaal bare toepassingen bouwen die gericht zijn op gegevensgestuurde scenario's, zoals machine learning (ML), uitpak-Transform-load (ETL) en Log Analytics. Schrijven naar Azure Data Explorer kan worden uitgevoerd in de batch-en streaming-modus.
Het lezen van Azure Data Explorer ondersteunt het weghalen van kolommen en predikaten pushdown, waarmee het volume van de overgedragen gegevens wordt beperkt door gegevens in azure Data Explorer te filteren.

Azure Data Explorer Spark-connector is een [open-source project](https://github.com/Azure/azure-kusto-spark) dat kan worden uitgevoerd op elk Spark-cluster. De Azure Data Explorer Spark-connector maakt Azure Data Explorer een geldige gegevens opslag voor standaard Spark-bron-en Sink-bewerkingen, zoals schrijven, lezen en writeStream. 

> [!NOTE]
> Hoewel sommige van de onderstaande voor beelden verwijzen naar een [Azure Databricks](https://docs.azuredatabricks.net/) Spark-cluster, nemen Azure Data Explorer Spark-connector geen rechtstreekse afhankelijkheden op Databricks of andere Spark-distributie.

## <a name="prerequisites"></a>Vereisten

* [Een Azure Data Explorer-cluster en-data base maken](/azure/data-explorer/create-cluster-database-portal) 
* Een Spark-cluster maken
* Installeer de Azure Data Explorer-connector bibliotheek en de bibliotheken die worden vermeld in [afhankelijkheden](https://github.com/Azure/azure-kusto-spark#dependencies) , waaronder de volgende [Kusto Java SDK](/azure/kusto/api/java/kusto-java-client-library) -bibliotheken:
    * [Kusto data-client](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-data)
    * [Kusto opname-client](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-ingest)
* Vooraf ontwikkelde bibliotheken voor [Spark 2,4, Scala 2,11](https://github.com/Azure/azure-kusto-spark/releases)

## <a name="how-to-build-the-spark-connector"></a>De Spark-connector maken

Spark-connector kan worden gebouwd op basis van [bronnen](https://github.com/Azure/azure-kusto-spark) zoals hieronder wordt beschreven.

> [!NOTE]
> Deze stap is optioneel. Als u vooraf gemaakte bibliotheken gebruikt, gaat u naar [Spark-cluster installatie](#spark-cluster-setup).

### <a name="build-prerequisites"></a>Vereiste onderdelen maken

* Java 1,8 SDK geïnstalleerd
* [Maven 3. x](https://maven.apache.org/download.cgi) geïnstalleerd
* Apache Spark versie 2.4.0 of hoger

> [!TIP]
> 2.3. x-versies worden ook ondersteund, maar kunnen wijzigingen in pom. XML-afhankelijkheden vereisen.

Voor scala/Java-toepassingen met Maven-project definities koppelt u uw toepassing aan het volgende artefact (de nieuwste versie kan anders zijn):

```Maven
   <dependency>
     <groupId>com.microsoft.azure</groupId>
     <artifactId>spark-kusto-connector</artifactId>
     <version>1.0.0-Beta-02</version>
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
> Het is raadzaam om de nieuwste versie van de Azure Data Explorer Spark-connector te gebruiken bij het uitvoeren van de volgende stappen:

1. Stel de volgende Spark-cluster instellingen in op basis van Azure Databricks cluster met Spark 2,4 en scala 2,11: 

    ![Databricks-cluster instellingen](media/spark-connector/databricks-cluster.png)

1. Importeer de Azure Data Explorer-connector bibliotheek:

    ![Azure Data Explorer-bibliotheek importeren](media/spark-connector/db-create-library.png)

1. Aanvullende afhankelijkheden toevoegen (niet nodig indien gebruikt vanuit Maven):

    ![Afhankelijkheden toevoegen](media/spark-connector/db-dependencies.png)

    > [!TIP]
    > De juiste Java-release versie voor elke Spark-versie vindt u [hier](https://github.com/Azure/azure-kusto-spark#dependencies).

1. Controleer of alle vereiste bibliotheken zijn geïnstalleerd:

    ![Geïnstalleerde bibliotheken controleren](media/spark-connector/db-libraries-view.png)

## <a name="authentication"></a>Authentication

Met Azure Data Explorer Spark-connector kunt u zich verifiëren met Azure Active Directory (Azure AD) met behulp van een [Azure AD-toepassing](#azure-ad-application-authentication), een [Azure AD-toegangs token](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#direct-authentication-with-access-token), een verificatie van het [apparaat](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#device-authentication) (voor niet-productie scenario's) of [Azure-sleutel Kluis](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#key-vault). De gebruiker moet het Azure-sleutel kluis pakket installeren en toepassings referenties opgeven om toegang te krijgen tot de Key Vault resource.

### <a name="azure-ad-application-authentication"></a>Azure AD-toepassings verificatie

De meest eenvoudige en algemene verificatie methode. Deze methode wordt aanbevolen voor het gebruik van Azure Data Explorer Spark-connector.

|properties  |Description  |
|---------|---------|
|**KUSTO_AAD_CLIENT_ID**     |   Id van de Azure AD-toepassing (client).      |
|**KUSTO_AAD_AUTHORITY_ID**     |  Azure AD-verificatie autoriteit. ID van Azure AD-Directory (Tenant).        |
|**KUSTO_AAD_CLIENT_PASSWORD**    |    Azure AD-toepassings sleutel voor de client.     |

### <a name="azure-data-explorer-privileges"></a>Bevoegdheden voor Azure Data Explorer

De volgende bevoegdheden moeten worden verleend op een Azure Data Explorer-cluster:

* Voor lezen (gegevens bron) moet de Azure AD-toepassing *Viewer* -bevoegdheden hebben voor de doel database of *beheerders* bevoegdheden voor de doel tabel.
* Voor schrijven (gegevens-Sink) moet de Azure AD-toepassing over *ingestie* -bevoegdheden beschikken voor de doel database. Het moet ook *gebruikers* rechten hebben voor de doel database om nieuwe tabellen te maken. Als de doel tabel al bestaat, kunnen *beheerders* bevoegdheden voor de doel tabel worden geconfigureerd.
 
Zie [autorisatie op basis van rollen](/azure/kusto/management/access-control/role-based-authorization)voor meer informatie over de belangrijkste rollen van Azure Data Explorer. Zie [beveiligings rollen](/azure/kusto/management/security-roles)beheren voor het beheren van beveiligings rollen.

## <a name="spark-sink-writing-to-azure-data-explorer"></a>Spark-Sink: Schrijven naar Azure Data Explorer

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
    val conf = Map(
            KustoSinkOptions.KUSTO_CLUSTER -> cluster,
            KustoSinkOptions.KUSTO_TABLE -> table,
            KustoSinkOptions.KUSTO_DATABASE -> database,
            KustoSinkOptions.KUSTO_AAD_CLIENT_ID -> appId,
            KustoSinkOptions.KUSTO_AAD_CLIENT_PASSWORD -> appKey,
            KustoSinkOptions.KUSTO_AAD_AUTHORITY_ID -> authorityId)
    
    df.write
      .format("com.microsoft.kusto.spark.datasource")
      .options(conf)
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

    // Set up a checkpoint and disable codeGen. Set up a checkpoint and disable codeGen as a workaround for an known issue 
    spark.conf.set("spark.sql.streaming.checkpointLocation", "/FileStore/temp/checkpoint")
    spark.conf.set("spark.sql.codegen.wholeStage","false") // Use in case a NullPointerException is thrown inside codegen iterator
    
    // Write to a Kusto table from a streaming source
    val kustoQ = df
          .writeStream
          .format("com.microsoft.kusto.spark.datasink.KustoSinkProvider")
          .options(conf) 
          .option(KustoSinkOptions.KUSTO_WRITE_ENABLE_ASYNC, "true") // Optional, better for streaming, harder to handle errors
          .trigger(Trigger.ProcessingTime(TimeUnit.SECONDS.toMillis(10))) // Sync this with the ingestionBatching policy of the database
          .start()
    
    ```

## <a name="spark-source-reading-from-azure-data-explorer"></a>Spark-Bron: Lezen van Azure Data Explorer

1. Bij het lezen van kleine hoeveel heden gegevens, definieert u de gegevens query:

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

1. Bij het lezen van grote hoeveel heden gegevens moet tijdelijke Blob-opslag worden gegeven. Geef de SAS-sleutel van de opslag container of de naam van het opslag account, de account sleutel en de naam van de container op. Deze stap is alleen vereist voor de huidige preview-versie van de Spark-connector.

    ```scala
    // Use either container/account-key/account name, or container SaS
    val container = dbutils.secrets.get(scope = "KustoDemos", key = "blobContainer")
    val storageAccountKey = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountKey")
    val storageAccountName = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountName")
    // val storageSas = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageSasUrl")
    ```

    In het bovenstaande voor beeld hebben we geen toegang tot de Key Vault met behulp van de connector interface. U kunt ook gebruikmaken van een eenvoudigere methode voor het gebruik van de Databricks-geheimen.

1. Lezen uit Azure Data Explorer:

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
