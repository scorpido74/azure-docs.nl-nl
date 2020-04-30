---
title: 'Zelf studie: R in een Spark-Compute-context gebruiken in azure HDInsight'
description: 'Zelf studie: aan de slag met R en Spark op een Azure HDInsight Machine Learning Services-cluster.'
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 06/21/2019
ms.openlocfilehash: 73ca0d089ab758fb13e69d341337139d79194cc5
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "71121937"
---
# <a name="tutorial-use-r-in-a-spark-compute-context-in-azure-hdinsight"></a>Zelf studie: R in een Spark-Compute-context gebruiken in azure HDInsight

Deze zelf studie biedt een stapsgewijze inleiding op het gebruik van de R-functies in Apache Spark die worden uitgevoerd op een Azure HDInsight Machine Learning Services-cluster.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * De voorbeeld gegevens downloaden naar lokale opslag
> * De gegevens naar de standaard opslag kopiëren
> * Een gegevensset instellen
> * Gegevens bronnen maken
> * Een compute-context voor Spark maken
> * Een lineair model passend maken
> * Samengestelde XDF-bestanden gebruiken
> * XDF naar CSV converteren

## <a name="prerequisites"></a>Vereisten

* Een Azure HDInsight Machine Learning Services-cluster. Ga naar [Apache Hadoop clusters maken met behulp van de Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md) en selecteer in het **cluster type**de optie **milliliters Services**.

## <a name="connect-to-rstudio-server"></a>Verbinding maken met RStudio Server

De RStudio-server wordt uitgevoerd op het Edge-knoop punt van het cluster. Ga naar de volgende site (waarbij *clustername* in de URL de naam is van het HDInsight Machine Learning Services-cluster dat u hebt gemaakt):

```
https://CLUSTERNAME.azurehdinsight.net/rstudio/
```

De eerste keer dat u zich aanmeldt, moet u twee maal verifiëren. Geef bij de eerste verificatie prompt de gebruikers naam en het wacht woord voor de Cluster beheerder op (de standaard instelling is *beheerder*). Geef bij de tweede verificatie prompt de SSH-gebruikers naam en het wacht woord op (de standaard waarde is *sshuser*). Voor volgende aanmeldingen zijn alleen de SSH-referenties vereist.

## <a name="download-the-sample-data-to-local-storage"></a>De voorbeeld gegevens downloaden naar lokale opslag

De *gegevens reeks van de luchtvaart maatschappij 2012 is in een periode* van 12 door komma's gescheiden bestanden die vlucht-en vertrek gegevens voor alle commerciële vluchten binnen de VS voor het jaar 2012 bevatten. Deze gegevensset is groot, met meer dan 6.000.000 metingen.

1. Initialiseer een aantal omgevings variabelen. Voer in de RStudio-Server-console de volgende code in:

    ```R
    bigDataDirRoot <- "/tutorial/data" # root directory on cluster default storage
    localDir <- "/tmp/AirOnTimeCSV2012" # directory on edge node
    remoteDir <- "https://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012" # location of data
    ```

1. Selecteer in het rechterdeel venster het tabblad **omgeving** . De variabelen worden weer gegeven onder **waarden**.

    ![HDInsight R Studio-webconsole](./media/ml-services-tutorial-spark-compute/hdinsight-rstudio-image.png)

1. Maak een lokale map en down load de voorbeeld gegevens. Voer in RStudio de volgende code in:

    ```R
    # Create local directory
    dir.create(localDir)
    
    # Download data to the tmp folder(local)
    download.file(file.path(remoteDir, "airOT201201.csv"), file.path(localDir, "airOT201201.csv"))
    download.file(file.path(remoteDir, "airOT201202.csv"), file.path(localDir, "airOT201202.csv"))
    download.file(file.path(remoteDir, "airOT201203.csv"), file.path(localDir, "airOT201203.csv"))
    download.file(file.path(remoteDir, "airOT201204.csv"), file.path(localDir, "airOT201204.csv"))
    download.file(file.path(remoteDir, "airOT201205.csv"), file.path(localDir, "airOT201205.csv"))
    download.file(file.path(remoteDir, "airOT201206.csv"), file.path(localDir, "airOT201206.csv"))
    download.file(file.path(remoteDir, "airOT201207.csv"), file.path(localDir, "airOT201207.csv"))
    download.file(file.path(remoteDir, "airOT201208.csv"), file.path(localDir, "airOT201208.csv"))
    download.file(file.path(remoteDir, "airOT201209.csv"), file.path(localDir, "airOT201209.csv"))
    download.file(file.path(remoteDir, "airOT201210.csv"), file.path(localDir, "airOT201210.csv"))
    download.file(file.path(remoteDir, "airOT201211.csv"), file.path(localDir, "airOT201211.csv"))
    download.file(file.path(remoteDir, "airOT201212.csv"), file.path(localDir, "airOT201212.csv"))
    ```

    De down load moet ongeveer 9,5 minuten zijn voltooid.

