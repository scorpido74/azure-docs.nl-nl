---
title: Apache Hadoop Hive gebruiken met Curl in HDInsight - Azure
description: Meer informatie over het op afstand verzenden van Apache Pig-taken naar Azure HDInsight met Behulp van Curl.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/06/2020
ms.openlocfilehash: 10a2f413142124db7547e68280a0d5e9abac9b98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79298747"
---
# <a name="run-apache-hive-queries-with-apache-hadoop-in-hdinsight-using-rest"></a>Apache Hive-query's uitvoeren met Apache Hadoop in HDInsight met BEHULP van REST

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Meer informatie over het gebruik van de WebHCat REST API om Apache Hive-query's uit te voeren met Apache Hadoop op azure HDInsight-cluster.

## <a name="prerequisites"></a>Vereisten

* Een Apache Hadoop cluster op HDInsight. Zie [Aan de slag met HDInsight op Linux](./apache-hadoop-linux-tutorial-get-started.md).

* Een REST-client. Dit document maakt gebruik [van Invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest) op Windows PowerShell en [Curl](https://curl.haxx.se/) on [Bash.](https://docs.microsoft.com/windows/wsl/install-win10)

* Als je Bash gebruikt, heb je ook jq nodig, een json-processor voor de command-line.  Zie [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/).

## <a name="base-uri-for-rest-api"></a>URI voor rest-API baseren

De basis Uniform Resource Identifier (URI) voor `https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME`de `CLUSTERNAME` REST API op HDInsight is , waar is de naam van uw cluster.  Clusternamen in URI's zijn **hoofdlettergevoelig**.  Hoewel de clusternaam in het volledig gekwalificeerde domeinnaam (FQDN) deel van de URI (`CLUSTERNAME.azurehdinsight.net`) case-ongevoelig is, zijn andere gebeurtenissen in de URI hoofdlettergevoelig.

## <a name="authentication"></a>Authentication

Wanneer u cURL of andere REST-communicatie met WebHCat gebruikt, moet u de aanvragen verifiëren door de gebruikersnaam en het wachtwoord voor de HDInsight-clusterbeheerder op te geven. De REST API is beveiligd via [basisverificatie](https://en.wikipedia.org/wiki/Basic_access_authentication). Als u ervoor wilt zorgen dat uw referenties veilig naar de server worden verzonden, doet u altijd verzoeken met Behulp van Secure HTTP (HTTPS).

### <a name="setup-preserve-credentials"></a>Instellen (Referenties behouden)

Bewaar uw referenties om te voorkomen dat ze opnieuw worden ingevoerd voor elk voorbeeld.  De clusternaam blijft in een afzonderlijke stap behouden.

**A. Bash**  
Bewerk het script hieronder `PASSWORD` door te vervangen door uw werkelijke wachtwoord.  Voer vervolgens de opdracht in.

```bash
export password='PASSWORD'
```  

**B. PowerShell** Voer de onderstaande code uit en voer uw referenties in in het pop-upvenster:

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
```

### <a name="identify-correctly-cased-cluster-name"></a>De juiste clusternaam van de hoofdletters identificeren

De werkelijke behuizing van de clusternaam kan anders zijn dan u verwacht, afhankelijk van hoe het cluster is gemaakt.  De stappen hier tonen de werkelijke behuizing en slaan deze vervolgens op in een variabele voor alle latere voorbeelden.

Bewerk de onderstaande `CLUSTERNAME` scripts om uw clusternaam te vervangen. Voer vervolgens de opdracht in. (De clusternaam voor de FQDN is niet hoofdlettergevoelig.)

```bash
export clusterName=$(curl -u admin:$password -sS -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
echo $clusterName
```  

```powershell
# Identify properly cased cluster name
$resp = Invoke-WebRequest -Uri "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters" `
    -Credential $creds -UseBasicParsing
$clusterName = (ConvertFrom-Json $resp.Content).items.Clusters.cluster_name;

# Show cluster name
$clusterName
```

## <a name="run-a-hive-query"></a>Een Hive-query uitvoeren

1. Als u wilt controleren of u verbinding maken met uw HDInsight-cluster, gebruikt u een van de volgende opdrachten:

    ```bash
    curl -u admin:$password -G https://$clusterName.azurehdinsight.net/templeton/v1/status
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/status" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    U ontvangt een antwoord dat vergelijkbaar is met de volgende tekst:

    ```json
    {"status":"ok","version":"v1"}
    ```

    In deze opdracht worden de volgende parameters gebruikt:

    * `-u`- De gebruikersnaam en het wachtwoord die worden gebruikt om het verzoek te verifiëren.
    * `-G`- Geeft aan dat dit verzoek een GET-bewerking is.

1. Het begin van `https://$CLUSTERNAME.azurehdinsight.net/templeton/v1`de URL, , is hetzelfde voor alle aanvragen. Het pad `/status`geeft aan dat het verzoek is om een status van WebHCat (ook bekend als Templeton) voor de server terug te sturen. U de versie van Hive ook aanvragen met de volgende opdracht:

    ```bash
    curl -u admin:$password -G https://$clusterName.azurehdinsight.net/templeton/v1/version/hive
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/version/hive" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    Met dit verzoek wordt een antwoord geretourneerd dat vergelijkbaar is met de volgende tekst:

    ```json
    {"module":"hive","version":"1.2.1000.2.6.5.3008-11"}
    ```

1. Gebruik het volgende om een tabel met de naam **log4jLogs**te maken:

    ```bash
    jobid=$(curl -s -u admin:$password -d user.name=admin -d execute="DROP+TABLE+log4jLogs;CREATE+EXTERNAL+TABLE+log4jLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+ROW+FORMAT+DELIMITED+FIELDS+TERMINATED+BY+' '+STORED+AS+TEXTFILE+LOCATION+'/example/data/';SELECT+t4+AS+sev,COUNT(*)+AS+count+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log'+GROUP+BY+t4;" -d statusdir="/example/rest" https://$clusterName.azurehdinsight.net/templeton/v1/hive | jq -r .id)
    echo $jobid
    ```

    ```powershell
    $reqParams = @{"user.name"="admin";"execute"="DROP TABLE log4jLogs;CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED BY ' ' STORED AS TEXTFILE LOCATION '/example/data/;SELECT t4 AS sev,COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;";"statusdir"="/example/rest"}
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/hive" `
       -Credential $creds `
       -Body $reqParams `
       -Method POST `
       -UseBasicParsing
    $jobID = (ConvertFrom-Json $resp.Content).id
    $jobID
    ```

    Deze aanvraag maakt gebruik van de POST-methode, die gegevens als onderdeel van de aanvraag naar de REST-API verzendt. De volgende gegevenswaarden worden verzonden met de aanvraag:

     * `user.name`- De gebruiker die de opdracht uitvoert.
     * `execute`- De HiveQL verklaringen uit te voeren.
     * `statusdir`- De map waarnaar de status voor deze taak is geschreven.

   Met deze instructies worden de volgende acties uitgevoerd:

   * `DROP TABLE`- Als de tabel al bestaat, wordt deze verwijderd.
   * `CREATE EXTERNAL TABLE`- Hiermee maakt u een nieuwe 'externe' tabel in Hive. Externe tabellen slaan alleen de tabeldefinitie op in Hive. De gegevens worden op de oorspronkelijke locatie achtergelaten.

     > [!NOTE]  
     > Externe tabellen moeten worden gebruikt wanneer u verwacht dat de onderliggende gegevens worden bijgewerkt door een externe bron. Bijvoorbeeld een geautomatiseerd proces voor het uploaden van gegevens of een andere MapReduce-bewerking.
     >
     > Als u een externe tabel laat vallen, worden de gegevens **niet** verwijderd, alleen de tabeldefinitie.

   * `ROW FORMAT`- Hoe de gegevens worden opgemaakt. De velden in elk logboek worden gescheiden door een spatie.
   * `STORED AS TEXTFILE LOCATION`- Wanneer de gegevens worden opgeslagen (het voorbeeld/gegevensmap) en dat deze als tekst worden opgeslagen.
   * `SELECT`- Hiermee selecteert u een aantal rijen waarin kolom **t4** de waarde **bevat [FOUT]**. Met deze instructie wordt een waarde van **3** geretourneerd, aangezien er drie rijen zijn die deze waarde bevatten.

     > [!NOTE]  
     > Merk op dat de spaties tussen `+` HiveQL-instructies worden vervangen door het teken wanneer ze worden gebruikt met Curl. Genoteerde waarden die een spatie bevatten, zoals de scheidingsteken, mogen niet worden vervangen door `+`.

      Met deze opdracht retourneert een taak-id die kan worden gebruikt om de status van de taak te controleren.

1. Als u de status van de taak wilt controleren, gebruikt u de volgende opdracht:

    ```bash
    curl -u admin:$password -d user.name=admin -G https://$clusterName.azurehdinsight.net/templeton/v1/jobs/$jobid | jq .status.state
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

    Als de taak is voltooid, wordt de status **OPGEVOLGD.**

1. Zodra de status van de taak is gewijzigd in **GESLAAGD,** u de resultaten van de taak ophalen uit Azure Blob-opslag. De `statusdir` parameter die met de query is doorgegeven, bevat de locatie van het uitvoerbestand. in dit `/example/rest`geval, . Met dit adres wordt `example/curl` de uitvoer opgeslagen in de map in de standaardopslag van clusters.

    U deze bestanden aanbieden en downloaden met behulp van de [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). Zie het document Azure CLI gebruiken met [Azure Storage](https://docs.microsoft.com/azure/storage/storage-azure-cli) voor meer informatie over het gebruik van de Azure CLI met Azure Storage.

## <a name="next-steps"></a>Volgende stappen

Voor informatie over andere manieren waarop u met Hadoop werken aan HDInsight:

* [Gebruik Apache Hive met Apache Hadoop op HDInsight](hdinsight-use-hive.md)
* [MapGebruikenReduce met Apache Hadoop op HDInsight](hdinsight-use-mapreduce.md)

Zie het [WebHCat-referentiedocument](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference) voor meer informatie over de REST-API die in dit document wordt gebruikt.