---
title: Python UDF met Apache Hive en Apache Pig - Azure HDInsight
description: Meer informatie over het gebruik van Python User Defined Functions (UDF) van Apache Hive en Apache Pig in HDInsight, de Apache Hadoop-technologiestack op Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/15/2019
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 201bb40e5024442587f5508886da7e844f35be40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74148406"
---
# <a name="use-python-user-defined-functions-udf-with-apache-hive-and-apache-pig-in-hdinsight"></a>Gebruik Python User Defined Functions (UDF) met Apache Hive en Apache Pig in HDInsight

Meer informatie over het gebruik van Python-gebruikersgedefinieerde functies (UDF) met Apache Hive en Apache Pig in Apache Hadoop op Azure HDInsight.

## <a name="python-on-hdinsight"></a><a name="python"></a>Python op HDInsight

Python2.7 is standaard geïnstalleerd op HDInsight 3.0 en hoger. Apache Hive kan worden gebruikt met deze versie van Python voor streamverwerking. Streamprocessing maakt gebruik van STDOUT en STDIN om gegevens door te geven tussen Hive en de UDF.

HDInsight bevat ook Jython, een Python-implementatie geschreven in Java. Jython draait rechtstreeks op de Java Virtual Machine en maakt geen gebruik van streaming. Jython is de aanbevolen Python-tolk bij het gebruik van Python met Pig.

## <a name="prerequisites"></a>Vereisten

