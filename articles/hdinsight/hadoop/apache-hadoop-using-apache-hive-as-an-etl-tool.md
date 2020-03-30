---
title: Apache Hive gebruiken als ETL-tool - Azure HDInsight
description: Gebruik Apache Hive om ETL-gegevens te extraheren, te transformeren en te laden in Azure HDInsight.
ms.service: hdinsight
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/22/2019
ms.openlocfilehash: be331f36a6305b05ce83a2b2d5fdfb73a154ce3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77623123"
---
# <a name="use-apache-hive-as-an-extract-transform-and-load-etl-tool"></a>Apache Hive gebruiken als gereedschap ETL -extract, transformeren en laden

U moet doorgaans binnenkomende gegevens reinigen en transformeren voordat u deze in een bestemming laadt die geschikt is voor analyse. ETL-bewerkingen (Extract, Transform en Load) worden gebruikt om gegevens voor te bereiden en te laden in een gegevensbestemming.  Apache Hive op HDInsight kan ongestructureerde gegevens lezen, de gegevens indien nodig verwerken en de gegevens vervolgens laden in een relationeel datawarehouse voor beslissingsondersteunende systemen. In deze benadering worden gegevens uit de bron gehaald en opgeslagen in schaalbare opslag, zoals Azure Storage-blobs of Azure Data Lake Storage. De gegevens worden vervolgens getransformeerd met behulp van een reeks Hive-query's en worden uiteindelijk in Hive geënsceneerd ter voorbereiding op bulkladen in het doelgegevensarchief.

## <a name="use-case-and-model-overview"></a>Overzicht van de aanvraag en het model gebruiken

De volgende afbeelding geeft een overzicht van de use case en het model voor ETL-automatisering. Invoergegevens worden getransformeerd om de juiste uitvoer te genereren.  Tijdens die transformatie kunnen de gegevens de vorm, het gegevenstype en zelfs taal wijzigen.  ETL-processen kunnen Imperial omzetten in metrische gegevens, tijdzones wijzigen en de precisie verbeteren om goed af te stemmen op bestaande gegevens in de bestemming.  ETL-processen kunnen ook nieuwe gegevens combineren met bestaande gegevens om de rapportage up-to-date te houden of om meer inzicht te geven in bestaande gegevens.  Toepassingen zoals rapportagetools en services kunnen deze gegevens vervolgens in het gewenste formaat gebruiken.

![Apache Hive als ETL architectuur](./media/apache-hadoop-using-apache-hive-as-an-etl-tool/hdinsight-etl-architecture.png)