## <a name="copy-the-data-to-default-storage"></a>De gegevens naar de standaard opslag kopiëren

De locatie van de Hadoop Distributed File System (HDFS) wordt opgegeven `airDataDir` met de variabele. Voer in RStudio de volgende code in:

```R
# Set directory in bigDataDirRoot to load the data into
airDataDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")

# Create directory (default storage)
rxHadoopMakeDir(airDataDir)

# Copy data from local storage to default storage
rxHadoopCopyFromLocal(localDir, bigDataDirRoot)
    
# Optional. Verify files
rxHadoopListFiles(airDataDir)
```

De stap moet in ongeveer 10 seconden worden voltooid.

## <a name="set-up-a-dataset"></a>Een gegevensset instellen

1. Maak een bestandssysteem object dat gebruikmaakt van de standaard waarden. Voer in RStudio de volgende code in:

    ```R
    # Define the HDFS (WASB) file system
    hdfsFS <- RxHdfsFileSystem()
    ```

1. Omdat de oorspronkelijke CSV-bestanden in plaats daarvan onhandige variabelenamen hebben, kunt u een *colInfo* -lijst opgeven om ze beter te kunnen beheren. Voer in RStudio de volgende code in:

    ```R
    airlineColInfo <- list(
         MONTH = list(newName = "Month", type = "integer"),
        DAY_OF_WEEK = list(newName = "DayOfWeek", type = "factor",
            levels = as.character(1:7),
            newLevels = c("Mon", "Tues", "Wed", "Thur", "Fri", "Sat",
                          "Sun")),
        UNIQUE_CARRIER = list(newName = "UniqueCarrier", type =
                                "factor"),
        ORIGIN = list(newName = "Origin", type = "factor"),
        DEST = list(newName = "Dest", type = "factor"),
        CRS_DEP_TIME = list(newName = "CRSDepTime", type = "integer"),
        DEP_TIME = list(newName = "DepTime", type = "integer"),
        DEP_DELAY = list(newName = "DepDelay", type = "integer"),
        DEP_DELAY_NEW = list(newName = "DepDelayMinutes", type =
                             "integer"),
        DEP_DEL15 = list(newName = "DepDel15", type = "logical"),
        DEP_DELAY_GROUP = list(newName = "DepDelayGroups", type =
                               "factor",
           levels = as.character(-2:12),
           newLevels = c("< -15", "-15 to -1","0 to 14", "15 to 29",
                         "30 to 44", "45 to 59", "60 to 74",
                         "75 to 89", "90 to 104", "105 to 119",
                         "120 to 134", "135 to 149", "150 to 164",
                         "165 to 179", ">= 180")),
        ARR_DELAY = list(newName = "ArrDelay", type = "integer"),
        ARR_DELAY_NEW = list(newName = "ArrDelayMinutes", type =
                             "integer"),  
        ARR_DEL15 = list(newName = "ArrDel15", type = "logical"),
        AIR_TIME = list(newName = "AirTime", type =  "integer"),
        DISTANCE = list(newName = "Distance", type = "integer"),
        DISTANCE_GROUP = list(newName = "DistanceGroup", type =
                             "factor",
         levels = as.character(1:11),
         newLevels = c("< 250", "250-499", "500-749", "750-999",
             "1000-1249", "1250-1499", "1500-1749", "1750-1999",
             "2000-2249", "2250-2499", ">= 2500")))
    
    varNames <- names(airlineColInfo)
    ```