* **Een Hadoop cluster op HDInsight**. Zie [Aan de slag met HDInsight op Linux](apache-hadoop-linux-tutorial-get-started.md).
* **Een SSH-client.** Zie voor meer informatie [Verbinding maken met HDInsight (Apache Hadoop) via SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).
* Het [URI-schema](../hdinsight-hadoop-linux-information.md#URI-and-scheme) voor de primaire opslag van uw clusters. Dit geldt `wasb://` voor Azure `abfs://` Storage, voor Azure Data Lake Storage Gen2 of adl:// voor Azure Data Lake Storage Gen1. Als beveiligde overdracht is ingeschakeld voor Azure Storage, wordt de URI wasbs://.  Zie ook, [veilige overdracht](../../storage/common/storage-require-secure-transfer.md).
* **Mogelijke wijziging in de opslagconfiguratie.**  Zie [Opslagconfiguratie](#storage-configuration) als u `BlobStorage`de opslagaccountsoort gebruikt.
* Optioneel.  Als u PowerShell wilt gebruiken, moet u de [AZ-module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az) installeren.

> [!NOTE]  
> Het opslagaccount dat in dit artikel wordt gebruikt, `wasbs` was Azure Storage met beveiligde [overdracht](../../storage/common/storage-require-secure-transfer.md) ingeschakeld en wordt dus in het hele artikel gebruikt.

## <a name="storage-configuration"></a>Opslagconfiguratie

Er is geen actie vereist als `Storage (general purpose v1)` het `StorageV2 (general purpose v2)`gebruikte opslagaccount van soort is of .  Het proces in dit artikel zal `/tezstaging`produceren output tot ten minste .  Een standaard hadoopconfiguratie `/tezstaging` bevat `fs.azure.page.blob.dir` in `core-site.xml` de `HDFS`configuratievariabele voor service .  Deze configuratie zorgt ervoor dat uitvoer naar de map paginablobs is, `BlobStorage`die niet worden ondersteund voor opslagaccount.  Als `BlobStorage` u dit artikel `/tezstaging` wilt `fs.azure.page.blob.dir` gebruiken, verwijdert u uit de configuratievariabele.  De configuratie is toegankelijk via de [Ambari UI.](../hdinsight-hadoop-manage-ambari.md)  Anders ontvangt u het foutbericht:`Page blob is not supported for this account type.`

> [!WARNING]  
> De stappen in dit document maken de volgende aannames:  
>
> * U maakt de Python-scripts op uw lokale ontwikkelomgeving.
> * U uploadt de scripts naar `scp` HDInsight met behulp van de opdracht of het meegeleverde PowerShell-script.
>
> Als u de [Azure Cloud Shell (bash)](https://docs.microsoft.com/azure/cloud-shell/overview) wilt gebruiken om met HDInsight te werken, moet u het:
>
> * Maak de scripts in de cloudshell-omgeving.
> * Met `scp` gebruiken om de bestanden van de cloudshell naar HDInsight te uploaden.
> * Gebruik `ssh` vanuit de cloudshell om verbinding te maken met HDInsight en voer de voorbeelden uit.

## <a name="apache-hive-udf"></a><a name="hivepython"></a>Apache Hive UDF

Python kan worden gebruikt als een UDF `TRANSFORM` van Hive via de HiveQL-instructie. Met de volgende HiveQL wordt `hiveudf.py` bijvoorbeeld het bestand aangeroepen dat is opgeslagen in het standaard Azure Storage-account voor het cluster.

```hiveql
add file wasbs:///hiveudf.py;

SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'python hiveudf.py' AS
    (clientid string, phoneLabel string, phoneHash string)
FROM hivesampletable
ORDER BY clientid LIMIT 50;
```

Dit voorbeeld doet dit voorbeeld als volgt:

1. De `add file` instructie aan het begin `hiveudf.py` van het bestand voegt het bestand toe aan de gedistribueerde cache, zodat het toegankelijk is voor alle knooppunten in het cluster.
2. De `SELECT TRANSFORM ... USING` instructie selecteert gegevens `hivesampletable`uit de . Het geeft ook de clientid, devicemake en `hiveudf.py` devicemodel waarden door aan het script.
3. De `AS` clausule beschrijft de `hiveudf.py`velden die zijn geretourneerd van .

<a name="streamingpy"></a>

### <a name="create-file"></a>Bestand maken

Maak op uw ontwikkelomgeving een `hiveudf.py`tekstbestand met de naam . Gebruik de volgende code als de inhoud van het bestand:

```python
#!/usr/bin/env python
import sys
import string
import hashlib

while True:
    line = sys.stdin.readline()
    if not line:
        break

    line = string.strip(line, "\n ")
    clientid, devicemake, devicemodel = string.split(line, "\t")
    phone_label = devicemake + ' ' + devicemodel
    print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])
```

In dit script worden de volgende acties uitgevoerd:

1. Hiermee leest u een regel gegevens van SOAIN.
2. Het slepende nieuweregelteken `string.strip(line, "\n ")`wordt verwijderd met behulp van .
3. Bij het verwerken van stroom bevat één regel alle waarden met een tabteken tussen elke waarde. Dus `string.split(line, "\t")` kan worden gebruikt om de invoer te splitsen op elk tabblad, terug te keren alleen de velden.
4. Wanneer de verwerking is voltooid, moet de uitvoer als één regel naar STDOUT worden geschreven, met een tabblad tussen elk veld. Bijvoorbeeld `print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])`.
5. De `while` lus wordt `line` herhaald totdat er geen wordt gelezen.

De scriptuitvoer is een samenvoeging `devicemake` van `devicemodel`de invoerwaarden voor en , en een hash van de samengevoegde waarde.

### <a name="upload-file-shell"></a>Bestand uploaden (shell)

Vervang in de onderstaande opdrachten `sshuser` de werkelijke gebruikersnaam als deze anders is.  Vervang `mycluster` door de werkelijke clusternaam.  Zorg ervoor dat uw werkmap zich bevindt.

1. Met `scp` deze items u de bestanden naar uw HDInsight-cluster kopiëren. Bewerk en voer de onderstaande opdracht in:

    ```cmd
    scp hiveudf.py sshuser@mycluster-ssh.azurehdinsight.net:
    ```

2. Gebruik SSH om verbinding te maken met het cluster.  Bewerk en voer de onderstaande opdracht in:

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

3. Voeg in de SSH-sessie de eerder geüploade python-bestanden toe aan de opslag voor het cluster.

    ```bash
    hdfs dfs -put hiveudf.py /hiveudf.py
    ```

### <a name="use-hive-udf-shell"></a>Hive UDF (shell) gebruiken

1. Als u verbinding wilt maken met Hive, gebruikt u de volgende opdracht uit uw geopende SSH-sessie:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

    Met deze opdracht wordt de Beeline-client gestart.

2. Voer de volgende `0: jdbc:hive2://headnodehost:10001/>` query in bij de prompt:

   ```hive
   add file wasbs:///hiveudf.py;
   SELECT TRANSFORM (clientid, devicemake, devicemodel)
       USING 'python hiveudf.py' AS
       (clientid string, phoneLabel string, phoneHash string)
   FROM hivesampletable
   ORDER BY clientid LIMIT 50;
   ```

3. Na het invoeren van de laatste regel, moet de taak beginnen. Zodra de taak is voltooid, retourneert deze uitvoer die vergelijkbaar is met het volgende voorbeeld:

        100041    RIM 9650    d476f3687700442549a83fac4560c51c
        100041    RIM 9650    d476f3687700442549a83fac4560c51c
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9

4. Als u Beeline wilt afsluiten, voert u de volgende opdracht in:

    ```hive
    !q
    ```

### <a name="upload-file-powershell"></a>Bestand uploaden (PowerShell)

PowerShell kan ook worden gebruikt om Hive-query's op afstand uit te voeren. Zorg ervoor dat `hiveudf.py` uw werkmap zich bevindt.  Gebruik het volgende PowerShell-script om een `hiveudf.py` Hive-query uit te voeren die het script gebruikt:

```PowerShell
# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

# Revise file path as needed
$pathToStreamingFile = ".\hiveudf.py"

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$clusterInfo = Get-AzHDInsightCluster -ClusterName $clusterName
$resourceGroup = $clusterInfo.ResourceGroup
$storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
$container=$clusterInfo.DefaultStorageContainer
$storageAccountKey=(Get-AzStorageAccountKey `
   -ResourceGroupName $resourceGroup `
   -Name $storageAccountName)[0].Value

# Create an Azure Storage context
$context = New-AzStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey $storageAccountKey

# Upload local files to an Azure Storage blob
Set-AzStorageBlobContent `
    -File $pathToStreamingFile `
    -Blob "hiveudf.py" `
    -Container $container `
    -Context $context
```

> [!NOTE]  
> Zie De [uploadgegevens voor Apache Hadoop-taken in het HDInsight-document voor](../hdinsight-upload-data.md) meer informatie over het uploaden van bestanden.

#### <a name="use-hive-udf"></a>Hive UDF gebruiken

```PowerShell
# Script should stop on failures
$ErrorActionPreference = "Stop"

# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$creds=Get-Credential -UserName "admin" -Message "Enter the login for the cluster"

$HiveQuery = "add file wasbs:///hiveudf.py;" +
                "SELECT TRANSFORM (clientid, devicemake, devicemodel) " +
                "USING 'python hiveudf.py' AS " +
                "(clientid string, phoneLabel string, phoneHash string) " +
                "FROM hivesampletable " +
                "ORDER BY clientid LIMIT 50;"

# Create Hive job object
$jobDefinition = New-AzHDInsightHiveJobDefinition `
    -Query $HiveQuery

# For status bar updates
$activity="Hive query"

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Starting query..."

# Start defined Azure HDInsight job on specified cluster.
$job = Start-AzHDInsightJob `
    -ClusterName $clusterName `
    -JobDefinition $jobDefinition `
    -HttpCredential $creds

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Waiting on query to complete..."

# Wait for completion or failure of specified job
Wait-AzHDInsightJob `
    -JobId $job.JobId `
    -ClusterName $clusterName `
    -HttpCredential $creds

# Uncomment the following to see stderr output
<#
Get-AzHDInsightJobOutput `
   -Clustername $clusterName `
   -JobId $job.JobId `
   -HttpCredential $creds `
   -DisplayOutputType StandardError
#>

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Retrieving output..."

# Gets the log output
Get-AzHDInsightJobOutput `
    -Clustername $clusterName `
    -JobId $job.JobId `
    -HttpCredential $creds
```

De uitvoer voor de **hive-taak** moet vergelijkbaar lijken met het volgende voorbeeld:

    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9

## <a name="apache-pig-udf"></a><a name="pigpython"></a>Apache Varken UDF

Een Python-script kan via de `GENERATE` instructie worden gebruikt als UDF van Pig. U het script uitvoeren met Jython of C Python.

* Jython loopt op de JVM, en kan native worden opgeroepen van Pig.
* C Python is een extern proces, dus de gegevens van Pig op de JVM worden verzonden naar het script dat wordt uitgevoerd in een Python-proces. De uitvoer van het Python-script wordt teruggestuurd naar Pig.

Als u de Python-tolk wilt opgeven, gebruikt u bij `register` verwijzing naar het Python-script. De volgende voorbeelden registreren scripts `myfuncs`met Pig als:

* **Om Jython te gebruiken:**`register '/path/to/pigudf.py' using jython as myfuncs;`
* **C Python gebruiken:**`register '/path/to/pigudf.py' using streaming_python as myfuncs;`

> [!IMPORTANT]  
> Bij het gebruik van Jython kan het pad naar het pig_jython bestand een lokaal pad of een WASBS:// pad zijn. Wanneer u Echter C Python gebruikt, moet u verwijzen naar een bestand op het lokale bestandssysteem van het knooppunt dat u gebruikt om de opdracht Varken in te dienen.

Eenmaal na de registratie is het Pig Latin voor dit voorbeeld voor beide hetzelfde:

```pig
LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
LOG = FILTER LOGS by LINE is not null;
DETAILS = FOREACH LOG GENERATE myfuncs.create_structure(LINE);
DUMP DETAILS;
```

Dit voorbeeld doet dit voorbeeld als volgt:

1. De eerste regel laadt het `sample.log` `LOGS`voorbeeldgegevensbestand in . Het definieert ook `chararray`elke record als een .
2. De volgende regel filtert alle null-waarden uit `LOG`en slaat het resultaat van de bewerking op in .
3. Vervolgens wordt het over de `LOG` records `GENERATE` in en `create_structure` gebruikt om de methode in de Python `myfuncs`/ Jython script geladen als aanroepen . `LINE`wordt gebruikt om de huidige record door te geven aan de functie.
4. Ten slotte worden de uitgangen met `DUMP` behulp van de opdracht naar STDOUT gedumpt. Met deze opdracht worden de resultaten weergegeven nadat de bewerking is voltooid.

### <a name="create-file"></a>Bestand maken

Maak op uw ontwikkelomgeving een `pigudf.py`tekstbestand met de naam . Gebruik de volgende code als de inhoud van het bestand:

<a name="streamingpy"></a>

```python
# Uncomment the following if using C Python
#from pig_util import outputSchema


@outputSchema("log: {(date:chararray, time:chararray, classname:chararray, level:chararray, detail:chararray)}")
def create_structure(input):
    if (input.startswith('java.lang.Exception')):
        input = input[21:len(input)] + ' - java.lang.Exception'
    date, time, classname, level, detail = input.split(' ', 4)
    return date, time, classname, level, detail
```

In het voorbeeld Pig `LINE` Latin wordt de invoer gedefinieerd als een chararray omdat er geen consistent schema voor de invoer is. Het Python-script transformeert de gegevens in een consistent schema voor uitvoer.

1. De `@outputSchema` instructie definieert de indeling van de gegevens die worden geretourneerd naar Pig. In dit geval is het een **databag**, dat is een Pig data type. De zak bevat de volgende velden, die allemaal chararray (strings):

   * datum - de datum waarop de logvermelding is gemaakt
   * tijd - het tijdstip dat de logboekvermelding is gemaakt
   * klassennaam - de klassenaam waarvoor het item is gemaakt
   * niveau - het logboekniveau
   * detail - uitgebreide details voor de log entry

2. Vervolgens definieert u de `def create_structure(input)` functie waaraan Pig regelitems doorgeeft.

3. De voorbeeldgegevens `sample.log`voldoen meestal aan het datum-, tijd-, klasse-, niveau- en detailschema. Het bevat echter een paar `*java.lang.Exception*`regels die beginnen met . Deze regels moeten worden aangepast aan het schema. De `if` instructie controleert op deze, vervolgens masseert de invoergegevens om de `*java.lang.Exception*` string te verplaatsen naar het einde, waardoor de gegevens in lijn met de verwachte uitvoer schema.

4. Vervolgens wordt `split` de opdracht gebruikt om de gegevens bij de eerste vier spatietekens te splitsen. De uitvoer wordt `date` `time`toegewezen `classname` `level`aan `detail`, , , en .

5. Ten slotte worden de waarden teruggegeven aan Pig.

Wanneer de gegevens worden geretourneerd naar Pig, heeft deze `@outputSchema` een consistent schema zoals gedefinieerd in de instructie.

### <a name="upload-file-shell"></a>Bestand uploaden (shell)

Vervang in de onderstaande opdrachten `sshuser` de werkelijke gebruikersnaam als deze anders is.  Vervang `mycluster` door de werkelijke clusternaam.  Zorg ervoor dat uw werkmap zich bevindt.

1. Met `scp` deze items u de bestanden naar uw HDInsight-cluster kopiëren. Bewerk en voer de onderstaande opdracht in:

    ```cmd
    scp pigudf.py sshuser@mycluster-ssh.azurehdinsight.net:
    ```

2. Gebruik SSH om verbinding te maken met het cluster.  Bewerk en voer de onderstaande opdracht in:

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

3. Voeg in de SSH-sessie de eerder geüploade python-bestanden toe aan de opslag voor het cluster.

    ```bash
    hdfs dfs -put pigudf.py /pigudf.py
    ```

### <a name="use-pig-udf-shell"></a>Gebruik Pig UDF (shell)

1. Als u verbinding wilt maken met een varken, gebruikt u de volgende opdracht uit uw geopende SSH-sessie:

    ```bash
    pig
    ```

2. Voer de volgende `grunt>` instructies in op de prompt:

   ```pig
   Register wasbs:///pigudf.py using jython as myfuncs;
   LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

3. Na het invoeren van de volgende regel moet de taak beginnen. Zodra de taak is voltooid, retourneert deze uitvoer die vergelijkbaar is met de volgende gegevens:

        ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
        ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
        ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
        ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
        ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

4. Hiermee `quit` wilt u de grunt-shell afsluiten en vervolgens het volgende gebruiken om het pigudf.py-bestand op het lokale bestandssysteem te bewerken:

    ```bash
    nano pigudf.py
    ```

5. Eenmaal in de editor u de `#` volgende regel ongedaan maken door het teken te verwijderen vanaf het begin van de regel:

    ```bash
    #from pig_util import outputSchema
    ```

    Deze regel wijzigt het Python-script om met C Python te werken in plaats van Jython. Zodra de wijziging is aangebracht, gebruikt u **Ctrl+X** om de editor te sluiten. Selecteer **Y**en **voer deze in** om de wijzigingen op te slaan.

6. Gebruik `pig` de opdracht om de shell opnieuw te starten. Zodra u bij `grunt>` de prompt bent, gebruikt u het volgende om het Python-script uit te voeren met behulp van de C Python-tolk.

   ```pig
   Register 'pigudf.py' using streaming_python as myfuncs;
   LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

    Zodra deze taak is voltooid, moet u dezelfde uitvoer zien als toen u het script eerder met Jython hebt uitgevoerd.

### <a name="upload-file-powershell"></a>Bestand uploaden (PowerShell)

PowerShell kan ook worden gebruikt om Hive-query's op afstand uit te voeren. Zorg ervoor dat `pigudf.py` uw werkmap zich bevindt.  Gebruik het volgende PowerShell-script om een `pigudf.py` Hive-query uit te voeren die het script gebruikt:

```PowerShell
# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

# Revise file path as needed
$pathToJythonFile = ".\pigudf.py"


# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$clusterInfo = Get-AzHDInsightCluster -ClusterName $clusterName
$resourceGroup = $clusterInfo.ResourceGroup
$storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
$container=$clusterInfo.DefaultStorageContainer
$storageAccountKey=(Get-AzStorageAccountKey `
   -ResourceGroupName $resourceGroup `
   -Name $storageAccountName)[0].Value

# Create an Azure Storage context
$context = New-AzStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey $storageAccountKey

# Upload local files to an Azure Storage blob
Set-AzStorageBlobContent `
    -File $pathToJythonFile `
    -Blob "pigudf.py" `
    -Container $container `
    -Context $context
```

### <a name="use-pig-udf-powershell"></a>VarkensudF gebruiken (PowerShell)

> [!NOTE]  
> Wanneer u op afstand een taak indient met PowerShell, is het niet mogelijk om C Python als tolk te gebruiken.

PowerShell kan ook worden gebruikt om Pig Latin-taken uit te voeren. Als u een Varkens-Latijnse `pigudf.py` taak wilt uitvoeren die het script gebruikt, gebruikt u het volgende PowerShell-script:

```PowerShell
# Script should stop on failures
$ErrorActionPreference = "Stop"

# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$creds=Get-Credential -UserName "admin" -Message "Enter the login for the cluster"


$PigQuery = "Register wasbs:///pigudf.py using jython as myfuncs;" +
            "LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);" +
            "LOG = FILTER LOGS by LINE is not null;" +
            "DETAILS = foreach LOG generate myfuncs.create_structure(LINE);" +
            "DUMP DETAILS;"

# Create Pig job object
$jobDefinition = New-AzHDInsightPigJobDefinition -Query $PigQuery

# For status bar updates
$activity="Pig job"

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Starting job..."

# Start defined Azure HDInsight job on specified cluster.
$job = Start-AzHDInsightJob `
    -ClusterName $clusterName `
    -JobDefinition $jobDefinition `
    -HttpCredential $creds

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Waiting for the Pig job to complete..."

# Wait for completion or failure of specified job
Wait-AzHDInsightJob `
    -Job $job.JobId `
    -ClusterName $clusterName `
    -HttpCredential $creds

# Uncomment the following to see stderr output
<#
Get-AzHDInsightJobOutput `
    -Clustername $clusterName `
    -JobId $job.JobId `
    -HttpCredential $creds `
    -DisplayOutputType StandardError
#>

# Progress bar (optional)
Write-Progress -Activity $activity "Retrieving output..."

# Gets the log output
Get-AzHDInsightJobOutput `
    -Clustername $clusterName `
    -JobId $job.JobId `
    -HttpCredential $creds
```

De uitvoer voor de taak **Varken** moet vergelijkbaar lijken met de volgende gegevens:

    ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
    ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
    ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
    ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
    ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

## <a name="troubleshooting"></a><a name="troubleshooting"></a>Probleemoplossing

### <a name="errors-when-running-jobs"></a>Fouten bij het uitvoeren van taken

Bij het uitvoeren van de hive-taak u een fout tegenkomen die vergelijkbaar is met de volgende tekst:

    Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.

Dit probleem kan worden veroorzaakt door de regeleinde in het Python-bestand. Veel Windows-editors standaard met behulp van CRLF als de lijn eindigt, maar Linux-toepassingen verwachten meestal LF.

U de volgende PowerShell-instructies gebruiken om de CR-tekens te verwijderen voordat u het bestand uploadt naar HDInsight:

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=148-150)]

### <a name="powershell-scripts"></a>PowerShell-scripts

Beide voorbeeld-PowerShell-scripts die worden gebruikt om de voorbeelden uit te voeren, bevatten een opmerkingsregel die foutuitvoer voor de taak weergeeft. Als u de verwachte uitvoer voor de taak niet ziet, geeft u geen commentaar op de volgende regel en ziet u of de foutgegevens een probleem aangeeft.

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=135-139)]

De foutinformatie (STDERR) en het resultaat van de taak (STDOUT) worden ook aangemeld bij uw HDInsight-opslag.

| Voor deze baan... | Bekijk deze bestanden in de blobcontainer |
| --- | --- |
| Hive |/HivePython/stderr<p>/HivePython/stdout |
| Pig |/PigPython/stderr<p>/PigPython/stdout |

## <a name="next-steps"></a><a name="next"></a>Volgende stappen

Zie [Een module implementeren naar Azure HDInsight](https://blogs.msdn.com/b/benjguin/archive/2014/03/03/how-to-deploy-a-python-module-to-windows-azure-hdinsight.aspx)als u Python-modules moet laden die niet standaard worden geleverd.

Zie de volgende documenten voor andere manieren om Pig, Hive te gebruiken en meer te weten te komen over het gebruik van MapReduce:

* [Apache Hive gebruiken met HDInsight](hdinsight-use-hive.md)
* [MapReduce gebruiken met HDInsight](hdinsight-use-mapreduce.md)
