---
title: Krul gebruiken om gegevens te exporteren met Apache Sqoop in azure HDInsight
description: Meer informatie over het op afstand verzenden van Apache Sqoop-taken naar Azure HDInsight met behulp van krul.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: f70c0a0b68e24e3d61a6c0cef238d1f60911e271
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/09/2019
ms.locfileid: "70810725"
---
# <a name="run-apache-sqoop-jobs-in-hdinsight-with-curl"></a>Apache Sqoop-taken in HDInsight uitvoeren met krul
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Meer informatie over hoe u krul kunt gebruiken om Apache Sqoop-taken uit te voeren op een Apache Hadoop cluster in HDInsight. In dit artikel wordt beschreven hoe u gegevens uit Azure Storage exporteert en kunt importeren in een SQL Server-Data Base met behulp van krul. Dit artikel is een voortzetting van het [gebruik van Apache Sqoop met Hadoop in HDInsight](./hdinsight-use-sqoop.md).

Krul wordt gebruikt om te laten zien hoe u kunt communiceren met HDInsight door onbewerkte HTTP-aanvragen te gebruiken om de resultaten van Sqoop-taken uit te voeren, te controleren en op te halen. Dit werkt met behulp van de WebHCat-REST API (voorheen bekend als Templeton) van uw HDInsight-cluster.

## <a name="prerequisites"></a>Vereisten

* Volt ooien van de [instelling test omgeving](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) [voor het gebruik van Apache Sqoop met Hadoop in HDInsight](./hdinsight-use-sqoop.md).

* Een client voor het opvragen van de Azure SQL database. Overweeg het gebruik van [SQL Server Management Studio](../../sql-database/sql-database-connect-query-ssms.md) of [Visual Studio code](../../sql-database/sql-database-connect-query-vscode.md).

