---
title: Gebruik MapReduce en krul met Apache Hadoop in HDInsight-Azure
description: Meer informatie over het extern uitvoeren van MapReduce-taken met Apache Hadoop op HDInsight met behulp van krul.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/27/2018
ms.openlocfilehash: 274d8dc80d9318aa3ddf4a904a5b623319ea01f4
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/03/2020
ms.locfileid: "75645001"
---
# <a name="run-mapreduce-jobs-with-apache-hadoop-on-hdinsight-using-rest"></a>MapReduce-taken uitvoeren met Apache Hadoop op HDInsight met behulp van REST

Meer informatie over het gebruik van de Apache Hive WebHCat REST API om MapReduce-taken uit te voeren op een Apache Hadoop in HDInsight-cluster. Krul wordt gebruikt om te laten zien hoe u kunt communiceren met HDInsight met behulp van onbewerkte HTTP-aanvragen om MapReduce-taken uit te voeren.

> [!NOTE]  
> Als u al bekend bent met het gebruik van op Linux gebaseerde Hadoop-servers, maar u geen ervaring hebt met HDInsight, raadpleegt u de [informatie die u nodig hebt om te weten te komen over op Linux gebaseerde Apache Hadoop in hdinsight-](../hdinsight-hadoop-linux-information.md) document.


## <a id="prereq"></a>Vereisten