## <a name="create-data-sources"></a>Gegevens bronnen maken

In een Spark-Compute-context kunt u gegevens bronnen maken met behulp van de volgende functies:

|Functie | Beschrijving |
|---------|-------------|
|`RxTextData` | Een door komma's gescheiden tekst gegevens bron. |
|`RxXdfData` | Gegevens in de indeling van het XDF-gegevens bestand. In RevoScaleR wordt de bestands indeling XDF gewijzigd voor Hadoop om gegevens op te slaan in een samengestelde set bestanden in plaats van één bestand. |
|`RxHiveData` | Hiermee wordt een Hive-gegevens bron object gegenereerd.|
|`RxParquetData` | Hiermee wordt een Parquet-gegevens bron object gegenereerd.|
|`RxOrcData` | Hiermee wordt een Orc-gegevens bron object gegenereerd.|

Maak een [RxTextData](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxtextdata) -object met behulp van de bestanden die u naar HDFS hebt gekopieerd. Voer in RStudio de volgende code in:

```R
airDS <- RxTextData( airDataDir,
                        colInfo = airlineColInfo,
                        varsToKeep = varNames,
                        fileSystem = hdfsFS ) 
```

## <a name="create-a-compute-context-for-spark"></a>Een compute-context voor Spark maken

Als u gegevens wilt laden en analyses wilt uitvoeren op worker-knoop punten, stelt u de compute-context in uw script in op [RxSpark](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxspark). In deze context distribueren R-functies automatisch de werk belasting over alle worker-knoop punten, zonder ingebouwde vereiste voor het beheren van taken of de wachtrij. De Spark-Compute-context `RxSpark` wordt `rxSparkConnect()` tot stand gebracht via of om de Spark-Compute-context te maken, en wordt gebruikt `rxSparkDisconnect()` om terug te keren naar een lokale Compute-context. Voer in RStudio de volgende code in:

```R
# Define the Spark compute context
mySparkCluster <- RxSpark()

# Set the compute context
rxSetComputeContext(mySparkCluster)
```

## <a name="fit-a-linear-model"></a>Een lineair model passend maken

1. Gebruik de functie [rxLinMod](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxlinmod) om een lineair model aan te passen `airDS` met behulp van uw gegevens bron. Voer in RStudio de volgende code in:

    ```R
    system.time(
         delayArr <- rxLinMod(ArrDelay ~ DayOfWeek, data = airDS,
              cube = TRUE)
    )
    ```
    
    Deze stap moet in twee tot drie minuten worden voltooid.

1. Bekijk de resultaten. Voer in RStudio de volgende code in:

    ```R
    summary(delayArr)
    ```

    U ziet de volgende resultaten:

    ```output
    Call:
    rxLinMod(formula = ArrDelay ~ DayOfWeek, data = airDS, cube = TRUE)
    
    Cube Linear Regression Results for: ArrDelay ~ DayOfWeek
    Data: airDataXdf (RxXdfData Data Source)
    File name: /tutorial/data/AirOnTimeCSV2012
    Dependent variable(s): ArrDelay
    Total independent variables: 7 
    Number of valid observations: 6005381
    Number of missing observations: 91381 
     
    Coefficients:
                   Estimate Std. Error t value Pr(>|t|)     | Counts
    DayOfWeek=Mon   3.54210    0.03736   94.80 2.22e-16 *** | 901592
    DayOfWeek=Tues  1.80696    0.03835   47.12 2.22e-16 *** | 855805
    DayOfWeek=Wed   2.19424    0.03807   57.64 2.22e-16 *** | 868505
    DayOfWeek=Thur  4.65502    0.03757  123.90 2.22e-16 *** | 891674
    DayOfWeek=Fri   5.64402    0.03747  150.62 2.22e-16 *** | 896495
    DayOfWeek=Sat   0.91008    0.04144   21.96 2.22e-16 *** | 732944
    DayOfWeek=Sun   2.82780    0.03829   73.84 2.22e-16 *** | 858366
    ---
    Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
    
    Residual standard error: 35.48 on 6005374 degrees of freedom
    Multiple R-squared: 0.001827 (as if intercept included)
    Adjusted R-squared: 0.001826 
    F-statistic:  1832 on 6 and 6005374 DF,  p-value: < 2.2e-16 
    Condition number: 1 
    ```

    De resultaten geven aan dat u alle gegevens hebt verwerkt, 6.000.000 waarnemingen, met behulp van alle CSV-bestanden in de opgegeven map. Omdat u hebt `cube = TRUE`opgegeven, hebt u een geschatte coëfficiënt voor elke dag van de week (en niet het interceptie).

