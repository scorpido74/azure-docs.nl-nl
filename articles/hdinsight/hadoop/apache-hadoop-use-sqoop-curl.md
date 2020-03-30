---
title: Curl gebruiken om gegevens te exporteren met Apache Sqoop in Azure HDInsight
description: Meer informatie over het op afstand verzenden van Apache Sqoop-taken naar Azure HDInsight met Behulp van Curl.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: da29785547d1b6eb4b38d07f020ba885dc5137ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75767583"
---
# <a name="run-apache-sqoop-jobs-in-hdinsight-with-curl"></a>Apache Sqoop-taken uitvoeren in HDInsight met Curl

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Meer informatie over het gebruik van Curl om Apache Sqoop-taken uit te voeren op een Apache Hadoop-cluster in HDInsight. In dit artikel wordt uitgelegd hoe u gegevens uit Azure Storage exporteert en importeert in een SQL Server-database met Behulp van Curl. Dit artikel is een voortzetting van [Gebruik Apache Sqoop met Hadoop in HDInsight](./hdinsight-use-sqoop.md).

Curl wordt gebruikt om aan te tonen hoe u communiceren met HDInsight door ruwe HTTP-aanvragen te gebruiken om de resultaten van Sqoop-taken uit te voeren, te controleren en op te halen. Dit werkt met behulp van de WebHCat REST API (voorheen bekend als Templeton) geleverd door uw HDInsight cluster.

## <a name="prerequisites"></a>Vereisten

* Voltooiing van [de testomgeving instellen](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) van [Apache Sqoop gebruiken met Hadoop in HDInsight.](./hdinsight-use-sqoop.md)

* Een client die de Azure SQL Database moet opvragen. Overweeg [sql serverbeheerstudio](../../sql-database/sql-database-connect-query-ssms.md) of [Visual Studio-code te gebruiken.](../../sql-database/sql-database-connect-query-vscode.md)