* [Krul](https://curl.haxx.se/). Krul is een hulp programma voor het overdragen van gegevens van of naar een HDInsight-cluster.

* [jq](https://stedolan.github.io/jq/). Het hulp programma JQ wordt gebruikt voor het verwerken van de JSON-gegevens die door REST-aanvragen worden geretourneerd.

## <a name="submit-apache-sqoop-jobs-by-using-curl"></a>Apache Sqoop-taken verzenden met behulp van krul

Gebruik krul om gegevens te exporteren met Apache Sqoop-taken van Azure Storage naar SQL Server.

> [!NOTE]  
> Wanneer u Curl of een andere REST-communicatie gebruikt met WebHCat, moet u de aanvragen verifiëren door de gebruikersnaam en het wachtwoord voor de beheerder van het HDInsight-cluster op te geven. U moet ook de clusternaam gebruiken als onderdeel van de URI (Uniform Resource Identifier) die wordt gebruikt om de aanvragen naar de server te verzenden.

Voor de opdrachten in deze sectie vervangt `USERNAME` u door de gebruiker om te verifiëren bij het cluster en vervangt `PASSWORD` u het wacht woord voor het gebruikers account. Vervang `CLUSTERNAME` door de naam van uw cluster.
 
De REST API is beveiligd via [basisverificatie](https://en.wikipedia.org/wiki/Basic_access_authentication). U moet aanvragen altijd uitvoeren via een beveiligde HTTP-verbinding (HTTPS). Zo zorgt u ervoor dat uw referenties veilig worden verzonden naar de server.

1. Gebruik een opdrachtregel met de volgende opdracht om te controleren of u verbinding met uw HDInsight-cluster kunt maken:

    ```cmd
    curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    Het antwoord dat u ontvangt, is vergelijkbaar met het volgende antwoord:

    ```json
    {"status":"ok","version":"v1"}
    ```

2. Vervang `SQLDATABASESERVERNAME` ,`USERNAME@SQLDATABASESERVERNAME`, ,`SQLDATABASENAME`door de juiste waarden van de vereisten. `PASSWORD` Gebruik de volgende opdracht om een sqoop-taak in te dienen:

    ```cmd
    curl -u USERNAME:PASSWORD -d user.name=USERNAME -d command="export --connect jdbc:sqlserver://SQLDATABASESERVERNAME.database.windows.net;user=USERNAME@SQLDATABASESERVERNAME;password=PASSWORD;database=SQLDATABASENAME --table log4jlogs --export-dir /example/data/sample.log --input-fields-terminated-by \0x20 -m 1" -d statusdir="wasb:///example/data/sqoop/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/sqoop
    ```

    In deze opdracht worden de volgende parameters gebruikt:

   * **-d** -omdat `-G` niet wordt gebruikt, wordt de aanvraag standaard ingesteld op de methode post. `-d`Hiermee geeft u de gegevens waarden op die met de aanvraag worden verzonden.

       * **User.name** : de gebruiker die de opdracht uitvoert.

       * **opdracht** : de Sqoop opdracht die moet worden uitgevoerd.

       * **statusdir** : de map waarin de status voor deze taak wordt geschreven.

     Met deze opdracht wordt een taak-ID geretourneerd die kan worden gebruikt om de status van de taak te controleren.

       ```json
       {"id":"job_1415651640909_0026"}
       ```

3. Gebruik de volgende opdracht om de status van de taak te controleren. Vervang `JOBID` door de waarde die u in de vorige stap hebt geretourneerd. Als de retour waarde bijvoorbeeld was `{"id":"job_1415651640909_0026"}` `JOBID` , zou zijn `job_1415651640909_0026`.

    ```cmd
    curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
    ```

    Als de taak is voltooid, wordt de status **voltooid**.
   
   > [!NOTE]  
   > Deze krul aanvraag retourneert een JavaScript Object Notation (JSON)-document met informatie over de taak. JQ wordt gebruikt om alleen de status waarde op te halen.

4. Zodra de status van de taak is gewijzigd in **geslaagd**, kunt u de resultaten van de taak ophalen uit Azure Blob-opslag. De `statusdir` para meter die is door gegeven met de query bevat de locatie van het uitvoer bestand, `wasb:///example/data/sqoop/curl`in dit geval. Dit adres bevat de uitvoer van de taak in de `example/data/sqoop/curl` map op de standaard opslag container die wordt gebruikt door uw HDInsight-cluster.

    U kunt de Azure Portal gebruiken om toegang te krijgen tot stderr-en stdout-blobs.

5. Als u wilt controleren of de gegevens zijn geëxporteerd, gebruikt u de volgende query's van uw SQL-client om de geëxporteerde gegevens weer te geven:

    ```sql
    SELECT COUNT(*) FROM [dbo].[log4jlogs] WITH (NOLOCK);
    SELECT TOP(25) * FROM [dbo].[log4jlogs] WITH (NOLOCK);
    ```

## <a name="limitations"></a>Beperkingen
* Bulk export-met HDInsight op basis van Linux, de Sqoop-connector die wordt gebruikt om gegevens te exporteren naar Microsoft SQL Server of Azure SQL Database biedt momenteel geen ondersteuning voor bulksgewijs invoegen.
* Batch verwerking: met HDInsight op basis van Linux, worden bij `-batch` het gebruik van de switch tijdens het invoegen meerdere inserts uitgevoerd in plaats van batch verwerking van de Insert-bewerkingen.

## <a name="summary"></a>Samenvatting
Zoals gedemonstreerd in dit document, kunt u een onbewerkte HTTP-aanvraag gebruiken om de resultaten van Sqoop-taken op uw HDInsight-cluster uit te voeren, te controleren en weer te geven.

Voor meer informatie over de REST-interface die in dit artikel wordt gebruikt, raadpleegt u de <a href="https://sqoop.apache.org/docs/1.99.3/RESTAPI.html" target="_blank">Apache Sqoop rest API Guide (Engelstalig</a>).

## <a name="next-steps"></a>Volgende stappen
[Apache Sqoop gebruiken met Apache Hadoop op HDInsight](hdinsight-use-sqoop.md)

Voor andere HDInsight-artikelen met krul:
 
* [Apache Hadoop clusters maken met behulp van de Azure-REST API](../hdinsight-hadoop-create-linux-clusters-curl-rest.md)
* [Apache Hive query's uitvoeren met Apache Hadoop in HDInsight met behulp van REST](apache-hadoop-use-hive-curl.md)
* [MapReduce-taken uitvoeren met Apache Hadoop op HDInsight met behulp van REST](apache-hadoop-use-mapreduce-curl.md)

