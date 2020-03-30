---
title: MapReduce en Curl gebruiken met Apache Hadoop in HDInsight - Azure
description: Meer informatie over het op afstand uitvoeren van MapReduce-taken met Apache Hadoop op HDInsight met Curl.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/13/2020
ms.openlocfilehash: abc3cc8c526e37e18f1e67b109a9a8e15ff8c989
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302709"
---
# <a name="run-mapreduce-jobs-with-apache-hadoop-on-hdinsight-using-rest"></a>Kaart uitvoerenWerk verminderen met Apache Hadoop op HDInsight met BEHULP van REST

Meer informatie over het gebruik van de Apache Hive WebHCat REST API om MapReduce-taken uit te voeren op een Apache Hadoop op hdinsight-cluster. Curl wordt gebruikt om aan te tonen hoe u communiceren met HDInsight door ruwe HTTP-aanvragen te gebruiken om MapReduce-taken uit te voeren.

> [!NOTE]  
> Als u al bekend bent met het gebruik van Linux-gebaseerde Hadoop servers, maar je bent nieuw bij HDInsight, zie de [Wat je moet weten over Linux-gebaseerde Apache Hadoop op HDInsight](../hdinsight-hadoop-linux-information.md) document.

## <a name="prerequisites"></a>Vereisten

* Een Apache Hadoop cluster op HDInsight. Zie [Apache Hadoop-clusters maken met behulp van de Azure-portal](../hdinsight-hadoop-create-linux-clusters-portal.md).

