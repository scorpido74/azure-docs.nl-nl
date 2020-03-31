---
title: Livy Spark gebruiken om taken in te dienen bij Spark-cluster op Azure HDInsight
description: Meer informatie over het gebruik van Apache Spark REST API om Spark-taken op afstand in te dienen bij een Azure HDInsight-cluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 02/28/2020
ms.openlocfilehash: ac3904284ebf20fa1d5e75f9249732be3963f677
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206279"
---
# <a name="use-apache-spark-rest-api-to-submit-remote-jobs-to-an-hdinsight-spark-cluster"></a>Apache Spark REST API gebruiken voor het verzenden van externe taken naar een HDInsight Spark-cluster

Meer informatie over het gebruik van [Apache Livy](https://livy.incubator.apache.org/), de Apache Spark REST API, die wordt gebruikt om externe taken in te dienen bij een Azure HDInsight Spark-cluster. Zie [Apache Livy](https://livy.incubator.apache.org/docs/latest/rest-api.html)voor gedetailleerde documentatie.

U Livy gebruiken om interactieve Spark-shells uit te voeren of batchtaken in te dienen die op Spark moeten worden uitgevoerd. In dit artikel wordt gesproken over het gebruik van Livy om batchtaken in te dienen. De fragmenten in dit artikel gebruiken cURL om REST API-aanroepen naar het Livy Spark-eindpunt te maken.

## <a name="prerequisites"></a>Vereisten

Een Apache Spark-cluster in HDInsight. Zie [Apache Spark-clusters maken in Azure HDInsight](apache-spark-jupyter-spark-sql.md) voor instructies.

## <a name="submit-an-apache-livy-spark-batch-job"></a>Een batchtaak van Apache Livy Spark indienen

Voordat u een batchtaak indient, moet u de toepassingspot uploaden op de clusteropslag die aan het cluster is gekoppeld. Dit kan met [AzCopy](../../storage/common/storage-use-azcopy.md), een opdrachtregelprogramma. Er zijn verschillende andere clients die u gebruiken om gegevens te uploaden. Meer informatie hierover vindt u in [Gegevens voor Apache Hadoop-taken uploaden in HDInsight](../hdinsight-upload-data.md).

```cmd
curl -k --user "admin:password" -v -H "Content-Type: application/json" -X POST -d '{ "file":"<path to application jar>", "className":"<classname in jar>" }' 'https://<spark_cluster_name>.azurehdinsight.net/livy/batches' -H "X-Requested-By: admin"
```

### <a name="examples"></a>Voorbeelden

* Als het potbestand zich op de clusteropslag (WASBS) bevindt

    ```cmd  
    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST -d '{ "file":"wasbs://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://mysparkcluster.azurehdinsight.net/livy/batches" -H "X-Requested-By: admin"
    ```

* Als u de bestandsnaam van de pot en de klassenaam wilt doorgeven als onderdeel van een invoerbestand (in dit voorbeeld input.txt)

    ```cmd
    curl -k  --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches" -H "X-Requested-By: admin"
    ```

## <a name="get-information-on-livy-spark-batches-running-on-the-cluster"></a>Informatie krijgen over Livy Spark-batches die op het cluster worden uitgevoerd

Syntaxis:

```cmd
curl -k --user "admin:password" -v -X GET "https://<spark_cluster_name>.azurehdinsight.net/livy/batches"
```

### <a name="examples"></a>Voorbeelden

* Als u alle Livy Spark-batches die op het cluster worden uitgevoerd, wilt ophalen:

    ```cmd
    curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches"
    ```

* Als u een specifieke batch met een bepaalde batch-id wilt ophalen

    ```cmd
    curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"
    ```

## <a name="delete-a-livy-spark-batch-job"></a>Een batchtaak van Livy Spark verwijderen

```cmd
curl -k --user "admin:mypassword1!" -v -X DELETE "https://<spark_cluster_name>.azurehdinsight.net/livy/batches/{batchId}"
```

### <a name="example"></a>Voorbeeld

Een batchtaak verwijderen met `5`batch-ID .

```cmd
curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/5"
```

## <a name="livy-spark-and-high-availability"></a>Livy Spark en hoge beschikbaarheid

Livy biedt hoge beschikbaarheid voor Spark-taken die op het cluster worden uitgevoerd. Hier is een paar voorbeelden.

* Als de Livy-service wordt uitgeschakeld nadat u een taak op afstand hebt verzonden naar een Spark-cluster, blijft de taak op de achtergrond worden uitgevoerd. Wanneer Livy een back-up is, herstelt het de status van de taak en rapporteert het terug.
* Jupyter notebooks voor HDInsight worden aangedreven door Livy in de backend. Als een notitieblok een Spark-taak uitvoert en de Livy-service opnieuw wordt gestart, blijft het notitieblok de codecellen uitvoeren.

## <a name="show-me-an-example"></a>Laat me een voorbeeld zien

In deze sectie bekijken we voorbeelden om Livy Spark te gebruiken om batchtaak in te dienen, de voortgang van de taak te controleren en deze vervolgens te verwijderen. De toepassing die we gebruiken in dit voorbeeld is die ontwikkeld in het artikel [Maak een standalone Scala applicatie en te draaien op HDInsight Spark cluster](apache-spark-create-standalone-application.md). De stappen hier veronderstellen:

* U hebt de toepassingspot al gekopieerd naar het opslagaccount dat aan het cluster is gekoppeld.
* U hebt CuRL geïnstalleerd op de computer waar u deze stappen probeert.

Voer de volgende stappen uit:

1. Stel omgevingsvariabelen in voor gebruiksgemak. Dit voorbeeld is gebaseerd op een Windows-omgeving en wijzigt variabelen indien nodig voor uw omgeving. Vervang `CLUSTERNAME`en `PASSWORD` met de juiste waarden.

    ```cmd
    set clustername=CLUSTERNAME
    set password=PASSWORD
    ```

1. Controleer of Livy Spark op het cluster wordt uitgevoerd. We kunnen dit doen door het krijgen van een lijst van lopende partijen. Als u voor het eerst een taak uitvoert met Livy, moet de uitvoer nul opleveren.

    ```cmd
    curl -k --user "admin:%password%" -v -X GET "https://%clustername%.azurehdinsight.net/livy/batches"
    ```

    U moet een uitvoer krijgen die vergelijkbaar is met het volgende fragment:

    ```output
    < HTTP/1.1 200 OK
    < Content-Type: application/json; charset=UTF-8
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Fri, 20 Nov 2015 23:47:53 GMT
    < Content-Length: 34
    <
    {"from":0,"total":0,"sessions":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
    ```

    Merk op hoe de laatste regel in de uitvoer **totaal:0**zegt, wat suggereert dat er geen lopende batches zijn.

1. Laten we nu een batch baan indienen. In het volgende fragment wordt een invoerbestand (input.txt) gebruikt om de naam van de pot en de klassenaam als parameters door te geven. Als u deze stappen uitvoert vanaf een Windows-computer, is het gebruik van een invoerbestand de aanbevolen aanpak.

    ```cmd
    curl -k --user "admin:%password%" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://%clustername%.azurehdinsight.net/livy/batches" -H "X-Requested-By: admin"
    ```

    De parameters in het bestand **input.txt** worden als volgt gedefinieerd:

    ```text
    { "file":"wasbs:///example/jars/SparkSimpleApp.jar", "className":"com.microsoft.spark.example.WasbIOTest" }
    ```

    Als het goed is, wordt ongeveer het volgende codefragment weergegeven:

    ```output
    < HTTP/1.1 201 Created
    < Content-Type: application/json; charset=UTF-8
    < Location: /0
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Fri, 20 Nov 2015 23:51:30 GMT
    < Content-Length: 36
    <
    {"id":0,"state":"starting","log":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
    ```

    Merk op hoe de laatste regel van de uitvoer **staat zegt:starten**. Er staat ook, **id:0**. Hier, **0** is de batch-ID.

1. U nu de status van deze specifieke batch ophalen met behulp van de batch-id.

    ```cmd
    curl -k --user "admin:%password%" -v -X GET "https://%clustername%.azurehdinsight.net/livy/batches/0"
    ```

    Als het goed is, wordt ongeveer het volgende codefragment weergegeven:

    ```output
    < HTTP/1.1 200 OK
    < Content-Type: application/json; charset=UTF-8
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Fri, 20 Nov 2015 23:54:42 GMT
    < Content-Length: 509
    <
    {"id":0,"state":"success","log":["\t diagnostics: N/A","\t ApplicationMaster host: 10.0.0.4","\t ApplicationMaster RPC port: 0","\t queue: default","\t start time: 1448063505350","\t final status: SUCCEEDED","\t tracking URL: http://myspar.lpel.jx.internal.cloudapp.net:8088/proxy/application_1447984474852_0002/","\t user: root","15/11/20 23:52:47 INFO Utils: Shutdown hook called","15/11/20 23:52:47 INFO Utils: Deleting directory /tmp/spark-b72cd2bf-280b-4c57-8ceb-9e3e69ac7d0c"]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
    ```

    De uitvoer toont nu **status:succes**, wat suggereert dat de taak met succes is voltooid.

1. Als u wilt, u de batch nu verwijderen.

    ```cmd
    curl -k --user "admin:%password%" -v -X DELETE "https://%clustername%.azurehdinsight.net/livy/batches/0"
    ```

    Als het goed is, wordt ongeveer het volgende codefragment weergegeven:

    ```output
    < HTTP/1.1 200 OK
    < Content-Type: application/json; charset=UTF-8
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Sat, 21 Nov 2015 18:51:54 GMT
    < Content-Length: 17
    <
    {"msg":"deleted"}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
    ```

    De laatste regel van de uitvoer geeft aan dat de batch is verwijderd. Een baan verwijderen, terwijl het loopt, doodt ook de baan. Als u een taak verwijdert die is voltooid, al dan niet met succes, worden de taakgegevens volledig verwijderd.

## <a name="updates-to-livy-configuration-starting-with-hdinsight-35-version"></a>Updates voor Livy-configuratie vanaf HDInsight 3.5-versie

HDInsight 3.5-clusters en hoger schakelen standaard het gebruik van lokale bestandspaden uit om toegang te krijgen tot voorbeeldgegevensbestanden of -potten. We raden u `wasbs://` aan om het pad in plaats daarvan te gebruiken om toegang te krijgen tot potten of voorbeeldgegevensbestanden uit het cluster.

## <a name="submitting-livy-jobs-for-a-cluster-within-an-azure-virtual-network"></a>Livy-taken indienen voor een cluster binnen een virtueel Azure-netwerk

Als u verbinding maakt met een HDInsight Spark-cluster vanuit een Azure Virtual Network, u rechtstreeks verbinding maken met Livy op het cluster. In een dergelijk geval is `http://<IP address of the headnode>:8998/batches`de URL voor Livy-eindpunt . Hier is **8998** de haven waarop Livy op de clusterheadnode draait. Zie Ports die worden gebruikt door Apache [Hadoop-services op HDInsight](../hdinsight-hadoop-port-settings-for-services.md)voor meer informatie over de toegang tot services op niet-openbare poorten.

## <a name="next-steps"></a>Volgende stappen

* [Apache Livy REST API-documentatie](https://livy.incubator.apache.org/docs/latest/rest-api.html)
* [Resources beheren voor het Apache Spark-cluster in Azure HDInsight](apache-spark-resource-manager.md)
* [Taken die worden uitgevoerd in een Apache Spark-cluster in HDInsight, traceren en er fouten in oplossen](apache-spark-job-debugging.md)
