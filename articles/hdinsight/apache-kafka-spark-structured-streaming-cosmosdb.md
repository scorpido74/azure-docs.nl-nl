---
title: Apache Spark & Apache Kafka met Cosmos DB - Azure HDInsight
description: Meer informatie over het gebruik van Apache Spark Structured Streaming om gegevens van Apache Kafka te lezen en deze vervolgens op te slaan in Azure Cosmos DB. In dit voorbeeld gaat u met behulp van een Jupyter Notebook gegevens streamen van Spark naar HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/18/2019
ms.openlocfilehash: 04faafca0811e60ded47d1e91a82054a1c1cdb25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74406162"
---
# <a name="use-apache-spark-structured-streaming-with-apache-kafka-and-azure-cosmos-db"></a>Gebruik Apache Spark Structured Streaming met Apache Kafka en Azure Cosmos DB

Meer informatie over het gebruik van [Apache Spark](https://spark.apache.org/) [Structured Streaming](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html) om gegevens van Apache [Kafka](https://kafka.apache.org/) op Azure HDInsight te lezen en de gegevens vervolgens op te slaan in Azure Cosmos DB.

[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) is een wereldwijd gedistribueerde database met meerdere modellen. In dit voorbeeld wordt een SQL API-databasemodel gebruikt. Zie het DOCUMENT [Welkom bij Azure Cosmos DB](../cosmos-db/introduction.md) voor meer informatie.

Spark Structured Streaming is een streamverwerkingsengine gebaseerd op Spark SQL. Hiermee kunt u streamingberekeningen op dezelfde manier weergeven als batchberekeningen van statische gegevens. Zie de [programmeerhandleiding voor gestructureerde streaming](https://spark.apache.org/docs/2.2.0/structured-streaming-programming-guide.html) op Apache.org voor meer informatie over structured streaming.

> [!IMPORTANT]  
> In dit voorbeeld wordt Spark 2.2 gebruikt op HDInsight 3.6.
>
> Met de stappen in dit document wordt een Azure-resourcegroep gemaakt die zowel een Spark in HDInsight- als een Kafka in HDInsight-cluster bevat. Deze clusters bevinden zich beide binnen een Azure Virtual Network, waardoor het Spark-cluster rechtstreeks kan communiceren met het Kafka-cluster.
>
> Nadat u de stappen in dit document hebt doorlopen, moet u niet vergeten de clusters te verwijderen om overtollige kosten te voorkomen.

## <a name="create-the-clusters"></a>De clusters maken

Apache Kafka op HDInsight biedt geen toegang tot de Kafka makelaars via het openbare internet. Alles wat met Kafka praat, moet zich in hetzelfde Azure virtuele netwerk bevinden als de knooppunten in het Kafka-cluster. In dit voorbeeld bevinden zowel de clusters Kafka als Spark zich in een virtueel Azure-netwerk. In het volgende diagram ziet u hoe de communicatie tussen de clusters verloopt:

![Diagram van Spark- en Kafka-clusters in een Azure Virtual Network](./media/apache-kafka-spark-structured-streaming-cosmosdb/apache-spark-kafka-vnet.png)

> [!NOTE]  
> De Kafka-service blijft beperkt tot communicatie binnen het virtuele netwerk. Andere services in het cluster, zoals SSH en Ambari, zijn toegankelijk via internet. Zie [Poorten en URI's die worden gebruikt door HDInsight](hdinsight-hadoop-port-settings-for-services.md) voor meer informatie over de openbare poorten die beschikbaar zijn voor HDInsight.

Hoewel u handmatig een Azure-virtueel netwerk, Kafka en Spark-clusters maken, is het eenvoudiger om een Azure Resource Manager-sjabloon te gebruiken. Gebruik de volgende stappen om een Azure virtueel netwerk, Kafka en Spark-clusters te implementeren in uw Azure-abonnement.

1. Gebruik de volgende knop om u aan te melden bij Azure en de sjabloon in de Azure Portal te openen.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-spark-scala-kafka-cosmosdb%2Fmaster%2Fazuredeploy.json" target="_blank">
    <img src="./media/apache-kafka-spark-structured-streaming-cosmosdb/resource-manager-deploy.png" alt="Deploy to Azure"/>
    </a>

    De sjabloon Azure Resource Manager bevindt zich in de[https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb)GitHub-opslagplaats voor dit project ( ).

    Met deze sjabloon maakt u de volgende bronnen:

   * Een Kafka in HDInsight 3.6-cluster.

   * Een Spark op HDInsight 3.6 cluster.

   * Een Azure Virtual Network dat de HDInsight-clusters bevat. Het virtuele netwerk dat door de sjabloon is gemaakt, gebruikt de adresruimte 10.0.0.0/16.

   * Een Azure Cosmos DB SQL API-database.

    > [!IMPORTANT]  
    > Voor de Structured Streaming-notebook die in dit voorbeeld wordt gebruikt, is Spark in HDInsight 3.6 vereist. Als u een eerdere versie van Spark in HDInsight gebruikt, worden er fouten gegenereerd bij gebruik van de notebook.

1. Gebruik de volgende gegevens om de vermeldingen in de sectie **Aangepaste implementatie** in te vullen:

    |Eigenschap |Waarde |
    |---|---|
    |Abonnement|Selecteer uw Azure-abonnement.|
    |Resourcegroep|Maak een groep of selecteer een bestaande groep. Deze groep bevat het HDInsight-cluster.|
    |Cosmos DB-accountnaam|Deze waarde wordt gebruikt als de naam voor het Cosmos DB-account. De naam kan alleen kleine letters, cijfers en het koppelteken (-) bevatten. Het moet tussen de 3-31 tekens in lengte.|
    |Naam basiscluster|Deze waarde wordt gebruikt als basisnaam voor de spark- en kafkaclusters. Als u bijvoorbeeld **myhdi** invoert, wordt een Spark-cluster met de naam __spark-myhdi__ en een Kafka-cluster met de naam **kafka-myhdi .**|
    |Clusterversie|De HDInsight-clusterversie. Dit voorbeeld wordt getest met HDInsight 3.6 en werkt mogelijk niet met andere clustertypen.|
    |Gebruikersnaam voor clusteraanmelding|De beheerdersgebruikersnaam voor de spark- en kafkaclusters.|
    |Wachtwoord voor clusteraanmelding|Het beheerdersgebruikerswachtwoord voor de spark- en kafkaclusters.|
    |Ssh-gebruikersnaam|De SSH-gebruiker om te maken voor de Spark- en Kafka-clusters.|
    |Ssh-wachtwoord|Het wachtwoord voor de SSH-gebruiker voor de Spark- en Kafka-clusters.|

    ![HDInsight aangepaste implementatiewaarden](./media/apache-kafka-spark-structured-streaming-cosmosdb/hdi-custom-parameters.png)

1. Lees de **voorwaarden** en schakel vervolgens het selectievakje **Ik ga akkoord met de bovenstaande voorwaarden** in.

1. Selecteer Tot slot **Aankoop**. Het kan tot 45 minuten duren om het clusters-, virtuele netwerk- en Cosmos DB-account te maken.

## <a name="create-the-cosmos-db-database-and-collection"></a>De Cosmos DB-database en -verzameling maken

Het project dat in dit document wordt gebruikt, slaat gegevens op in Cosmos DB. Voordat u de code uitvoert, moet u eerst een _database_ en _verzameling_ maken in uw Cosmos DB-exemplaar. U moet ook het documenteindpunt en de _sleutel_ ophalen die wordt gebruikt om aanvragen voor Cosmos DB te verifiëren.

Een manier om dit te doen is het gebruik van de [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest). In het volgende script `kafkadata` wordt een `kafkacollection`database gemaakt met de naam en een verzameling met de naam . Het retourneert vervolgens de primaire sleutel.

```azurecli
#!/bin/bash

# Replace 'myresourcegroup' with the name of your resource group
resourceGroupName='myresourcegroup'
# Replace 'mycosmosaccount' with the name of your Cosmos DB account name
name='mycosmosaccount'

# WARNING: If you change the databaseName or collectionName
#          then you must update the values in the Jupyter notebook
databaseName='kafkadata'
collectionName='kafkacollection'

# Create the database
az cosmosdb sql database create --account-name $name --name $databaseName --resource-group $resourceGroupName

# Create the collection
az cosmosdb sql container create --account-name $name --database-name $databaseName --name $collectionName --partition-key-path "/my/path" --resource-group $resourceGroupName

# Get the endpoint
az cosmosdb show --name $name --resource-group $resourceGroupName --query documentEndpoint

# Get the primary key
az cosmosdb keys list --name $name --resource-group $resourceGroupName --type keys
```

Het documenteindpunt en de primaire sleutelinformatie zijn vergelijkbaar met de volgende tekst:

```text
# endpoint
"https://mycosmosaccount.documents.azure.com:443/"
# key
"YqPXw3RP7TsJoBF5imkYR0QNA02IrreNAlkrUMkL8EW94YHs41bktBhIgWq4pqj6HCGYijQKMRkCTsSaKUO2pw=="
```

> [!IMPORTANT]  
> Sla het eindpunt en de belangrijkste waarden op, zoals ze nodig zijn in de Jupyter-notitieblokken.

## <a name="get-the-notebooks"></a>De notitieblokken aanschaffen

De code voor het voorbeeld dat [https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb)in dit document wordt beschreven, is beschikbaar op .

## <a name="upload-the-notebooks"></a>De notebooks uploaden

Gebruik de volgende stappen om de notitieblokken van het project te uploaden naar uw Spark op HDInsight-cluster:

1. Maak vanuit uw webbrowser verbinding met de Jupyter-notebook in uw Spark-cluster. In de volgende URL vervangt u `CLUSTERNAME` door de naam van uw __Spark__-cluster:

        https://CLUSTERNAME.azurehdinsight.net/jupyter

    Typ desgevraagd de cluster-aanmelding (beheerder) en het cluster-wachtwoord die u hebt gebruikt bij het maken van het cluster.

2. Gebruik vanaf de rechterbovenhoek van de pagina de knop __Uploaden__ om het __bestand Stream-taxi-data-to-kafka.ipynb__ naar het cluster te uploaden. Selecteer __Openen__ om te beginnen met uploaden.

3. Zoek de __vermelding Stream-taxi-data-to-kafka.ipynb__ in de lijst met notitieblokken en selecteer De knop __Uploaden__ ernaast.

4. Herhaal stap 1-3 om het __stream-data-from-Kafka-to-Cosmos-DB.ipynb-notitieblok te__ laden.

## <a name="load-taxi-data-into-kafka"></a>Laad taxigegevens in Kafka

Zodra de bestanden zijn geüpload, selecteert u de __Stream-taxi-data-to-kafka.ipynb-vermelding__ om het notitieblok te openen. Volg de stappen in het notitieblok om gegevens in Kafka te laden.

## <a name="process-taxi-data-using-spark-structured-streaming"></a>Taxigegevens verwerken met Spark Structured Streaming

Selecteer op de startpagina [van Jupyter Notebook](https://jupyter.org/) de __vermelding Stream-data-from-Kafka-to-Cosmos-DB.ipynb.__ Volg de stappen in het notitieblok om gegevens van Kafka en Azure Cosmos DB te streamen met Spark Structured Streaming.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u Apache Spark Structured Streaming gebruiken, raadpleegt u de volgende documenten voor meer informatie over het werken met Apache Spark, Apache Kafka en Azure Cosmos DB:

* [Hoe Apache Spark streaming (DStream) te gebruiken met Apache Kafka](hdinsight-apache-spark-with-kafka.md).
* [Begin met Jupyter Notebook en Apache Spark op HDInsight](spark/apache-spark-jupyter-spark-sql.md)
* [Welkom bij Azure Cosmos DB](../cosmos-db/introduction.md)