* Een Hadoop in HDInsight-cluster
* Windows Power shell of [krul](https://curl.haxx.se/) en [JQ](https://stedolan.github.io/jq/)

## <a id="curl"></a>Een MapReduce-taak uitvoeren

> [!NOTE]  
> Wanneer u een krul of andere REST-communicatie met WebHCat gebruikt, moet u de aanvragen verifiëren door de gebruikers naam en het wacht woord van de HDInsight-cluster beheerder op te geven. U moet de cluster naam gebruiken als onderdeel van de URI die wordt gebruikt om de aanvragen naar de server te verzenden.
>
> De REST API wordt beveiligd met behulp van [basis verificatie voor toegang](https://en.wikipedia.org/wiki/Basic_access_authentication). U moet altijd aanvragen indienen via HTTPS om ervoor te zorgen dat uw referenties veilig worden verzonden naar de server.

1. Gebruik een van de volgende opdrachten om de cluster aanmelding in te stellen die wordt gebruikt door de scripts in dit document:

    ```bash
    read -p "Enter your cluster login account name: " LOGIN
    ```

    ```powershell
    $creds = Get-Credential -UserName admin -Message "Enter the cluster login name and password"
    ```

2. Als u de cluster naam wilt instellen, gebruikt u een van de volgende opdrachten:

    ```bash
    read -p "Enter the HDInsight cluster name: " CLUSTERNAME
    ```

    ```powershell
    $clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
    ```

3. Gebruik een opdrachtregel met de volgende opdracht om te controleren of u verbinding met uw HDInsight-cluster kunt maken:

    ```bash
    curl -u $LOGIN -G https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clustername.azurehdinsight.net/templeton/v1/status" `
        -Credential $creds `
        -UseBasicParsing
    $resp.Content
    ```

    U ontvangt een antwoord dat vergelijkbaar is met de volgende JSON:

        {"status":"ok","version":"v1"}

    In deze opdracht worden de volgende parameters gebruikt:

   * **-u**: geeft de gebruikers naam en het wacht woord aan die worden gebruikt om de aanvraag te verifiëren
   * **-G**: geeft aan dat deze bewerking een GET-aanvraag is

   Het begin van de URI, `https://CLUSTERNAME.azurehdinsight.net/templeton/v1`, is hetzelfde voor alle aanvragen.

4. Als u een MapReduce-taak wilt verzenden, gebruikt u de volgende opdracht:

    ```bash
    JOBID=`curl -u $LOGIN -d user.name=$LOGIN -d jar=/example/jars/hadoop-mapreduce-examples.jar -d class=wordcount -d arg=/example/data/gutenberg/davinci.txt -d arg=/example/data/output https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/mapreduce/jar | jq .id`
    echo $JOBID
    ```

    ```powershell
    $reqParams = @{}
    $reqParams."user.name" = "admin"
    $reqParams.jar = "/example/jars/hadoop-mapreduce-examples.jar"
    $reqParams.class = "wordcount"
    $reqParams.arg = @()
    $reqParams.arg += "/example/data/gutenberg/davinci.txt"
    $reqparams.arg += "/example/data/output"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/mapreduce/jar" `
       -Credential $creds `
       -Body $reqParams `
       -Method POST `
       -UseBasicParsing
    $jobID = (ConvertFrom-Json $resp.Content).id
    $jobID
    ```

    Het einde van de URI (/MapReduce/jar) vertelt WebHCat dat deze aanvraag een MapReduce-taak start vanuit een klasse in een jar-bestand. In deze opdracht worden de volgende parameters gebruikt:

   * **-d**: `-G` wordt niet gebruikt, waardoor de aanvraag standaard wordt ingesteld op de methode post. `-d` geeft de gegevens waarden op die met de aanvraag worden verzonden.
     * **User.name**: de gebruiker die de opdracht uitvoert
     * **jar**: de locatie van het jar-bestand met de klasse die moet worden uitgevoerd
     * **klasse**: de klasse die de MapReduce Logic bevat
     * **ARG**: de argumenten die moeten worden door gegeven aan de MapReduce-taak. In dit geval het invoer tekst bestand en de map die worden gebruikt voor de uitvoer

   Met deze opdracht moet een taak-ID worden geretourneerd die kan worden gebruikt om de status van de taak te controleren:

       job_1415651640909_0026

5. Als u de status van de taak wilt controleren, gebruikt u de volgende opdracht:

    ```bash
    curl -G -u $LOGIN -d user.name=$LOGIN https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/$JOBID | jq .status.state
    ```

    ```powershell
    $reqParams=@{"user.name"="admin"}
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/jobs/$jobID" `
       -Credential $creds `
       -Body $reqParams `
       -UseBasicParsing
    # ConvertFrom-JSON can't handle duplicate names with different case
    # So change one to prevent the error
    $fixDup=$resp.Content.Replace("jobID","job_ID")
    (ConvertFrom-Json $fixDup).status.state
    ```

    Als de taak is voltooid, wordt de geretourneerde status `SUCCEEDED`.

   > [!NOTE]  
   > Deze krul aanvraag retourneert een JSON-document met informatie over de taak. JQ wordt gebruikt om alleen de status waarde op te halen.

6. Wanneer de status van de taak is gewijzigd in `SUCCEEDED`, kunt u de resultaten van de taak ophalen uit Azure Blob-opslag. De para meter `statusdir` die wordt door gegeven met de query, bevat de locatie van het uitvoer bestand. In dit voor beeld is de locatie `/example/curl`. Dit adres slaat de uitvoer van de taak op in de standaard opslag voor clusters op `/example/curl`.

U kunt deze bestanden weer geven en downloaden met behulp van de [Azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli). Zie de [Azure CLI gebruiken met Azure Storage](../../storage/common/storage-azure-cli.md#create-and-manage-blobs) -document voor meer informatie over het werken met blobs in de Azure cli.

## <a id="nextsteps"></a>Volgende stappen

Voor algemene informatie over MapReduce-taken in HDInsight:

* [MapReduce gebruiken met Apache Hadoop op HDInsight](hdinsight-use-mapreduce.md)

Voor informatie over andere manieren om met Hadoop in HDInsight te werken:

* [Apache Hive gebruiken met Apache Hadoop op HDInsight](hdinsight-use-hive.md)
* [Apache Pig gebruiken met Apache Hadoop op HDInsight](hdinsight-use-pig.md)

Zie de [WebHCat-verwijzing](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference)voor meer informatie over de rest-interface die in dit artikel wordt gebruikt.