* [Krul](https://curl.haxx.se/). Curl is een hulpmiddel om gegevens over te zetten van of naar een HDInsight-cluster.

* [jq](https://stedolan.github.io/jq/). Het jq-hulpprogramma wordt gebruikt voor het verwerken van de JSON-gegevens die zijn geretourneerd uit REST-aanvragen.

* Vertrouwdheid met Sqoop. Zie [Sqoop-gebruikershandleiding](https://sqoop.apache.org/docs/1.4.7/SqoopUserGuide.html)voor meer informatie.

## <a name="submit-apache-sqoop-jobs-by-using-curl"></a>Apache Sqoop-taken verzenden met Curl

Gebruik Curl om gegevens te exporteren met Apache Sqoop-taken van Azure Storage naar SQL Server.

> [!NOTE]  
> Wanneer u Curl of een andere REST-communicatie gebruikt met WebHCat, moet u de aanvragen verifiëren door de gebruikersnaam en het wachtwoord voor de beheerder van het HDInsight-cluster op te geven. U moet ook de clusternaam gebruiken als onderdeel van de URI (Uniform Resource Identifier) die wordt gebruikt om de aanvragen naar de server te verzenden.

Vervang voor de opdrachten in `USERNAME` deze sectie de gebruiker om `PASSWORD` te verifiëren aan het cluster en vervang het wachtwoord voor het gebruikersaccount. Vervang `CLUSTERNAME` door de naam van uw cluster.

De REST API is beveiligd via [basisverificatie](https://en.wikipedia.org/wiki/Basic_access_authentication). U moet aanvragen altijd uitvoeren via een beveiligde HTTP-verbinding (HTTPS). Zo zorgt u ervoor dat uw referenties veilig worden verzonden naar de server.

1. Voor gebruiksgemak stelt u de onderstaande variabelen in. Dit voorbeeld is gebaseerd op een Windows-omgeving, herzien als dat nodig is voor uw omgeving.

    ```cmd
    set CLUSTERNAME=
    set USERNAME=admin
    set PASSWORD=
    set SQLDATABASESERVERNAME=
    set SQLDATABASENAME=
    set SQLPASSWORD=
    set SQLUSER=sqluser
    ```

1. Gebruik een opdrachtregel met de volgende opdracht om te controleren of u verbinding met uw HDInsight-cluster kunt maken:

    ```cmd
    curl -u %USERNAME%:%PASSWORD% -G https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/status
    ```

    Het antwoord dat u ontvangt, is vergelijkbaar met het volgende antwoord:

    ```json
    {"status":"ok","version":"v1"}
    ```

1. Gebruik het volgende om een sqoop-taak in te dienen:

    ```cmd
    curl -u %USERNAME%:%PASSWORD% -d user.name=%USERNAME% -d command="export --connect jdbc:sqlserver://%SQLDATABASESERVERNAME%.database.windows.net;user=%SQLUSER%@%SQLDATABASESERVERNAME%;password=%PASSWORD%;database=%SQLDATABASENAME% --table log4jlogs --export-dir /example/data/sample.log --input-fields-terminated-by \0x20 -m 1" -d statusdir="wasb:///example/data/sqoop/curl" https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/sqoop
    ```

    In deze opdracht worden de volgende parameters gebruikt:

   * **-d** - `-G` Aangezien niet wordt gebruikt, de aanvraag standaard aan de POST-methode. `-d`hiermee worden de gegevenswaarden opgegeven die met de aanvraag worden verzonden.

       * **user.name** - De gebruiker die de opdracht uitvoert.

       * **opdracht** - De opdracht Sqoop uit te voeren.

       * **statusdir** - De map waarnaar de status voor deze taak wordt geschreven.

     Met deze opdracht wordt een taak-id retourneren die kan worden gebruikt om de status van de taak te controleren.

       ```json
       {"id":"job_1415651640909_0026"}
       ```

1. Als u de status van de taak wilt controleren, gebruikt u de volgende opdracht. Vervang `JOBID` de waarde die in de vorige stap is geretourneerd. Als de retourwaarde bijvoorbeeld `{"id":"job_1415651640909_0026"}`was, `JOBID` `job_1415651640909_0026`dan zou dat zijn . Herzien locatie `jq` van indien nodig.

    ```cmd
    set JOBID=job_1415651640909_0026

    curl -G -u %USERNAME%:%PASSWORD% -d user.name=%USERNAME% https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/jobs/%JOBID% | C:\HDI\jq-win64.exe .status.state
    ```

    Als de taak is voltooid, wordt de status **OPGEVOLGD.**

   > [!NOTE]  
   > Met deze aanvraag Curl wordt een JSON-document (JavaScript Object Notation) geretourneerd met informatie over de taak. jq wordt gebruikt om alleen de statuswaarde op te halen.

1. Zodra de status van de taak is gewijzigd in **GESLAAGD,** u de resultaten van de taak ophalen uit Azure Blob-opslag. De `statusdir` parameter die met de query is doorgegeven, bevat de locatie van het uitvoerbestand. in dit `wasb:///example/data/sqoop/curl`geval, . Met dit adres wordt de `example/data/sqoop/curl` uitvoer van de taak opgeslagen in de map op de standaardopslagcontainer die wordt gebruikt door uw HDInsight-cluster.

    U de Azure-portal gebruiken om toegang te krijgen tot stderr- en stdoutblobs.

1. Als u wilt controleren of gegevens zijn geëxporteerd, gebruikt u de volgende query's van uw SQL-client om de geëxporteerde gegevens weer te geven:

    ```sql
    SELECT COUNT(*) FROM [dbo].[log4jlogs] WITH (NOLOCK);
    SELECT TOP(25) * FROM [dbo].[log4jlogs] WITH (NOLOCK);
    ```

## <a name="limitations"></a>Beperkingen

* Bulkexport - Met HDInsight op Basis van Linux biedt de Sqoop-connector die wordt gebruikt om gegevens te exporteren naar Microsoft SQL Server of Azure SQL Database momenteel geen bulkinserts.
* Batching - Met HdInsight op Basis `-batch` van Linux voert Sqoop bij het gebruik van de switch bij het uitvoeren van wisselplaten meerdere wisselplaten uit in plaats van de invoegbewerkingen te batcheren.

## <a name="summary"></a>Samenvatting

Zoals in dit document wordt aangetoond, u een onbewerkte HTTP-aanvraag gebruiken om de resultaten van Sqoop-taken op uw HDInsight-cluster uit te voeren, te controleren en te bekijken.

Zie de <a href="https://sqoop.apache.org/docs/1.99.3/RESTAPI.html" target="_blank">Api-handleiding voor Apache Sqoop REST voor</a>meer informatie over de REST-interface die in dit artikel wordt gebruikt.

## <a name="next-steps"></a>Volgende stappen

[Gebruik Apache Sqoop met Apache Hadoop op HDInsight](hdinsight-use-sqoop.md)

Voor andere HDInsight artikelen met betrekking tot krul:

* [Apache Hadoop-clusters maken met behulp van de Azure REST API](../hdinsight-hadoop-create-linux-clusters-curl-rest.md)
* [Apache Hive-query's uitvoeren met Apache Hadoop in HDInsight met BEHULP van REST](apache-hadoop-use-hive-curl.md)
* [Kaart uitvoerenWerk verminderen met Apache Hadoop op HDInsight met BEHULP van REST](apache-hadoop-use-mapreduce-curl.md)