Er zijn verschillende opties:
  * Windows PowerShell of,
  * [Krul](https://curl.haxx.se/) met [jq](https://stedolan.github.io/jq/)

## <a name="run-a-mapreduce-job"></a>Een taak Kaart verminderen uitvoeren

> [!NOTE]  
> Wanneer u Curl of een andere REST-communicatie met WebHCat gebruikt, moet u de aanvragen verifiëren door de gebruikersnaam en het wachtwoord van de HDInsight-clusterbeheerder op te geven. U moet de clusternaam gebruiken als onderdeel van de URI die wordt gebruikt om de aanvragen naar de server te verzenden.
>
> De REST API wordt beveiligd met behulp van [basistoegangsverificatie.](https://en.wikipedia.org/wiki/Basic_access_authentication) U moet altijd verzoeken indienen door HTTPS te gebruiken om ervoor te zorgen dat uw referenties veilig naar de server worden verzonden.

### <a name="curl"></a>Curl

1. Voor gebruiksgemak stelt u de onderstaande variabelen in. Dit voorbeeld is gebaseerd op een Windows-omgeving, herzien als dat nodig is voor uw omgeving.

    ```cmd
    set CLUSTERNAME=
    set PASSWORD=
    ```

1. Gebruik een opdrachtregel met de volgende opdracht om te controleren of u verbinding met uw HDInsight-cluster kunt maken:

    ```bash
    curl -u admin:%PASSWORD% -G https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/status
    ```

    In deze opdracht worden de volgende parameters gebruikt:

   * **-u**: Geeft de gebruikersnaam en het wachtwoord aan die worden gebruikt om de aanvraag te verifiëren
   * **-G**: Geeft aan dat deze bewerking een GET-aanvraag is

   Het begin van `https://CLUSTERNAME.azurehdinsight.net/templeton/v1`de URI, , is hetzelfde voor alle aanvragen.

    U ontvangt een antwoord dat vergelijkbaar is met de volgende JSON:

    ```output
    {"version":"v1","status":"ok"}
    ```

1. Als u een taak MapReduce wilt verzenden, gebruikt u de volgende opdracht. Wijzig indien nodig het pad naar **jq.**

    ```cmd
    curl -u admin:%PASSWORD% -d user.name=admin ^
    -d jar=/example/jars/hadoop-mapreduce-examples.jar ^
    -d class=wordcount -d arg=/example/data/gutenberg/davinci.txt -d arg=/example/data/output ^
    https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/mapreduce/jar | ^
    C:\HDI\jq-win64.exe .id
    ```

    Het einde van de URI (/mapreduce/jar) vertelt WebHCat dat deze aanvraag een MapReduce-taak van een klasse in een potbestand start. In deze opdracht worden de volgende parameters gebruikt:

   * **-d** `-G` : wordt niet gebruikt, dus de aanvraag wordt standaard gebruikt voor de post-methode. `-d`hiermee worden de gegevenswaarden opgegeven die met de aanvraag worden verzonden.
     * **user.name:** de gebruiker die de opdracht uitvoert
     * **pot**: De locatie van het potbestand met klasse dat moet worden uitgevoerd
     * **klasse**: De klasse die de logica MapReduce bevat
     * **arg:** de argumenten die moeten worden doorgegeven aan de taak MapReduce. In dit geval het invoertekstbestand en de map die worden gebruikt voor de uitvoer

    Met deze opdracht moet een taak-id worden retourneren die kan worden gebruikt om de status van de taak te controleren:

       job_1415651640909_0026

1. Als u de status van de taak wilt controleren, gebruikt u de volgende opdracht. Vervang de `JOBID` waarde voor de **werkelijke** waarde die in de vorige stap is geretourneerd. Herzien locatie van **jq** als dat nodig is.

    ```cmd
    set JOBID=job_1415651640909_0026

    curl -G -u admin:%PASSWORD% -d user.name=admin https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/jobs/%JOBID% | ^
    C:\HDI\jq-win64.exe .status.state
    ```

### <a name="powershell"></a>PowerShell

1. Voor gebruiksgemak stelt u de onderstaande variabelen in. Vervang `CLUSTERNAME` door de werkelijke clusternaam. Voer de opdracht uit en voer het wachtwoord voor het inloggen van het cluster in wanneer u daarom wordt gevraagd.

    ```powershell
    $clusterName="CLUSTERNAME"
    $creds = Get-Credential -UserName admin -Message "Enter the cluster login password"
    ```

1. gebruik de volgende opdracht om te controleren of u verbinding maken met uw HDInsight-cluster:

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clustername.azurehdinsight.net/templeton/v1/status" `
        -Credential $creds `
        -UseBasicParsing
    $resp.Content
    ```

    U ontvangt een antwoord dat vergelijkbaar is met de volgende JSON:

    ```output
    {"version":"v1","status":"ok"}
    ```

1. Als u een taak MapReduce wilt verzenden, gebruikt u de volgende opdracht:

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

    Het einde van de URI (/mapreduce/jar) vertelt WebHCat dat deze aanvraag een MapReduce-taak van een klasse in een potbestand start. In deze opdracht worden de volgende parameters gebruikt:

    * **user.name:** de gebruiker die de opdracht uitvoert
    * **pot**: De locatie van het potbestand met klasse dat moet worden uitgevoerd
    * **klasse**: De klasse die de logica MapReduce bevat
    * **arg:** de argumenten die moeten worden doorgegeven aan de taak MapReduce. In dit geval het invoertekstbestand en de map die worden gebruikt voor de uitvoer

   Met deze opdracht moet een taak-id worden retourneren die kan worden gebruikt om de status van de taak te controleren:

       job_1415651640909_0026

1. Als u de status van de taak wilt controleren, gebruikt u de volgende opdracht:

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

### <a name="both-methods"></a>Beide methoden

1. Als de taak is voltooid, `SUCCEEDED`is de geretourneerde status .

1. Wanneer de status van de `SUCCEEDED`taak is gewijzigd in, u de resultaten van de taak ophalen uit Azure Blob-opslag. De `statusdir` parameter die met de query wordt doorgegeven, bevat de locatie van het uitvoerbestand. In dit voorbeeld is `/example/curl`de locatie . Dit adres slaat de uitvoer van de `/example/curl`taak op in de standaardopslag van clusters op .

U deze bestanden aanbieden en downloaden met behulp van de [Azure CLI](/cli/azure/install-azure-cli). Zie [Quickstart: Blobs maken, downloaden en aanbieden met Azure CLI](../../storage/blobs/storage-quickstart-blobs-cli.md)voor meer informatie over het gebruik van de Azure CLI om met Azure Blob-opslag te werken.

## <a name="next-steps"></a>Volgende stappen

Voor informatie over andere manieren waarop u met Hadoop werken aan HDInsight:

* [MapGebruikenReduce met Apache Hadoop op HDInsight](hdinsight-use-mapreduce.md)
* [Gebruik Apache Hive met Apache Hadoop op HDInsight](hdinsight-use-hive.md)

Zie de [WebHCat-referentie](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference)voor meer informatie over de REST-interface die in dit artikel wordt gebruikt.