Hadoop wordt meestal gebruikt in ETL-processen die een enorm aantal tekstbestanden importeren (zoals CZV's) of een kleiner, maar vaak veranderend aantal tekstbestanden, of beide.  Hive is een geweldig hulpmiddel om te gebruiken om de gegevens voor te bereiden voordat ze in de gegevensbestemming worden geladen.  Met Hive u een schema maken via de CSV en een SQL-achtige taal gebruiken om MapReduce-programma's te genereren die met de gegevens communiceren.

De typische stappen om Hive te gebruiken om ETL uit te voeren zijn als volgt:

1. Gegevens laden in Azure Data Lake Storage of Azure Blob Storage.
2. Maak een Metadata Store-database (met Azure SQL Database) voor gebruik door Hive bij het opslaan van uw schema's.
3. Maak een HDInsight-cluster en verbind het gegevensarchief.
4. Definieer het schema dat u in de leestijd wilt toepassen op gegevens in het gegevensarchief:

    ```
    DROP TABLE IF EXISTS hvac;

    --create the hvac table on comma-separated sensor data stored in Azure Storage blobs

    CREATE EXTERNAL TABLE hvac(`date` STRING, time STRING, targettemp BIGINT,
        actualtemp BIGINT,
        system BIGINT,
        systemage BIGINT,
        buildingid BIGINT)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
    STORED AS TEXTFILE LOCATION 'wasbs://{container}@{storageaccount}.blob.core.windows.net/HdiSamples/SensorSampleData/hvac/';
    ```

5. Transformeer de gegevens en laad ze in de bestemming.  Er zijn verschillende manieren om Hive te gebruiken tijdens de transformatie en het laden:

    * Beveel en bereid gegevens voor met Hive en sla deze op als een CSV in Azure Data Lake Storage of Azure blob storage.  Gebruik vervolgens een tool zoals SQL Server Integration Services (SSIS) om deze Csv's te verkrijgen en de gegevens te laden in een relationele database van de bestemming, zoals SQL Server.
    * Bevraag de gegevens rechtstreeks vanuit Excel of C# met het Hive ODBC-stuurprogramma.
    * Gebruik [Apache Sqoop](apache-hadoop-use-sqoop-mac-linux.md) om de voorbereide platte CSV-bestanden te lezen en deze in de relationele database van de bestemming te laden.

## <a name="data-sources"></a>Gegevensbronnen

Gegevensbronnen zijn meestal externe gegevens die kunnen worden gekoppeld aan bestaande gegevens in uw gegevensarchief, bijvoorbeeld:

* Sociale mediagegevens, logbestanden, sensoren en toepassingen die gegevensbestanden genereren.
* Gegevenssets verkregen van gegevensproviders, zoals weerstatistieken of verkoopaantallen van leveranciers.
* Streaming gegevens vastgelegd, gefilterd en verwerkt via een geschikt hulpmiddel of framework.

<!-- TODO: (see Collecting and loading data into HDInsight). -->

## <a name="output-targets"></a>Uitvoerdoelen

U Hive gebruiken om gegevens uit te geven naar verschillende doelen, waaronder:

* Een relationele database, zoals SQL Server of Azure SQL Database.
* Een datawarehouse, zoals Azure SQL Data Warehouse.
* Excel.
* Azure-tabel- en blobopslag.
* Toepassingen of services waarvoor gegevens moeten worden verwerkt in specifieke indelingen of als bestanden die specifieke typen informatiestructuur bevatten.
* Een JSON-documentarchief zoals [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

## <a name="considerations"></a>Overwegingen

Het ETL-model wordt meestal gebruikt wanneer u:

* Streamgegevens of grote hoeveelheden semi-gestructureerde of ongestructureerde gegevens uit externe bronnen laden in een bestaande database of informatiesysteem.
* De gegevens reinigen, transformeren en valideren voordat u ze laadt, misschien door meer dan één transformatie door het cluster te gebruiken.
* Genereer rapporten en visualisaties die regelmatig worden bijgewerkt. Als het rapport bijvoorbeeld te lang duurt om overdag te genereren, u het rapport 's nachts plannen. Als u een Hive-query automatisch wilt uitvoeren, u [Azure Logic Apps](../../logic-apps/logic-apps-overview.md) en PowerShell gebruiken.

Als het doel voor de gegevens geen database is, u een bestand genereren in de juiste indeling binnen de query, bijvoorbeeld een CSV. Dit bestand kan vervolgens worden geïmporteerd in Excel of Power BI.

Als u meerdere bewerkingen op de gegevens moet uitvoeren als onderdeel van het ETL-proces, moet u overwegen hoe u ze beheert. Als de bewerkingen worden beheerd door een extern programma, in plaats van als een werkstroom binnen de oplossing, moet u beslissen of sommige bewerkingen parallel kunnen worden uitgevoerd en om te detecteren wanneer elke taak is voltooid. Het gebruik van een werkstroommechanisme zoals Oozie binnen Hadoop kan eenvoudiger zijn dan proberen een reeks bewerkingen te orkestreren met behulp van externe scripts of aangepaste programma's. Zie [Workflow en joborchestration](https://msdn.microsoft.com/library/dn749829.aspx)voor meer informatie over Oozie.

## <a name="next-steps"></a>Volgende stappen

* [ETL op schaal](apache-hadoop-etl-at-scale.md)
* [Een gegevenspijplijn operationaliseren](../hdinsight-operationalize-data-pipeline.md)

<!-- * [ETL Deep Dive](../hdinsight-etl-deep-dive.md) -->