## <a name="use-composite-xdf-files"></a>Samengestelde XDF-bestanden gebruiken

Zoals u hebt gezien, kunt u CSV-bestanden rechtstreeks analyseren met R op Hadoop. U kunt de analyse echter sneller uitvoeren als u de gegevens in een efficiëntere indeling opslaat. De R XDF-bestands indeling is efficiënt, maar is enigszins gewijzigd voor HDFS zodat afzonderlijke bestanden binnen één HDFS-blok blijven. (De grootte van het formaat HDFS is afhankelijk van de installatie tot de installatie, maar is doorgaans 64 MB of 128 MB.) 

Wanneer u [rxImport](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rximport) op Hadoop gebruikt om een set samengestelde XDF-bestanden te maken, geeft u `RxTextData` een gegevens bron, `AirDS` zoals de ingegevens en een `RxXdfData` gegevens bron met File System ingesteld op als het argument outfile. U kunt het `RxXdfData` object vervolgens gebruiken als het argument gegevens in volgende R-analyses.

1. Definieer een `RxXdfData` object. Voer in RStudio de volgende code in:

    ```R
    airDataXdfDir <- file.path(bigDataDirRoot,"AirOnTimeXDF2012")
    
    airDataXdf <- RxXdfData( airDataXdfDir,
                            fileSystem = hdfsFS )
    ```

1. Stel een blok grootte van 250000 rijen in en geef op dat alle gegevens zijn gelezen. Voer in RStudio de volgende code in:

    ```R
    blockSize <- 250000
    numRowsToRead = -1
    ```

1. Importeer de gegevens met `rxImport`behulp van. Voer in RStudio de volgende code in:

    ```R
    rxImport(inData = airDS,
             outFile = airDataXdf,
             rowsPerRead = blockSize,
             overwrite = TRUE,
             numRows = numRowsToRead )
    ```
    
    Deze stap is in een paar minuten voltooid.

1. U kunt hetzelfde lineaire model opnieuw ramen met de nieuwe, snellere gegevens bron. Voer in RStudio de volgende code in:

    ```R
    system.time(
         delayArr <- rxLinMod(ArrDelay ~ DayOfWeek, data = airDataXdf,
              cube = TRUE)
    )
    ```
    
    De stap moet in minder dan een minuut worden uitgevoerd.

1. Bekijk de resultaten. De resultaten moeten overeenkomen met die van de CSV-bestanden. Voer in RStudio de volgende code in:

    ```R
    summary(delayArr)
    ```

## <a name="convert-xdf-to-csv"></a>XDF naar CSV converteren

### <a name="in-a-spark-context"></a>In een Spark-context

Als u uw CSV-bestanden hebt geconverteerd naar een XDF-bestands indeling voor een grotere efficiëntie tijdens het uitvoeren van de analyses, maar u de gegevens nu wilt converteren naar CSV, kunt u dit doen met behulp van [rxDataStep](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxdatastep).

Als u een map met CSV-bestanden wilt maken, `RxTextData` maakt u eerst een-object met behulp van een directory naam als het bestands argument. Dit object vertegenwoordigt de map waarin de CSV-bestanden worden gemaakt. Deze map wordt gemaakt wanneer u de `rxDataStep`uitvoert. Ga vervolgens naar dit `RxTextData` object in het `outFile` argument van de. `rxDataStep` Elk CSV-bestand dat wordt gemaakt, heeft de naam op basis van de mapnaam en gevolgd door een getal.

Stel dat u een map van CSV-bestanden in HDFS wilt schrijven vanuit uw `airDataXdf` samengestelde XDF nadat u de logistieke regressie en voor spelling hebt uitgevoerd, zodat de nieuwe CSV-bestanden de voorspelde waarden en verschillen bevatten. Voer in RStudio de volgende code in:

```R
airDataCsvDir <- file.path(bigDataDirRoot,"AirDataCSV2012")
airDataCsvDS <- RxTextData(airDataCsvDir,fileSystem=hdfsFS)
rxDataStep(inData=airDataXdf, outFile=airDataCsvDS)
```

Deze stap moet ongeveer 2,5 minuten worden voltooid.

U `rxDataStep` hebt één CSV-bestand geschreven voor elk XDFD-bestand in het XDF-bestand. Dit is het standaard gedrag voor het schrijven van CSV-bestanden van samengestelde XDF-bestanden naar HDFS wanneer de compute-context is ingesteld op `RxSpark`.

### <a name="in-a-local-context"></a>In een lokale context

Wanneer u klaar bent met het uitvoeren van uw analyses, kunt u ook uw Compute-context `local` weer omzetten in om te profiteren van `RxTextData` twee argumenten in die u iets meer controle geven wanneer u CSV-bestanden naar `createFileSet` HDFS `rowsPerOutFile`schrijft: en. Wanneer u deze `createFileSet` instelt `TRUE`op, wordt een map met CSV-bestanden geschreven naar de map die u opgeeft. Wanneer u deze `createFileSet` instelt `FALSE`op, wordt één CSV-bestand geschreven. U kunt het tweede argument `rowsPerOutFile`instellen op een geheel getal om aan te geven hoeveel rijen er naar elk CSV-bestand moeten `createFileSet` worden `TRUE`geschreven wanneer dat het geval is.

Voer in RStudio de volgende code in:

```R
rxSetComputeContext("local")
airDataCsvRowsDir <- file.path(bigDataDirRoot,"AirDataCSVRows2012")
airDataCsvRowsDS <- RxTextData(airDataCsvRowsDir, fileSystem=hdfsFS, createFileSet=TRUE, rowsPerOutFile=1000000)
rxDataStep(inData=airDataXdf, outFile=airDataCsvRowsDS)
```

Deze stap moet in ongeveer 10 minuten worden voltooid.

Wanneer `RxSpark` u een compute-context `createFileSet` gebruikt, `TRUE` wordt `rowsPerOutFile` standaard ingesteld op en heeft dit geen effect. Als u één CSV wilt maken of het aantal rijen per bestand wilt aanpassen, moet u daarom een `rxDataStep` `local` Compute-context uitvoeren (de gegevens kunnen zich nog steeds in HDFS bevindt).

## <a name="final-steps"></a>Laatste stappen

1. De gegevens opschonen. Voer in RStudio de volgende code in:

    ```R
    rxHadoopRemoveDir(airDataDir)
    rxHadoopRemoveDir(airDataXdfDir)
    rxHadoopRemoveDir(airDataCsvDir)
    rxHadoopRemoveDir(airDataCsvRowsDir)
    rxHadoopRemoveDir(bigDataDirRoot)
    ```

1. Stop de externe Spark-toepassing. Voer in RStudio de volgende code in:

    ```R
    rxStopEngine(mySparkCluster)
    ```

1. Sluit de R-sessie af. Voer in RStudio de volgende code in:

    ```R
    quit()
    ```

## <a name="clean-up-resources"></a>Resources opschonen

Nadat u de zelf studie hebt voltooid, wilt u het cluster misschien verwijderen. Met HDInsight worden uw gegevens opgeslagen in Azure Storage, zodat u een cluster veilig kunt verwijderen wanneer het niet in gebruik is. U betaalt ook voor een HDInsight-cluster, zelfs wanneer het niet in gebruik is. Omdat de kosten voor het cluster veel keren meer zijn dan de kosten voor opslag, is het economisch zinvol om clusters te verwijderen wanneer ze niet worden gebruikt.

Als u een cluster wilt verwijderen, raadpleegt u [een HDInsight-cluster verwijderen met behulp van uw browser, Power shell of de Azure cli](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd hoe u R-functies gebruikt in Apache Spark die worden uitgevoerd op een HDInsight Machine Learning Services-cluster. Raadpleeg voor meer informatie de volgende artikelen:

* [Opties voor Compute-context voor een Azure HDInsight Machine Learning Services-cluster](r-server-compute-contexts.md)
* [R-functies voor Spark op Hadoop](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler-hadoop-functions)
